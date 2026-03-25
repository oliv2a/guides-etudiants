# Installation de Nextcloud avec Docker sur Raspberry Pi

## Prérequis

- Raspberry Pi (testé sur Pi 5) avec Raspberry Pi OS
- Docker et Docker Compose installés
- Accès SSH au Raspberry Pi

---

## 1. Préparation de l'environnement

### Créer la structure de dossiers

```bash
mkdir -p ~/nextcloud
cd ~/nextcloud
```

---

## 2. Configuration avec Docker Compose

### Créer le fichier docker-compose.yml

```bash
nano docker-compose.yml
```

### Contenu du fichier

```yaml
version: '3'

services:
  nextcloud-db:
    image: mariadb:10.11
    container_name: nextcloud-db
    restart: unless-stopped
    command: --transaction-isolation=READ-COMMITTED --log-bin=binlog --binlog-format=ROW
    volumes:
      - ./db:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=rootpassword
      - MYSQL_PASSWORD=nextcloud
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud

  nextcloud-app:
    image: nextcloud:latest
    container_name: nextcloud-app
    restart: unless-stopped
    ports:
      - 8081:80
    volumes:
      - ./data:/var/www/html
    environment:
      - MYSQL_PASSWORD=nextcloud
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_HOST=nextcloud-db
    depends_on:
      - nextcloud-db
```

**Important :** 
- Port d'accès : `8081` (modifiable selon vos besoins)
- Les données sont stockées dans `./data` et `./db`
- `restart: unless-stopped` assure le redémarrage automatique au boot

---

## 3. Lancement de Nextcloud

> **📌 Note importante :** Toutes les commandes `docker compose` doivent être exécutées depuis le répertoire `~/nextcloud` où se trouve le fichier `docker-compose.yml`. Si vous changez de répertoire, pensez à revenir avec `cd ~/nextcloud`.

### Démarrer les conteneurs

**Important :** Assurez-vous d'être dans le répertoire contenant le fichier `docker-compose.yml` avant de lancer les commandes.

```bash
cd ~/nextcloud
docker compose up -d
```

### Vérifier que tout fonctionne

```bash
docker compose ps
```

Vous devriez voir les deux conteneurs avec le statut "Up".

---

## 4. Configuration initiale

### Accéder à l'interface web

Ouvrez votre navigateur et accédez à :

```
http://[IP_du_Raspberry_Pi]:8081
```

### Page de configuration initiale

1. **Créez un compte administrateur**
   - Nom d'utilisateur : à votre choix
   - Mot de passe : choisissez un mot de passe fort

2. **Configuration de la base de données**
   - Ces paramètres sont déjà configurés automatiquement via docker-compose
   - Ne modifiez rien si les champs sont pré-remplis

3. **Applications recommandées**
   - Vous pouvez installer les applications proposées ou les ignorer
   - Les applications peuvent être ajoutées ultérieurement via Settings → Apps

---

## 5. Gestion des utilisateurs et permissions

### Créer un groupe

1. Cliquez sur votre **avatar** (en haut à droite) → **Users**
2. Créez un groupe (ex: `etudiants`)

### Créer des utilisateurs

1. Dans **Users**, cliquez sur **+ New user**
2. Remplissez :
   - Username
   - Password
   - Assignez au groupe créé

### Partager des dossiers avec permissions spécifiques

1. Dans **Files**, créez vos dossiers
2. Cliquez sur les **3 points** à côté d'un dossier → **Details**
3. Onglet **Sharing** → Partagez avec le groupe ou un utilisateur
4. Configurez les permissions :
   - ✅ Read (lecture)
   - ✅ Download (téléchargement)
   - ✅ Create (création/upload)
   - ✅ Edit (modification)
   - ✅ Delete (suppression)

**Avantage de Nextcloud :** Contrairement à Filebrowser, vous pouvez définir des permissions **différentes par dossier** pour un même utilisateur.

---

## 6. Commandes utiles

### Arrêter Nextcloud

```bash
cd ~/nextcloud
docker compose stop
```

### Redémarrer Nextcloud

```bash
cd ~/nextcloud
docker compose restart
```

### Voir les logs

```bash
cd ~/nextcloud
docker logs nextcloud-app
docker logs nextcloud-db
```

### Lister tous les utilisateurs (via terminal)

```bash
docker exec -it nextcloud-app su -s /bin/bash www-data -c "php occ user:list"
```

### Sauvegarder les données

Les données importantes sont dans :
- `~/nextcloud/data` : fichiers et configuration Nextcloud
- `~/nextcloud/db` : base de données MariaDB

Sauvegardez ces dossiers régulièrement !

---

## 7. Accès depuis l'extérieur (optionnel)

Pour accéder à Nextcloud depuis internet :

1. **Configurez la redirection de port** sur votre box internet (port 8081)
2. **Ajoutez votre domaine** dans la configuration Nextcloud :

```bash
docker exec -it nextcloud-app su -s /bin/bash www-data -c "php occ config:system:set trusted_domains 1 --value=votre-domaine.com"
```

3. **Utilisez HTTPS** (recommandé) avec Let's Encrypt / Certbot

---

## 8. Dépannage

### Le conteneur ne démarre pas

```bash
docker compose logs
```

### Problème de permissions

```bash
sudo chown -R 33:33 ~/nextcloud/data
```

### Réinitialiser complètement

```bash
cd ~/nextcloud
docker compose down
sudo rm -rf db/ data/
docker compose up -d
```

---

## Conclusion

Vous disposez maintenant d'un serveur Nextcloud fonctionnel avec :
- ✅ Gestion multi-utilisateurs
- ✅ Permissions granulaires par dossier
- ✅ Redémarrage automatique au boot
- ✅ Interface web moderne et intuitive

**Cas d'usage pédagogique :**
- Partage de ressources de cours en lecture seule
- Dépôt de travaux par les étudiants
- Collaboration sur documents
- Communication via Talk (optionnel)
