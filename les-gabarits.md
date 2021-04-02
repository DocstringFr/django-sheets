# Les gabarits

## Les variables

Pour insérer des données dans un fichier de gabarit HTML, on utilise les doubles accolades `{{ var_name }}`. Ces variables doivent au préalable être passées au paramètre `context` de la fonction `render` dans la vue :

```python
# views.py
from django.shortcuts import render
from datetime import datetime

def index(request):
    today = datetime.today()    

    return render(request, 'index.html', context={"date": today})
```

On peut ensuite utiliser les variables passées au contexte à l'intérieur de notre fichier HTML :
```html
<!-- index.html -->
<!doctype html>
<html lang="fr">
<head>
     <title>Index</title>
</head>
<body>
    <h1>Aujourd'hui nous sommes le {{ date }}</h1>
</body>
</html>
```

> Attention ! Le nom des variables (contrairement à Python) ne peut pas commencer par un tiret du bas. Si vous nommez une variable `_date` par exemple, vous aurez une erreur dans le fichier de template.

On peut, à l'intérieur des doubles accolades, afficher l'attribut d'un objet ou utiliser des méthodes ou propriétés de nos objets. Pour les méthodes, vous ne devez pas utiliser les parenthèses pour appeler la méthode.

Quelques exemples :
```python
from django.shortcuts import render

class User:
    def __init__(self, firstname, lastname):
        self.firstname = firstname
        self.lastname = lastname

    def display_name(self):
        return f"{self.firstname} {self.lastname}"

def index(request):
    user = User(firstname="Patrick", lastname="LeBeau")

    return render(request, 'index.html', context={"user": user})
```

Dans le fichier HTML :
```html
<!-- index.html -->
<!doctype html>
<html lang="fr">
<head>
     <title>Index</title>
</head>
<body>
    <!-- On accède à l'attribut d'instance firstname -->
    <h1>Bienvenue {{ user.firstname }} !</h1>
    <!-- On appelle la méthode display_name, sans utiliser les parenthèses -->
    <h3>Votre nom complet est {{ user.display_name }}</h3>
</body>
</html>
```

## Les structures conditionnelles

Pour créer une structure conditionnelle dans un fichier de gabarit, on utilise la balise suivante :
```djangotemplate
{% if condition %}
    <h1>La condition est vraie !</h1>
{% endif %}
```

> Notez l'utilisation d'une balise de fermeture `{% endif %}` qui permet d'indiquer la fin de la condition. Cette balise de fermeture est nécessaire car contrairement à Python, l'indentation dans un fichier HTML n'a aucune valeur. Là où Python utilise les niveaux d'indentations pour savoir les lignes de codes qui appartiennent à une structure conditionnelle, nous devons à l'intérieur d'un fichier de gabarit, utiliser cette balise de fermeture.

> Notez également l'absence des deux points à la fin de la condition.

On peut créer une structure conditionnelle plus avancées avec les balises `elif` et `else` :
```djangotemplate
{% if user.subscription == "Business" %}
    <h1>Bienvenue dans le club VIP !</h1>
{% elif user.subscription == "Premium" %}
    <h1>Bienvenue dans le club Premium.</h1>
{% else %}
    <h1>Vous ne disposez d'aucun abonnement au site.</h1>
{% endif %}
```

Tout le reste est similaire à Python. On peut ainsi utiliser les opérateurs `not`, `and` et `or` à l'intérieur des conditions. Vous trouverez plus d'informations [dans la documentation](https://docs.djangoproject.com/fr/3.1/ref/templates/builtins/#complex-expressions).

## Les boucles

Dans le langage de gabarits de Django, seule la boucle `for` est disponible (la boucle `while` n'existe pas).

Comme pour la balise `if`, la boucle `for` nécessite une balise de fermeture à cause de l'absence d'indentation dans les fichiers de gabarits :
```djangotemplate
{% for post in blog_posts %}
    <h1>{{ post.title }}</h1>
{% endfor %}
```

La boucle `for` dispose également de plusieurs variables très pratique que vous pouvez utiliser à l'intérieur des balises :
La boucle for définit un certain nombre de variables disponibles à l’intérieur de la boucle :

`forloop.counter` :	L’itération actuelle de la boucle (index à partir de 1)  
`forloop.counter0` : L’itération actuelle de la boucle (index à partir de 0)  
`forloop.revcounter` : Le nombre d’itérations à partir de la fin de la boucle (index à partir de 1)  
`forloop.revcounter0` : Le nombre d’itérations à partir de la fin de la boucle (index à partir de 0)  
`forloop.first` : True si la boucle est dans sa première itération  
`forloop.last` : True si la boucle est dans sa dernière itération  
`forloop.parentloop` : Pour les boucles imbriquées, il s’agit de la boucle de niveau supérieur  

On peut également utiliser la boucle `for ... empty` pour afficher un élément dans le cas d'un itérable vide :
```djangotemplate
{% for post in blog_posts %}
    <h1>{{ post.title }}</h1>
{% empty %}
    <h1>Le blog ne contient aucun article...</h1>
{% endfor %}
```

## Les URL

La balise `url` permet de référer à un chemin absolu d'un site. Cette balise utilise le paramètre `name` des chemins d'URL définis dans le fichier `urls.py` et est très utile pour utiliser des chemins dynamiques. 

Considérant un fichier `urls.py` qui contient les chemins d'URL suivants :
```python
# urls.py
from django.urls import path

urlpatterns = [
    path('home/', views.index, name='index'),
    path('blog/<str:slug>/', views.blog_post, name='post')
]
```

Nous pouvons accéder au chemin absolu de ces URL dans un fichier de gabarit de la façon suivante :

#### Pour un chemin d'URL sans paramètres d'URL (la vue `"index"`)
```djangotemplate
<a href="{% url 'index' %}">Accueil</a> 
```

Sera remplacé par :
```html
<a href="home/">Accueil</a>
```

#### Pour un chemin d'URL avec paramètres (la vue `"post"`)
```djangotemplate
<a href="{% url 'post' slug='un-super-article' %}">Un super article</a>
```

Sera remplacé par :

```html
<a href="blog/un-super-article/">Un super article</a>
```

L'intérêt d'utiliser la balise `url` est que nous pouvons à tout moment changer le chemin des URL (en remplaçant `'home/'` par `'index/'` par exemple) et tous nos chemins d'URL à l'intérieur des fichiers de gabarits seront toujours valides (car on utilise le paramètre `name` du chemin, qui lui ne change pas).

## Les filtres

Il existe des dizaines (voire centaines) de filtres disponibles. Vous trouverez la liste exhaustive de tous les filtres disponibles dans [la documentation officielle](https://docs.djangoproject.com/fr/3.1/ref/templates/builtins/#built-in-filter-reference).

Ces filtres s'utilisent avec l'opérateur « pipe » (`|`) après une variable :
```html
<h1>{{ var|filtre }}</h1>
```

On peut par exemple utiliser le filtre `capfirst` pour mettre en majuscule la première lettre d'un mot. Pratique pour uniformiser un prénom rentré par un utilisateur par exemple :
```html
<h1>{{ name|capfirst }}</h1>
```
☝️ Le filtre ci-dessus transformera `patrick` en `Patrick`.

> Il est préférable de ne pas trop abuser des filtres. En règle général, il vaut mieux faire ces opérations directement avec Python, sur vos modèles par exemple. Si on souhaite normaliser un prénom, on peut par exemple ajouter une propriété à notre modèle et utiliser la méthode `capitalize` disponible avec Python.

## L'héritate de gabarits

Pour éviter de répéter du code HTML, on peut utiliser un système d'héritage de gabarits grâce aux balises `extends` et `block`.

On peut ainsi créer un fichier HTML que l'on viendra étendre avec un autre fichier HTML.

L'exemple le plus courant est de créer un fichier HTML de base qui contient toutes les balises HTML pour l'entête et le footer. On vient par la suite étendre ce fichier de base avec le contenu d'autres pages.

On créer donc un fichier `base.html` (le nom du fichier ainsi que le nom des blocs n'ont pas d'importance) :
```html
<!-- base.html -->
<!doctype html>
<html lang="fr">
<head>
     {% block title %}<title>Page de base</title>{% endblock %}
</head>
<body>
    {% block content %}
    {% endblock %}
</body>
</html>
```

On peut ensuite étendre ce fichier grâce à la balise `extends` et modifier le contenu des blocs avec la balise `block` :
```html
<!-- index.html -->
{% extends 'base.html' %}

{% block title %}<title>Accueil du site</title>{% endblock %}

{% block content %}
    <h1>Le contenu du site</h1>
{% endblock %}
```

Le contenu que l'on ajoute dans les balises `block` viendra remplacer celui de la page qu'on étends. On remplace ainsi la balise `<title>Page de base</title>` du fichier `base.html` par `<title>Accueil du site</title>` dans le fichier `index.html`.

Dans un navigateur, la page `index.html` affiché contiendra ainsi le code HTML suivant :
```html
<!-- index.html -->
<!doctype html>
<html lang="fr">
<head>
     <title>Accueil du site</title>
</head>
<body>
    <h1>Le contenu du site</h1>
</body>
</html>
```