# Créer une application

Pour créer une application dans un projet Django, c'est très simple, on utilise le fichier `manage.py` et la commande `startapp` suivie du nom de l'application :

```
python manage.py startapp application
```

Cette commande va automatiquement créer un dossier à l'intérieur du projet avec le nom de votre application.
Ce dossier contiendra par défaut les dossiers et fichiers suivants :

- application -> dossier principal de l'application.
  - migrations  -> dossier qui contient les fichiers de migrations des modèles.
  - `__init__.py`
  - `admin.py` -> fichier pour enregistrer les modèles dans l'interface d'administration.
  - `apps.py` -> fichier pour définir des configurations de l'application.
  - `models.py` -> fichier dans lequel on crée les modèles de l'application.
  - `test.py` -> fichier pour créer les tests unitaires de l'application.
  - `views.py` -> fichier pour crées les vues de l'application.
  
C'est une structure de base que vous pouvez bien entendu modifier. On voudra par exemple probablement créer par la suite un fichier `urls.py` pour les chemins d'URLs propre à l'application. Par ailleurs, si vous ne souhaitez pas créer de tests unitaires pour l'application, vous pouvez supprimer le fichier `test.py` sans que cela ne pose de problèmes.

### ☑️ Ajouter l'application au projet Django

Pour que Django reconaisse votre application, vous devez l'ajouter dans la liste `INSTALLED_APPS` contenue dans le fichier `settings.py` de votre projet. Pour ajouter l'application, il suffit d'indiquer le nom de l'application sous forme de chaîne de caractères :

```python
# settings.py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'application'  # Vous indiquez ici le nom de l'application créée.
]
```