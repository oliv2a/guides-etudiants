# Tutoriel : Afficher les erreurs PHP sur Apache 2

## Introduction

Par défaut, PHP n'affiche pas les erreurs dans le navigateur pour des raisons de sécurité. Cependant, pendant le développement, il est très utile d'afficher ces erreurs pour déboguer votre code. Ce tutoriel vous montre comment activer l'affichage des erreurs PHP sur un serveur Apache 2.

⚠️ **Important** : Cette configuration est recommandée **uniquement en environnement de développement**. Ne jamais activer l'affichage des erreurs sur un serveur de production !

---

## Prérequis

- Serveur Apache 2 installé
- PHP installé (version 7.x ou 8.x)
- Accès root ou sudo
- Éditeur de texte en ligne de commande (nano, vim, etc.)

---

## Procédure

### Étape 1 : Localiser le fichier php.ini

Le fichier de configuration PHP se trouve dans `/etc/php/[VERSION]/apache2/php.ini` où `[VERSION]` correspond à votre version de PHP.

**Exemples de chemins selon la version :**
- PHP 7.4 : `/etc/php/7.4/apache2/php.ini`
- PHP 8.0 : `/etc/php/8.0/apache2/php.ini`
- PHP 8.1 : `/etc/php/8.1/apache2/php.ini`
- PHP 8.2 : `/etc/php/8.2/apache2/php.ini`

**Pour connaître votre version de PHP :**
```bash
php -v
```

---

### Étape 2 : Éditer le fichier php.ini

Ouvrez le fichier php.ini avec un éditeur de texte en mode console. Nous utilisons `nano` dans cet exemple :

**Pour PHP 7.4 :**
```bash
sudo nano /etc/php/7.4/apache2/php.ini
```

**Pour PHP 8.1 (exemple) :**
```bash
sudo nano /etc/php/8.1/apache2/php.ini
```

Remplacez la version selon votre installation.

---

### Étape 3 : Modifier le niveau de reporting des erreurs

Une fois dans l'éditeur nano :

1. Appuyez sur **Ctrl + W** pour ouvrir la recherche
2. Tapez : `error_reporting`
3. Appuyez sur **Entrée**

Vous devriez trouver une ligne similaire à :
```ini
error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT
```

**Modifiez-la en :**
```ini
error_reporting = E_ALL
```

Cette modification permet d'afficher **tous les types d'erreurs** PHP.

---

### Étape 4 : Activer l'affichage des erreurs

Toujours dans le fichier php.ini :

1. Appuyez sur **Ctrl + W** pour rechercher
2. Tapez : `display_errors`
3. Appuyez sur **Entrée**

Vous devriez trouver une ligne :
```ini
display_errors = Off
```

**Modifiez-la en :**
```ini
display_errors = On
```

Cette modification active l'affichage des erreurs dans le navigateur.

---

### Étape 5 : Sauvegarder et quitter

Pour enregistrer les modifications et quitter nano :

1. Appuyez sur **Ctrl + X**
2. Tapez **O** (pour Oui) ou **Y** (pour Yes)
3. Appuyez sur **Entrée** pour confirmer le nom du fichier

---

### Étape 6 : Redémarrer le système

Pour que les modifications prennent effet, redémarrez votre système :

```bash
sudo reboot
```

**Alternative (plus rapide) :** Si vous ne souhaitez pas redémarrer tout le système, vous pouvez simplement redémarrer le service Apache :

```bash
sudo systemctl restart apache2
```

ou

```bash
sudo service apache2 restart
```

---

## Vérification

Pour vérifier que l'affichage des erreurs fonctionne :

1. Créez un fichier PHP de test avec une erreur volontaire :

```bash
echo "<?php echo \$variable_inexistante; ?>" > /var/www/html/test_erreur.php
```

2. Ouvrez ce fichier dans votre navigateur :
   - Sur le Raspberry Pi : `http://localhost/test_erreur.php`
   - Depuis un autre PC : `http://[IP_DU_RASPBERRY]/test_erreur.php`

3. Vous devriez voir un message d'erreur PHP s'afficher dans le navigateur

**Exemple d'erreur attendue :**
```
Warning: Undefined variable $variable_inexistante in /var/www/html/test_erreur.php on line 1
```

---

## Récapitulatif des modifications

| Paramètre | Valeur d'origine | Nouvelle valeur |
|-----------|------------------|-----------------|
| `error_reporting` | `E_ALL & ~E_DEPRECATED & ~E_STRICT` | `E_ALL` |
| `display_errors` | `Off` | `On` |

---

## Paramètres additionnels (optionnels)

Vous pouvez également activer ces paramètres pour un débogage plus complet :

### Afficher les erreurs de démarrage
```ini
display_startup_errors = On
```

### Enregistrer les erreurs dans un fichier log
```ini
log_errors = On
error_log = /var/log/php_errors.log
```

### Afficher les erreurs HTML
```ini
html_errors = On
```

---

## Désactiver l'affichage des erreurs (production)

**⚠️ IMPORTANT** : Avant de mettre votre site en production, **désactivez l'affichage des erreurs** pour des raisons de sécurité :

1. Rouvrez le fichier php.ini
2. Modifiez :
   ```ini
   display_errors = Off
   error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT
   ```
3. Activez le logging des erreurs :
   ```ini
   log_errors = On
   ```
4. Redémarrez Apache

---

## Dépannage

### Les erreurs ne s'affichent toujours pas

**Vérifiez que vous avez édité le bon fichier php.ini :**
```bash
php -i | grep "Loaded Configuration File"
```

Cette commande affiche le chemin du fichier php.ini utilisé.

**Vérifiez les paramètres actuels :**
```bash
php -i | grep display_errors
php -i | grep error_reporting
```

### Erreur "Permission denied"

Si vous n'avez pas les droits pour éditer le fichier, assurez-vous d'utiliser `sudo` :
```bash
sudo nano /etc/php/7.4/apache2/php.ini
```

### Apache ne redémarre pas

Vérifiez les erreurs de configuration :
```bash
sudo apache2ctl configtest
```

---

## Ressources utiles

- [Documentation officielle PHP - Gestion des erreurs](https://www.php.net/manual/fr/errorfunc.configuration.php)
- [Documentation Apache](https://httpd.apache.org/docs/)
- [PHP Error Reporting Levels](https://www.php.net/manual/fr/errorfunc.constants.php)

---

## Conclusion

Vous savez maintenant comment :
- ✅ Localiser le fichier php.ini
- ✅ Activer l'affichage des erreurs PHP
- ✅ Configurer le niveau de reporting des erreurs
- ✅ Redémarrer Apache pour appliquer les modifications

**N'oubliez pas** : L'affichage des erreurs est utile en développement mais **dangereux en production** car il peut révéler des informations sensibles sur votre système !

---

## Bonnes pratiques

✅ **En développement** : `display_errors = On`  
✅ **En production** : `display_errors = Off` + `log_errors = On`  
✅ Toujours consulter les logs d'erreurs : `/var/log/apache2/error.log`  
✅ Ne jamais exposer les erreurs PHP sur un site public
