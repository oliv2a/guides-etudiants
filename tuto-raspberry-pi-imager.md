# Tutoriel : Créer une image Raspberry Pi avec Raspberry Pi Imager

## Introduction

Ce tutoriel vous guide pour créer une carte SD bootable pour votre Raspberry Pi en utilisant **Raspberry Pi Imager**, l'outil officiel recommandé par la Fondation Raspberry Pi.

Raspberry Pi Imager permet de :
- Télécharger et installer automatiquement un système d'exploitation
- Utiliser une image préalablement téléchargée
- Configurer le système avant même le premier démarrage (WiFi, SSH, utilisateur)

---

## Prérequis

### Matériel nécessaire
- **Carte SD** (minimum 8 Go, 16 Go ou plus recommandé)
- **Lecteur de carte SD** (USB ou intégré à votre ordinateur)
- **Ordinateur** (Windows, macOS ou Linux)
- **Connexion internet** (si téléchargement direct)

### Logiciel
- **Raspberry Pi Imager** (gratuit)

---

## Étape 1 : Télécharger et installer Raspberry Pi Imager

### Windows

1. Allez sur le site officiel : [https://www.raspberrypi.com/software/](https://www.raspberrypi.com/software/)
2. Cliquez sur **"Download for Windows"**
3. Exécutez le fichier `.exe` téléchargé
4. Suivez les instructions d'installation

### macOS

1. Allez sur le site officiel : [https://www.raspberrypi.com/software/](https://www.raspberrypi.com/software/)
2. Cliquez sur **"Download for macOS"**
3. Ouvrez le fichier `.dmg`
4. Glissez l'application dans le dossier Applications

### Linux (Debian/Ubuntu)

```bash
sudo apt update
sudo apt install rpi-imager
```

Ou téléchargez le paquet `.deb` depuis le site officiel.

**Vérification :**
Une fois installé, lancez **Raspberry Pi Imager** depuis le menu de vos applications.

---

## Étape 2 : Préparer la carte SD

1. **Insérez la carte SD** dans votre lecteur de carte
2. **Sauvegardez les données importantes** : la carte sera entièrement effacée !
3. **Vérifiez** que la carte est bien détectée par votre ordinateur

⚠️ **ATTENTION** : Toutes les données sur la carte SD seront supprimées lors de l'installation.

---

## Méthode 1 : Installation avec téléchargement automatique

### Étape 3A : Lancer Raspberry Pi Imager

1. Ouvrez **Raspberry Pi Imager**
2. Vous verrez trois boutons principaux :
   - **Choisir l'OS** (Operating System)
   - **Choisir le stockage** (carte SD)
   - **Suivant** (pour continuer)

---

### Étape 4A : Choisir le système d'exploitation

1. Cliquez sur **"Choisir l'OS"** ou **"Choose OS"**

2. Vous avez plusieurs options :

   **Pour un usage standard :**
   - **Raspberry Pi OS (64-bit)** - Recommandé pour Raspberry Pi 4/5
   - **Raspberry Pi OS (32-bit)** - Pour Raspberry Pi 3 ou antérieur
   
   **Variantes disponibles :**
   - **Raspberry Pi OS (with desktop)** - Avec interface graphique
   - **Raspberry Pi OS Lite** - Version minimale sans interface (ligne de commande uniquement)
   - **Raspberry Pi OS Full** - Version complète avec logiciels préinstallés

   **Autres systèmes :**
   - Ubuntu
   - LibreELEC (média center)
   - RetroPie (émulation de jeux rétro)
   - Et bien d'autres...

3. **Sélectionnez** le système que vous souhaitez installer

**Recommandation :** Pour un usage éducatif/développement, choisissez **"Raspberry Pi OS (64-bit)"**

---

### Étape 5A : Sélectionner la carte SD

1. Cliquez sur **"Choisir le stockage"** ou **"Choose Storage"**
2. **Sélectionnez votre carte SD** dans la liste
3. Vérifiez bien la capacité pour ne pas vous tromper de périphérique !

⚠️ **ATTENTION** : Assurez-vous de sélectionner la bonne carte SD et non un autre disque dur !

---

### Étape 6A : Configuration avancée (recommandé)

Avant d'écrire l'image, vous pouvez **pré-configurer** le système :

1. Cliquez sur **"Suivant"**
2. Une popup apparaît : **"Voulez-vous appliquer les paramètres de personnalisation ?"**
3. Cliquez sur **"Modifier les paramètres"**

**Configuration recommandée :**

#### Onglet "Général"
- ✅ **Définir le nom d'hôte** : `raspberrypi` (ou un nom personnalisé)
- ✅ **Activer SSH** : cochez cette case
  - Choisir : **"Utiliser l'authentification par mot de passe"**
- ✅ **Définir le nom d'utilisateur et le mot de passe** :
  - Nom d'utilisateur : `pi` (ou votre choix)
  - Mot de passe : choisissez un mot de passe fort
- ✅ **Configurer le réseau sans fil** (si vous utilisez WiFi) :
  - SSID : nom de votre réseau WiFi
  - Mot de passe : mot de passe WiFi
  - Pays : `FR` (France)
- ✅ **Définir les paramètres régionaux** :
  - Fuseau horaire : `Europe/Paris`
  - Disposition du clavier : `fr` (AZERTY)

#### Onglet "Services"
- ✅ **Activer SSH** : déjà fait dans l'onglet précédent

#### Onglet "Options"
- ✅ **Éjecter automatiquement** : pratique pour retirer la carte sans risque

4. Cliquez sur **"Oui"** pour appliquer ces paramètres

---

### Étape 7A : Écrire l'image sur la carte SD

1. Cliquez sur **"Oui"** pour confirmer
2. Une fenêtre vous demande de confirmer : **toutes les données seront effacées**
3. Cliquez sur **"Oui"** pour continuer
4. Entrez votre **mot de passe administrateur** si demandé (Windows/macOS/Linux)

**Le processus commence :**
- **Téléchargement** de l'image (selon votre connexion internet)
- **Écriture** sur la carte SD
- **Vérification** de l'intégrité

**Durée estimée :** 5 à 20 minutes selon la vitesse de votre connexion et de votre carte SD.

---

### Étape 8A : Finalisation

Une fois terminé :
1. Le message **"Write Successful"** s'affiche
2. La carte SD est automatiquement éjectée (si option activée)
3. **Retirez la carte SD** en toute sécurité
4. **Insérez-la dans votre Raspberry Pi**

Passez à l'**Étape 9** pour le premier démarrage.

---

## Méthode 2 : Installation avec une image locale

Si vous avez **déjà téléchargé** une image Raspberry Pi OS (fichier `.img` ou `.img.xz`), suivez ces étapes :

### Étape 3B : Lancer Raspberry Pi Imager

1. Ouvrez **Raspberry Pi Imager**

---

### Étape 4B : Sélectionner une image personnalisée

1. Cliquez sur **"Choisir l'OS"**
2. Descendez tout en bas et cliquez sur **"Use custom"** ou **"Utiliser une image personnalisée"**
3. **Naviguez** jusqu'au fichier image téléchargé
4. **Sélectionnez** le fichier (`.img`, `.img.xz`, `.zip`)

**Types de fichiers acceptés :**
- `.img` (image brute)
- `.img.xz` (image compressée XZ)
- `.zip` (image compressée ZIP)
- `.gz` (image compressée GZIP)

---

### Étape 5B : Sélectionner la carte SD

1. Cliquez sur **"Choisir le stockage"**
2. **Sélectionnez votre carte SD**

---

### Étape 6B : Configuration avancée

Suivez les mêmes instructions que l'**Étape 6A** ci-dessus.

---

### Étape 7B : Écrire l'image

1. Cliquez sur **"Suivant"**
2. Confirmez l'écriture
3. Le processus d'écriture commence (pas de téléchargement cette fois)

**Durée estimée :** 3 à 10 minutes selon la vitesse de votre carte SD.

---

### Étape 8B : Finalisation

Une fois terminé :
1. Message **"Write Successful"**
2. **Retirez la carte SD**
3. **Insérez-la dans le Raspberry Pi**

---

## Étape 9 : Premier démarrage du Raspberry Pi

### Installation dans le Raspberry Pi

1. **Insérez la carte SD** dans le slot du Raspberry Pi (face vers le haut)
2. **Connectez** :
   - Écran (HDMI)
   - Clavier/souris (USB)
   - Alimentation (dernière étape)
3. **Branchez l'alimentation** : le Raspberry Pi démarre automatiquement

### Premier démarrage (avec interface graphique)

1. Le système démarre (LEDs clignotent)
2. L'écran affiche le logo Raspberry Pi
3. Le bureau apparaît après 1-2 minutes

**Si vous avez configuré SSH et WiFi** : le Raspberry Pi est directement accessible en réseau !

### Premier démarrage (version Lite - sans interface)

1. Connexion automatique ou demande de login
2. Si login demandé :
   - Login : `pi` (ou celui que vous avez configuré)
   - Mot de passe : celui que vous avez défini

---

## Étape 10 : Vérifications post-installation

### Vérifier la connexion réseau

**Via WiFi (si configuré) :**
```bash
ip addr show wlan0
```

**Via Ethernet :**
```bash
ip addr show eth0
```

Notez l'**adresse IP** affichée (ex: `192.168.1.100`)

### Mettre à jour le système

```bash
sudo apt update
sudo apt upgrade -y
```

### Vérifier SSH (si activé)

Depuis un autre ordinateur :
```bash
ssh pi@192.168.1.100
```
(Remplacez par l'IP de votre Raspberry Pi)

---

## Différences entre les versions de Raspberry Pi OS

| Version | Taille | Interface | Usage recommandé |
|---------|--------|-----------|------------------|
| **Lite** | ~500 Mo | ❌ Aucune (ligne de commande) | Serveurs, projets embarqués |
| **Desktop** | ~1 Go | ✅ Interface graphique | Usage général, éducation |
| **Full** | ~2.5 Go | ✅ Interface + logiciels | Débutants, multimédia |

---

## Où télécharger les images Raspberry Pi OS

### Téléchargement officiel

**Site officiel :**
- [https://www.raspberrypi.com/software/operating-systems/](https://www.raspberrypi.com/software/operating-systems/)

**Versions disponibles :**
- Raspberry Pi OS with desktop (recommandé)
- Raspberry Pi OS with desktop and recommended software
- Raspberry Pi OS Lite

**Formats :**
- `.img.xz` : fichier compressé (plus petit, à décompresser)
- Torrent : téléchargement via BitTorrent

### Pour un téléchargement préalable

Si vous avez une **connexion lente** ou voulez **préparer plusieurs cartes** :

1. Téléchargez l'image depuis le site officiel
2. Conservez le fichier `.img.xz` ou `.zip`
3. Utilisez la **Méthode 2** avec Raspberry Pi Imager

---

## Dépannage

### La carte SD n'est pas détectée

**Solutions :**
- Vérifiez que le lecteur de carte fonctionne
- Essayez un autre port USB
- Testez la carte SD sur un autre appareil
- Formatez la carte en FAT32 si elle est corrompue

### Erreur d'écriture

**Solutions :**
- Désactivez temporairement l'antivirus
- Exécutez Raspberry Pi Imager en tant qu'administrateur
- Essayez une autre carte SD (certaines sont défectueuses)

### Le Raspberry Pi ne démarre pas

**Vérifications :**
- La LED rouge (alimentation) est-elle allumée ?
- La LED verte (activité) clignote-t-elle ?
- L'écran affiche-t-il quelque chose ?
- La carte SD est-elle correctement insérée ?

**Solutions :**
- Réécrivez l'image sur la carte
- Testez avec une autre carte SD
- Vérifiez l'alimentation (minimum 5V 2.5A pour Raspberry Pi 3/4)

### Impossible de se connecter en SSH

**Vérifications :**
- SSH a bien été activé lors de la configuration ?
- Le Raspberry Pi est sur le même réseau ?
- Le pare-feu bloque-t-il le port 22 ?

**Trouver l'IP du Raspberry Pi :**
```bash
# Sur Linux/Mac
arp -a | grep raspberry

# Ou utilisez un scanner réseau comme Angry IP Scanner
```

---

## Conseils et bonnes pratiques

### Choix de la carte SD

✅ **Recommandé :**
- Carte de marque reconnue (SanDisk, Samsung, Kingston)
- Classe 10 minimum (U1 ou U3)
- 16 Go ou plus pour le confort
- Application A1 ou A2 (optimisé pour les applications)

❌ **À éviter :**
- Cartes génériques de mauvaise qualité
- Cartes trop anciennes
- Cartes trop petites (<8 Go)

### Sauvegardes

- **Créez une image de sauvegarde** une fois votre système configuré
- Utilisez `Win32DiskImager` ou `dd` pour créer une copie complète

### Sécurité

✅ **Bonnes pratiques :**
- Changez le mot de passe par défaut
- Mettez à jour régulièrement : `sudo apt update && sudo apt upgrade`
- Configurez un pare-feu si exposé sur internet
- Utilisez des clés SSH au lieu de mots de passe

---

## Alternatives à Raspberry Pi Imager

Si vous préférez d'autres outils :

### Windows
- **Win32DiskImager** : simple et efficace
- **Rufus** : polyvalent
- **Balena Etcher** : interface moderne

### macOS
- **Balena Etcher**
- Ligne de commande avec `dd`

### Linux
- **dd** (ligne de commande)
- **Balena Etcher**
- **GNOME Disks**

---

## Ressources supplémentaires

### Documentation officielle
- [Guide de démarrage Raspberry Pi](https://www.raspberrypi.com/documentation/)
- [Forum Raspberry Pi](https://forums.raspberrypi.com)

### Tutoriels vidéo
- Chaîne YouTube officielle Raspberry Pi
- Tutoriels francophones sur YouTube

### Communauté
- [Reddit r/raspberry_pi](https://www.reddit.com/r/raspberry_pi/)
- Forums français dédiés au Raspberry Pi

---

## Résumé des étapes

**Méthode avec téléchargement automatique :**
1. ✅ Installer Raspberry Pi Imager
2. ✅ Insérer la carte SD
3. ✅ Choisir l'OS (téléchargement automatique)
4. ✅ Choisir le stockage (carte SD)
5. ✅ Configurer SSH, WiFi, utilisateur
6. ✅ Écrire l'image
7. ✅ Insérer dans le Raspberry Pi
8. ✅ Premier démarrage

**Méthode avec image locale :**
1. ✅ Télécharger l'image au préalable
2. ✅ Installer Raspberry Pi Imager
3. ✅ Insérer la carte SD
4. ✅ Choisir "Use custom" et sélectionner l'image
5. ✅ Choisir le stockage
6. ✅ Configurer les paramètres
7. ✅ Écrire l'image
8. ✅ Premier démarrage

---

**Vous êtes maintenant prêt à utiliser votre Raspberry Pi ! 🚀**