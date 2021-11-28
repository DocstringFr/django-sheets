# Les fichiers statiques

Par défaut Django trouve automatiquement tous les fichiers inclus dans un dossier nommé `static` à l'intérieur d'une application :

- application
  - static -> le dossier doit être nommé exactement `static`
    - css
      - `style.css`
      
Vous pouvez ensuite charger facilement ces fichiers statiques dans un fichier HTML grâce à la balise `static`. Il vous faudra au préalable charger cette balise `static` avec la balise `{% load static %}`.

Par exemple pour sourcer le fichier `style.css` :

```html
{% load static %}
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <title>Mon site</title>
    <link rel="stylesheet" href="{% static 'css/style.css' %}">
</head>
<body>
</body>
</html>
```

Attention d'utiliser des guillemets simples pour entourer le chemin relatif vers le fichier style.css à l'intérieur de la balise `static`. On utilise des guillemets doubles pour entourer l'URL associée à l'attribut `href`, il faut donc utiliser des guillemets simples pour ne pas rentrer en conflit avec ces guillemets.

## Ajouter des fichiers statiques dans un autre dossier

Il est possible d'indiquer à Django des dossiers en dehors d'une application qui contiennent des fichiers statiques.

Vous pouvez créer une liste `STATICFILES_DIRS` dans le fichier `settings.py` avec des chemins de dossier.

Pour garder le caractère dynamique des chemins, il est conseillé de concaténer les chemins à partir de la variable `BASE_DIR` définie au tout début du fichier fichier `settings.py`.

Par exemple :
```python
# settings.py
from pathlib import Path

# Build paths inside the project like this: BASE_DIR / 'subdir'.
BASE_DIR = Path(__file__).resolve().parent.parent

STATICFILES_DIRS = [
    BASE_DIR / "data" / "static",
]
```
