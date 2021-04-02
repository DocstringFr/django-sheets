# Les URLs

Django ne dispose que d'un seul point d'entrées pour les chemins d'urls, défini par la variable `ROOT_URLCONF` dans le fichier `settings.py`.

Par défaut, c'est le fichier `urls.py` de votre projet qui est utilisé. Django va chercher dans ce fichier une variable nommée `urlpatterns` qui défini une liste de chemins définis avec la fonction `path` :

```python
from django.urls import path

from . import views

urlpatterns = [
    path('articles/2003/', views.special_case_2003),
    path('articles/<int:year>/', views.year_archive),
    path('articles/<int:year>/<int:month>/', views.month_archive),
    path('articles/<int:year>/<int:month>/<slug:slug>/', views.article_detail),
]
``` 
☝️ Exemple tiré de la [documentation officielle de Django](https://docs.djangoproject.com/fr/3.1/topics/http/urls/#example).

## Capturer une valeur dans l'URL

Pour capturer une valeur contenue dans l'URL, on utilise les chevrons (`< >`). On peut directement utiliser un convertisseur de type pour récupérer la valeur dans un certain type.
Par défaut, les valeurs sont retournées sous forme de chaîne de caractères.

Quand vous effectuez une requête vers une adresse qui correspond à un chemin défini dans le fichier `urls.py`, Django appelle la vue correspondante en passant les valeurs capturées dans l'URL à la fonction (la vue) correspondante.

Une requête effectuée vers `articles/2021/04/` redirigerait ainsi vers la fonction `views.month_archive(request, year=2021, month=4)`. Notez que la requête est toujours passée en premier argument.

#### Convertisseur de chemins
Tiré de la [documentation officielle](https://docs.djangoproject.com/fr/3.1/topics/http/urls/#path-converters).

Les convertisseurs de chemin suivants sont disponibles par défaut :

`str` - correspond à n’importe quelle chaîne non vide, à l’exception du séparateur de chemin, `'/'`. C’est ce qui est utilisé par défaut si aucun convertisseur n’est indiqué dans l’expression.  

`int` - correspond à zéro ou un autre nombre entier positif. Renvoie le type `int`.  

`slug` - correspond à toute chaîne composée de lettres ou chiffres ASCII, du trait d’union ou du caractère soulignement. Par exemple, construire-votre-1er-site-django.  

`uuid` - correspond à un identifiant UUID. Pour empêcher plusieurs URL de correspondre à une même page, les tirets doivent être inclus et les lettres doivent être en minuscules. Par exemple, 075194d3-6885-417e-a8a8-6c931e272f00. Renvoie une instance UUID.

`path` - correspond à n’importe quelle chaîne non vide, y compris le séparateur de chemin, `'/'`. Cela permet de correspondre à un chemin d’URL complet au lieu d’un segment de chemin d’URL comme avec str.

