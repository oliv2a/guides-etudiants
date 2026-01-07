# Guide de Configuration WiFi - Raspberry Pi OS Lite

## Contexte
Ce guide explique comment configurer le WiFi sur Raspberry Pi OS Lite lorsque `raspi-config` échoue et que le WiFi est bloqué par rfkill.

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

## Résumé de la solution complète

1. ✅ Débloquer le WiFi avec `rfkill unblock wifi`
2. ✅ Installer NetworkManager
3. ✅ Configurer la connexion WiFi avec `nmcli`
4. ✅ Créer le service `unblock-wifi.service` pour la persistance
5. ✅ Tester avec un redémarrage

Votre Raspberry Pi devrait maintenant se connecter automatiquement au WiFi à chaque démarrage !
