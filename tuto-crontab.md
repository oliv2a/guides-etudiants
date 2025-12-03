# Automatiser un Script Python avec Crontab sur Raspberry Pi

## Introduction

Ce tutoriel explique comment automatiser l'exécution d'un script Python sur un Raspberry Pi en utilisant **crontab**. Il inclut la gestion d'un environnement virtuel Python et la configuration d'une exécution programmée.

---

## Prérequis

- Raspberry Pi avec Raspbian/Raspberry Pi OS
- Python installé
- Un script Python à automatiser
- (Optionnel) Environnement virtuel Python configuré

---

## Étape 1 : Identifier les chemins complets

Avant de configurer Crontab, vous devez noter les **chemins absolus** de vos fichiers.

### Chemins nécessaires

- **Chemin du script Python** : `/home/pi/mon_projet/test.py`
- **Chemin de l'environnement virtuel** : `/home/pi/mon_projet/venv`
- **Chemin du Python du venv** : `/home/pi/mon_projet/venv/bin/python`

### Vérifier le chemin de Python

Pour connaître le chemin de votre Python actuel :

```bash
which python
```

ou

```bash
which python3
```

**Exemple de sortie :**
```
/home/pi/mon_projet/venv/bin/python
```

---

## Étape 2 : Créer un script de lancement

Plutôt que d'appeler directement le script Python depuis crontab, il est recommandé de créer un **script shell** qui gère l'activation de l'environnement virtuel.

### Créer le fichier run_test.sh

Créez un fichier nommé `run_test.sh` dans votre projet :

```bash
nano /home/pi/mon_projet/run_test.sh
```

### Contenu du script

Copiez ce contenu dans le fichier :

```bash
#!/bin/bash

# Activer l'environnement virtuel
source /home/pi/mon_projet/venv/bin/activate

# Exécuter le script Python
python /home/pi/mon_projet/test.py
```

**Important** : Adaptez les chemins selon votre configuration réelle.

### Rendre le script exécutable

Une fois le fichier créé, rendez-le exécutable :

```bash
chmod +x /home/pi/mon_projet/run_test.sh
```

### Tester le script manuellement

Avant de l'ajouter à crontab, testez-le :

```bash
/home/pi/mon_projet/run_test.sh
```

Si le script s'exécute correctement, vous pouvez passer à l'étape suivante.

---

## Étape 3 : Ajouter la tâche Crontab

### Ouvrir l'éditeur crontab

Pour modifier votre crontab personnelle :

```bash
crontab -e
```

Si c'est la première fois, le système vous demandera de choisir un éditeur. Choisissez `nano` (option 1) pour plus de simplicité.

### Ajouter la ligne de planification

Ajoutez cette ligne à la fin du fichier :

```bash
* * * * * /home/pi/mon_projet/run_test.sh >> /home/pi/mon_projet/cron.log 2>&1
```

### Explication de la syntaxe

```
* * * * * commande
│ │ │ │ │
│ │ │ │ └─── Jour de la semaine (0-7, 0 et 7 = dimanche)
│ │ │ └───── Mois (1-12)
│ │ └─────── Jour du mois (1-31)
│ └───────── Heure (0-23)
└─────────── Minute (0-59)
```

**`* * * * *`** signifie : **toutes les minutes**

**`>> /home/pi/mon_projet/cron.log 2>&1`** redirige la sortie standard et les erreurs vers un fichier log.

### Exemples d'autres planifications

```bash
# Toutes les 5 minutes
*/5 * * * * /home/pi/mon_projet/run_test.sh >> /home/pi/mon_projet/cron.log 2>&1

# Toutes les heures
0 * * * * /home/pi/mon_projet/run_test.sh >> /home/pi/mon_projet/cron.log 2>&1

# Tous les jours à 8h00
0 8 * * * /home/pi/mon_projet/run_test.sh >> /home/pi/mon_projet/cron.log 2>&1

# Du lundi au vendredi à 9h30
30 9 * * 1-5 /home/pi/mon_projet/run_test.sh >> /home/pi/mon_projet/cron.log 2>&1

# Toutes les 30 minutes
*/30 * * * * /home/pi/mon_projet/run_test.sh >> /home/pi/mon_projet/cron.log 2>&1
```

### Sauvegarder et quitter

- Appuyez sur **Ctrl + X**
- Tapez **O** (Oui) ou **Y** (Yes)
- Appuyez sur **Entrée**

---

## Étape 4 : Vérifier le fonctionnement

### Consulter les logs en temps réel

Pour vérifier que votre tâche s'exécute correctement, consultez les logs :

```bash
tail -f /home/pi/mon_projet/cron.log
```

Cette commande affiche les dernières lignes du fichier et continue d'afficher les nouvelles en temps réel.

**Pour quitter** : Appuyez sur **Ctrl + C**

### Lister les tâches crontab actives

Pour voir toutes vos tâches programmées :

```bash
crontab -l
```

### Vérifier que cron est en cours d'exécution

```bash
sudo systemctl status cron
```

Le service doit être **active (running)**.

---

## Conseils utiles

### Utilisez toujours des chemins absolus

❌ **Mauvais :**
```bash
python test.py
```

✅ **Bon :**
```bash
/home/pi/mon_projet/venv/bin/python /home/pi/mon_projet/test.py
```

### L'environnement de cron est limité

Cron n'hérite pas de toutes les variables d'environnement de votre session utilisateur. Évitez de compter sur des variables comme `PATH`.

### Ajoutez des logs détaillés dans vos scripts

Dans votre script Python, ajoutez des logs pour faciliter le débogage :

```python
import logging
from datetime import datetime

logging.basicConfig(
    filename='/home/pi/mon_projet/script.log',
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s'
)

logging.info("Script démarré")
# Votre code ici
logging.info("Script terminé")
```

### Rediriger les erreurs vers un fichier séparé

Si vous voulez séparer les sorties normales et les erreurs :

```bash
* * * * * /home/pi/mon_projet/run_test.sh >> /home/pi/mon_projet/cron.log 2>> /home/pi/mon_projet/cron_error.log
```

---

## Dépannage

### Le script ne s'exécute pas

**Vérifiez que le service cron fonctionne :**
```bash
sudo systemctl status cron
```

Si inactif, démarrez-le :
```bash
sudo systemctl start cron
```

**Vérifiez les permissions du script :**
```bash
ls -l /home/pi/mon_projet/run_test.sh
```

Le fichier doit être exécutable (`-rwxr-xr-x`).

**Vérifiez les logs système de cron :**
```bash
grep CRON /var/log/syslog
```

### Le script s'exécute mais génère des erreurs

**Consultez le fichier de log :**
```bash
cat /home/pi/mon_projet/cron.log
```

**Testez le script manuellement :**
```bash
/home/pi/mon_projet/run_test.sh
```

**Vérifiez les chemins :**
Assurez-vous que tous les chemins dans votre script sont absolus et corrects.

### Problèmes de permissions

Si vous obtenez "Permission denied" :
```bash
chmod +x /home/pi/mon_projet/run_test.sh
chmod +r /home/pi/mon_projet/test.py
```

---

## Supprimer une tâche crontab

Pour supprimer une tâche :

1. Ouvrez crontab :
```bash
crontab -e
```

2. Supprimez la ligne correspondante ou commentez-la avec `#` :
```bash
# * * * * * /home/pi/mon_projet/run_test.sh >> /home/pi/mon_projet/cron.log 2>&1
```

3. Sauvegardez et quittez

---

## Exemples de scripts Python automatisables

### Collecte de données de capteur
```python
import time
from datetime import datetime

def log_temperature():
    # Simuler la lecture d'un capteur
    temp = 22.5
    timestamp = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    
    with open('/home/pi/mon_projet/temperature.log', 'a') as f:
        f.write(f"{timestamp} - Température: {temp}°C\n")

if __name__ == "__main__":
    log_temperature()
```

### Sauvegarde automatique
```python
import shutil
from datetime import datetime

def backup():
    source = '/home/pi/mon_projet/data'
    dest = f'/home/pi/backups/backup_{datetime.now().strftime("%Y%m%d_%H%M%S")}'
    shutil.copytree(source, dest)
    print(f"Backup créé: {dest}")

if __name__ == "__main__":
    backup()
```

---

## Résumé

Votre script Python sera désormais exécuté automatiquement selon la planification définie, dans son environnement virtuel, grâce à la planification Crontab.

**Ce que vous avez appris :**
- ✅ Identifier les chemins absolus nécessaires
- ✅ Créer un script shell de lancement
- ✅ Configurer une tâche crontab
- ✅ Consulter les logs d'exécution
- ✅ Déboguer les problèmes courants

---

## Ressources supplémentaires

- [Crontab Generator](https://crontab.guru) - Outil en ligne pour créer des expressions cron
- [Documentation Cron](https://man7.org/linux/man-pages/man5/crontab.5.html)
- [Guide Python Logging](https://docs.python.org/3/howto/logging.html)

---

**Bonne automatisation ! ⚙️**