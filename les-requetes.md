# Les requêtes

Pour créer, récupérer, modifier ou supprimer des enregistrements dans notre base de données à partir des modèles, on utilise les requêtes de Django.

### ✅ Créer un enregistrement

La classe d'un modèle représente la table correspondante de notre base de données. Pour créer un nouvel enregistrement dans cette table, on peut créer une instance à partir de cette classe et utiliser la méthode `save` sur cette instance.

Par exemple :
```python
from blog.models import BlogPost

post = BlogPost(title="Un super article de blog")
post.save()
```

Quand vous utilisez la méthode `save`, une commande SQL de type `INSERT` est effectuée pour insérer des données dans la base de données. Cette commande n'est pas exécutée tant que vous n'appelez pas la méthode `save` : créer une instance à partir de la classe ne suffit donc pas à créer l'enregistrement dans la base de donées.

Une autre façon de créer un enregistrement est de passer par la méthode `create` du _manager_ de notre modèle qui par défaut est appelé `objects` :

```python
from blog.models import BlogPost

post = BlogPost.objects.create(title="Un super article")
```

Avec cette façon de faire, nous n'avons pas besoin d'utiliser la méthode `save`. La méthode `create` crée directement l'enregistrement dans la base de données.

### ↩️ Récupérer des données

Pour récupérer un ou plusieurs enregistrements, on utilise le _manager_ (par défaut `objects`) de notre modèle. Ce manager nous retourne un objet de type `QuerySet` qui représente une collection d'objets de la base de données.

Pour récupérer toutes les entrées d'un modèle :
```python
BlogPost.objects.all()
```

On peut récupérer une entrée précise avec la méthode `get` :
```python
post = BlogPost.objects.get(title="Un super article")
```

Attention ! La méthode `get` lève une erreur si plusieurs enregistrements dans la base de données correspondent aux paramètres que vous indiquez. Si dans notre base de données deux articles ont le titre "Un super article", vous obtiendrez une erreur.

Pour récupérer plusieurs éléments de notre base de données, on peut utiliser à la place la méthode `filter` :
```python
posts = BlogPost.objects.filter(title="Un super article")
```

Il est important de noter qu'avec `filter`, même si un seul objet correspond au filtre indiqué, vous obtiendrez tout de même un QuerySet (donc une collection d'objets). Si on souhaite par la suite récupérer par exemple le premier élément trouvé, on peut utiliser la méthode `first` (vous noterez également qu'on peut enchaîner les méthodes) :
```python
post = BlogPost.objects.filter(title="Un super article").first()
```

Il est bien entendu possible de spécifier plusieurs paramètres à la méthode `filter` pour une recherche plus précise :
```python
posts = BlogPost.objects.filter(title="Un super article", author="Thibault")
```

### Options de filtre avancées

Les options de filtres peuvent être très poussées.

On peut par exemple chercher plus précisément dans les champs avec des conditions `WHERE` ou `LIKE` en SQL.

Pour illustrer tout ça, quelques exemples :

Récupérer tous les articles dont l'année de publication est 2020 :
```python
BlogPost.objects.filter(date__year="2020")
```

Récupérer tous les articles qui contiennent le mot Python (sensible à la casse) :
```python
BlogPost.objects.filter(name__contains="Python")
```

Récupérer tous les articles qui contiennent le mot Python (insensible à la casse) :
```python
BlogPost.objects.filter(name__icontains="Python")
```

Récupérer tous les articles qui comment par le mot Python (sensible à la casse) :
```python
BlogPost.objects.filter(name__startswith="Python")
```

### Ordonner et limiter un QuerySet

Pour ordonner un QuerySet selon un champ, on utilise la méthode `order_by` :
```python
BlogPost.objects.order_by('date')
BlogPost.objects.order_by('-date')  # on peut inverser le sens en mettant - devant le nom du champ
```

> Il n'est pas obligatoire de préciser `all`. Par défaut, si vous ne mettez pas `all`, toutes les entrées de la base de données seront retournées. Il est donc redondant d'écrire `BlogPost.objects.all().order_by('date')`.

Pour récupérer seulement une partie d'un QuerySet, on peut utiliser les slices natifs de Python.
Par exemple pour récupérer seulement les trois premiers articles de blog :
```python
BlogPost.objects.all()[:3]
```

## 🟠 Modifier des données

Pour modifier un enregistrement, il suffit de modifier l'attribut de l'instance tout comme on le ferait nativement avec Python. Il ne faut cependant pas oublier d'utiliser la méthode `save` pour sauvegarder les changements effectuées sur l'instance dans la base de données :

```python
post = BlogPost.objects.get(title="JavaScript c'est super !")
post.title = "Python c'est super !"
post.save()
```

## 🔴 Supprimer des données

Pour supprimer des entrées de la base de données, on utilise tout simplement la méthode `delete`. On peut l'utiliser sur une entrée unique ou sur un QuerySet directement :

Supprimer tous les articles de blog :
```python
posts = BlogPost.objects.all()
posts.delete()
```

Supprimer un article en particulier :
```python
post = BlogPost.objects.get(title="Un article plus trop super")
post.delete()
```

