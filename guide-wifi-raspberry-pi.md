# Guide de Configuration WiFi - Raspberry Pi OS Lite

## Contexte
Ce guide explique comment configurer le WiFi sur Raspberry Pi OS Lite, avec ou sans accès Ethernet.

## Deux Approches Principales

### Approche A : Configuration AVANT le premier démarrage (SANS Ethernet)
**Recommandée si vous n'avez pas d'accès Ethernet**

### Approche B : Dépannage APRÈS installation (AVEC Ethernet)
**Si le WiFi ne fonctionne pas après installation**

---

# APPROCHE A : Configuration Sans Ethernet

## Prérequis
- Raspberry Pi Imager installé sur votre PC
- Carte SD vierge
- Les informations de votre réseau WiFi (SSID et mot de passe)

## Étape 1 : Utiliser Raspberry Pi Imager

1. **Téléchargez et lancez Raspberry Pi Imager** depuis https://www.raspberrypi.com/software/

2. **Sélectionnez votre matériel :**
   - Choisissez le modèle exact : Raspberry Pi 4, Pi Zero, etc.
   - ⚠️ **IMPORTANT** : Sélectionnez le BON modèle, car cela affecte les pilotes

3. **Choisissez l'OS :**
   - Raspberry Pi OS Lite (recommandé pour serveur)
   - Ou Raspberry Pi OS Legacy Lite 32-bit (plus stable sur matériel ancien)

4. **AVANT de graver, cliquez sur l'icône de paramètres (roue dentée) :**
   - ✅ Activez SSH
   - ✅ Définissez nom d'utilisateur et mot de passe
   - ✅ **Configurez le WiFi :**
     - SSID : votre nom de réseau
     - Mot de passe : votre mot de passe WiFi
     - **Pays WiFi : FR** (ou votre code pays ISO) ⚠️ OBLIGATOIRE
   - ✅ Définissez la locale (fuseau horaire, clavier)

5. **Gravez la carte SD**

6. **Insérez la carte dans le Raspberry Pi et démarrez**

## Étape 2 : Premier démarrage

**Attendez 2-3 minutes** que le système démarre et se connecte au WiFi.

### Trouver l'adresse IP du Raspberry Pi

**Méthode 1 : Via votre box/routeur**
- Connectez-vous à l'interface web de votre box
- Cherchez dans la liste des appareils connectés
- Repérez "raspberrypi" ou le nom que vous avez défini

**Méthode 2 : Via mDNS (si votre PC supporte)**
```bash
ssh pi@raspberrypi.local
```
(Remplacez "raspberrypi" par le hostname que vous avez défini)

**Méthode 3 : Scanner le réseau (Windows)**
```cmd
arp -a
```
Cherchez une adresse MAC commençant par `B8:27:EB`, `DC:A6:32` ou `E4:5F:01` (Raspberry Pi Foundation)

**Méthode 4 : Scanner avec nmap (Linux/Mac)**
```bash
nmap -sn 192.168.1.0/24
```
(Adaptez la plage IP à votre réseau)

## Étape 3 : Vérification après connexion SSH

Une fois connecté en SSH :

```bash
# Vérifier le statut du WiFi
nmcli device status

# Vérifier l'adresse IP
ip addr show wlan0

# Tester la connectivité
ping -c 4 8.8.8.8
```

## ⚠️ Si le WiFi ne se connecte PAS après le premier démarrage

### Diagnostic avec clavier/écran

Si vous avez accès au terminal du Pi :

**1. Vérifiez rfkill :**
```bash
rfkill list
```

Si "Soft blocked: yes" :
```bash
sudo rfkill unblock wifi
```

**2. Vérifiez NetworkManager :**
```bash
nmcli device status
```

Si `wlan0` est "disconnected" :

```bash
sudo nmcli device wifi rescan
sleep 3
sudo nmcli device wifi list
```

**3. Reconnectez-vous manuellement :**
```bash
sudo nmcli device wifi connect "VotreSSID" password "VotreMotDePasse"
```

**4. ⚠️ Solution miracle si NetworkManager ne fonctionne pas :**

Reconfigurez via `raspi-config` :
```bash
sudo raspi-config
```
- System Options → Wireless LAN
- Entrez SSID et mot de passe
- Redémarrez

**Cette méthode résout souvent les problèmes de configuration incomplète de l'Imager.**

### Bug connu : Raspberry Pi OS Bookworm 2025

Si vous utilisez une version très récente (2025) et que le WiFi ne fonctionne toujours pas :

```bash
sudo nano /boot/firmware/cmdline.txt
```

À la fin de la ligne (sans créer de nouvelle ligne), ajoutez :
```
brcmfmac.feature_disable=0x82000
```

Redémarrez :
```bash
sudo reboot
```

---

# APPROCHE B : Dépannage Avec Ethernet

## Symptômes
- Le WiFi ne se connecte pas automatiquement
- `raspi-config` affiche "There was an error running option S1 Wireless LAN"
- L'interface `wlan0` est détectée mais ne fonctionne pas

## Diagnostic Initial

### 1. Vérifier si l'interface WiFi est détectée
```bash
ip link show
```
Vous devriez voir une interface `wlan0` listée.

### 2. Vérifier le statut rfkill
```bash
rfkill list
```

Si vous voyez `Soft blocked: yes`, c'est la cause du problème.

## Solution : Installation de NetworkManager

### Étape 1 : Débloquer le WiFi
```bash
sudo rfkill unblock wifi
```

### Étape 2 : Installer NetworkManager
```bash
sudo apt update
sudo apt install network-manager
```

### Étape 3 : Activer NetworkManager
```bash
sudo systemctl enable NetworkManager
sudo systemctl start NetworkManager
```

### Étape 4 : Désactiver wpa_supplicant (éviter les conflits)
```bash
sudo systemctl disable wpa_supplicant
sudo systemctl stop wpa_supplicant
```

### Étape 5 : Se connecter au réseau WiFi
```bash
sudo nmcli device wifi connect "VotreSSID" password "VotreMotDePasse"
```

### Étape 6 : Vérifier la connexion
```bash
nmcli device status
ip addr show wlan0
ping -c 4 8.8.8.8
```

## Rendre la configuration permanente

### Créer un service pour débloquer le WiFi au démarrage

1. Créer le fichier de service :
```bash
sudo nano /etc/systemd/system/unblock-wifi.service
```

2. Ajouter le contenu suivant :
```ini
[Unit]
Description=Unblock WiFi at boot
DefaultDependencies=no
Before=network-pre.target
Wants=network-pre.target

[Service]
Type=oneshot
ExecStart=/usr/sbin/rfkill unblock all
RemainAfterExit=yes

[Install]
WantedBy=sysinit.target
```

3. Activer le service :
```bash
sudo systemctl daemon-reload
sudo systemctl enable unblock-wifi.service
```

### Configuration alternative : Règle udev

Vous pouvez également créer une règle udev :

```bash
sudo nano /etc/udev/rules.d/80-wifi-unblock.rules
```

Ajouter :
```
ACTION=="add", SUBSYSTEM=="rfkill", ATTR{type}=="wlan", ATTR{soft}="0"
```

Recharger les règles :
```bash
sudo udevadm control --reload-rules
```

## Optimisations dans config.txt

Ajoutez ces lignes dans `/boot/firmware/config.txt` :

```bash
sudo nano /boot/firmware/config.txt
```

Ajouter à la fin :
```
# USB quirks pour stabilité WiFi
program_usb_boot_mode=1
usb_max_current_enable=1
```

## Vérification après redémarrage

Après un `sudo reboot`, vérifiez :

```bash
# Vérifier le statut du WiFi
nmcli device status

# Vérifier l'adresse IP
ip addr show wlan0

# Tester la connectivité
ping -c 4 8.8.8.8
```

## Dépannage

### Le WiFi se bloque encore après redémarrage
Vérifiez que le service unblock-wifi est actif :
```bash
sudo systemctl status unblock-wifi.service
```

### NetworkManager ne voit pas les réseaux
Forcez un nouveau scan :
```bash
sudo rfkill unblock wifi
sudo nmcli radio wifi on
sudo nmcli device wifi rescan
sleep 3
sudo nmcli device wifi list
```

### Lister les réseaux disponibles
```bash
sudo nmcli device wifi list
```

### Se reconnecter manuellement
```bash
sudo rfkill unblock wifi
sudo nmcli radio wifi on
sudo nmcli device set wlan0 managed yes
sudo nmcli device wifi connect "VotreSSID" password "VotreMotDePasse"
```

## Commandes utiles

### Voir les connexions enregistrées
```bash
nmcli connection show
```

### Supprimer une connexion
```bash
nmcli connection delete "nom-connexion"
```

### Désactiver/activer le WiFi
```bash
nmcli radio wifi off
nmcli radio wifi on
```

### Voir les détails d'une interface
```bash
nmcli device show wlan0
```

## Notes importantes

- **NetworkManager** gère automatiquement le rfkill et se reconnecte au démarrage
- L'approche avec `wpa_supplicant` manuel est plus complexe et sujette aux problèmes de rfkill
- Le service `unblock-wifi` assure que le WiFi n'est jamais bloqué au démarrage
- Sur Raspberry Pi OS Lite récent, NetworkManager est la solution recommandée

## Problèmes Courants et Solutions

### Le WiFi se connecte manuellement mais pas au redémarrage

**Solution : Reconfigurer via raspi-config**

Même si vous avez déjà configuré le WiFi via l'Imager ou nmcli, parfois la configuration n'est pas correctement persistée.

```bash
sudo raspi-config
```

1. Allez dans "System Options"
2. Sélectionnez "Wireless LAN"
3. Entrez votre SSID
4. Entrez votre mot de passe
5. Redémarrez

**Cette méthode résout mystérieusement beaucoup de problèmes de persistence !**

### Le WiFi fonctionne puis se déconnecte

Vérifiez la gestion d'alimentation :
```bash
iwconfig wlan0 | grep "Power Management"
```

Si activée, désactivez-la :
```bash
sudo iwconfig wlan0 power off
```

Pour rendre permanent, créez un fichier :
```bash
sudo nano /etc/network/if-up.d/disable-power-management
```

Ajoutez :
```bash
#!/bin/sh
iwconfig wlan0 power off
```

Rendez exécutable :
```bash
sudo chmod +x /etc/network/if-up.d/disable-power-management
```

### Guillemets courbes vs droits

⚠️ **IMPORTANT** : Les fichiers de configuration WiFi doivent utiliser des **guillemets droits `"`** et NON des **guillemets courbes `""`**

Si vous copiez-collez depuis Word ou certains éditeurs, les guillemets peuvent être mal encodés.

Vérifiez dans `/etc/wpa_supplicant/wpa_supplicant.conf` :
```bash
# CORRECT
ssid="olivier"

# INCORRECT
ssid="olivier"
```

### Le Pi Zero ne se connecte pas au WiFi 5GHz

Le Raspberry Pi Zero et Pi Zero W ne supportent **QUE le WiFi 2.4GHz**.

Assurez-vous que votre réseau est en 2.4GHz, pas en 5GHz.

## Résumé des Solutions par Cas

### Cas 1 : Installation neuve SANS Ethernet
1. ✅ Utilisez Raspberry Pi Imager avec configuration WiFi
2. ✅ Assurez-vous de définir le code pays WiFi
3. ✅ Si ça ne marche pas : reconfigurez via `raspi-config` avec clavier/écran
4. ✅ En dernier recours : ajoutez le paramètre kernel pour le bug Bookworm

### Cas 2 : Système existant qui ne se connecte plus
1. ✅ Vérifiez `rfkill list` et débloquez si nécessaire
2. ✅ Utilisez `nmcli` pour scanner et connecter
3. ✅ Reconfigurez via `raspi-config` pour la persistence
4. ✅ Créez le service `unblock-wifi` si rfkill se réactive au boot

### Cas 3 : Système existant AVEC Ethernet disponible
1. ✅ Connectez via Ethernet
2. ✅ Installez NetworkManager si absent
3. ✅ Configurez WiFi via `nmcli`
4. ✅ Créez le service `unblock-wifi.service` pour la persistance
5. ✅ Testez avec un redémarrage

## Ligne de Commande de Secours

Si vraiment rien ne fonctionne, cette séquence devrait vous connecter :

```bash
sudo rfkill unblock wifi
sudo ip link set wlan0 up
sudo wpa_supplicant -B -i wlan0 -c /etc/wpa_supplicant/wpa_supplicant.conf
sudo dhcpcd wlan0
```

Avec un fichier `/etc/wpa_supplicant/wpa_supplicant.conf` minimal :
```
country=FR

network={
    ssid="VotreSSID"
    psk="VotreMotDePasse"
}
```

---

Votre Raspberry Pi devrait maintenant se connecter automatiquement au WiFi à chaque démarrage !
