# Tutoriel : Créer son premier dépôt GitHub

## Introduction

Ce tutoriel vous guide pas à pas pour créer votre premier dépôt (repository) sur GitHub. Un dépôt est comme un dossier de projet qui stocke votre code et son historique de modifications.

---

## Prérequis

- Avoir un compte GitHub (créez-en un gratuitement sur [github.com](https://github.com))
- Être connecté à votre compte

---

## Étape 1 : Accéder à la création d'un nouveau dépôt

1. Connectez-vous à [GitHub](https://github.com)
2. Cliquez sur le bouton **"+"** en haut à droite de la page
3. Sélectionnez **"New repository"** dans le menu déroulant

![Bouton de création](https://docs.github.com/assets/cb-11427/mw-1440/images/help/repository/repo-create.webp)

---

## Étape 2 : Configurer votre dépôt

### Nom du dépôt (Repository name)

Choisissez un nom descriptif pour votre projet :
- Utilisez des lettres minuscules
- Remplacez les espaces par des tirets `-`
- Exemples : `mon-premier-projet`, `tp-python`, `site-web-perso`

### Description (optionnel)

Ajoutez une courte description de votre projet (recommandé).

Exemple : *"Mon premier projet sur GitHub pour apprendre les bases"*

### Visibilité

Choisissez entre :
- **Public** : Tout le monde peut voir votre dépôt (recommandé pour l'apprentissage)
- **Private** : Seulement vous et les personnes que vous invitez peuvent le voir

---

## Étape 3 : Initialiser le dépôt

### ✅ Cochez "Add a README file"

Le fichier README est la page d'accueil de votre projet. Il est écrit en Markdown et explique ce que fait votre projet.

**Toujours cocher cette option pour commencer !**

### Ajouter un .gitignore (optionnel)

Le fichier `.gitignore` indique à Git quels fichiers ne pas suivre (par exemple, fichiers temporaires).

Si vous travaillez avec un langage spécifique :
- Cliquez sur le menu déroulant **"Add .gitignore"**
- Sélectionnez votre langage (Python, Java, Node, etc.)

### Choisir une licence (optionnel)

Pour un projet d'apprentissage, vous pouvez :
- **Choisir "MIT License"** : simple et permissive
- **Ne pas choisir de licence** : si c'est juste pour vous

---

## Étape 4 : Créer le dépôt

1. Vérifiez que toutes les informations sont correctes
2. Cliquez sur le bouton vert **"Create repository"**

🎉 **Félicitations !** Votre premier dépôt est créé !

---

## Étape 5 : Découvrir votre dépôt

Vous êtes maintenant sur la page de votre dépôt. Vous y trouverez :

- **Le README.md** affiché en bas de page
- **Les boutons** : Code, Issues, Pull requests, etc.
- **L'URL** de votre dépôt en haut

---

## Étape 6 : Modifier le README

Le README est la première chose que les visiteurs voient. Personnalisons-le !

1. Cliquez sur le fichier **README.md**
2. Cliquez sur l'icône **crayon (✏️)** en haut à droite pour éditer
3. Modifiez le contenu avec du Markdown (voir exemples ci-dessous)
4. Descendez en bas et cliquez sur **"Commit changes"**
5. Dans la popup, cliquez à nouveau sur **"Commit changes"**

### Exemple de README simple

```markdown
# Mon Premier Projet

## Description
Ceci est mon premier dépôt GitHub créé pour apprendre à utiliser Git et GitHub.

## Objectifs
- Comprendre comment créer un dépôt
- Apprendre le Markdown
- Faire mes premiers commits

## À propos
Projet réalisé dans le cadre de ma formation en développement.
```

---

## Étape 7 : Cloner votre dépôt sur votre ordinateur

Pour travailler localement sur votre projet :

1. Sur la page de votre dépôt, cliquez sur le bouton vert **"Code"**
2. Copiez l'URL HTTPS (ressemble à `https://github.com/votre-nom/nom-depot.git`)
3. Ouvrez un terminal sur votre ordinateur
4. Tapez la commande suivante :

```bash
git clone [collez l'URL ici]
```

Exemple :
```bash
git clone https://github.com/john/mon-premier-projet.git
```

5. Accédez au dossier créé :
```bash
cd mon-premier-projet
```

Vous pouvez maintenant travailler sur votre projet localement !

---

## Bases du Markdown pour le README

Le Markdown est un langage simple pour formater du texte.

### Titres
```markdown
# Titre principal (H1)
## Sous-titre (H2)
### Sous-sous-titre (H3)
```

### Texte
```markdown
**Texte en gras**
*Texte en italique*
```

### Listes
```markdown
- Élément 1
- Élément 2
- Élément 3

1. Premier
2. Deuxième
3. Troisième
```

### Code
````markdown
`code en ligne`

```python
# Bloc de code
print("Hello World")
```
````

### Liens
```markdown
[Texte du lien](https://exemple.com)
```

### Images
```markdown
![Texte alternatif](https://url-de-image.com/image.png)
```

---

## Commandes Git de base

Une fois votre dépôt cloné, voici les commandes essentielles :

### Voir l'état de vos fichiers
```bash
git status
```

### Ajouter des fichiers modifiés
```bash
git add nom-du-fichier.txt
# ou pour tout ajouter :
git add .
```

### Créer un commit (sauvegarder vos modifications)
```bash
git commit -m "Description de vos modifications"
```

### Envoyer vos modifications sur GitHub
```bash
git push
```

### Récupérer les dernières modifications depuis GitHub
```bash
git pull
```

---

## Bonnes pratiques

✅ **Écrivez un README clair** : expliquez ce que fait votre projet  
✅ **Faites des commits réguliers** : sauvegardez souvent votre travail  
✅ **Utilisez des messages de commit descriptifs** : "Ajout de la fonction de connexion" plutôt que "update"  
✅ **Organisez votre code** : créez des dossiers pour structurer votre projet  
✅ **Mettez à jour le README** : gardez-le synchronisé avec l'évolution du projet

---

## Ressources utiles

- [Documentation officielle GitHub](https://docs.github.com)
- [Guide Markdown](https://www.markdownguide.org)
- [Git Cheat Sheet](https://education.github.com/git-cheat-sheet-education.pdf)
- [Apprendre Git en 15 minutes](https://try.github.io)

---

## Dépannage

### Problème : Git n'est pas reconnu
Installez Git depuis [git-scm.com](https://git-scm.com)

### Problème : Erreur lors du push
Assurez-vous d'avoir fait un `git pull` avant de push

### Problème : Authentification refusée
Utilisez un Personal Access Token au lieu de votre mot de passe (voir documentation GitHub)

---

## Conclusion

Félicitations ! Vous savez maintenant :
- ✅ Créer un dépôt GitHub
- ✅ Éditer un fichier README
- ✅ Utiliser le Markdown
- ✅ Cloner un dépôt localement
- ✅ Utiliser les commandes Git de base

**Prochaines étapes :**
- Ajoutez des fichiers à votre projet
- Expérimentez avec les branches
- Collaborez avec d'autres développeurs
- Explorez les Issues et Pull Requests

Bon codage ! 🚀