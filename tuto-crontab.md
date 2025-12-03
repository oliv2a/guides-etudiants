# Automatiser un Script Python avec Crontab sur Raspberry PI (H1)
## Introduction

Ce tutoriel explique comment automatiser l’exécution d’un script Python sur un Raspberry Pi en
utilisant **crontab**. Il inclut la gestion d’un environnement virtuel Python et la configuration d’une
exécution toutes les minutes.

---

## Prérequis

- Avoir un environement virtuel sur le Raspberry
- Avoir un projet fonctioneel
  
---

## Étape 1 : Identifier les chemins complets

Avant de configurer Crontab, notez les chemins absolus : 
    - Chemin du script Python : /home/pi/mon_projet/test.py 
    - Chemin de l’environnement virtuel : /home/pi/mon_projet/venv 
    - Chemin du Python du venv : /home/pi/mon_projet/venv/bin/python Vous pouvez vérifier le chemin de Python avec : which python

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
