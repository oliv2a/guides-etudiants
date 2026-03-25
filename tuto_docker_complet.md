# Docker : Guide complet pour débutants

## Table des matières
1. [Qu'est-ce que Docker ?](#quest-ce-que-docker)
2. [Avantages et intérêts](#avantages-et-intérêts)
3. [Concepts clés](#concepts-clés)
4. [Installation sur Raspberry Pi](#installation-sur-raspberry-pi)
5. [Docker Compose](#docker-compose)
6. [Commandes essentielles](#commandes-essentielles)

---

## Qu'est-ce que Docker ?

**Docker** est une plateforme de **conteneurisation** qui permet d'empaqueter une application et toutes ses dépendances (bibliothèques, configurations, etc.) dans un **conteneur** isolé et portable.

### Analogie simple

Imaginez Docker comme un **conteneur maritime** :
- Le conteneur peut être transporté partout (bateau, camion, train)
- Son contenu est protégé et isolé
- Il fonctionne de la même manière peu importe où il se trouve

De même, un conteneur Docker :
- Peut tourner sur n'importe quelle machine (Linux, Windows, Mac, Raspberry Pi)
- Contient tout ce dont l'application a besoin
- Fonctionne de manière identique partout

### Docker vs Machine Virtuelle

| Aspect | Machine Virtuelle | Docker |
|--------|-------------------|--------|
| **Isolation** | Système complet (OS invité) | Processus isolés |
| **Taille** | Plusieurs Go | Quelques Mo à centaines de Mo |
| **Démarrage** | Minutes | Secondes |
| **Performance** | Overhead significatif | Quasi-native |
| **Ressources** | Lourdes | Légères |

**En résumé :** Docker est **beaucoup plus léger et rapide** qu'une VM, car il partage le noyau du système hôte au lieu de créer un OS complet.

---

## Avantages et intérêts

### 1. **Portabilité**
- "It works on my machine" n'est plus un problème
- Déploiement identique en développement, test et production
- Fonctionne sur n'importe quel système supportant Docker

### 2. **Isolation**
- Chaque conteneur est isolé des autres
- Pas de conflits de versions de bibliothèques
- Sécurité renforcée : un conteneur compromis n'affecte pas les autres

### 3. **Reproductibilité**
- Configuration déclarative (Dockerfile)
- Builds automatisés et versionnés
- Facilite le travail en équipe

### 4. **Gain de ressources**
- Plusieurs conteneurs sur une même machine
- Idéal pour Raspberry Pi avec ressources limitées
- Démarre et s'arrête en quelques secondes

### 5. **Écosystème riche**
- Docker Hub : des milliers d'images prêtes à l'emploi
- Nextcloud, Gitea, PostgreSQL, Redis, etc.
- Pas besoin de tout installer manuellement

### 6. **Facilité de mise à jour**
- Nouvelle version = nouvelle image
- Retour en arrière simple si problème
- Pas de "pollution" du système hôte

### 7. **Cas d'usage pédagogique**
- TP reproductibles pour tous les étudiants
- Environnements de développement standardisés
- Déploiement rapide de services (bases de données, serveurs web, etc.)

---

## Concepts clés

### Image Docker
Une **image** est un modèle en lecture seule contenant :
- Le système de fichiers de base
- L'application et ses dépendances
- Les configurations par défaut

**Analogie :** C'est comme un "CD d'installation" ou un "moule à gâteau".

```bash
# Télécharger une image depuis Docker Hub
docker pull nginx
```

### Conteneur
Un **conteneur** est une **instance en cours d'exécution** d'une image.

**Analogie :** Si l'image est un moule, le conteneur est le gâteau qui en sort.

```bash
# Créer et démarrer un conteneur depuis une image
docker run -d -p 80:80 nginx
```

### Volume
Les **volumes** permettent de **persister les données** au-delà de la durée de vie d'un conteneur.

Sans volume → les données disparaissent quand le conteneur est supprimé  
Avec volume → les données sont sauvegardées sur le système hôte

```bash
# Monter un dossier local dans un conteneur
docker run -v /home/user/data:/var/www/html nginx
```

### Réseau Docker
Docker crée des **réseaux virtuels** pour que les conteneurs puissent communiquer entre eux.

```bash
# Créer un réseau
docker network create mon-reseau

# Lancer des conteneurs sur ce réseau
docker run --network mon-reseau --name db postgres
docker run --network mon-reseau --name app nginx
```

### Registry (Docker Hub)
Un **registry** est un dépôt d'images Docker.

- **Docker Hub** (hub.docker.com) : registry public officiel
- Images officielles : `nginx`, `postgres`, `python`, etc.
- Images communautaires : `nextcloud/nextcloud`, `gitea/gitea`, etc.

---

## Installation sur Raspberry Pi

### Configuration matérielle testée
- Raspberry Pi 5 avec boot sur NVMe
- Raspberry Pi OS (Debian-based)

### Étapes d'installation

#### 1. Mise à jour du système

```bash
sudo apt update && sudo apt upgrade -y
```

#### 2. Installation des dépendances

```bash
sudo apt install -y ca-certificates curl gnupg
```

#### 3. Ajout de la clé GPG officielle Docker

```bash
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

#### 4. Ajout du dépôt Docker

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

#### 5. Mise à jour de la liste des paquets

```bash
sudo apt update
```

#### 6. Installation de Docker

```bash
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

#### 7. Ajouter l'utilisateur au groupe docker

Pour éviter d'utiliser `sudo` à chaque commande :

```bash
sudo usermod -aG docker $USER
```

**Important :** Déconnectez-vous et reconnectez-vous (ou redémarrez) pour que les changements prennent effet.

#### 8. Activation au démarrage

```bash
sudo systemctl enable docker
```

#### 9. Vérification de l'installation

```bash
# Version de Docker
docker --version

# Version de Docker Compose
docker compose version

# Test avec un conteneur Hello World
docker run hello-world
```

Si le test Hello World fonctionne, Docker est correctement installé ! ✅

---

## Docker Compose

### Qu'est-ce que Docker Compose ?

**Docker Compose** est un outil qui permet de **définir et gérer des applications multi-conteneurs** via un fichier de configuration YAML.

### Pourquoi utiliser Docker Compose ?

#### Sans Docker Compose
Pour lancer une application avec base de données :

```bash
# Créer un réseau
docker network create mon-reseau

# Lancer la base de données
docker run -d --name db --network mon-reseau \
  -e POSTGRES_PASSWORD=secret \
  -v /home/user/db:/var/lib/postgresql/data \
  postgres:15

# Lancer l'application
docker run -d --name app --network mon-reseau \
  -p 8080:80 \
  -e DB_HOST=db \
  -e DB_PASSWORD=secret \
  -v /home/user/app:/var/www/html \
  mon-application
```

**Problèmes :**
- Commandes longues et complexes
- Difficile à reproduire
- Erreurs fréquentes

#### Avec Docker Compose

Tout est défini dans un fichier `docker-compose.yml` :

```yaml
version: '3'

services:
  db:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: secret
    volumes:
      - ./db:/var/lib/postgresql/data
  
  app:
    image: mon-application
    ports:
      - "8080:80"
    environment:
      DB_HOST: db
      DB_PASSWORD: secret
    volumes:
      - ./app:/var/www/html
    depends_on:
      - db
```

**Lancement en une seule commande :**

```bash
docker compose up -d
```

**Avantages :**
- ✅ Configuration lisible et versionnée
- ✅ Reproductible facilement
- ✅ Gestion simplifiée de plusieurs conteneurs
- ✅ Réseau créé automatiquement
- ✅ Ordre de démarrage respecté (`depends_on`)

### Structure d'un fichier docker-compose.yml

```yaml
version: '3'  # Version du format Docker Compose

services:  # Liste des conteneurs
  
  nom-service-1:
    image: nom-image:tag  # Image Docker à utiliser
    container_name: mon-conteneur  # Nom du conteneur (optionnel)
    restart: unless-stopped  # Politique de redémarrage
    ports:  # Mapping de ports (hôte:conteneur)
      - "8080:80"
    volumes:  # Montage de volumes (hôte:conteneur)
      - ./data:/var/www/html
    environment:  # Variables d'environnement
      - MA_VARIABLE=valeur
    depends_on:  # Dépendances (ordre de démarrage)
      - nom-service-2
    networks:  # Réseaux (optionnel)
      - mon-reseau
  
  nom-service-2:
    image: autre-image:tag
    # ...

networks:  # Définition de réseaux personnalisés (optionnel)
  mon-reseau:

volumes:  # Définition de volumes nommés (optionnel)
  mon-volume:
```

### Commandes Docker Compose essentielles

```bash
# Démarrer tous les services en arrière-plan
docker compose up -d

# Arrêter tous les services
docker compose stop

# Arrêter et supprimer les conteneurs
docker compose down

# Voir les logs
docker compose logs

# Suivre les logs en temps réel
docker compose logs -f

# Redémarrer tous les services
docker compose restart

# Voir l'état des services
docker compose ps

# Reconstruire les images
docker compose build

# Mettre à jour et redémarrer
docker compose pull
docker compose up -d
```

### Exemple concret : Stack LAMP

```yaml
version: '3'

services:
  # Serveur web Apache + PHP
  web:
    image: php:8.2-apache
    container_name: lamp-web
    restart: unless-stopped
    ports:
      - "8080:80"
    volumes:
      - ./www:/var/www/html
    depends_on:
      - db
  
  # Base de données MySQL
  db:
    image: mysql:8.0
    container_name: lamp-db
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: myapp
      MYSQL_USER: user
      MYSQL_PASSWORD: password
    volumes:
      - ./db-data:/var/lib/mysql
  
  # PhpMyAdmin (administration de la BDD)
  phpmyadmin:
    image: phpmyadmin:latest
    container_name: lamp-phpmyadmin
    restart: unless-stopped
    ports:
      - "8081:80"
    environment:
      PMA_HOST: db
      PMA_USER: root
      PMA_PASSWORD: rootpassword
    depends_on:
      - db
```

**Utilisation :**
```bash
docker compose up -d
```

Accès :
- Application web : `http://localhost:8080`
- PhpMyAdmin : `http://localhost:8081`

---

## Commandes essentielles

### Gestion des conteneurs

```bash
# Lister les conteneurs en cours d'exécution
docker ps

# Lister tous les conteneurs (même arrêtés)
docker ps -a

# Démarrer un conteneur arrêté
docker start nom-conteneur

# Arrêter un conteneur
docker stop nom-conteneur

# Redémarrer un conteneur
docker restart nom-conteneur

# Supprimer un conteneur (doit être arrêté)
docker rm nom-conteneur

# Supprimer un conteneur en cours d'exécution
docker rm -f nom-conteneur

# Voir les logs d'un conteneur
docker logs nom-conteneur

# Suivre les logs en temps réel
docker logs -f nom-conteneur

# Exécuter une commande dans un conteneur
docker exec -it nom-conteneur bash

# Inspecter un conteneur (configuration complète)
docker inspect nom-conteneur

# Statistiques en temps réel (CPU, RAM, réseau)
docker stats
```

### Gestion des images

```bash
# Lister les images locales
docker images

# Télécharger une image
docker pull nginx:latest

# Supprimer une image
docker rmi nom-image:tag

# Construire une image depuis un Dockerfile
docker build -t mon-image:tag .

# Historique des couches d'une image
docker history nom-image
```

### Gestion des volumes

```bash
# Lister les volumes
docker volume ls

# Créer un volume
docker volume create mon-volume

# Inspecter un volume
docker volume inspect mon-volume

# Supprimer un volume
docker volume rm mon-volume

# Supprimer tous les volumes non utilisés
docker volume prune
```

### Gestion des réseaux

```bash
# Lister les réseaux
docker network ls

# Créer un réseau
docker network create mon-reseau

# Inspecter un réseau
docker network inspect mon-reseau

# Connecter un conteneur à un réseau
docker network connect mon-reseau nom-conteneur

# Supprimer un réseau
docker network rm mon-reseau
```

### Nettoyage

```bash
# Supprimer tous les conteneurs arrêtés
docker container prune

# Supprimer toutes les images non utilisées
docker image prune

# Supprimer tous les volumes non utilisés
docker volume prune

# Nettoyage complet (conteneurs, images, volumes, réseaux non utilisés)
docker system prune -a
```

### Politiques de redémarrage

Lors du lancement d'un conteneur, vous pouvez définir une politique de redémarrage :

```bash
# Ne jamais redémarrer (par défaut)
docker run --restart no nginx

# Toujours redémarrer
docker run --restart always nginx

# Redémarrer sauf si arrêt manuel (RECOMMANDÉ)
docker run --restart unless-stopped nginx

# Redémarrer uniquement en cas d'erreur
docker run --restart on-failure nginx
```

Pour modifier la politique d'un conteneur existant :

```bash
docker update --restart unless-stopped nom-conteneur
```

---

## Bonnes pratiques

### 1. Utiliser des tags de version spécifiques

❌ **Mauvais :**
```yaml
image: nginx
```

✅ **Bon :**
```yaml
image: nginx:1.25
```

**Raison :** Garantit la reproductibilité et évite les surprises lors des mises à jour.

### 2. Toujours utiliser des volumes pour les données persistantes

```yaml
services:
  db:
    image: postgres:15
    volumes:
      - ./db-data:/var/lib/postgresql/data  # Données persistantes
```

### 3. Ne jamais stocker de secrets en clair dans docker-compose.yml

❌ **Mauvais :**
```yaml
environment:
  - DB_PASSWORD=motdepasse123
```

✅ **Bon :** Utiliser un fichier `.env` (à exclure du Git)
```yaml
environment:
  - DB_PASSWORD=${DB_PASSWORD}
```

Fichier `.env` :
```
DB_PASSWORD=motdepasse123
```

### 4. Limiter les ressources si nécessaire

```yaml
services:
  app:
    image: mon-app
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 512M
```

### 5. Utiliser des réseaux pour isoler les services

```yaml
networks:
  frontend:
  backend:

services:
  web:
    networks:
      - frontend
  
  app:
    networks:
      - frontend
      - backend
  
  db:
    networks:
      - backend
```

---

## Dépannage

### Le conteneur ne démarre pas

```bash
# Voir les logs
docker logs nom-conteneur

# Voir les logs en temps réel
docker logs -f nom-conteneur
```

### Port déjà utilisé

```bash
# Vérifier quel processus utilise le port
sudo lsof -i :8080

# Changer le port dans docker-compose.yml
ports:
  - "8082:80"  # Au lieu de 8080
```

### Problème de permissions sur les volumes

```bash
# Donner les bonnes permissions au dossier
sudo chown -R 1000:1000 ./data

# Ou pour certaines applications (comme Nextcloud)
sudo chown -R 33:33 ./data
```

### Conteneur qui redémarre en boucle

```bash
# Voir les logs pour identifier l'erreur
docker logs nom-conteneur

# Démarrer en mode interactif pour débugger
docker run -it --entrypoint /bin/bash nom-image
```

### Réseau Docker qui ne fonctionne pas

```bash
# Recréer les réseaux
docker compose down
docker compose up -d
```

---

## Ressources utiles

- **Documentation officielle :** https://docs.docker.com
- **Docker Hub :** https://hub.docker.com
- **Docker Compose documentation :** https://docs.docker.com/compose
- **Awesome Docker :** https://github.com/veggiemonk/awesome-docker (liste de ressources)

---

## Conclusion

Docker est un outil **incontournable** pour :
- Le **développement moderne** d'applications
- Le **déploiement simplifié** de services
- L'**enseignement** de l'administration système et réseau

**Avantages clés :**
- ✅ Portabilité totale
- ✅ Isolation et sécurité
- ✅ Légèreté (vs VM)
- ✅ Écosystème riche (Docker Hub)
- ✅ Reproductibilité garantie

**Docker Compose** rend la gestion multi-conteneurs **simple et efficace** grâce à une configuration déclarative en YAML.

Pour un usage pédagogique sur Raspberry Pi, Docker permet de **déployer rapidement** des environnements de TP complets et reproductibles pour tous les étudiants.
