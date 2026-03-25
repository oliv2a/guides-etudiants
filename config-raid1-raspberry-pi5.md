# Configuration RAID 1 sur Raspberry Pi 5

Guide complet pour installer un système Raspberry Pi OS avec RAID 1 logiciel pour les données Docker.

## 📋 Prérequis

- Raspberry Pi 5
- 2 x NVMe de taille identique (via HAT PCIe)
- 1 carte SD pour l'installation temporaire
- 1 clé USB pour la sauvegarde (facultatif mais recommandé)

## 🎯 Objectif final

- **Système** : sur NVMe 1 (partitions p1 et p2)
- **Données Docker** : RAID 1 entre NVMe 1 (p3) et NVMe 2 (p1)
- **Redondance** : Les données Docker sont protégées sur 2 disques

---

## 📦 Phase 1 : Sauvegarde (si système existant)

### Monter la clé USB

```bash
# Identifier la clé
lsblk

# Formater en ext4 (EFFACE TOUT)
sudo umount /mnt/backup 2>/dev/null
sudo mkfs.ext4 -L BACKUP /dev/sdX1  # Remplacer sdX1

# Monter
sudo mkdir -p /mnt/backup
sudo mount /dev/sdX1 /mnt/backup
```

### Sauvegarder le système

```bash
# Sauvegarde complète
sudo rsync -aAXv --info=progress2 \
  --exclude={"/dev/*","/proc/*","/sys/*","/tmp/*","/run/*","/mnt/*","/media/*","/lost+found","/swapfile"} \
  / /mnt/backup/raspberry-backup/

# Sauvegarde du boot
sudo rsync -aAXv --info=progress2 /boot/firmware/ /mnt/backup/boot-backup/

# Sauvegarder les informations système
sudo bash << 'EOF'
dpkg --get-selections > /mnt/backup/packages.list
docker ps -a > /mnt/backup/docker-containers.txt 2>/dev/null || true
cat /etc/fstab > /mnt/backup/fstab.backup
lsblk > /mnt/backup/lsblk-before-raid.txt
EOF

# Démonter
sudo sync
sudo umount /mnt/backup
```

---

## 🔧 Phase 2 : Partitionnement des NVMe

### Boot sur carte SD

1. Flasher une carte SD avec **Raspberry Pi OS Lite 64-bit** via Raspberry Pi Imager
2. Configurer SSH et utilisateur dans les paramètres avancés
3. Insérer SEULEMENT la carte SD (retirer les NVMe temporairement)
4. Booter et se connecter en SSH

### Installer les outils

```bash
sudo apt update
sudo apt install mdadm parted gdisk rsync -y
```

### Remettre les 2 NVMe et les partitionner

```bash
# Nettoyer les deux disques
sudo wipefs -a /dev/nvme0n1
sudo wipefs -a /dev/nvme1n1

# Partitionner nvme0n1 (système + RAID)
sudo parted /dev/nvme0n1 mklabel gpt
sudo parted /dev/nvme0n1 mkpart primary fat32 1MiB 513MiB
sudo parted /dev/nvme0n1 set 1 boot on
sudo parted /dev/nvme0n1 mkpart primary ext4 513MiB 50.5GiB
sudo parted /dev/nvme0n1 mkpart primary ext4 50.5GiB 100%

# Partitionner nvme1n1 (RAID uniquement)
sudo parted /dev/nvme1n1 mklabel gpt
sudo parted /dev/nvme1n1 mkpart primary ext4 1MiB 100%

# Vérifier
lsblk
```

**Résultat attendu :**
```
nvme0n1
├─nvme0n1p1  512M   (boot)
├─nvme0n1p2  ~50G   (système)
└─nvme0n1p3  ~183G  (RAID données)
nvme1n1
└─nvme1n1p1  ~233G  (RAID données)
```

---

## 🔁 Phase 3 : Création du RAID 1

### Créer le RAID

```bash
# Créer le RAID 1 (md0)
sudo mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/nvme0n1p3 /dev/nvme1n1p1

# Confirmer avec 'y' si demandé

# Vérifier (synchronisation en cours, ~15 min)
cat /proc/mdstat
sudo mdadm --detail /dev/md0
```

**Note :** La synchronisation se fait en arrière-plan, on peut continuer.

---

## 💾 Phase 4 : Installation du système

### Formater les partitions système

```bash
# Boot en FAT32
sudo mkfs.vfat -F 32 -n BOOT /dev/nvme0n1p1

# Système en ext4
sudo mkfs.ext4 -L rootfs /dev/nvme0n1p2

# RAID en ext4
sudo mkfs.ext4 -L docker-data /dev/md0
```

### Copier le système

```bash
# Créer les points de montage
sudo mkdir -p /mnt/nvme-root
sudo mkdir -p /mnt/nvme-boot

# Monter
sudo mount /dev/nvme0n1p2 /mnt/nvme-root
sudo mount /dev/nvme0n1p1 /mnt/nvme-boot

# Copier le système (10 minutes)
sudo rsync -axHAWX --numeric-ids --info=progress2 \
  --exclude=/mnt \
  --exclude=/proc \
  --exclude=/sys \
  --exclude=/dev \
  --exclude=/tmp \
  --exclude=/run \
  --exclude=/lost+found \
  / /mnt/nvme-root/

# Copier le boot
sudo rsync -axHAWX --numeric-ids --info=progress2 \
  /boot/firmware/ /mnt/nvme-boot/
```

---

## ⚙️ Phase 5 : Configuration du boot

### Récupérer les UUID

```bash
sudo blkid /dev/nvme0n1p1  # UUID boot
sudo blkid /dev/nvme0n1p2  # UUID root
sudo blkid /dev/md0        # UUID RAID
```

### Configurer fstab

```bash
sudo nano /mnt/nvme-root/etc/fstab
```

**Contenu (adapter les UUID) :**
```
proc            /proc           proc    defaults          0       0
UUID=<boot-uuid>  /boot/firmware  vfat    defaults          0       2
UUID=<root-uuid>  /               ext4    defaults,noatime  0       1
UUID=<raid-uuid>  /var/lib/docker ext4    defaults,noatime,nofail  0  2
```

**Important :** L'option `nofail` permet au système de booter même si le RAID échoue.

### Configurer cmdline.txt

```bash
sudo nano /mnt/nvme-boot/cmdline.txt
```

**Remplacer** `root=PARTUUID=...` par `root=PARTUUID=<root-partuuid>` (obtenu avec blkid)

### Créer le dossier Docker

```bash
sudo mkdir -p /mnt/nvme-root/var/lib/docker
```

---

## 🛡️ Phase 6 : Configuration RAID dans initramfs

### Sauvegarder la config mdadm

```bash
# Créer le dossier
sudo mkdir -p /mnt/nvme-root/etc/mdadm

# Sauvegarder la configuration
sudo mdadm --detail --scan | sudo tee /mnt/nvme-root/etc/mdadm/mdadm.conf

# Vérifier
cat /mnt/nvme-root/etc/mdadm/mdadm.conf
```

### Ajouter les modules RAID

```bash
# Créer les dossiers nécessaires
sudo mkdir -p /mnt/nvme-root/dev
sudo mkdir -p /mnt/nvme-root/proc
sudo mkdir -p /mnt/nvme-root/sys
sudo mkdir -p /mnt/nvme-root/tmp
sudo mkdir -p /mnt/nvme-root/run

# Monter pour chroot
sudo mount --bind /dev /mnt/nvme-root/dev
sudo mount --bind /proc /mnt/nvme-root/proc
sudo mount --bind /sys /mnt/nvme-root/sys

# Entrer dans le système
sudo chroot /mnt/nvme-root

# Ajouter les modules RAID
grep -q "raid1" /etc/initramfs-tools/modules || echo "raid1" >> /etc/initramfs-tools/modules
grep -q "md_mod" /etc/initramfs-tools/modules || echo "md_mod" >> /etc/initramfs-tools/modules

# Mettre à jour initramfs
update-initramfs -u -k all

# Sortir
exit

# Démonter
sudo umount /mnt/nvme-root/dev
sudo umount /mnt/nvme-root/proc
sudo umount /mnt/nvme-root/sys
```

---

## 🚀 Phase 7 : Premier boot sur NVMe

### Démonter et redémarrer

```bash
# Démonter les partitions
sudo umount /mnt/nvme-boot
sudo umount /mnt/nvme-root

# Synchroniser
sudo sync

# Arrêter
sudo shutdown -h now
```

### Boot sur NVMe

1. **RETIRER LA CARTE SD**
2. Redémarrer le Pi
3. Attendre ~1 minute
4. Se reconnecter en SSH

```bash
ssh olivier@ServeurMaison.local
```

---

## ✅ Phase 8 : Vérifications post-installation

### Vérifier le système

```bash
# Vérifier que vous êtes sur le NVMe
df -h /

# Vérifier le RAID
cat /proc/mdstat
sudo mdadm --detail /dev/md0

# Vérifier le montage Docker
df -h /var/lib/docker

# Voir tous les montages
lsblk
```

**Résultat attendu :**
```
/ → nvme0n1p2 (49G)
/var/lib/docker → md0 (183G, RAID1)
md0 → active raid1 [UU]
```

### Si le RAID ne s'assemble pas automatiquement

```bash
# Installer mdadm (si pas déjà fait)
sudo apt update
sudo apt install mdadm -y

# Assembler manuellement
sudo mdadm --assemble --scan

# Monter
sudo mount /dev/md0 /var/lib/docker

# Vérifier la config
cat /etc/mdadm/mdadm.conf
cat /etc/initramfs-tools/modules | grep -E "raid1|md_mod"

# Si besoin, mettre à jour initramfs
sudo update-initramfs -u -k all

# Redémarrer pour tester
sudo reboot
```

---

## 🐳 Phase 9 : Installation de Docker

```bash
# Installer Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Ajouter l'utilisateur au groupe docker
sudo usermod -aG docker $USER

# Redémarrer la session SSH pour appliquer le groupe
exit
# Se reconnecter
```

### Vérifier Docker

```bash
docker --version
docker ps
df -h /var/lib/docker  # Doit montrer md0
```

---

## 💾 Phase 10 : Restauration des données (si sauvegarde)

### Monter la clé USB

```bash
# Identifier
lsblk

# Monter
sudo mkdir -p /mnt/backup
sudo mount /dev/sdX1 /mnt/backup
```

### Restaurer les volumes Docker

```bash
# Restaurer les volumes
sudo rsync -av /mnt/backup/raspberry-backup/var/lib/docker/volumes/ /var/lib/docker/volumes/

# Restaurer vos docker-compose.yml
rsync -av /mnt/backup/raspberry-backup/home/olivier/ /home/olivier/

# Vérifier
ls -la /var/lib/docker/volumes/
```

### Relancer les containers

```bash
# Si vous utilisez docker-compose
cd ~/nextcloud  # ou le dossier de vos docker-compose.yml
docker-compose up -d

# Ou restaurer manuellement vos containers
```

### Démonter la clé

```bash
sudo umount /mnt/backup
```

---

## 🔍 Commandes de maintenance RAID

### Vérifier l'état du RAID

```bash
# État général
cat /proc/mdstat

# Détails complets
sudo mdadm --detail /dev/md0

# Surveiller la synchronisation en temps réel
watch cat /proc/mdstat
```

### Simuler une panne de disque

```bash
# Marquer un disque comme défaillant
sudo mdadm --manage /dev/md0 --fail /dev/nvme1n1p1

# Retirer le disque
sudo mdadm --manage /dev/md0 --remove /dev/nvme1n1p1

# Vérifier (doit montrer [U_])
cat /proc/mdstat
```

### Remplacer un disque défaillant

```bash
# Après remplacement physique, ajouter le nouveau disque
sudo mdadm --manage /dev/md0 --add /dev/nvme1n1p1

# Surveiller la reconstruction
watch cat /proc/mdstat
```

### Arrêter proprement le RAID

```bash
# Démonter
sudo umount /var/lib/docker

# Arrêter le RAID
sudo mdadm --stop /dev/md0
```

---

## 🐛 Dépannage

### Le système ne boot pas (emergency mode)

**Cause :** Problème de montage, souvent le RAID.

**Solution :**
1. Booter sur la carte SD
2. Monter le système NVMe
3. Éditer `/mnt/nvme-root/etc/fstab`
4. Ajouter `nofail` à la ligne du RAID :
   ```
   UUID=...  /var/lib/docker  ext4  defaults,noatime,nofail  0  2
   ```

### Le RAID ne s'assemble pas au boot

**Vérifications :**

```bash
# 1. Config mdadm présente ?
cat /etc/mdadm/mdadm.conf

# 2. Modules dans initramfs ?
cat /etc/initramfs-tools/modules | grep -E "raid1|md_mod"

# 3. Timers actifs ?
systemctl list-timers | grep md
```

**Correction :**

```bash
# Assembler manuellement
sudo mdadm --assemble --scan

# Vérifier et corriger la config
sudo mdadm --detail --scan | sudo tee /etc/mdadm/mdadm.conf

# Ajouter les modules
echo "raid1" | sudo tee -a /etc/initramfs-tools/modules
echo "md_mod" | sudo tee -a /etc/initramfs-tools/modules

# Mettre à jour initramfs
sudo update-initramfs -u -k all

# Redémarrer
sudo reboot
```

### /var/lib/docker non monté

```bash
# Vérifier que le RAID existe
cat /proc/mdstat

# Monter manuellement
sudo mount /dev/md0 /var/lib/docker

# Vérifier fstab
cat /etc/fstab | grep docker
```

---

## 📊 Résumé de l'architecture finale

```
Raspberry Pi 5
│
├─ NVMe 0 (250GB)
│  ├─ p1 (512MB)   → /boot/firmware (FAT32)
│  ├─ p2 (50GB)    → / (ext4, système)
│  └─ p3 (183GB)   → RAID 1 (md0)
│                      │
├─ NVMe 1 (250GB)     │
│  └─ p1 (233GB)   → RAID 1 (md0)
│                      │
└─ RAID md0 (183GB) ───┘
   └─ /var/lib/docker (ext4, données Docker protégées)
```

**Avantages :**
- ✅ Boot simple et fiable sur NVMe
- ✅ Données Docker protégées en RAID 1
- ✅ Perte d'un disque = données Docker intactes
- ✅ Pas de complexité de boot sur RAID

---

## 📝 Notes importantes

1. **Option nofail** : Essentielle dans fstab pour éviter les blocages au boot
2. **Synchronisation** : La première synchro RAID prend 15-30 minutes
3. **Performance** : Le RAID 1 offre de meilleures lectures, écritures identiques
4. **Backup** : Le RAID n'est PAS une sauvegarde, continuez à faire des backups réguliers
5. **Surveillance** : Vérifiez régulièrement `cat /proc/mdstat`

---

**Auteur :** Guide créé suite à l'installation réussie sur Raspberry Pi 5  
**Date :** Mars 2026  
**Version Raspberry Pi OS :** Debian Trixie (Bookworm) Lite 64-bit  
**Kernel :** 6.12.47+rpt
