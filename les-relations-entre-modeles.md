# Les relations entre modèles

Avec l'ORM de Django, il est possible de lier plusieurs modèles les uns avec les autres. Par exemple, relier un écrivain à un livre, lier un rédacteur à un article de blog ou encore relier des articles à des catégories. 

## Les relations plusieurs-à-un

Pour créer une relation plusieurs-à-un entre deux modèles, on utilise le champ `ForeignKey`. Cette relation permet d'associer un modèle à plusieurs modèles. Par exemple, un rédacteur sur un blog peut être relié à plusieurs articles : on aura donc une relation plusieurs-à-un (plusieurs articles de blogs reliés à un rédacteur) :

```python
from django.db import models

class Author(models.Model):
    firstname = models.CharField(max_length=100)
    lastname = models.CharField(max_length=100)  


class Book(models.Model):
    title = models.CharField(max_length=255)
    author = models.ForeignKey(Author, on_delete=models.CASCADE) 
```

> À noter que vous pouvez également passer une chaîne de caractères au champ `ForeignKey`, si le modèle correspondant se trouve dans une autre application et que vous ne souhaitez pas importer la classe par exemple.

```python
class Book(models.Model):
    title = models.CharField(max_length=255)
    # Dans le cas d'une classe Author définie dans une application nommée "library".
    author = models.ForeignKey("library.Author", on_delete=models.CASCADE)
```

Le paramètre on_delete permet de spécifier le comportement qui sera utilisé si un modèle lié est supprimé.

Avec `models.CASCADE`, on aura une suppression en cascade. Dans le cas ci-dessus, si on supprime un auteur, tous les livres associés à cet auteur seront supprimés ! Il est donc bien important de comprendre les conséquences d'un tel choice pour le paramètre on_delete.

Si vous ne souhaitez pas supprimer les modèles liés, vous pouvez mettre par exemple `models.SET_NULL` (dans ce cas, il faut également préciser `null=True` pour autoriser le champ à avoir une valeur NULL dans la base de données). Ainsi, si vous supprimez un auteur, les livres ne seront pas supprimés et le champ auteur sera seulement 'vidé'.

## Les relations plusieurs-à-plusieurs

Pour créer une relation plusieurs-à-plusieurs entre deux modèles, on utilise le champ `ManyToManyField`.

Cette relation permet d'associer plusieurs modèles à plusieurs autres modèles. Par exemple, un article de blog peut être associé à plusieurs catégories (Python, Web, Tuto...) et une catégorie peut être associée à plusieurs articles.

```python
from django.db import models

class Category(models.Model):
    name = models.CharField(max_length=36)
    slug = models.SlugField()  


class Book(models.Model):
    title = models.CharField(max_length=255)
    category = models.ManyToManyField(Category)
```

## Les requêtes sur des objets liés

Pour récupérer un modèle lié avec une relation plusieurs-à-un, on peut tout simplement récupérer l'attribut correspondant du modèle.

Par exemple, pour récupérer l'auteur d'un article de blog :
```python
post = BlogPost.objects.get(title="Un super article")
post.author  # On récupère l'auteur de l'article
```

On peut également effectuer une relation inverse, c'est à dire partir de l'auteur pour récupérer tous les articles de blog qui lui sont associés.

Pour ce faire, on part de l'auteur et on utilise le nom du modèle suivie de `_set` :
```python
author = Author.objects.get(firstname="Thibault")
author.blogpost_set.all()  # On récupère tous les articles de blog liés à l'auteur.
``` 

> À noter qu'on peut cibler le champ d'un modèle lié en utilisant le double underscore dans une requête.

Par exemple pour récupérer tous les articles dont le champ `firstname` de l'auteur est égal à `Thibault` on peut faire :
```python
BlogPost.objects.filter(author__firstname="Thibault")
```

Plus vous ferez de requêtes plus vous découvrirez la puissance du langage de requêtes de Django.

On pourrait par exemple rajouter `__startswith` pour récupérer tous les articles de blog donc le prénom de l'auteur commence par la lettre `T` :
```python
BlogPost.objects.filter(author__firstname__startswith="T")
```