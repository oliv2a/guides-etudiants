# Tutoriel : Installation et Configuration de VSFTPD sur Linux

## Introduction

Ce tutoriel explique comment installer et configurer **VSFTPD** (Very Secure FTP Daemon) sur un système Linux avec :
- Accès **multi-utilisateurs** (etudiant1 à etudiant9)
- Accès **anonyme** avec répertoire dédié
- Sécurisation avec liste d'utilisateurs autorisés
- Configuration des ports PASV

---

## Prérequis

- Système Linux (Debian/Ubuntu/Raspbian)
- Accès root ou sudo
- Connexion internet pour télécharger les paquets

---

## Étape 1 : Installation de VSFTPD

### Mise à jour du système

Avant toute installation, mettez à jour la liste des paquets :

```bash
sudo apt update && sudo apt upgrade -y
```

### Installation de VSFTPD

Installez le serveur FTP VSFTPD :

```bash
sudo apt install vsftpd -y
```

### Vérification du service

Vérifiez que le service est bien démarré :

```bash
sudo systemctl status vsftpd
```

Vous devriez voir `active (running)` en vert.

**Si le service n'est pas démarré :**
```bash
sudo systemctl start vsftpd
sudo systemctl enable vsftpd
```

---

## Étape 2 : Configuration de VSFTPD

### Sauvegarder la configuration d'origine

Avant de modifier le fichier de configuration, faites une copie de sauvegarde :

```bash
sudo cp /etc/vsftpd.conf /etc/vsftpd.conf.backup
```

### Éditer le fichier de configuration

Ouvrez le fichier de configuration :

```bash
sudo nano /etc/vsftpd.conf
```

### Paramètres à modifier

Recherchez et modifiez (ou ajoutez) les lignes suivantes :

```bash
# Activer l'accès anonyme
anonymous_enable=YES

# Activer les utilisateurs locaux
local_enable=YES

# Autoriser l'écriture (upload)
write_enable=YES

# Confiner les utilisateurs dans leur répertoire home
chroot_local_user=YES

# Autoriser l'écriture dans le chroot (nécessaire avec chroot_local_user=YES)
allow_writeable_chroot=YES

# Définir le répertoire pour l'accès anonyme
anon_root=/anonyme/

# Configuration des ports PASV (mode passif)
pasv_min_port=40000
pasv_max_port=50000
```

**Explications :**
- `anonymous_enable=YES` : Permet les connexions anonymes
- `local_enable=YES` : Permet aux utilisateurs locaux de se connecter
- `write_enable=YES` : Autorise l'upload de fichiers
- `chroot_local_user=YES` : Empêche les utilisateurs de sortir de leur répertoire home
- `allow_writeable_chroot=YES` : Nécessaire pour éviter une erreur avec chroot
- `anon_root=/anonyme/` : Définit le dossier racine pour les connexions anonymes
- `pasv_min_port` et `pasv_max_port` : Plage de ports pour le mode passif

**Sauvegarder et quitter :**
- Appuyez sur `Ctrl+X`
- Tapez `O` (Oui) ou `Y` (Yes)
- Appuyez sur `Entrée`

### Créer le répertoire anonyme

Créez le répertoire pour les connexions anonymes :

```bash
sudo mkdir -p /anonyme
```

Définir les permissions :
- Propriétaire : `nobody:nogroup` (utilisateur système)
- Permissions : lecture seule (555)

```bash
sudo chown nobody:nogroup /anonyme
sudo chmod 555 /anonyme
```

**Note :** Les utilisateurs anonymes pourront uniquement lire (télécharger) des fichiers, pas en écrire.

### Créer un sous-dossier pour les uploads anonymes (optionnel)

Si vous souhaitez permettre les uploads anonymes :

```bash
sudo mkdir /anonyme/upload
sudo chown ftp:ftp /anonyme/upload
sudo chmod 755 /anonyme/upload
```

Ajoutez dans `/etc/vsftpd.conf` :
```bash
anon_upload_enable=YES
anon_mkdir_write_enable=YES
```

### Redémarrer VSFTPD

Appliquez les modifications en redémarrant le service :

```bash
sudo systemctl restart vsftpd
```

Vérifiez que le service fonctionne :

```bash
sudo systemctl status vsftpd
```

---

## Étape 3 : Création des Utilisateurs (etudiant1 à etudiant9)

### Création manuelle des utilisateurs

Pour chaque utilisateur (etudiant1 à etudiant9), exécutez :

```bash
sudo useradd -m -d /home/etudiant1 -s /bin/bash etudiant1
sudo passwd etudiant1
```

Entrez le mot de passe lorsque demandé.

**Répétez pour chaque utilisateur :**

```bash
sudo useradd -m -d /home/etudiant2 -s /bin/bash etudiant2
sudo passwd etudiant2

sudo useradd -m -d /home/etudiant3 -s /bin/bash etudiant3
sudo passwd etudiant3

# ... et ainsi de suite jusqu'à etudiant9
```

### Création automatisée (script)

Pour gagner du temps, utilisez cette boucle :

```bash
for i in {1..9}; do
    sudo useradd -m -d /home/etudiant$i -s /bin/bash etudiant$i
    echo "etudiant$i:motdepasse$i" | sudo chpasswd
    echo "Utilisateur etudiant$i créé avec le mot de passe : motdepasse$i"
done
```

**⚠️ Important :** Changez `motdepasse$i` par un mot de passe plus sécurisé !

### Créer la liste des utilisateurs autorisés

Créez le fichier de liste des utilisateurs autorisés :

```bash
sudo nano /etc/vsftpd.userlist
```

Ajoutez les utilisateurs (un par ligne) :

```
etudiant1
etudiant2
etudiant3
etudiant4
etudiant5
etudiant6
etudiant7
etudiant8
etudiant9
```

**Sauvegarder et quitter** (Ctrl+X, O, Entrée)

### Activer la liste dans la configuration

Éditez à nouveau le fichier de configuration :

```bash
sudo nano /etc/vsftpd.conf
```

Ajoutez ou modifiez ces lignes :

```bash
# Activer la liste d'utilisateurs
userlist_enable=YES

# Fichier contenant la liste
userlist_file=/etc/vsftpd.userlist

# NO = seuls les utilisateurs de la liste peuvent se connecter
# YES = les utilisateurs de la liste sont interdits
userlist_deny=NO
```

**Sauvegarder et quitter**

### Redémarrer VSFTPD

Appliquez les modifications :

```bash
sudo systemctl restart vsftpd
```

---

## Étape 4 : Test des Accès

### Test de la connexion anonyme

**Depuis le serveur lui-même :**

```bash
ftp localhost
```

À l'invite :
- **Nom** : `anonymous`
- **Mot de passe** : Appuyez sur Entrée (laissez vide)

**Commandes FTP utiles :**
```
ls                  # Lister les fichiers
cd dossier          # Changer de répertoire
get fichier.txt     # Télécharger un fichier
bye                 # Quitter
```

### Test de la connexion utilisateur

**Depuis le serveur :**

```bash
ftp localhost
```

À l'invite :
- **Nom** : `etudiant1`
- **Mot de passe** : Le mot de passe que vous avez défini

**Vérifier le chroot :**
Une fois connecté, tapez `pwd`. Vous devriez être dans `/` mais en réalité confiné à `/home/etudiant1`.

### Test depuis un autre ordinateur

**Depuis un PC Windows :**

1. Ouvrir l'invite de commandes
2. Taper :
```cmd
ftp 192.168.1.XXX
```
(Remplacez par l'IP de votre serveur)

**Depuis un PC Linux/Mac :**
```bash
ftp 192.168.1.XXX
```

**Avec un client graphique (FileZilla) :**

1. Télécharger et installer [FileZilla](https://filezilla-project.org/)
2. Se connecter :
   - **Hôte** : `ftp://192.168.1.XXX`
   - **Identifiant** : `etudiant1`
   - **Mot de passe** : Le mot de passe défini
   - **Port** : `21`

---

## Configuration avancée

### Autoriser les connexions depuis l'extérieur

Si votre serveur est derrière un pare-feu ou routeur, ouvrez les ports :

**Ports à ouvrir :**
- **Port 21** : Commandes FTP
- **Ports 40000-50000** : Mode passif (PASV)

**Avec UFW (Ubuntu Firewall) :**

```bash
sudo ufw allow 21/tcp
sudo ufw allow 40000:50000/tcp
sudo ufw reload
```

### Logs et dépannage

**Consulter les logs VSFTPD :**

```bash
sudo tail -f /var/log/vsftpd.log
```

Si le fichier n'existe pas, activez le logging dans `/etc/vsftpd.conf` :

```bash
xferlog_enable=YES
xferlog_file=/var/log/vsftpd.log
```

Puis redémarrez :
```bash
sudo systemctl restart vsftpd
```

### Limiter la vitesse de transfert (optionnel)

Pour limiter la bande passante par utilisateur :

```bash
# Limiter à 1 Mo/s (1000000 octets)
local_max_rate=1000000
anon_max_rate=500000
```

### Limiter le nombre de connexions simultanées

```bash
max_clients=50
max_per_ip=5
```

---

## Sécurité

### Désactiver l'accès anonyme (si non nécessaire)

Si vous ne voulez pas d'accès anonyme :

```bash
anonymous_enable=NO
```

### Utiliser FTPS (FTP sécurisé avec SSL/TLS)

Pour chiffrer les connexions FTP :

1. Générer un certificat SSL :
```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/ssl/private/vsftpd.key \
-out /etc/ssl/certs/vsftpd.crt
```

2. Configurer VSFTPD :
```bash
sudo nano /etc/vsftpd.conf
```

Ajouter :
```bash
rsa_cert_file=/etc/ssl/certs/vsftpd.crt
rsa_private_key_file=/etc/ssl/private/vsftpd.key
ssl_enable=YES
allow_anon_ssl=NO
force_local_data_ssl=YES
force_local_logins_ssl=YES
ssl_tlsv1=YES
ssl_sslv2=NO
ssl_sslv3=NO
require_ssl_reuse=NO
ssl_ciphers=HIGH
```

3. Redémarrer :
```bash
sudo systemctl restart vsftpd
```

---

## Dépannage

### Erreur "500 OOPS: vsftpd: refusing to run with writable root inside chroot()"

**Solution :** Ajouter dans `/etc/vsftpd.conf` :
```bash
allow_writeable_chroot=YES
```

### Les utilisateurs ne peuvent pas se connecter

**Vérifier :**
- Que l'utilisateur existe : `cat /etc/passwd | grep etudiant1`
- Que l'utilisateur est dans la liste : `cat /etc/vsftpd.userlist`
- Les logs : `sudo tail -f /var/log/vsftpd.log`

### Connexion refusée depuis l'extérieur

**Vérifier :**
- Le pare-feu : `sudo ufw status`
- Que le service écoute : `sudo netstat -tuln | grep 21`
- La configuration du routeur (port forwarding si nécessaire)

### Mode passif ne fonctionne pas

**Ajouter dans `/etc/vsftpd.conf` :**
```bash
pasv_enable=YES
pasv_address=VOTRE_IP_PUBLIQUE
pasv_min_port=40000
pasv_max_port=50000
```

---

## Commandes utiles

### Gestion du service

```bash
# Démarrer
sudo systemctl start vsftpd

# Arrêter
sudo systemctl stop vsftpd

# Redémarrer
sudo systemctl restart vsftpd

# Statut
sudo systemctl status vsftpd

# Activer au démarrage
sudo systemctl enable vsftpd

# Désactiver au démarrage
sudo systemctl disable vsftpd
```

### Gestion des utilisateurs

```bash
# Lister les utilisateurs FTP
cat /etc/vsftpd.userlist

# Supprimer un utilisateur
sudo userdel -r etudiant1

# Changer le mot de passe
sudo passwd etudiant1

# Verrouiller un compte
sudo usermod -L etudiant1

# Déverrouiller un compte
sudo usermod -U etudiant1
```

---

## Résumé de la configuration

**Fichiers importants :**
- Configuration principale : `/etc/vsftpd.conf`
- Liste des utilisateurs : `/etc/vsftpd.userlist`
- Logs : `/var/log/vsftpd.log`
- Répertoire anonyme : `/anonyme/`

**Utilisateurs créés :**
- etudiant1 à etudiant9 avec accès restreint à leur home
- anonymous avec accès en lecture seule à `/anonyme/`

**Ports utilisés :**
- Port 21 : Commandes FTP
- Ports 40000-50000 : Mode passif (PASV)

---

## Ressources

- [Documentation officielle VSFTPD](https://security.appspot.com/vsftpd.html)
- [Man page vsftpd.conf](https://linux.die.net/man/5/vsftpd.conf)
- [Client FTP FileZilla](https://filezilla-project.org/)

---

**Votre serveur FTP VSFTPD est maintenant opérationnel ! 🎉**