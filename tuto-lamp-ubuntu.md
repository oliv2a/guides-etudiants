# Tutoriel : Installation Apache, PHP, MariaDB et phpMyAdmin sur Ubuntu

## Introduction

Ce tutoriel explique comment installer une pile **LAMP** complète (Linux, Apache, MySQL/MariaDB, PHP) sur Ubuntu/Debian avec phpMyAdmin pour la gestion de bases de données.

**Stack installée :**
- **Apache2** : Serveur web
- **PHP** : Langage de script côté serveur
- **MariaDB** : Système de gestion de base de données (fork de MySQL)
- **phpMyAdmin** : Interface web pour gérer MariaDB

---

## Prérequis

- Système Ubuntu/Debian/Raspbian
- Accès root ou sudo
- Connexion internet

---

## 0) Préliminaires

### Mise à jour du système

Avant toute installation, mettez à jour le système :

```bash
sudo apt update
sudo apt upgrade -y
```

**Explications :**
- `apt update` : Met à jour la liste des paquets disponibles
- `apt upgrade -y` : Met à jour tous les paquets installés (le `-y` accepte automatiquement)

---

## 1) Installation d'Apache

### Installer Apache2

```bash
sudo apt install -y apache2
```

### Démarrer et activer Apache

```bash
# Démarrer le service
sudo systemctl start apache2

# Activer au démarrage du système
sudo systemctl enable apache2

# Vérifier le statut
sudo systemctl status apache2
```

Vous devriez voir `active (running)` en vert.

### Test de fonctionnement

Ouvrez un navigateur et accédez à :

```
http://localhost/
```

Ou depuis un autre ordinateur du réseau :

```
http://[IP_DU_SERVEUR]/
```

**Vous devriez voir la page par défaut d'Apache :** "Apache2 Ubuntu Default Page"

### Trouver l'adresse IP du serveur

```bash
hostname -I
```

ou

```bash
ip addr show
```

---

## 2) Installation de PHP et modules

### Installer PHP avec les modules essentiels

```bash
sudo apt install -y php libapache2-mod-php php-mysql php-cli php-curl php-gd php-mbstring php-xml php-zip
```

**Modules installés :**
- `php` : Interpréteur PHP
- `libapache2-mod-php` : Module Apache pour PHP
- `php-mysql` : Connexion à MySQL/MariaDB
- `php-cli` : Interface en ligne de commande
- `php-curl` : Requêtes HTTP
- `php-gd` : Manipulation d'images
- `php-mbstring` : Gestion des chaînes multi-octets
- `php-xml` : Traitement XML
- `php-zip` : Compression/décompression ZIP

### Redémarrer Apache

```bash
sudo systemctl restart apache2
```

### Vérifier la version de PHP

```bash
php -v
```

### Tester PHP

Créez un fichier de test :

```bash
echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/info.php
```

Accédez à :

```
http://localhost/info.php
```

Vous devriez voir la page d'information PHP.

**⚠️ Sécurité :** Supprimez ce fichier après le test :

```bash
sudo rm /var/www/html/info.php
```

---

## 3) Installation de MariaDB

### Installer MariaDB Server

```bash
sudo apt install -y mariadb-server
```

### Démarrer et activer MariaDB

```bash
# Démarrer le service
sudo systemctl start mariadb

# Activer au démarrage
sudo systemctl enable mariadb

# Vérifier le statut
sudo systemctl status mariadb
```

### Sécuriser l'installation

Lancez le script de sécurisation :

```bash
sudo mysql_secure_installation
```

**Répondez aux questions :**

1. **Enter current password for root (enter for none):**  
   → Appuyez sur `Entrée` (pas de mot de passe par défaut)

2. **Switch to unix_socket authentication [Y/n]:**  
   → Tapez `n` (on va utiliser un mot de passe)

3. **Change the root password? [Y/n]:**  
   → Tapez `Y` puis entrez un mot de passe fort

4. **Remove anonymous users? [Y/n]:**  
   → Tapez `Y` (sécurité)

5. **Disallow root login remotely? [Y/n]:**  
   → Tapez `Y` (sécurité)

6. **Remove test database and access to it? [Y/n]:**  
   → Tapez `Y` (nettoyage)

7. **Reload privilege tables now? [Y/n]:**  
   → Tapez `Y` (appliquer les changements)

### Se connecter à MariaDB

```bash
sudo mysql -u root -p
```

Entrez le mot de passe root que vous venez de définir.

**Pour quitter :**
```sql
EXIT;
```

---

## 4) Créer une base de données et un utilisateur

### Se connecter à MariaDB

```bash
sudo mysql -u root -p
```

### Créer une base de données

```sql
CREATE DATABASE dbname;
```

Remplacez `dbname` par le nom de votre base de données (exemple : `monsite`, `test_db`, etc.)

### Créer un utilisateur

```sql
CREATE USER 'dbuser'@'localhost' IDENTIFIED BY 'dbpass';
```

Remplacez :
- `dbuser` : nom d'utilisateur
- `dbpass` : mot de passe (choisissez un mot de passe fort !)

### Donner les privilèges

```sql
GRANT ALL PRIVILEGES ON dbname.* TO 'dbuser'@'localhost';
```

### Appliquer les changements

```sql
FLUSH PRIVILEGES;
```

### Quitter

```sql
EXIT;
```

### Exemple complet

```sql
CREATE DATABASE ma_base;
CREATE USER 'mon_user'@'localhost' IDENTIFIED BY 'MotDePasse123!';
GRANT ALL PRIVILEGES ON ma_base.* TO 'mon_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

### Tester la connexion

```bash
mysql -u mon_user -p ma_base
```

---

## 5) Installation de phpMyAdmin

### Installer phpMyAdmin

```bash
sudo apt install -y phpmyadmin
```

**Pendant l'installation, plusieurs questions vous seront posées :**

1. **Choisir le serveur web à configurer :**  
   → Sélectionnez `apache2` (utilisez la barre d'espace pour sélectionner)  
   → Appuyez sur `Tab` puis `Entrée` pour valider

2. **Configurer la base de données pour phpmyadmin avec dbconfig-common ?**  
   → Choisissez `Oui`

3. **Mot de passe de l'administrateur MySQL :**  
   → Entrez le mot de passe root de MariaDB

4. **Mot de passe pour phpMyAdmin :**  
   → Choisissez un mot de passe ou laissez vide pour en générer un automatiquement

### Créer un lien symbolique

```bash
sudo ln -s /etc/phpmyadmin/apache.conf /etc/apache2/conf-available/phpmyadmin.conf
```

### Activer la configuration

```bash
sudo a2enconf phpmyadmin
```

### Recharger Apache

```bash
sudo systemctl reload apache2
```

### Accéder à phpMyAdmin

Ouvrez un navigateur et accédez à :

```
http://localhost/phpmyadmin
```

Ou depuis un autre ordinateur :

```
http://[IP_DU_SERVEUR]/phpmyadmin
```

**Connexion :**
- **Utilisateur :** `root` ou l'utilisateur créé (`mon_user`)
- **Mot de passe :** Le mot de passe correspondant

---

## 6) Configuration de PHP

### Trouver la version de PHP

```bash
php -v
```

Exemple de sortie : `PHP 8.1.2`

### Éditer le fichier php.ini

Remplacez `8.1` par votre version de PHP :

```bash
sudo nano /etc/php/8.1/apache2/php.ini
```

### Paramètres recommandés à modifier

Recherchez et modifiez ces lignes (utilisez `Ctrl+W` pour rechercher) :

**Affichage des erreurs (développement uniquement) :**
```ini
display_errors = On
error_reporting = E_ALL
```

**Taille maximale des uploads :**
```ini
upload_max_filesize = 64M
post_max_size = 64M
```

**Limite de temps d'exécution :**
```ini
max_execution_time = 300
max_input_time = 300
```

**Limite de mémoire :**
```ini
memory_limit = 256M
```

**Timezone :**
```ini
date.timezone = Europe/Paris
```

### Redémarrer Apache

Après toute modification du php.ini :

```bash
sudo systemctl restart apache2
```

### Vérifier les paramètres

Créez un fichier de test :

```bash
echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/phpinfo.php
```

Accédez à `http://localhost/phpinfo.php` et recherchez les valeurs modifiées.

**N'oubliez pas de supprimer le fichier après :**
```bash
sudo rm /var/www/html/phpinfo.php
```

---

## 7) Configuration HTTPS (optionnel)

### Installer Certbot

Certbot permet d'obtenir des certificats SSL/TLS gratuits avec Let's Encrypt :

```bash
sudo apt install -y certbot python3-certbot-apache
```

### Obtenir un certificat SSL

**Important :** Votre serveur doit être accessible depuis internet avec un nom de domaine.

```bash
sudo certbot --apache
```

**Suivez les instructions :**
1. Entrez votre email
2. Acceptez les conditions d'utilisation
3. Choisissez le domaine à sécuriser
4. Choisissez la redirection HTTP → HTTPS (recommandé)

### Renouvellement automatique

Certbot configure automatiquement un cron pour renouveler les certificats.

**Tester le renouvellement :**
```bash
sudo certbot renew --dry-run
```

### Accéder en HTTPS

Votre site sera accessible via :

```
https://votre-domaine.com
```

---

## Configuration supplémentaire

### Modifier le propriétaire du répertoire web

Pour permettre à l'utilisateur courant de modifier les fichiers web :

```bash
sudo chown -R $USER:www-data /var/www/html
sudo chmod -R 755 /var/www/html
```

### Activer le module de réécriture d'URL (mod_rewrite)

Utile pour les frameworks (WordPress, Laravel, etc.) :

```bash
sudo a2enmod rewrite
sudo systemctl restart apache2
```

### Créer des Virtual Hosts

Pour héberger plusieurs sites :

```bash
sudo nano /etc/apache2/sites-available/monsite.conf
```

Contenu exemple :

```apache
<VirtualHost *:80>
    ServerName monsite.local
    DocumentRoot /var/www/monsite
    
    <Directory /var/www/monsite>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    
    ErrorLog ${APACHE_LOG_DIR}/monsite_error.log
    CustomLog ${APACHE_LOG_DIR}/monsite_access.log combined
</VirtualHost>
```

Activer le site :

```bash
sudo a2ensite monsite
sudo systemctl reload apache2
```

---

## Dépannage

### Apache ne démarre pas

**Vérifier les erreurs :**
```bash
sudo systemctl status apache2
sudo journalctl -xeu apache2
```

**Tester la configuration :**
```bash
sudo apache2ctl configtest
```

### PHP ne fonctionne pas

**Vérifier le module PHP :**
```bash
sudo a2enmod php8.1
sudo systemctl restart apache2
```

### Impossible de se connecter à MariaDB

**Vérifier le service :**
```bash
sudo systemctl status mariadb
```

**Réinitialiser le mot de passe root :**
```bash
sudo mysql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'nouveau_mdp';
FLUSH PRIVILEGES;
EXIT;
```

### phpMyAdmin affiche une erreur

**Vérifier la configuration :**
```bash
sudo dpkg-reconfigure phpmyadmin
```

**Vérifier les logs :**
```bash
sudo tail -f /var/log/apache2/error.log
```

---

## Commandes utiles

### Gestion d'Apache

```bash
# Démarrer
sudo systemctl start apache2

# Arrêter
sudo systemctl stop apache2

# Redémarrer
sudo systemctl restart apache2

# Recharger la configuration
sudo systemctl reload apache2

# Statut
sudo systemctl status apache2
```

### Gestion de MariaDB

```bash
# Démarrer
sudo systemctl start mariadb

# Arrêter
sudo systemctl stop mariadb

# Redémarrer
sudo systemctl restart mariadb

# Statut
sudo systemctl status mariadb
```

### Logs

```bash
# Logs Apache
sudo tail -f /var/log/apache2/access.log
sudo tail -f /var/log/apache2/error.log

# Logs PHP
sudo tail -f /var/log/apache2/error.log

# Logs MariaDB
sudo tail -f /var/log/mysql/error.log
```

---

## Sécurité

### Désactiver l'affichage des erreurs PHP (production)

```bash
sudo nano /etc/php/8.1/apache2/php.ini
```

```ini
display_errors = Off
log_errors = On
error_log = /var/log/php_errors.log
```

### Limiter l'accès à phpMyAdmin

Créer un fichier `.htaccess` dans `/usr/share/phpmyadmin/` :

```bash
sudo nano /usr/share/phpmyadmin/.htaccess
```

```apache
AuthType Basic
AuthName "Accès Restreint"
AuthUserFile /etc/phpmyadmin/.htpasswd
Require valid-user
```

Créer le fichier de mots de passe :

```bash
sudo htpasswd -c /etc/phpmyadmin/.htpasswd admin
```

### Configurer le pare-feu

```bash
sudo ufw allow 'Apache Full'
sudo ufw enable
sudo ufw status
```

---

## Résumé de l'installation

**Services installés :**
- ✅ Apache2 (serveur web)
- ✅ PHP 8.x avec modules
- ✅ MariaDB (base de données)
- ✅ phpMyAdmin (interface de gestion)

**Fichiers importants :**
- Configuration Apache : `/etc/apache2/`
- Fichiers web : `/var/www/html/`
- Configuration PHP : `/etc/php/8.1/apache2/php.ini`
- Logs Apache : `/var/log/apache2/`

**Accès web :**
- Site par défaut : `http://localhost/`
- phpMyAdmin : `http://localhost/phpmyadmin`

---

## Ressources

- [Documentation Apache](https://httpd.apache.org/docs/)
- [Documentation PHP](https://www.php.net/manual/fr/)
- [Documentation MariaDB](https://mariadb.com/kb/en/)
- [Documentation phpMyAdmin](https://docs.phpmyadmin.net/)

---

**Votre serveur LAMP est maintenant opérationnel ! 🚀**