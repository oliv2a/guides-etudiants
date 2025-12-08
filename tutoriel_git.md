# 📘 Tutoriel : Installer Git et cloner un premier dépôt

*Projet : « Services-Web-sur-Raspberry »*

## 🎯 Objectif

Ce guide explique comment : 1. Installer **Git** sur Windows, macOS ou
Linux\
2. Vérifier l'installation\
3. Cloner le dépôt **Services-Web-sur-Raspberry** depuis GitHub\
On suppose que vous avez déjà un **compte GitHub**.

------------------------------------------------------------------------

# 1️⃣ Installer Git

## 🪟 Sous Windows

1.  Rendez-vous sur : https://git-scm.com/install/windows
2.  Le téléchargement démarre automatiquement.\
3.  Lancez le fichier `.exe` téléchargé.\
4.  Cliquez sur **Next** pour toutes les options par défaut.\
5.  Terminez avec **Install**, puis **Finish**.

------------------------------------------------------------------------

## 🍎 Sous macOS

Ouvrez le **Terminal**, puis tapez :

``` sh
git --version
```

-   Si Git n'est pas installé, macOS vous proposera automatiquement de
    l'installer.
-   Cliquez sur **Installer**.

**Alternative :** installer via Homebrew

``` sh
brew install git
```

------------------------------------------------------------------------

## 🐧 Sous Linux (Ubuntu / Debian)

Dans un terminal :

``` sh
sudo apt update
sudo apt install git
```

Pour Fedora :

``` sh
sudo dnf install git
```

------------------------------------------------------------------------

# 2️⃣ Vérifier l'installation

Dans un terminal (Windows = Git Bash) :

``` sh
git --version
```

Vous devez voir quelque chose comme :

    git version 2.43.0

------------------------------------------------------------------------

# 3️⃣ Configurer Git (obligatoire la première fois)

``` sh
git config --global user.name "Votre Nom"
git config --global user.email "votre.email@exemple.com"
```

Vérification :

``` sh
git config --list
```

------------------------------------------------------------------------

# 4️⃣ Cloner votre premier dépôt : *Services-Web-sur-Raspberry*

1.  Connectez-vous à GitHub.\
2.  Allez sur le dépôt **Services-Web-sur-Raspberry** (fourni par
    l'enseignant).\
3.  Cliquez sur le bouton **\<\> Code** (en vert).\
4.  Copiez l'URL HTTPS, par exemple :

```{=html}
<!-- -->
```
    https://github.com/votre-projet/Services-Web-sur-Raspberry.git

5.  Dans un terminal, choisissez l'emplacement sur votre PC, puis tapez
    :

``` sh
git clone https://github.com/votre-projet/Services-Web-sur-Raspberry.git
```

6.  Le dossier apparaît désormais sur votre machine.

------------------------------------------------------------------------

# 5️⃣ Ouvrir le dossier cloné

``` sh
cd Services-Web-sur-Raspberry
```

Vous êtes maintenant dans le projet et pouvez commencer à travailler !

------------------------------------------------------------------------

# 🎉 Félicitations !

Vous avez installé Git et cloné votre premier dépôt.\
Vous êtes maintenant prêts pour la suite : commits, push/pull, branches,
etc.
