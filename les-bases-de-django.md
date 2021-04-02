# 💿 Installer Django

Pour installer Django c'est très simple, il suffit d'utiliser l'utilitaire `pip` :

```
pip install django
```

# 📖 Créer un projet Django

Pour créer un projet Django, on utilise la commande `django-admin` suivie de la commande `startproject` et du nom du projet.

Par exemple :  
```
django-admin startproject todoapp
``` 

Cette commande va créer plusieurs dossiers et fichiers :

- todoapp -> le dossier principal du projet
  - `manage.py`
  - todoapp
    - `__init__.py`
    - `asgi.py`
    - `settings.py`
    - `urls.py`
    - `wsgi.py`

# ⚙️ Lancer le serveur de développement

Pour lancer le serveur de développement en local, il suffit d'utiliser la commande `runserver` à partir du fichier `manage.py` :

```
python manage.py runserver
``` 

Vous devez bien sûr au préalable vous assurer d'avoir activé votre environnement virtuel et de vous trouver dans le dossier qui contient le fichier `manage.py`.

Une fois le serveur de développement lancé, vous pouvez voir la page d'accueil par défaut de votre projet Django à l'adresse `127.0.0.1:8000` dans un navigateur web.