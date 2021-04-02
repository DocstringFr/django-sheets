# Les modèles

Les modèles Django permettent de faire le lien avec la base de données de notre application. On utilise pour ça un **ORM** (Object Relational Mapping) qui va nous permettre, avec du code Python, de représenter notre base de données et de créer, mettre à jour et supprimer des entrées dans notre base de données.

Pour créer un modèle, on utilise la programmation orientée objet en créant une classe qui hérite de la classe `Model` du module `django.db.models`.

## Les champs de modèle

Pour représenter les données dans notre base de données, on peut utiliser différents types de champs, qui ressemblent beaucoup aux différents types natifs disponibles avec Python.

Parmi ces champs, on retiendra par exemple les champs :

`CharField` pour stocker des chaînes de caractères d'une longueur maximale prédéfinie (définie par le paramètre `max_length`).  
`TextField` pour stocker des chaînes de caractères sans limite de caractères.  
`SlugField` pour stocker des chaînes de caractères sous forme de slug.  
`IntegerField` et `FloatField` pour stocker des nombres entiers et des nombres décimaux.  
`BooleanField` pour stocker une valeur booléenne (`True` ou `False`).  
`EmailField` pour stocker des adresses email.  
`DateField` et `DateTimeField` pour stocker des dates.  
`URLField` pour stocker des URLs.  

Vous pouvez retrouver une liste exhaustive de tous les champs disponibles sur [cette page](https://docs.djangoproject.com/fr/3.1/ref/models/fields/#field-types) de la documentation officielle.

### **Créer un premier modèle**

Pour créer un modèle, c'est très simple, on crée une classe qui hérite de la classe `Model` et on spécifie les champs que l'on souhaite associer au modèle en tant qu'attributs de classe :

```python
from django.db import models

class BlogPost(models.Model):
    title = models.CharField(max_length=100)
    slug = models.SlugField()
    published = models.BooleanField(default=False)
```

## Créer les migrations

Afin de créer les fichiers de migrations, des fichiers Python qui vont nous permettre de créer les schémas (tables) dans notre base de donnée, il faut utiliser la commande `makemigrations` :

```
python manage.py makemigrations
```

Cette commande ne fait que crée les fichiers Python à l'intérieur des dossiers `migrations` de nos applications.

Par exemple, voici à quoi ressemble un fichier pour une migration initiale dans le cas d'un modèle pour l'article d'un blog :

```python
class Migration(migrations.Migration):

    initial = True

    dependencies = [
        migrations.swappable_dependency(settings.AUTH_USER_MODEL),
    ]

    operations = [
        migrations.CreateModel(
            name='BlogPost',
            fields=[
                ('id', models.AutoField(auto_created=True, primary_key=True, serialize=False, verbose_name='ID')),
                ('title', models.CharField(max_length=255, unique=True, verbose_name='Titre')),
                ('slug', models.SlugField(blank=True, max_length=255, unique=True)),
                ('last_updated', models.DateTimeField(auto_now=True)),
                ('created_on', models.DateField(blank=True, null=True)),
                ('published', models.BooleanField(default=False, verbose_name='Publié')),
                ('content', models.TextField(blank=True, verbose_name='Contenu')),
                ('author', models.ForeignKey(blank=True, null=True, on_delete=django.db.models.deletion.SET_NULL, to=settings.AUTH_USER_MODEL)),
            ],
            options={
                'verbose_name': 'Article',
                'ordering': ['-created_on'],
            },
        ),
    ]
```

Vous n'avez donc pas besoin de créer vous-même ce code, il est généré automatiquement par Django avec la commande `makemigrations`.

## Appliquer les migrations

Une fois les migrations créées, il faut les appliquer pour créer les tables dans notre base de donnée.

Pour ça on utilise la commande `migrate` :
```
python manage.py migrate
```