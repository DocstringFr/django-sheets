# Les vues fondées sur les classes

Les vues fondées sur les classes sont une autre façon de créer des vues avec le framework Django.

Certains adorent, d'autres détestent. L'intérêt principal des vues fondées sur les classes c'est qu'elles permettent, grâce à l'orienté objet, d'utiliser les concepts d'héritage. Elles simplifient également certaines opérations (notamment les opérations CRUD).

Dans la suite de cette fiche, j'utilise l'acronyme anglais de CBV (Class-Based Views) plutôt que le terme français un peu trop long de 'Vue fondée sur les classes'.

## Notre première CBV

La vue la plus basique est disponible avec la classe `View` du module `django.views` :

```python
# views.py
from django.views import View


class HomeView(View):
    pass
```

Pour créer une CBV, on hérite donc de la classe `View`. Le nom de notre classe n'a, lui, pas d'importance.

On peut ensuite surcharger certaines méthodes avec des noms bien précis. Par exemple, on peut retourner un objet `HttpResponse` dans le cas d'une requête de type `GET` effectuée vers notre vue, avec la méthode `get` :
```python
# views.py
from django.http import HttpResponse
from django.views import View


class HomeView(View):
    def get(self, request):
        return HttpResponse("Accueil du site")
```

Pour relier une URL à cette vue, il faut l'instancier avec la méthode `as_view` dans le fichier `urls.py` :
```python
# urls.py
from django.urls import path

from .views import HomeView

urlpatterns = [
    path('', HomeView.as_view())
]
```

Déjà avec une vue aussi simple, nous pouvons voir l'intérêt de l'orienté objet pour retourner deux vues différentes à partir de la même classe.

On peut par exemple ajouter un attribut à la classe et instancier différemment la classe dans le fichier `urls.py` pour deux vues différentes :
```python
# views.py
from django.http import HttpResponse
from django.views import View


class HomeView(View):
    title = ""
    def get(self, request):
        return HttpResponse(f"Accueil du {self.title}")
```
```python
# urls.py
from django.urls import path

from .views import HomeView

urlpatterns = [
    path('', HomeView.as_view(title="site")),
    path('blog/', HomeView.as_view(title="blog")),
]
```

## Les classes pour les opérations CRUD

### Pour afficher des éléments (R: Retrieve)

Pour afficher des éléments, on peut utiliser deux classes : `ListView` et `DetailView`.

`ListView` permet d'afficher plusieurs éléments d'un modèle. `DetailView` permet d'afficher une vue de détail d'une instance en particulier d'un modèle.

On pourrait ainsi créer une page d'accueil pour un Blog avec une `ListView` :
```python
# views.py
from django.views.generic import ListView
from .models import BlogPost

class BlogIndex(ListView):
    model = BlogPost
```

Les CBV possèdent des attributs par défaut que l'on peut spécifier qui vont nous permettre d'aller automatiquement récupérer certaines informations. L'attribut `model` par exemple permet de spécifier un modèle (ici, `BlogPost`) dont on pourra afficher toutes les instances dans un fichier de gabarit.

Par défaut, le nom du fichier de gabarit attendu par une `ListView` est `modelname_list.html`. Dans notre cas, le fichier HTML attendu par cette vue devrait donc s'appeler `blogpost_list.html`.

Il est possible de modifier le nom du template en modifiant l'attribut `template_name` sur notre classe :
```python
# views.py
from django.views.generic import ListView
from .models import BlogPost

class BlogIndex(ListView):
    model = BlogPost
    template_name = "blog_index.html"
```

À l'intérieur du fichier de gabarits, on peut accéder à toutes les instances de notre modèle avec la variable `object_list`. On peut là encore modifier le nom de cette variable si on le souhaite avec l'attribut `context_object_name` :
```python
# views.py
from django.views.generic import ListView
from .models import BlogPost

class BlogIndex(ListView):
    model = BlogPost
    template_name = "blog_index.html"
    context_object_name = "articles"
```

On peut ainsi afficher tous les articles du blog dans un fichier de gabarit nommé `blog_index.html` avec le code suivant :
```html
<!doctype html>
<html lang="fr">
<head>
     <title>Accueil du blog</title>
</head>
<body>
    {% for article in articles %}
        <h1>{{ article.title }}</h1>
    {% endblog %}  
</body>
</html>
```

Et dans le fichier `urls.py` nous n'avons qu'à instancier la classe avec la méthode `as_view` :
```python
# urls.py
from django.urls import path

from .views import BlogIndex

urlpatterns = [
    path('blog/', BlogIndex.as_view()),
]
```

### Les autres classes

Pour les autres opérations (Create, Update, Delete), on peut utiliser les classes `CreateView`, `UpdateView` et `DeleteView`. Toutes ces classes fonctionnent sur le même principe car elles héritent plus ou moins des mêmes « mixins » (des classes qui permettent de définir des fonctionnalités de base, dont les CBV héritent).

Pour explorer plus en détail toutes les possibilités offertes par les CBV (elles sont nombreuses !), je vous réfère à l'excellent site [http://ccbv.co.uk/](http://ccbv.co.uk/).