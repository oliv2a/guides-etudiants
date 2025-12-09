# TP Wireshark - Analyse de Protocoles Réseaux
## Installation, Prise en Main et Capture de Trames

---

## Table des matières

1. [Introduction](#introduction)
2. [Installation de Wireshark](#installation)
3. [Prise en main de l'interface](#prise-en-main)
4. [Analyse de trames](#analyse-de-trames)
5. [Analyse du protocole DHCP](#analyse-dhcp)
6. [Capture de trames](#capture-de-trames)
7. [Manipulations pratiques](#manipulations)
8. [Questions de synthèse](#questions)

---

## Introduction

### Objectifs du TP

- Installer et configurer Wireshark
- Comprendre l'interface et les fonctionnalités d'analyse
- Découvrir l'encapsulation des protocoles TCP/IP
- Analyser les échanges DHCP au démarrage d'une machine
- Capturer et filtrer le trafic réseau
- Utiliser les outils statistiques

### Qu'est-ce que Wireshark ?

**Wireshark** (anciennement Ethereal) est un analyseur de protocoles réseau (packet sniffer) open source utilisé pour :

- Le dépannage et l'analyse de réseaux
- Le développement de protocoles
- L'éducation et la formation
- La sécurité informatique et l'audit
- La rétro-ingénierie

**Caractéristiques :**
- Multi-plateforme (Windows, Linux, macOS)
- Reconnaît plus de 2000 protocoles
- Interface graphique intuitive
- Puissants filtres d'affichage et de capture
- Analyse en temps réel ou différé

**Site officiel :** https://www.wireshark.org/

### Contexte matériel

Pour réaliser ce TP, vous aurez besoin de :
- Un ordinateur avec carte réseau Ethernet ou WiFi
- Système d'exploitation : Windows, Linux ou macOS
- Droits administrateur (root) pour la capture de paquets
- Connexion réseau active

---

## Installation de Wireshark

### Installation sous Windows

#### Étape 1 : Téléchargement

1. Rendez-vous sur https://www.wireshark.org/download.html
2. Téléchargez la version correspondant à votre système :
   - Windows Installer (64-bit) pour Windows 10/11 64 bits
   - Windows Installer (32-bit) pour les systèmes 32 bits

#### Étape 2 : Installation

1. Exécutez le fichier `.exe` téléchargé
2. Cliquez sur "Next" pour démarrer l'installation
3. Acceptez la licence GNU GPL
4. Sélectionnez les composants à installer (laisser par défaut)
5. **Important :** Cochez l'option pour installer **Npcap** (ou WinPcap)
   - Npcap est nécessaire pour capturer les paquets
6. Choisissez le dossier d'installation (par défaut : `C:\Program Files\Wireshark`)
7. Cliquez sur "Install"
8. Patientez pendant l'installation
9. Si demandé, installez Npcap avec les options par défaut
10. Terminez l'installation et lancez Wireshark

### Installation sous Linux

#### Ubuntu / Debian

Ouvrez un terminal et exécutez :

```bash
# Mise à jour des dépôts
sudo apt update

# Installation de Wireshark
sudo apt install wireshark

# Configuration pour permettre aux utilisateurs non-root de capturer
sudo dpkg-reconfigure wireshark-common
# Sélectionnez "Yes" pour autoriser les non-superutilisateurs

# Ajout de votre utilisateur au groupe wireshark
sudo usermod -aG wireshark $USER

# Redémarrage de la session nécessaire
# Déconnectez-vous puis reconnectez-vous
```

#### Fedora / CentOS / RHEL

```bash
# Installation
sudo dnf install wireshark

# Ou pour les versions plus anciennes
sudo yum install wireshark

# Ajout au groupe wireshark
sudo usermod -aG wireshark $USER
```

#### Arch Linux

```bash
sudo pacman -S wireshark-qt
sudo usermod -aG wireshark $USER
```

#### Vérification de l'installation

```bash
# Vérifier la version installée
wireshark --version

# Résultat attendu :
# Wireshark 4.x.x (ou version supérieure)
```

### Installation sous macOS

1. Téléchargez le fichier `.dmg` depuis https://www.wireshark.org/download.html
2. Ouvrez le fichier `.dmg`
3. Glissez l'icône Wireshark dans le dossier Applications
4. Installez ChmodBPF (nécessaire pour la capture)
5. Lancez Wireshark depuis le Launchpad ou Applications

### Remarque importante sur les droits

**Sous Linux**, Wireshark propose deux modes d'utilisation :

1. **Mode utilisateur simple** : 
   - Permet uniquement l'analyse de captures déjà enregistrées
   - Pas d'accès aux interfaces réseau pour capturer

2. **Mode super-utilisateur (root)** :
   - Permet la capture en temps réel sur les interfaces réseau
   - Nécessite les privilèges administrateur

Pour capturer des paquets sans être root, suivez les instructions de configuration ci-dessus.

---

## Prise en main de l'interface

### Premier lancement

Au lancement de Wireshark, vous arrivez sur l'écran d'accueil :

#### Zones principales de l'écran d'accueil

**1. Section "Capture"**
- **Interface List** : Liste des interfaces réseau disponibles
- **Capture Options** : Options de configuration de capture
- Boutons pour démarrer la capture

**2. Section "Files"**
- **Open** : Ouvrir un fichier de capture existant
- **Open Recent** : Fichiers récemment ouverts
- **Sample Captures** : Exemples de captures

**3. Section "Online"**
- Liens vers la documentation
- Guide utilisateur
- Informations de sécurité

**4. Section "Capture Help"**
- How to Capture : Guide pas à pas
- Network Media : Informations sur les médias réseau

### Vérification des interfaces réseau

Cliquez sur **"Interface List"** pour voir les interfaces disponibles :

- **eth0** ou **enp3s0** : Interface Ethernet filaire
- **wlan0** ou **wlp2s0** : Interface WiFi
- **lo** : Interface de loopback (localhost)
- **any** : Pseudo-interface capturant sur toutes les interfaces

Chaque interface affiche :
- Le nombre de paquets capturés
- L'adresse IP configurée
- Un graphique d'activité en temps réel

### Interface principale d'analyse

Lorsque vous ouvrez un fichier de capture ou démarrez une capture, l'interface se divise en plusieurs zones :

#### Barre de menus

- **File** : Ouvrir, sauvegarder, exporter des captures
- **Edit** : Préférences, recherche
- **View** : Options d'affichage
- **Go** : Navigation dans les paquets
- **Capture** : Démarrer/arrêter les captures
- **Analyze** : Outils d'analyse et filtres
- **Statistics** : Outils statistiques
- **Telephony** : Analyse de protocoles téléphoniques
- **Tools** : Outils divers
- **Help** : Aide et documentation

#### Barre d'outils principale

Icônes pour accès rapide aux fonctions courantes :
- Démarrer/arrêter une capture
- Ouvrir/sauvegarder un fichier
- Recharger, zoom
- Navigation (premier, précédent, suivant, dernier paquet)

#### Zone de filtrage

Barre avec :
- **Champ Filter** : Pour saisir des filtres d'affichage
- **Bouton Expression** : Assistant pour créer des filtres
- **Boutons Clear / Apply** : Effacer ou appliquer le filtre

#### Les trois cadres principaux

**Cadre 1 : Liste des paquets (Packet List)**
- Affiche la liste de tous les paquets capturés
- Colonnes : No., Time, Source, Destination, Protocol, Length, Info
- Code couleur pour identifier les types de protocoles
- Tri possible par colonne

**Cadre 2 : Détails du paquet (Packet Details)**
- Affiche les détails du paquet sélectionné
- Structure arborescente montrant l'encapsulation
- Chaque ligne correspond à un en-tête de protocole
- Possibilité de déplier chaque protocole pour voir les champs

**Cadre 3 : Données brutes (Packet Bytes)**
- Affichage hexadécimal et ASCII du paquet
- Vue brute des octets transmis sur le réseau
- Sélection d'un champ dans le cadre 2 le met en surbrillance ici

#### Barre de statut

En bas de la fenêtre :
- Nombre total de paquets
- Paquets affichés après filtrage
- Paquets marqués
- Profil actuel
- Commentaires

### Codes couleur par défaut

Wireshark utilise des couleurs pour identifier rapidement les types de trafic :

- **Vert clair** : Trafic TCP
- **Bleu clair** : Trafic UDP
- **Rose/Violet** : Trafic de routage
- **Jaune** : Trafic ICMP, ARP
- **Noir** : Paquets avec erreurs TCP
- **Gris** : Autres protocoles

Ces couleurs sont personnalisables dans `View > Coloring Rules`.

---

## Analyse de trames

### Chargement d'un fichier de capture

Pour commencer l'analyse sans faire de capture, nous allons charger un fichier exemple.

#### Téléchargement d'un fichier exemple

1. Rendez-vous sur http://wiki.wireshark.org/SampleCaptures
2. Téléchargez le fichier **http.cap** (section HTTP)
3. Ou téléchargez **dhcp.pcap** pour l'analyse DHCP

#### Ouverture dans Wireshark

1. Lancez Wireshark
2. Menu `File > Open` ou Ctrl+O
3. Sélectionnez le fichier téléchargé
4. Cliquez sur "Ouvrir"

### Comprendre l'encapsulation des protocoles

#### Le modèle TCP/IP (DoD)

Le modèle TCP/IP, aussi appelé modèle DoD (Department of Defense), organise les protocoles en 4 couches :

```
┌─────────────────────────┐
│   Couche Application    │  HTTP, FTP, DNS, DHCP, SMTP...
├─────────────────────────┤
│   Couche Transport      │  TCP, UDP
├─────────────────────────┤
│   Couche Réseau (IP)    │  IP, ICMP, ARP
├─────────────────────────┤
│   Couche Interface      │  Ethernet, WiFi (802.11)
└─────────────────────────┘
```

#### Principe de l'encapsulation

Chaque couche ajoute son en-tête (header) aux données de la couche supérieure :

```
┌────────────────────────────────────────────────────────┐
│  En-tête    │  En-tête  │  En-tête  │  En-tête  │      │
│  Ethernet   │    IP     │    TCP    │   HTTP    │ DATA │
└────────────────────────────────────────────────────────┘
   Interface      Réseau    Transport   Application
```

**Visualisation dans Wireshark :**

Lorsque vous sélectionnez un paquet, le cadre 2 affiche :

```
▼ Frame (trame complète)
  ▼ Ethernet II
    ▼ Internet Protocol Version 4
      ▼ Transmission Control Protocol
        ▼ Hypertext Transfer Protocol
```

Chaque niveau contient :
- Un **en-tête (Header)** : informations de contrôle du protocole
- Des **données (Data)** : contenu de la couche supérieure

#### Exemple d'analyse d'une trame HTTP

Sélectionnez une trame contenant du HTTP (colonne Protocol = HTTP) :

**1. Ethernet II (Couche Interface)**
- Adresse MAC source
- Adresse MAC destination
- Type : 0x0800 (indique IPv4 encapsulé)

**2. Internet Protocol Version 4 (Couche Réseau)**
- Adresse IP source
- Adresse IP destination
- Protocol : 6 (indique TCP encapsulé)
- TTL, flags, checksum...

**3. Transmission Control Protocol (Couche Transport)**
- Port source (généralement aléatoire)
- Port destination : 80 (HTTP)
- Numéros de séquence
- Flags (SYN, ACK, PSH, FIN...)
- Taille de fenêtre

**4. Hypertext Transfer Protocol (Couche Application)**
- Requête : GET, POST...
- Headers HTTP
- Données éventuelles

### Système d'adressage

Chaque couche utilise son propre système d'adressage :

| Couche | Type d'adresse | Exemple | Taille |
|--------|----------------|---------|--------|
| Interface | Adresse MAC | 00:1A:2B:3C:4D:5E | 48 bits (6 octets) |
| Réseau | Adresse IP | 192.168.1.10 | 32 bits (IPv4) |
| Transport | Numéro de port | 80 (HTTP), 443 (HTTPS) | 16 bits |

### Les numéros de protocole

#### Type Ethernet (champ EtherType)

Identifie le protocole de couche réseau :
- **0x0800** : IPv4
- **0x0806** : ARP
- **0x86DD** : IPv6

#### Numéro de protocole IP

Identifie le protocole de couche transport :
- **1** : ICMP
- **6** : TCP
- **17** : UDP

Sous Linux, consultez `/etc/protocols` :
```bash
grep -E 'tcp|udp|icmp' /etc/protocols
```

#### Numéros de port

Identifient les applications/services (16 bits = 0 à 65535) :

**Ports bien connus (0-1023) :**
- **20/21** : FTP
- **22** : SSH
- **23** : Telnet
- **25** : SMTP
- **53** : DNS
- **67/68** : DHCP
- **80** : HTTP
- **110** : POP3
- **143** : IMAP
- **443** : HTTPS

**Ports enregistrés (1024-49151) :**
Utilisés par des applications spécifiques

**Ports dynamiques (49152-65535) :**
Utilisés temporairement par les clients

Sous Linux, consultez `/etc/services` :
```bash
grep -E 'http|dns|dhcp' /etc/services
```

### Filtrage des paquets

Le filtrage est essentiel pour analyser efficacement le trafic réseau.

#### Types de filtres

**1. Filtres de capture** : 
- Appliqués pendant la capture
- Limitent les paquets enregistrés
- Syntaxe BPF (Berkeley Packet Filter)
- Non modifiables après la capture

**2. Filtres d'affichage** :
- Appliqués après la capture
- Ne suppriment pas les paquets, les masquent
- Plus puissants et flexibles
- Modifiables à tout moment

#### Syntaxe des filtres d'affichage

**Filtres par protocole :**
```
http
dns
tcp
udp
arp
icmp
dhcp
```

**Filtres par adresse IP :**
```
ip.addr == 192.168.1.1        # IP source ou destination
ip.src == 192.168.1.10        # IP source
ip.dst == 192.168.1.1         # IP destination
```

**Filtres par adresse MAC :**
```
eth.addr == 00:1a:2b:3c:4d:5e
eth.src == 00:1a:2b:3c:4d:5e
eth.dst == ff:ff:ff:ff:ff:ff  # Broadcast
```

**Filtres par port :**
```
tcp.port == 80                # Port source ou destination
tcp.srcport == 80             # Port source
tcp.dstport == 443            # Port destination
udp.port == 53
```

**Opérateurs de comparaison :**
```
==   (égal)
!=   (différent)
>    (supérieur)
<    (inférieur)
>=   (supérieur ou égal)
<=   (inférieur ou égal)
```

**Opérateurs logiques :**
```
&&   ou and   (ET)
||   ou or    (OU)
!    ou not   (NON)
```

**Exemples de filtres combinés :**
```
# Trafic HTTP vers un serveur spécifique
ip.dst == 192.168.1.10 && tcp.port == 80

# Tout le trafic sauf DNS
!dns

# Requêtes DNS ou ARP
dns || arp

# Trafic TCP vers les ports 80 ou 443
tcp && (tcp.port == 80 || tcp.port == 443)

# Trafic depuis un réseau local
ip.src == 192.168.1.0/24

# Paquets avec erreurs TCP
tcp.analysis.flags
```

#### Utilisation de l'assistant de filtres

1. Cliquez sur le bouton **"Expression..."** à côté du champ Filter
2. Parcourez les champs disponibles (Field name)
3. Sélectionnez un champ (exemple : `ip.dst`)
4. Choisissez une relation (==, !=, etc.)
5. Entrez une valeur
6. Cliquez sur "OK"
7. Cliquez sur "Apply" pour appliquer le filtre

#### Enregistrement des filtres

Les filtres fréquemment utilisés peuvent être sauvegardés :

1. Saisissez votre filtre
2. Cliquez sur le bouton "+" à droite du champ Filter
3. Donnez un nom à votre filtre
4. Validez

Les filtres sauvegardés apparaissent dans le menu déroulant du champ Filter.

### Exercice pratique : Analyse d'une capture HTTP

Ouvrez le fichier **http.cap**

**Question 1 : Nombre de paquets**
Combien de paquets contient cette capture ?
*Regardez la barre de statut en bas.*

**Question 2 : Filtrer le trafic HTTP**
Appliquez le filtre `http` et comptez les requêtes HTTP.

**Question 3 : Identifier une requête GET**
Trouvez une trame avec une requête HTTP GET :
- Quelle est l'URL complète demandée ?
- Quel est le User-Agent (navigateur) utilisé ?

**Question 4 : Analyser la réponse**
Pour la même requête GET :
- Quel est le code de statut HTTP de la réponse ?
- Quel est le type de contenu (Content-Type) ?

**Question 5 : Adresses IP**
- Quelle est l'adresse IP du client ?
- Quelle est l'adresse IP du serveur web ?

**Question 6 : Ports TCP**
- Quel port utilise le serveur ?
- Quel port utilise le client ?

**Question 7 : Three-way handshake**
Identifiez les 3 paquets de l'établissement de connexion TCP :
- Paquet SYN
- Paquet SYN-ACK
- Paquet ACK

---

## Analyse du protocole DHCP

### Introduction au DHCP

#### Qu'est-ce que le DHCP ?

**DHCP (Dynamic Host Configuration Protocol)** est un protocole réseau qui permet à un serveur d'attribuer automatiquement une configuration IP à un client.

**Configuration fournie par DHCP :**
- Adresse IP
- Masque de sous-réseau
- Passerelle par défaut (gateway)
- Serveurs DNS
- Bail (durée de validité de l'adresse)

#### Fonctionnement du DHCP

Le processus DHCP suit un échange en **4 étapes** appelé **DORA** :

```
CLIENT                                    SERVEUR DHCP
  │                                             │
  │  1. DHCP DISCOVER (broadcast)              │
  │ ──────────────────────────────────────────>│
  │                                             │
  │              2. DHCP OFFER                  │
  │ <──────────────────────────────────────────│
  │                                             │
  │  3. DHCP REQUEST (broadcast)               │
  │ ──────────────────────────────────────────>│
  │                                             │
  │              4. DHCP ACK                    │
  │ <──────────────────────────────────────────│
  │                                             │
```

**Détail des 4 étapes :**

**1. DHCP DISCOVER**
- Le client cherche un serveur DHCP
- Envoyé en broadcast (255.255.255.255)
- Source : 0.0.0.0, Destination : 255.255.255.255
- Port source : 68, Port destination : 67

**2. DHCP OFFER**
- Le serveur propose une configuration
- Contient une adresse IP disponible
- Peut être envoyé en broadcast ou unicast
- Port source : 67, Port destination : 68

**3. DHCP REQUEST**
- Le client accepte l'offre
- Envoyé en broadcast (pour informer tous les serveurs)
- Contient l'ID du serveur choisi
- Demande officielle de l'adresse IP

**4. DHCP ACK**
- Le serveur confirme l'attribution
- Fournit tous les paramètres (IP, masque, gateway, DNS)
- Le client peut maintenant utiliser sa configuration

#### Autres messages DHCP

**DHCP NAK** : Refus du serveur (adresse déjà attribuée)
**DHCP RELEASE** : Le client libère son adresse IP
**DHCP INFORM** : Le client demande des paramètres (il a déjà une IP)
**DHCP DECLINE** : Le client refuse l'offre (détection d'IP dupliquée)

### Capture et analyse d'un échange DHCP

#### Méthode 1 : Utiliser un fichier de capture DHCP

**Téléchargement d'un exemple :**

1. Visitez http://wiki.wireshark.org/SampleCaptures
2. Téléchargez le fichier **dhcp.pcap** (section DHCP)
3. Ouvrez-le dans Wireshark

#### Méthode 2 : Capturer un échange DHCP réel

**Préparation :**

1. Notez votre configuration réseau actuelle :
```bash
# Windows
ipconfig /all

# Linux
ip addr show
nmcli device show
```

2. Libérez votre adresse IP actuelle :
```bash
# Windows
ipconfig /release

# Linux (en root)
dhclient -r eth0
# ou
dhclient -r enp3s0
```

3. Démarrez la capture Wireshark sur l'interface réseau active

4. Demandez une nouvelle adresse IP :
```bash
# Windows
ipconfig /renew

# Linux (en root)
dhclient eth0
# ou
dhclient enp3s0
```

5. Arrêtez la capture après quelques secondes

#### Filtrage des paquets DHCP

Appliquez le filtre : `dhcp` ou `bootp`

**Note :** DHCP utilise le protocole BOOTP, d'où le filtre `bootp` possible.

### Analyse détaillée d'un échange DHCP

#### Paquet 1 : DHCP Discover

Sélectionnez le premier paquet DHCP Discover.

**Analyse de la trame Ethernet :**
```
Destination : ff:ff:ff:ff:ff:ff (Broadcast)
Source : <MAC du client>
Type : 0x0800 (IPv4)
```

**Analyse IP :**
```
Source : 0.0.0.0 (le client n'a pas encore d'IP)
Destination : 255.255.255.255 (Broadcast IP)
Protocol : UDP (17)
```

**Analyse UDP :**
```
Source Port : 68 (DHCP/BOOTP Client)
Destination Port : 67 (DHCP/BOOTP Server)
```

**Analyse DHCP :**
```
Message Type : Boot Request (1)
Hardware Type : Ethernet
Hardware Address Length : 6
Transaction ID : 0xXXXXXXXX (identifiant unique)
Client IP Address : 0.0.0.0
Your (client) IP Address : 0.0.0.0
Server IP Address : 0.0.0.0
Gateway IP Address : 0.0.0.0
Client MAC Address : <MAC du client>
Client Hostname : (optionnel)
Options DHCP :
  - DHCP Message Type : Discover (1)
  - Parameter Request List : Subnet Mask, Router, DNS, Domain Name...
  - Client Identifier : <MAC du client>
```

**Questions à répondre :**
- Pourquoi l'adresse IP source est-elle 0.0.0.0 ?
- Pourquoi utilise-t-on le broadcast au niveau Ethernet et IP ?
- Quel est le Transaction ID et à quoi sert-il ?

#### Paquet 2 : DHCP Offer

Sélectionnez le paquet DHCP Offer correspondant.

**Analyse de la trame Ethernet :**
```
Destination : <MAC du client> ou ff:ff:ff:ff:ff:ff
Source : <MAC du serveur DHCP>
```

**Analyse IP :**
```
Source : <IP du serveur DHCP>
Destination : <IP proposée> ou 255.255.255.255
```

**Analyse DHCP :**
```
Message Type : Boot Reply (2)
Transaction ID : 0xXXXXXXXX (même que Discover)
Your (client) IP Address : <IP proposée au client>
Server IP Address : <IP du serveur DHCP>
Options DHCP :
  - DHCP Message Type : Offer (2)
  - DHCP Server Identifier : <IP du serveur>
  - IP Address Lease Time : 86400 seconds (1 jour)
  - Subnet Mask : 255.255.255.0
  - Router : <IP de la passerelle>
  - Domain Name Server : <IP des DNS>
  - Domain Name : exemple.com
```

**Questions à répondre :**
- Quelle adresse IP le serveur propose-t-il ?
- Quelle est la durée du bail (lease time) ?
- Quelles sont les adresses des serveurs DNS fournis ?
- Quelle est l'adresse de la passerelle par défaut ?

#### Paquet 3 : DHCP Request

**Analyse DHCP :**
```
Message Type : Boot Request (1)
Transaction ID : 0xXXXXXXXX
Requested IP Address : <IP proposée>
Options DHCP :
  - DHCP Message Type : Request (3)
  - DHCP Server Identifier : <IP du serveur choisi>
  - Requested IP Address : <IP demandée>
  - Client Identifier : <MAC>
```

**Particularités :**
- Envoyé en broadcast même si le client connaît le serveur
- Permet d'informer tous les serveurs DHCP de son choix
- Contient l'IP du serveur sélectionné

**Questions à répondre :**
- Pourquoi ce paquet est-il envoyé en broadcast ?
- Le Transaction ID a-t-il changé depuis le Discover ?

#### Paquet 4 : DHCP ACK

**Analyse DHCP :**
```
Message Type : Boot Reply (2)
Transaction ID : 0xXXXXXXXX
Your (client) IP Address : <IP confirmée>
Options DHCP :
  - DHCP Message Type : ACK (5)
  - DHCP Server Identifier : <IP du serveur>
  - IP Address Lease Time : 86400 seconds
  - Subnet Mask : 255.255.255.0
  - Router : <IP passerelle>
  - Domain Name Server : <IP DNS>
  - (tous les paramètres de configuration)
```

**Questions à répondre :**
- La configuration réseau complète est-elle fournie dans ce paquet ?
- Que se passerait-il si le serveur envoyait un DHCP NAK au lieu d'un ACK ?

### Utilisation des outils statistiques pour DHCP

#### Graphique des flux

1. Menu `Statistics > Flow Graph`
2. Sélectionnez "Displayed packets"
3. Observez la séquence DHCP visualisée graphiquement

#### Conversations DHCP

1. Menu `Statistics > Conversations`
2. Onglet "UDP"
3. Identifiez les conversations entre ports 67 et 68

### Scénarios particuliers DHCP

#### Renouvellement de bail (DHCP Renewal)

Lorsque 50% du bail est écoulé, le client tente de renouveler :

```
CLIENT                          SERVEUR
  │  DHCP REQUEST (unicast)         │
  │ ────────────────────────────────>│
  │         DHCP ACK                 │
  │ <────────────────────────────────│
```

**Caractéristiques :**
- Échange en unicast (directement au serveur)
- Pas de Discover ni Offer
- Transaction plus rapide

#### Libération d'adresse (DHCP Release)

Quand une machine s'éteint proprement :

```
CLIENT                          SERVEUR
  │  DHCP RELEASE                    │
  │ ────────────────────────────────>│
```

Le client informe le serveur qu'il n'utilise plus l'IP.

#### Détection d'IP dupliquée

Si le client détecte que l'IP est déjà utilisée :

```
CLIENT                          SERVEUR
  │  DHCP DECLINE                    │
  │ ────────────────────────────────>│
  │  DHCP DISCOVER                   │
  │ ────────────────────────────────>│
  │  ...                             │
```

Le processus DORA recommence.

### Exercices pratiques DHCP

**Exercice 1 : Identification des adresses**

Sur votre capture DHCP :
1. Identifiez l'adresse MAC du client
2. Identifiez l'adresse IP du serveur DHCP
3. Relevez l'adresse IP attribuée au client
4. Notez l'adresse de la passerelle par défaut
5. Listez les serveurs DNS fournis

**Exercice 2 : Analyse temporelle**

1. Relevez le temps entre le Discover et l'Offer
2. Relevez le temps entre le Request et l'ACK
3. Calculez le temps total de l'échange DHCP

**Exercice 3 : Options DHCP**

Dans le paquet DHCP ACK, identifiez toutes les options fournies :
- Option 1 : Subnet Mask
- Option 3 : Router
- Option 6 : Domain Name Server
- Option 51 : IP Address Lease Time
- Autres options...

**Exercice 4 : Filtres avancés**

Créez des filtres pour isoler :
```
# Seulement les DHCP Discover
bootp.option.dhcp == 1

# Seulement les DHCP Offer
bootp.option.dhcp == 2

# Seulement les DHCP Request
bootp.option.dhcp == 3

# Seulement les DHCP ACK
bootp.option.dhcp == 5

# Transactions d'un client spécifique (par MAC)
eth.addr == xx:xx:xx:xx:xx:xx && dhcp
```

**Exercice 5 : Comparaison**

Comparez les valeurs des champs suivants entre Discover et Request :
- Transaction ID
- Client MAC Address
- Options demandées

**Exercice 6 : Sécurité**

Questions de réflexion :
- Un attaquant pourrait-il usurper un serveur DHCP ?
- Comment protéger le réseau contre un faux serveur DHCP ?
- Qu'est-ce que le DHCP Snooping ?

---

## Capture de trames en temps réel

### Préparation de la capture

#### Sélection de l'interface

1. Menu `Capture > Options` ou Ctrl+K
2. Sélectionnez l'interface réseau active :
   - **eth0** / **enp3s0** pour Ethernet filaire
   - **wlan0** / **wlp2s0** pour WiFi
   - **any** pour toutes les interfaces (Linux seulement)

#### Options de capture importantes

**Capture Options :**

- **Capture packets in promiscuous mode** : 
  - Activé : Capture tous les paquets sur le réseau (pas seulement ceux destinés à votre machine)
  - Nécessaire pour analyser le trafic réseau général
  - Peut ne pas fonctionner sur tous les réseaux WiFi

- **Limit each packet to X bytes** :
  - Limite la taille capturée de chaque paquet
  - Utile pour économiser l'espace disque
  - Valeur par défaut : désactivé (capture complète)

**Capture File(s) :**

- **File** : Chemin du fichier de sauvegarde
- **Use multiple files** : Créer plusieurs fichiers de capture
- **Ring buffer** : Limiter le nombre/taille des fichiers

**Stop Capture :**

- **after X packets** : Arrêter après N paquets
- **after X megabytes** : Arrêter après N Mo
- **after X minutes** : Arrêter après N minutes

**Display Options :**

- **Update list of packets in real time** : Afficher les paquets pendant la capture
- **Automatic scrolling in live capture** : Défilement automatique
- **Hide capture info dialog** : Masquer la fenêtre d'informations

**Name Resolution :**

- **Enable MAC name resolution** : Résoudre les noms constructeurs
- **Enable network name resolution** : Résoudre les noms d'hôtes (DNS)
- **Enable transport name resolution** : Résoudre les noms de services (ports)

**Attention :** La résolution de noms peut ralentir la capture et générer du trafic DNS.

### Démarrage d'une capture

**Méthode 1 : Capture simple**
1. Cliquez sur l'interface dans l'écran d'accueil
2. La capture démarre immédiatement

**Méthode 2 : Capture avec options**
1. Menu `Capture > Options`
2. Configurez les options
3. Cliquez sur "Start"

**Méthode 3 : Raccourci clavier**
- Ctrl+E : Démarrer/arrêter la capture

### Pendant la capture

**Indicateurs visuels :**
- Barre de progression montrant les paquets capturés
- Graphique d'activité réseau
- Compteur de paquets

**Actions possibles :**
- Arrêter : Cliquez sur le carré rouge ou Ctrl+E
- Redémarrer : Ctrl+R
- Continuer sans sauvegarder

### Arrêt et sauvegarde

**Arrêter la capture :**
1. Cliquez sur l'icône carré rouge
2. Ou Menu `Capture > Stop` ou Ctrl+E

**Sauvegarder la capture :**
1. Menu `File > Save As` ou Ctrl+Shift+S
2. Choisissez le format :
   - **pcap** : Format standard (lisible par tcpdump)
   - **pcapng** : Format Wireshark étendu (recommandé)
3. Nommez le fichier
4. Cliquez sur "Save"

### Filtres de capture

Les filtres de capture utilisent la syntaxe BPF (Berkeley Packet Filter).

**Syntaxe de base :**
```
# Par protocole
tcp
udp
icmp
arp

# Par hôte
host 192.168.1.1
host google.com

# Par réseau
net 192.168.1.0/24
net 192.168.1.0 mask 255.255.255.0

# Par port
port 80
port 443
portrange 1-1024

# Direction
src host 192.168.1.10
dst host 192.168.1.1
src port 80
dst port 443
```

**Opérateurs :**
```
and  (&&)
or   (||)
not  (!)
```

**Exemples de filtres de capture :**
```
# Tout le trafic HTTP et HTTPS
port 80 or port 443

# Trafic vers/depuis un serveur spécifique
host 192.168.1.100

# Trafic TCP sauf SSH
tcp and not port 22

# Trafic DNS et DHCP
port 53 or port 67 or port 68

# Trafic depuis un sous-réseau
src net 192.168.1.0/24

# Broadcast et multicast
ether broadcast or ether multicast
```

**Configuration du filtre de capture :**
1. Menu `Capture > Options`
2. Dans le champ "Capture Filter", saisissez le filtre
3. Cliquez sur "Start"

---

## Manipulations pratiques avancées

### Manipulation 1 : Analyse d'une navigation web

**Objectif :** Capturer et analyser une session de navigation HTTP/HTTPS.

**Étapes :**

1. **Préparation**
   - Videz le cache de votre navigateur
   - Fermez tous les onglets

2. **Capture**
   - Démarrez Wireshark en mode root/administrateur
   - Sélectionnez votre interface réseau active
   - Démarrez la capture
   - Ouvrez votre navigateur
   - Visitez un site en HTTP (exemple : http://neverssl.com)
   - Arrêtez la capture

3. **Analyse DNS**
   - Filtrez : `dns`
   - Identifiez la requête DNS (Query)
   - Identifiez la réponse DNS (Response)
   - Relevez l'adresse IP du serveur DNS
   - Relevez l'adresse IP retournée pour le site

4. **Analyse TCP**
   - Filtrez : `tcp`
   - Identifiez le three-way handshake (SYN, SYN-ACK, ACK)
   - Notez les numéros de séquence initiaux
   - Observez les flags TCP

5. **Analyse HTTP**
   - Filtrez : `http`
   - Examinez la requête HTTP GET
   - Examinez la réponse HTTP (code 200 OK)
   - Utilisez "Follow TCP Stream" (clic droit sur un paquet HTTP)
   - Observez l'échange complet

6. **Questions**
   - Combien de requêtes DNS ont été nécessaires ?
   - Quel est le User-Agent de votre navigateur ?
   - Combien de connexions TCP ont été établies ?
   - Quelle est la taille totale des données HTTP transférées ?

### Manipulation 2 : Analyse ICMP avec ping

**Objectif :** Analyser les paquets ICMP générés par la commande ping.

**Étapes :**

1. **Capture**
   - Démarrez la capture Wireshark
   - Dans un terminal, exécutez :
   ```bash
   ping -c 4 8.8.8.8
   # Windows : ping -n 4 8.8.8.8
   ```
   - Arrêtez la capture

2. **Filtrage**
   - Appliquez le filtre : `icmp`

3. **Analyse**
   - Identifiez les paquets Echo Request (Type 8)
   - Identifiez les paquets Echo Reply (Type 0)
   - Examinez les champs :
     - Type et Code
     - Identifier et Sequence Number
     - Data (payload)

4. **Calcul du RTT (Round Trip Time)**
   - Notez le Time delta entre Request et Reply
   - Comparez avec les valeurs affichées par ping

5. **Questions**
   - Quelle est la taille des paquets ICMP (en-tête + data) ?
   - Pourquoi y a-t-il des données dans le champ Data ?
   - À quoi servent Identifier et Sequence Number ?

### Manipulation 3 : Analyse d'une résolution ARP

**Objectif :** Observer le protocole ARP pour la résolution d'adresses.

**Étapes :**

1. **Préparation**
   - Videz le cache ARP :
   ```bash
   # Linux
   sudo ip -s -s neigh flush all
   
   # Windows
   arp -d
   ```

2. **Capture**
   - Démarrez Wireshark
   - Pingez la passerelle de votre réseau :
   ```bash
   ping -c 1 <IP_gateway>
   ```
   - Arrêtez la capture

3. **Filtrage**
   - Appliquez le filtre : `arp`

4. **Analyse ARP Request**
   - Source MAC : Votre MAC
   - Destination MAC : Broadcast (ff:ff:ff:ff:ff:ff)
   - Sender MAC : Votre MAC
   - Sender IP : Votre IP
   - Target MAC : 00:00:00:00:00:00
   - Target IP : IP de la gateway

5. **Analyse ARP Reply**
   - Source MAC : MAC de la gateway
   - Destination MAC : Votre MAC
   - Sender MAC : MAC de la gateway
   - Sender IP : IP de la gateway
   - Target MAC : Votre MAC
   - Target IP : Votre IP

6. **Questions**
   - Pourquoi l'ARP Request est-il envoyé en broadcast ?
   - Pourquoi l'ARP Reply est-il envoyé en unicast ?
   - Vérifiez le cache ARP après l'échange :
   ```bash
   # Linux : ip neigh show
   # Windows : arp -a
   ```

### Manipulation 4 : Comparaison TCP vs UDP

**Objectif :** Comparer les mécanismes de TCP (connecté) et UDP (non connecté).

**Trafic TCP (HTTP) :**

1. Capturez une session HTTP
2. Filtrez : `tcp.port == 80`
3. Observez :
   - Three-way handshake (établissement)
   - Transfert de données avec ACK
   - Fermeture de connexion (FIN, ACK)
   - Mécanisme de fenêtre glissante
   - Retransmissions en cas de perte

**Trafic UDP (DNS) :**

1. Capturez une requête DNS
2. Filtrez : `udp.port == 53`
3. Observez :
   - Requête envoyée
   - Réponse reçue
   - Aucun handshake
   - Aucun accusé de réception
   - Simple échange requête-réponse

**Questions comparatives :**
- Quel protocole est le plus rapide ? Pourquoi ?
- Quel protocole est le plus fiable ? Pourquoi ?
- Dans quels cas utiliser TCP ? UDP ?

### Manipulation 5 : Statistiques réseau

**Objectif :** Utiliser les outils statistiques de Wireshark.

**1. Summary (Résumé de capture)**
- Menu `Statistics > Capture File Properties`
- Informations : durée, nombre de paquets, taille, débit

**2. Protocol Hierarchy (Hiérarchie des protocoles)**
- Menu `Statistics > Protocol Hierarchy`
- Affiche la distribution des protocoles
- Pourcentage de bande passante par protocole

**3. Conversations**
- Menu `Statistics > Conversations`
- Onglets : Ethernet, IPv4, TCP, UDP
- Liste des conversations (source ↔ destination)
- Tri par nombre de paquets ou octets échangés

**4. Endpoints**
- Menu `Statistics > Endpoints`
- Liste des points de terminaison
- Trafic émis et reçu par chaque équipement

**5. I/O Graph**
- Menu `Statistics > I/O Graph`
- Graphique du débit en fonction du temps
- Possibilité d'appliquer des filtres

**Exercice :**
Sur une capture de plusieurs minutes :
1. Identifiez le protocole le plus utilisé
2. Trouvez les 3 conversations ayant échangé le plus de données
3. Identifiez les pics d'activité dans l'I/O Graph

### Manipulation 6 : Export de données

**Objectif :** Extraire des objets de la capture.

**Export d'objets HTTP :**

1. Capturez une navigation web (HTTP)
2. Menu `File > Export Objects > HTTP`
3. Visualisez les fichiers transférés (images, CSS, JS, HTML)
4. Sélectionnez un objet et cliquez sur "Save"

**Reconstruction TCP Stream :**

1. Cliquez droit sur un paquet TCP
2. `Follow > TCP Stream`
3. Visualisez l'échange complet
4. Possibilité de sauvegarder ou filtrer

**Export de paquets spécifiques :**

1. Appliquez un filtre
2. Menu `File > Export Specified Packets`
3. Sélectionnez "Displayed" pour exporter uniquement les paquets filtrés
4. Sauvegardez

---

## Questions de synthèse

### Questions sur l'adressage réseau

**Question 1 : Valeur du champ Type Ethernet pour IPv4**
Dans l'en-tête Ethernet II, quelle est la valeur du champ Type pour identifier le protocole IP ?

**Question 2 : Numéro de protocole TCP**
Dans l'en-tête IP, quelle est la valeur du champ Protocol pour identifier TCP ?

**Question 3 : Vérification sous Linux**
Sous Linux, consultez le fichier `/etc/protocols` pour vérifier :
```bash
grep 'tcp' /etc/protocols
grep 'udp' /etc/protocols
```

**Question 4 : Port HTTP**
Quel est le numéro de port standard utilisé par le service HTTP ?

**Question 5 : Port client**
Dans une communication HTTP, quel type de port utilise le client (éphémère, bien connu, enregistré) ?

**Question 6 : Codage des ports**
Sur combien d'octets sont codés les numéros de ports dans TCP et UDP ?
Combien de processus simultanés peuvent théoriquement communiquer via TCP sur une machine ?

**Question 7 : Vérification des services**
Sous Linux, consultez `/etc/services` pour vérifier :
```bash
grep 'http' /etc/services
grep '53' /etc/services
```

### Questions sur l'encapsulation

**Question 8 : Encodage ASCII**
Les en-têtes des protocoles Ethernet, IP et TCP sont-ils encodés en ASCII ?

**Question 9 : Protocole applicatif**
L'en-tête HTTP est-il encodé en ASCII ? Pourquoi ?

**Question 10 : Protocole UDP**
Quelle est la valeur du champ Protocol IP pour identifier UDP ?

### Questions sur DHCP

**Question 11 : Ports DHCP**
Quels sont les ports UDP utilisés par DHCP ?
- Port du client : ?
- Port du serveur : ?

**Question 12 : Adresse MAC du client**
Dans votre capture DHCP, relevez l'adresse MAC du client.

**Question 13 : Transaction ID**
À quoi sert le Transaction ID dans les échanges DHCP ?

**Question 14 : Broadcast ou Unicast**
Pourquoi le DHCP Request est-il envoyé en broadcast et non directement au serveur ?

**Question 15 : Durée de bail**
Quelle est la durée du bail (lease time) attribuée dans votre capture ?
Exprimez-la en heures et en jours.

**Question 16 : Options DHCP**
Listez toutes les options DHCP fournies dans le DHCP ACK.

**Question 17 : Renouvellement**
À quel moment (en % du bail) le client doit-il tenter de renouveler son adresse ?

**Question 18 : Serveur DNS**
Quelles sont les adresses des serveurs DNS fournis par le serveur DHCP ?

### Questions sur l'analyse de trafic

**Question 19 : Débit moyen**
Dans une capture de quelques minutes, utilisez `Statistics > Summary` pour relever le débit moyen.

**Question 20 : Protocole prépondérant**
Utilisez `Statistics > Protocol Hierarchy`. Quel protocole de transport (TCP ou UDP) est le plus utilisé ?

**Question 21 : End Packets**
Pourquoi la colonne "End packets" dans Protocol Hierarchy contient-elle moins de paquets que "Packets" pour TCP ?

**Question 22 : Rôle des paquets**
À quoi servent les paquets TCP qui ne sont pas comptabilisés dans "End packets" ?

**Question 23 : Débit par couche**
Pourquoi le débit affiché pour les couches hautes (Application) est-il inférieur à celui des couches basses (Ethernet) ?

### Questions pratiques

**Question 24 : Requête DNS**
Dans une capture web, identifiez :
- L'adresse IP du serveur DNS utilisé
- Le nom de domaine demandé
- L'adresse IP retournée

**Question 25 : Three-way handshake**
Dans une connexion TCP, identifiez les 3 paquets du handshake :
- Paquet 1 : Flags ?
- Paquet 2 : Flags ?
- Paquet 3 : Flags ?

**Question 26 : User-Agent**
Dans une requête HTTP, relevez le User-Agent. À quoi correspond-il ?

**Question 27 : Code de réponse HTTP**
Testez une URL inexistante. Quel code HTTP le serveur renvoie-t-il ?

**Question 28 : ARP Gateway**
L'adresse MAC destination d'une trame contenant une requête DNS est-elle celle du serveur DNS ?
Si non, à qui appartient-elle ?

---

## Annexes

### Raccourcis clavier utiles

| Raccourci | Action |
|-----------|--------|
| Ctrl+E | Démarrer/Arrêter capture |
| Ctrl+K | Options de capture |
| Ctrl+R | Redémarrer capture |
| Ctrl+O | Ouvrir fichier |
| Ctrl+S | Sauvegarder |
| Ctrl+Shift+S | Sauvegarder sous |
| Ctrl+F | Rechercher paquet |
| Ctrl+N | Paquet suivant |
| Ctrl+B | Paquet précédent |
| Ctrl+↑ | Premier paquet |
| Ctrl+↓ | Dernier paquet |
| Ctrl+. | Paquet suivant dans conversation |
| Ctrl+, | Paquet précédent dans conversation |
| Ctrl+G | Aller au paquet N° |
| Ctrl+M | Marquer paquet |
| Ctrl+Shift+M | Enlever toutes les marques |
| Ctrl+/ | Appliquer filtre |
| Alt+← | Retour arrière |
| Alt+→ | Avancer |

### Codes couleur par défaut

| Couleur | Protocole/Type |
|---------|----------------|
| Vert clair | TCP |
| Bleu clair | UDP |
| Noir | Paquets TCP avec erreurs |
| Rose | Paquets TCP avec problèmes |
| Jaune | Paquets marqués |
| Bleu foncé | DNS |
| Rouge | Erreurs, problèmes |
| Gris clair | ICMP |

### Filtres d'affichage courants

```
# Protocoles
http
https
dns
dhcp ou bootp
arp
icmp
tcp
udp
ssh
ftp
smtp

# Par adresse
ip.addr == 192.168.1.1
ip.src == 192.168.1.10
ip.dst == 8.8.8.8
eth.addr == 00:11:22:33:44:55

# Par port
tcp.port == 80
udp.port == 53
tcp.dstport == 443
tcp.srcport >= 1024

# Flags TCP
tcp.flags.syn == 1
tcp.flags.ack == 1
tcp.flags.fin == 1
tcp.flags.rst == 1
tcp.flags.push == 1

# Combinaisons
ip.addr == 192.168.1.1 && tcp.port == 80
!arp && !dns
(tcp.port == 80 || tcp.port == 443) && ip.dst == 192.168.1.1

# HTTP spécifique
http.request.method == "GET"
http.request.method == "POST"
http.response.code == 200
http.response.code >= 400

# DNS spécifique
dns.qry.name contains "google"
dns.flags.response == 1

# Erreurs
tcp.analysis.retransmission
tcp.analysis.lost_segment
tcp.analysis.duplicate_ack

# Taille
frame.len > 1000
tcp.len > 0
```

### Commandes utiles en ligne de commande

**tshark (Wireshark en CLI) :**

```bash
# Lister les interfaces
tshark -D

# Capturer sur eth0
tshark -i eth0

# Capturer avec filtre
tshark -i eth0 -f "port 80"

# Sauvegarder dans un fichier
tshark -i eth0 -w capture.pcap

# Lire un fichier
tshark -r capture.pcap

# Appliquer un filtre d'affichage
tshark -r capture.pcap -Y "http"

# Afficher uniquement certains champs
tshark -r capture.pcap -T fields -e ip.src -e ip.dst -e tcp.port
```

**tcpdump (alternative) :**

```bash
# Capturer sur eth0
sudo tcpdump -i eth0

# Sauvegarder
sudo tcpdump -i eth0 -w capture.pcap

# Lire un fichier
tcpdump -r capture.pcap

# Avec filtre
sudo tcpdump -i eth0 port 80
sudo tcpdump -i eth0 host 192.168.1.1
```

### Structure des protocoles

**En-tête Ethernet II :**
```
0                   1                   2                   3
0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Destination MAC (6 octets)                 |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                      Source MAC (6 octets)                    |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|         EtherType (2 octets)  |          Data...              |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

**En-tête IPv4 (simplifié) :**
```
0               1               2               3
0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|Version|  IHL  |Type of Service|          Total Length         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|         Identification        |Flags|      Fragment Offset    |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  Time to Live |    Protocol   |         Header Checksum       |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                       Source IP Address                       |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Destination IP Address                     |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

**En-tête TCP (simplifié) :**
```
0               1               2               3
0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|          Source Port          |       Destination Port        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                        Sequence Number                        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Acknowledgment Number                      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|Offset |Reserv.|     Flags     |            Window             |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|           Checksum            |         Urgent Pointer        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

**Flags TCP :**
- URG : Urgent
- ACK : Acknowledgment
- PSH : Push
- RST : Reset
- SYN : Synchronize
- FIN : Finish

### Ressources complémentaires

**Documentation officielle :**
- Site Wireshark : https://www.wireshark.org/
- Wiki Wireshark : https://wiki.wireshark.org/
- Guide utilisateur : https://www.wireshark.org/docs/wsug_html_chunked/
- Exemples de captures : https://wiki.wireshark.org/SampleCaptures

**Tutoriels et cours :**
- PacketLife : https://packetlife.net/
- Wireshark University : https://wiresharktraining.com/
- Cloudshark (analyse en ligne) : https://www.cloudshark.org/

**RFC (Request For Comments) :**
- RFC 791 : Internet Protocol (IP)
- RFC 793 : Transmission Control Protocol (TCP)
- RFC 768 : User Datagram Protocol (UDP)
- RFC 2131 : Dynamic Host Configuration Protocol (DHCP)
- RFC 826 : Address Resolution Protocol (ARP)
- RFC 1034/1035 : Domain Name System (DNS)

**Livres recommandés :**
- "Wireshark Network Analysis" - Laura Chappell
- "Practical Packet Analysis" - Chris Sanders
- "TCP/IP Illustrated" - Richard Stevens

---

## Conclusion

Ce TP vous a permis de :

✓ Installer et configurer Wireshark sur différents systèmes
✓ Comprendre l'interface et les fonctionnalités principales
✓ Analyser l'encapsulation des protocoles TCP/IP
✓ Maîtriser le filtrage et la recherche de paquets
✓ Comprendre en détail le protocole DHCP et son fonctionnement
✓ Capturer du trafic réseau en temps réel
✓ Utiliser les outils statistiques d'analyse
✓ Exporter et sauvegarder des captures

**Compétences acquises :**

- Analyse de protocoles réseaux
- Dépannage réseau
- Compréhension de TCP/IP
- Sécurité et audit réseau
- Utilisation d'outils professionnels

**Pour aller plus loin :**

- Analysez le trafic HTTPS avec décryptage SSL/TLS
- Étudiez les protocoles de routage (OSPF, BGP)
- Analysez des protocoles VoIP (SIP, RTP)
- Détectez des anomalies et attaques réseau
- Automatisez l'analyse avec tshark et scripts
- Explorez les protocoles sans fil (802.11)
- Pratiquez l'analyse forensique réseau

---

**Bon apprentissage avec Wireshark !**

*Document créé dans le cadre d'un TP d'analyse de réseaux*
*Version : 2.0*
