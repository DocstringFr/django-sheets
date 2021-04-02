# 🔗 Créer un chemin d'URL

Django utilise la variable `ROOT_URLCONF` définie dans le fichier `settings.py` pour savoir quel fichier utiliser pour résoudre les chemins d'URLs.

Par défaut, cette variable pointe vers le fichier `urls.py` qui se trouve dans le dossier principal de votre application Django.

À l'intérieur de ce fichier, on peut utiliser la fonction `path` pour associer un chemin d'URL à une vue :
```python
from django.urls import path
from .views import index

urlpatterns = [
    path("index/", index)
]
```

# 👀 Créer une vue pour l'URL

Une vue Django est tout simplement une fonction Python qui prend en paramètre la requête envoyée par le navigateur et retourne un objet de type `HttpResponse` :
```python
from django.http import HttpResponse

def index(request):
    return HttpResponse("Page d'accueil du site")
```

# 🔖 Retourner un template

Un fichier de template est tout simplement un fichier HTML que l'on retourne dans une vue et dans lequel on peut utiliser le langage de gabarits de Django pour par exemple insérer des données.

Pour retourner un template dans une vue, on utilise la fonction render :
```python
from django.shortcuts import render

def index(request):
    return render(request, "index.html")
```

# 🔢 Insérer des données dans un template

Pour passer des données de la vue au template, on utilise ce qu'on appelle le "contexte".

On peut passer un dictionnaire à la fonction `render` dans le paramètre context :
```python
from django.shortcuts import render

def index(request):
    return render(request, "index.html", context={"valeur": 5})
```

On peut ensuite utiliser le nom des clés du dictionnaire pour afficher les valeurs associées à l'intérieur du fichier de template HTML avec les doubles accolades :
```html
<h1>La valeur associée à la clé "valeur" est {{ valeur }}</h1>
```