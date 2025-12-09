# Tutoriel Cisco Packet Tracer
## Installation, Prise en Main et Configuration d'un Réseau Privé

---

## Table des matières

1. [Introduction](#introduction)
2. [Installation de Cisco Packet Tracer](#installation)
3. [Prise en main de l'interface](#prise-en-main)
4. [Configuration d'un réseau privé avec passerelle](#configuration-réseau)
5. [Tests et vérification](#tests-et-vérification)
6. [Dépannage](#dépannage)

---

## Introduction

**Cisco Packet Tracer** est un simulateur de réseau développé par Cisco Systems permettant de concevoir, configurer et tester des réseaux informatiques dans un environnement virtuel sans matériel physique.

### Objectifs de ce tutoriel

- Installer Cisco Packet Tracer
- Comprendre l'interface et les fonctionnalités de base
- Concevoir un réseau local privé (LAN)
- Configurer un routeur comme passerelle vers un réseau public (WAN)
- Tester la connectivité et valider la configuration

---

## Installation de Cisco Packet Tracer

### Prérequis

- Un compte Cisco Networking Academy (gratuit)
- Système d'exploitation : Windows, Linux ou macOS
- Espace disque : environ 500 MB
- RAM : minimum 4 GB recommandé

### Étapes d'installation

#### 1. Créer un compte Cisco Networking Academy

- Rendez-vous sur [netacad.com](https://www.netacad.com)
- Cliquez sur "Sign up" et créez votre compte gratuit
- Vérifiez votre email et activez votre compte

#### 2. Télécharger Packet Tracer

- Connectez-vous à votre compte Cisco Networking Academy
- Accédez à la section "Resources"
- Cherchez "Packet Tracer" dans les téléchargements
- Sélectionnez la version correspondant à votre système d'exploitation
- Téléchargez le fichier d'installation

#### 3. Installer le logiciel

**Pour Windows :**
- Double-cliquez sur le fichier `.exe` téléchargé
- Suivez l'assistant d'installation
- Acceptez les conditions d'utilisation
- Choisissez le répertoire d'installation
- Cliquez sur "Install" et attendez la fin de l'installation

**Pour Linux :**
```bash
# Extraire l'archive
tar -xvf PacketTracer_xxx_amd64.tar.gz

# Naviguer dans le dossier
cd PacketTracer_xxx_amd64

# Installer
sudo ./install
```

**Pour macOS :**
- Ouvrez le fichier `.dmg` téléchargé
- Glissez l'icône Packet Tracer dans le dossier Applications
- Lancez l'application depuis le Launchpad

#### 4. Premier lancement

- Lancez Cisco Packet Tracer
- Connectez-vous avec vos identifiants Cisco Networking Academy
- L'interface principale s'ouvre

---

## Prise en main de l'interface

### Vue d'ensemble de l'interface

L'interface de Packet Tracer se compose de plusieurs zones principales :

#### 1. Barre de menu supérieure
- **File** : Nouveau, Ouvrir, Sauvegarder
- **Edit** : Copier, Coller, Préférences
- **Options** : Configuration générale
- **View** : Affichage des fenêtres
- **Tools** : Outils de diagnostic
- **Extensions** : Modules complémentaires

#### 2. Barre d'outils principale
- Outils de sélection et de manipulation
- Outils de dessin et d'annotation
- Zoom et navigation

#### 3. Zone de travail logique/physique
- **Mode Logical** : Vue schématique du réseau
- **Mode Physical** : Vue physique des équipements en 3D

#### 4. Barre d'équipements (en bas)

Les équipements sont organisés par catégories :

- **Routers** : Routeurs Cisco (1841, 2911, etc.)
- **Switches** : Commutateurs (2960, 3560, etc.)
- **End Devices** : PC, ordinateurs portables, serveurs, imprimantes
- **Wireless Devices** : Points d'accès, contrôleurs
- **Connections** : Câbles réseau (console, cuivre, fibre)
- **Miscellaneous** : Nuage WAN, autres équipements

#### 5. Panneau de détails (à droite)
- Configuration des équipements sélectionnés
- Onglets Physical, Config, CLI, Desktop, Services

### Navigation de base

- **Sélectionner** : Cliquer sur un équipement
- **Déplacer** : Cliquer-glisser
- **Supprimer** : Sélectionner puis touche Suppr
- **Zoomer** : Molette de la souris ou icônes +/-
- **Annoter** : Utiliser l'outil de texte dans la barre d'outils

---

## Configuration d'un réseau privé avec passerelle

Nous allons créer un réseau local d'entreprise avec :
- Un réseau privé (192.168.1.0/24)
- Plusieurs PC clients
- Un switch pour interconnecter les PC
- Un routeur configuré comme passerelle vers Internet
- Une connexion simulée vers un réseau public

### Architecture du réseau

```
[Internet/Réseau Public]
         |
    [Routeur]
         |
     [Switch]
      /  |  \
   [PC1][PC2][PC3]
```

### Étape 1 : Ajouter les équipements

#### Ajouter un routeur

1. Cliquez sur l'icône **Routers** dans la barre d'équipements
2. Sélectionnez un routeur **1841** ou **2911**
3. Cliquez dans la zone de travail pour le placer
4. Renommez-le "Router-Gateway" (clic droit > Rename)

#### Ajouter un switch

1. Cliquez sur l'icône **Switches**
2. Sélectionnez un switch **2960**
3. Placez-le sous le routeur
4. Renommez-le "Switch-LAN"

#### Ajouter des PC

1. Cliquez sur **End Devices**
2. Sélectionnez **PC**
3. Ajoutez 3 PC dans la zone de travail
4. Renommez-les "PC1", "PC2", "PC3"

#### Ajouter un nuage WAN (pour simuler Internet)

1. Cliquez sur **Connections** puis **Cloud**
2. Placez le nuage au-dessus du routeur
3. Renommez-le "Internet"

### Étape 2 : Interconnecter les équipements

#### Connecter les PC au switch

1. Cliquez sur l'icône **Connections** (éclair orange)
2. Sélectionnez **Copper Straight-Through** (câble Ethernet droit)
3. Cliquez sur PC1, choisissez le port **FastEthernet0**
4. Cliquez sur le switch, choisissez un port **FastEthernet** (ex: Fa0/1)
5. Répétez pour PC2 (Fa0/2) et PC3 (Fa0/3)

#### Connecter le switch au routeur

1. Sélectionnez **Copper Straight-Through**
2. Cliquez sur le switch, port **GigabitEthernet0/1**
3. Cliquez sur le routeur, port **FastEthernet0/0** ou **GigabitEthernet0/0**

#### Connecter le routeur à Internet

1. Sélectionnez **Copper Straight-Through**
2. Cliquez sur le routeur, port **FastEthernet0/1** ou **GigabitEthernet0/1**
3. Cliquez sur le nuage Internet

**Note** : Les liaisons devraient apparaître en vert après quelques secondes (indiquant qu'elles sont actives).

### Étape 3 : Configuration IP du réseau privé

#### Configurer les PC

**Pour PC1 :**

1. Cliquez sur PC1
2. Allez dans l'onglet **Desktop**
3. Cliquez sur **IP Configuration**
4. Sélectionnez **Static**
5. Entrez les informations :
   - **IP Address** : `192.168.1.10`
   - **Subnet Mask** : `255.255.255.0`
   - **Default Gateway** : `192.168.1.1` (ce sera l'IP du routeur)

**Pour PC2 :**
- IP Address : `192.168.1.20`
- Subnet Mask : `255.255.255.0`
- Default Gateway : `192.168.1.1`

**Pour PC3 :**
- IP Address : `192.168.1.30`
- Subnet Mask : `255.255.255.0`
- Default Gateway : `192.168.1.1`

#### Configurer le routeur

##### Interface vers le réseau local (LAN)

1. Cliquez sur le routeur "Router-Gateway"
2. Allez dans l'onglet **CLI** (Command Line Interface)
3. Appuyez sur Entrée pour démarrer
4. Tapez `no` pour ignorer la configuration initiale
5. Entrez les commandes suivantes :

```cisco
Router> enable
Router# configure terminal
Router(config)# hostname Router-Gateway
Router-Gateway(config)# interface FastEthernet0/0
Router-Gateway(config-if)# ip address 192.168.1.1 255.255.255.0
Router-Gateway(config-if)# description Interface LAN
Router-Gateway(config-if)# no shutdown
Router-Gateway(config-if)# exit
```

**Explication des commandes :**
- `enable` : Passe en mode privilégié
- `configure terminal` : Entre en mode de configuration
- `hostname` : Définit le nom du routeur
- `interface FastEthernet0/0` : Sélectionne l'interface
- `ip address` : Assigne l'adresse IP et le masque
- `description` : Ajoute une description
- `no shutdown` : Active l'interface
- `exit` : Sort du mode d'interface

##### Interface vers le réseau public (WAN)

```cisco
Router-Gateway(config)# interface FastEthernet0/1
Router-Gateway(config-if)# ip address 203.0.113.1 255.255.255.0
Router-Gateway(config-if)# description Interface WAN vers Internet
Router-Gateway(config-if)# no shutdown
Router-Gateway(config-if)# exit
```

**Note** : 203.0.113.0/24 est un réseau de documentation. Dans un cas réel, vous utiliseriez l'IP publique fournie par votre FAI.

##### Configuration du routage par défaut

Pour permettre au réseau local d'accéder à Internet, configurez une route par défaut :

```cisco
Router-Gateway(config)# ip route 0.0.0.0 0.0.0.0 203.0.113.254
Router-Gateway(config)# exit
Router-Gateway# write memory
```

**Explication :**
- `ip route 0.0.0.0 0.0.0.0` : Route par défaut (tout le trafic)
- `203.0.113.254` : Passerelle du FAI (next-hop)
- `write memory` : Sauvegarde la configuration

### Étape 4 : Configuration NAT (Network Address Translation)

Le NAT permet aux adresses privées du LAN d'accéder à Internet via l'IP publique du routeur.

```cisco
Router-Gateway# configure terminal
Router-Gateway(config)# access-list 1 permit 192.168.1.0 0.0.0.255
Router-Gateway(config)# ip nat inside source list 1 interface FastEthernet0/1 overload
Router-Gateway(config)# interface FastEthernet0/0
Router-Gateway(config-if)# ip nat inside
Router-Gateway(config-if)# exit
Router-Gateway(config)# interface FastEthernet0/1
Router-Gateway(config-if)# ip nat outside
Router-Gateway(config-if)# exit
Router-Gateway(config)# exit
Router-Gateway# write memory
```

**Explication :**
- `access-list 1` : Définit les adresses autorisées à être traduites
- `ip nat inside source list 1 interface ... overload` : Active le NAT avec PAT (Port Address Translation)
- `ip nat inside` : Définit l'interface interne
- `ip nat outside` : Définit l'interface externe

### Étape 5 : Configuration du serveur DHCP (optionnel)

Pour automatiser l'attribution des IP aux PC :

```cisco
Router-Gateway# configure terminal
Router-Gateway(config)# ip dhcp pool LAN_POOL
Router-Gateway(dhcp-config)# network 192.168.1.0 255.255.255.0
Router-Gateway(dhcp-config)# default-router 192.168.1.1
Router-Gateway(dhcp-config)# dns-server 8.8.8.8
Router-Gateway(dhcp-config)# exit
Router-Gateway(config)# ip dhcp excluded-address 192.168.1.1 192.168.1.9
Router-Gateway(config)# exit
Router-Gateway# write memory
```

Si vous utilisez DHCP, retournez sur chaque PC et sélectionnez **DHCP** au lieu de Static dans IP Configuration.

---

## Tests et vérification

### Test 1 : Vérifier la connectivité locale

#### Depuis PC1, tester PC2

1. Cliquez sur PC1
2. Allez dans **Desktop** > **Command Prompt**
3. Tapez :

```
ping 192.168.1.20
```

Vous devriez voir des réponses positives :
```
Reply from 192.168.1.20: bytes=32 time<1ms TTL=128
```

#### Tester la passerelle

```
ping 192.168.1.1
```

### Test 2 : Vérifier la table de routage

Sur le routeur :

```cisco
Router-Gateway# show ip route
```

Vous devriez voir :
- Une route directement connectée vers 192.168.1.0/24
- Une route directement connectée vers 203.0.113.0/24
- Une route par défaut (S*) via 203.0.113.254

### Test 3 : Vérifier le NAT

```cisco
Router-Gateway# show ip nat translations
```

### Test 4 : Mode Simulation

Packet Tracer offre un mode simulation pour visualiser le cheminement des paquets :

1. Cliquez sur l'onglet **Simulation** (en bas à droite)
2. Cliquez sur **Add Simple PDU** (enveloppe avec un +)
3. Cliquez sur PC1 comme source
4. Cliquez sur PC2 comme destination
5. Cliquez sur **Play** pour voir le paquet traverser le réseau

### Test 5 : Vérifier les interfaces du routeur

```cisco
Router-Gateway# show ip interface brief
```

Toutes les interfaces devraient être "up/up".

---

## Dépannage

### Problème : Les liaisons restent rouges

**Causes possibles :**
- Interface administrativement désactivée
- Mauvais type de câble
- Port défectueux

**Solutions :**
- Vérifier avec `show ip interface brief`
- Utiliser `no shutdown` sur l'interface
- Changer le type de câble (straight-through vs crossover)

### Problème : Pas de ping entre PC

**Diagnostic :**

1. Vérifier les adresses IP des PC
2. Vérifier que les PC sont sur le même sous-réseau
3. Vérifier les connexions physiques
4. Tester la passerelle depuis chaque PC

**Solutions :**
- Reconfigurer les IP
- Vérifier le masque de sous-réseau
- Vérifier les câbles dans le mode Physical

### Problème : Pas d'accès à Internet

**Diagnostic :**

```cisco
Router-Gateway# show ip route
Router-Gateway# show ip nat translations
Router-Gateway# ping 203.0.113.254
```

**Solutions :**
- Vérifier la route par défaut
- Vérifier la configuration NAT
- Vérifier que les interfaces inside/outside sont correctes

### Commandes utiles de diagnostic

```cisco
# Afficher la configuration en cours
show running-config

# Afficher l'état des interfaces
show ip interface brief

# Afficher les routes
show ip route

# Afficher les traductions NAT
show ip nat translations

# Afficher les statistiques NAT
show ip nat statistics

# Effacer les traductions NAT
clear ip nat translation *

# Tester la connectivité
ping [adresse-ip]

# Tracer le chemin
traceroute [adresse-ip]
```

---

## Conclusion

Vous avez maintenant :

- Installé Cisco Packet Tracer
- Compris l'interface et les fonctionnalités de base
- Conçu un réseau local avec plusieurs PC
- Configuré un routeur comme passerelle
- Mis en place le NAT pour l'accès Internet
- Testé et validé votre configuration

### Pour aller plus loin

- Ajouter un serveur DHCP dédié
- Configurer des VLAN sur le switch
- Mettre en place un serveur DNS
- Créer un réseau sans fil avec un point d'accès
- Configurer des ACL (Access Control Lists) pour la sécurité
- Simuler des pannes et pratiquer le dépannage
- Explorer les protocoles de routage dynamique (RIP, OSPF, EIGRP)

### Ressources complémentaires

- Cisco Networking Academy : [netacad.com](https://www.netacad.com)
- Documentation Packet Tracer : Aide intégrée dans le logiciel
- Tutoriels vidéo : Rechercher "Cisco Packet Tracer tutorials" sur YouTube
- Communauté Cisco : [learningnetwork.cisco.com](https://learningnetwork.cisco.com)

---

**Bon apprentissage avec Cisco Packet Tracer !**
