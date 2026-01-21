# Guide d'installation de Gitea sur Raspberry Pi

## Introduction

Ce guide explique comment installer et configurer Gitea (un serveur Git local similaire à GitHub) sur un Raspberry Pi 4 avec Raspberry Pi OS Lite. Gitea permet de créer un serveur Git privé pour votre organisation, accessible uniquement sur votre réseau local.

## Prérequis

- Raspberry Pi 4 (4 Go RAM recommandé)
- Raspberry Pi OS Lite installé
- Accès SSH au Raspberry Pi
- Connexion réseau (Ethernet ou WiFi)

---

## Étape 1 : Préparation du système

### Mise à jour du système

```bash
sudo apt update
sudo apt upgrade -y
```

---

## Étape 2 : Installation de Docker

Docker permet de conteneuriser les applications pour faciliter leur déploiement.

### Installation via le script officiel (recommandé pour Raspberry Pi)

```bash
# Télécharger le script d'installation
curl -fsSL https://get.docker.com -o get-docker.sh

# Exécuter le script
sudo sh get-docker.sh
```

### Ajouter l'utilisateur au groupe Docker

Pour éviter d'utiliser `sudo` à chaque commande Docker :

```bash
sudo usermod -aG docker $USER
```

### Redémarrer le Raspberry Pi

```bash
sudo reboot
```

### Vérifier l'installation

Après le redémarrage, vérifiez que Docker fonctionne :

```bash
docker --version
```

Vous devriez voir la version de Docker installée (ex: `Docker version 29.1.2`).

---

## Étape 3 : Installation de Gitea

### Créer un dossier pour les données

```bash
mkdir -p ~/gitea
```

Ce dossier contiendra toutes les données de Gitea (configuration, dépôts Git, base de données).

### Lancer le conteneur Gitea

```bash
docker run -d \
  --name=gitea \
  -p 3000:3000 \
  -p 222:22 \
  -v ~/gitea:/data \
  --restart=always \
  gitea/gitea:latest
```

**Explication des paramètres :**
- `-d` : Exécute en arrière-plan
- `--name=gitea` : Nom du conteneur
- `-p 3000:3000` : Port HTTP pour l'interface web
- `-p 222:22` : Port SSH pour Git
- `-v ~/gitea:/data` : Montage du dossier de données
- `--restart=always` : Redémarre automatiquement au démarrage du système

### Vérifier que Gitea fonctionne

```bash
docker ps
```

Vous devriez voir une ligne avec `gitea/gitea` et le statut `Up`.

### Trouver l'adresse IP du Raspberry Pi

```bash
hostname -I
```

Notez la première adresse IP (ex: `192.168.1.20`).

---

## Étape 4 : Configuration initiale de Gitea

### Accéder à l'interface web

Depuis un autre ordinateur sur le réseau, ouvrez un navigateur et allez à :

```
http://ADRESSE_IP_DU_RASPBERRY:3000
```

Exemple : `http://192.168.1.20:3000`

### Paramètres de configuration

Lors du premier accès, vous verrez une page de configuration. Remplissez les champs suivants :

#### Paramètres de base de données
- **Type de base** : SQLite3 (le plus simple)

#### Paramètres généraux
- **Titre du site** : Nom de votre choix (ex: "Git P18")
- **Domaine du serveur** : L'adresse IP de votre Raspberry (ex: `192.168.1.20`)
- **Port SSH du serveur** : 22
- **Port HTTP** : 3000
- **URL de base** : `http://VOTRE_IP:3000/`

#### Compte administrateur
Créez votre compte administrateur :
- **Nom d'utilisateur** : Votre choix
- **Mot de passe** : Choisissez un mot de passe fort
- **Email** : Votre email

Cliquez sur **"Installer Gitea"**.

---

## Étape 5 : Créer une organisation

### Pourquoi une organisation ?

Une organisation permet de regrouper plusieurs membres et dépôts sous une entité commune.

### Créer l'organisation

1. Connectez-vous avec votre compte administrateur
2. Cliquez sur le **+** en haut à droite
3. Choisissez **"Nouvelle organisation"**
4. Remplissez :
   - **Nom de l'organisation** : Ex: `P18`
   - **Nom complet** : Optionnel
   - **Description** : Optionnel
5. Cliquez sur **"Créer une organisation"**

---

## Étape 6 : Créer des comptes utilisateurs

### Via l'interface d'administration

1. Cliquez sur votre avatar en haut à droite
2. Choisissez **"Administration du site"**
3. Allez dans **"Comptes utilisateur"**
4. Cliquez sur **"Créer un compte utilisateur"**
5. Remplissez les informations pour chaque utilisateur
6. Répétez pour tous les membres de votre équipe

### Vérifier les droits admin

Pour vérifier qu'un utilisateur est administrateur :

```bash
docker exec -u git gitea gitea admin user list
```

---

## Étape 7 : Ajouter des membres à l'organisation

1. Allez sur la page de votre organisation (ex: `http://192.168.1.20:3000/P18`)
2. Cliquez sur l'onglet **"Équipes"**
3. Sélectionnez l'équipe "Owners" ou créez une nouvelle équipe
4. Ajoutez les membres un par un

---

## Étape 8 : Migrer des dépôts depuis GitHub

### Migration simple (import unique)

1. Cliquez sur le **+** en haut à droite
2. Choisissez **"Nouvelle migration"**
3. Cliquez sur l'icône **GitHub**
4. Remplissez :
   - **URL du dépôt** : `https://github.com/utilisateur/nom-depot`
   - **Propriétaire** : Choisissez votre organisation (ex: P18)
   - **Nom du dépôt** : Gardez le même nom ou changez-le
5. Cliquez sur **"Migrer le dépôt"**

### Migration avec miroir (synchronisation automatique)

Pour synchroniser automatiquement GitHub → Gitea :

1. Suivez les mêmes étapes que ci-dessus
2. **Cochez "Ce dépôt sera un miroir"** pendant la migration
3. Configurez l'intervalle de synchronisation (ex: 8h)
4. Après la migration, allez dans **Paramètres → Miroir** pour gérer la synchro

**Note** : Le miroir synchronise uniquement GitHub → Gitea (sens unique).

---

## Utilisation pour les membres

### Cloner un dépôt

Les membres peuvent cloner les dépôts sur leur machine locale :

```bash
git clone http://192.168.1.20:3000/P18/nom-depot.git
```

### Workflow Git standard

```bash
# Modifier des fichiers
git add .
git commit -m "Description des modifications"

# Envoyer vers Gitea
git push

# Récupérer les modifications des autres
git pull
```

---

## Commandes utiles

### Gestion du conteneur Docker

```bash
# Voir les conteneurs en cours d'exécution
docker ps

# Voir tous les conteneurs (même arrêtés)
docker ps -a

# Démarrer Gitea
docker start gitea

# Arrêter Gitea
docker stop gitea

# Redémarrer Gitea
docker restart gitea

# Voir les logs de Gitea
docker logs gitea

# Suivre les logs en temps réel
docker logs -f gitea
```

### Gestion des utilisateurs (ligne de commande)

```bash
# Lister tous les utilisateurs
docker exec -u git gitea gitea admin user list

# Créer un utilisateur
docker exec -u git gitea gitea admin user create \
  --username nouveau_user \
  --password MotDePasse123 \
  --email user@example.com

# Changer le mot de passe d'un utilisateur
docker exec -u git gitea gitea admin user change-password \
  --username nom_user \
  --password NouveauMotDePasse123
```

---

## Dépannage

### Gitea ne démarre pas

```bash
# Vérifier les logs
docker logs gitea

# Redémarrer le conteneur
docker restart gitea
```

### Impossible d'accéder à l'interface web

1. Vérifiez que le conteneur fonctionne : `docker ps`
2. Vérifiez l'adresse IP : `hostname -I`
3. Assurez-vous d'utiliser le bon port : `:3000`
4. Vérifiez le firewall du Raspberry Pi

### Erreur de migration depuis GitHub

1. Vérifiez la connexion Internet : `ping github.com`
2. Si le dépôt est privé, vous aurez besoin d'un token d'accès GitHub
3. Supprimez le dépôt échoué et recommencez la migration

### Réinitialiser complètement Gitea

```bash
# Arrêter et supprimer le conteneur
docker stop gitea
docker rm gitea

# Supprimer les données (ATTENTION : perte de toutes les données)
rm -rf ~/gitea

# Relancer l'installation depuis l'étape 3
```

---

## Bonnes pratiques

### Sécurité

- Choisissez des mots de passe forts pour tous les comptes
- Désactivez l'auto-inscription si vous ne voulez pas que n'importe qui crée un compte
- Configurez une IP fixe pour le Raspberry Pi dans votre routeur

### Sauvegarde

Le dossier `~/gitea` contient toutes vos données. Pensez à le sauvegarder régulièrement :

```bash
# Créer une sauvegarde
tar -czf gitea-backup-$(date +%Y%m%d).tar.gz ~/gitea

# Copier sur une autre machine ou un disque externe
```

### Performance

- Un Raspberry Pi 4 avec 4 Go de RAM peut gérer confortablement 20-30 utilisateurs
- Pour de meilleures performances, utilisez une carte SD rapide (classe 10 minimum)
- Envisagez un SSD USB pour un accès plus rapide aux données

---

## Ressources

- Documentation officielle Gitea : https://docs.gitea.com
- Docker Hub Gitea : https://hub.docker.com/r/gitea/gitea
- Documentation Docker : https://docs.docker.com

---

## Conclusion

Vous disposez maintenant d'un serveur Git local fonctionnel pour votre organisation. Tous les membres peuvent collaborer sur des projets en utilisant un workflow Git standard, le tout hébergé localement sur votre réseau.

Pour toute question ou problème, consultez la section **Dépannage** ou la documentation officielle de Gitea.
