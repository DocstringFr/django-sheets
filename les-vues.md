# Les vues

## L'objet `HttpResponse`

L'objet `HttpResponse` est à la base de ce que l'on retourne dans une vue. Quand on retourne un fichier de template HTML avec la fonction `render` par exemple, on retourne tout de même (sans s'en rendre compte) un objet `HttpResponse`.

Vous n'êtes pas convaincu ? Allons analyser un peu la fonction `render` :
```python
# django/shortcuts.py

def render(request, template_name, context=None, content_type=None, status=None, using=None):
    """
    Return a HttpResponse whose content is filled with the result of calling
    django.template.loader.render_to_string() with the passed arguments.
    """
    content = loader.render_to_string(template_name, context, request, using=using)
    return HttpResponse(content, content_type, status)
```

La fonction `render` se contente bien de simplement récupérer le fichier HTML passé dans le paramètre `template_name` pour l'insérer dans un objet `HttpResponse`.

On peut également créer un objet `HttpResponse` et modifier certains de ses attributs comme le code HTTP :
```python
from django.http import HttpResponse

def home(request):
    response = HttpResponse("Hello World")
    response.status_code = 404
    return response    
```

## L'objet `JsonResponse`

La classe `JsonResponse` hérite de l'objet `HttpResponse`. Là encore il ne s'agit donc de rien de plus qu'un objet `HttpResponse` avec quelques attributs définis au préalable :

```python
class JsonResponse(HttpResponse):
    def __init__(self, data, encoder=DjangoJSONEncoder, safe=True, json_dumps_params=None, **kwargs):
        if safe and not isinstance(data, dict):
            raise TypeError(
                'In order to allow non-dict objects to be serialized set the '
                'safe parameter to False.'
            )
        if json_dumps_params is None:
            json_dumps_params = {}
        kwargs.setdefault('content_type', 'application/json')
        data = json.dumps(data, cls=encoder, **json_dumps_params)
        super().__init__(content=data, **kwargs)
```

L'objet `JsonResponse` est pratique pour retourner directement des données au format JSON.

On pourrait très bien retourner la même chose en créant une instance de `HttpResponse` et en modifiant quelques attributs, mais il n'est pas rare (surtout quand on crée une API) de devoir retourner des données au format JSON. La classe `JsonResponse` permet donc de gagner du temps et de retourner directement le bon type de données.

## Retourner une erreur 404

Pour retourner une page 404, la technique est un peu différente. En effet, il faut lever une erreur avec le mot clé `raise` et l'objet `Http404` :
```python
from django.http import HttpResponse, Http404

def home(request):
    try:
        post = BlogPost.objects.get(title="Un super article")
    except:
        raise Http404("L'article n'existe pas...")
    
    return HttpResponse(post.content)
```

C'est une erreur très courante de penser qu'il faut retourner l'objet `Http404` mais si vous utilisez `return` à la place de `raise` ça ne fonctionnera pas.

# Les fonctions raccourcis

Les fonctions suivantes sont disponibles dans le module `django.shortcuts`. Comme le nom du module l'indique, il s'agit de fonction qui permettent d'effectuer des opérations courantes, comme le chargement d'un fichier de template HTML ou la redirection vers une autre page du site.

## La fonction `render`

La fonction `render` permet de facilement retourner un fichier HTML enrichie de variables que l'on passe à un paramètre appelé `context` :
```python
from django.shortcuts import render

def home(request):
    posts = Blog.objects.all()
    return render(request, "blog/index.html", context={"blog_posts": posts})
```

## La fonction `redirect`

La fonction `redirect` permet (comme son nom l'indique) de rediriger vers une page grâce au nom d'une URL :
```python
from django.shortcuts import redirect

def old_index(request):
    return redirect("new-index")
```

## La fonction `get_object_or_404`

Cette fonction permet de récupérer un objet de notre base de données et de lever une erreur 404 si l'objet en question n'est pas trouvé.

Cela permet de simplifier ce genre de bloc `try` / `except` :
```python
from django.http import HttpResponse, Http404

def home(request):
    try:
        post = BlogPost.objects.get(title="Un super article")
    except:
        raise Http404("L'article n'existe pas...")
    
    return HttpResponse(post.content)
```

À la place, on peut réduire ces 4 lignes en une seule avec `get_object_or_404` :
```python
from django.http import HttpResponse
from django.shortcuts import get_object_or_404

def home(request):
    post = get_object_or_404(BlogPost, title="Un super article")
    
    return HttpResponse(post.content)
```

On passe en premier argument le modèle (ici `BlogPost`) puis un ou plusieurs champs liés au modèle.

Là encore, rien de magique, si on va voir le code source de cette fonction, on retourne le bloc `try` / `except` :
```python
def get_object_or_404(klass, *args, **kwargs):
    [...]
    try:
        return queryset.get(*args, **kwargs)
    except queryset.model.DoesNotExist:
        raise Http404('No %s matches the given query.' % queryset.model._meta.object_name)
```

# Restreindre l'accès à une vue

## Restreindre l'accès aux utilisateurs connectés

Pour restreindre l'accès à une vue aux utilisateurs connectés à un site, on peut utiliser le décorateur `login_required` sur la vue (donc sur la fonction Python) :
```python
from django.contrib.auth.decorators import login_required

@login_required
def index(request):
    return HttpResponse("Accueil du site")
```

## Restreindre l'accès selon une condition précise

Pour restreindre l'accès à une vue avec une condition spécifique, on peut utiliser le décorateur `user_passes_test`. On peut passer à ce décorateur une fonction qui retourne `True` ou `False` et indique ainsi si un utilisateur a le droit d'accéder à la vue correspondante. Ce décorateur reçoit l'objet utilisateur en premier paramètre, ce qui nous permet de faire des vérifications sur l'objet utilisateur (par exemple avec une fonction lambda comme dans l'exemple ci-dessous) :
```python
from django.contrib.auth.decorators import user_passes_test

# On n'autorise la vue qu'aux utilisateurs dont l'age est strictement supérieur à 18 ans
@user_passes_test(lambda user: user.age > 18)
def index(request):
    return HttpResponse("Accueil du site")    
```