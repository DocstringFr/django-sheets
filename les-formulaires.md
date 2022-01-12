# Les formulaires

## Créer un formulaire

Pour créer un formulaire, on crée une classe qui hérite de la classe `Form` du module `forms`. On crée ces classes généralement dans un fichier `forms.py` mais ce n'est pas une obligation.

```python
from django import forms

class SignupForm(forms.Form):
    pass
```

On peut ensuite créer des champs à l'intérieur de ce formulaire, tout comme on le ferait avec un modèle.

> Attention, bien que la technique ressemble à celle des modèles (on crée des champs), les champs ne sont pas les mêmes et proviennent du module `forms`. Vous pouvez retrouver une liste exhaustive de tous les champs disponibles dans la [documentation officielle](https://docs.djangoproject.com/fr/3.1/ref/forms/fields/#built-in-field-classes).

Exemple d'un formulaire d'inscription :
```python
from django import forms

class SignupForm(forms.Form):
    pseudo = forms.CharField(max_length=12, required=False)
    email = forms.EmailField()
    password = forms.CharField(min_length=6)
    cgu_accept = forms.BooleanField(initial=True)
```

On peut ensuite créer une instance de cette classe pour créer un formulaire. Si vous faites un print de cette instance, vous verrez le code HTML généré par le formulaire :
```python
from blog.forms import SignupForm

form = SignupForm()
```

## Afficher un formulaire

Pour afficher un formulaire à l'intérieur d'un fichier de gabarits, il suffit de créer une instance de ce formulaire dans la vue, le passer dans le contexte et l'insérer comme une variable dans le fichier HTML :
```python
# views.py
from blog.forms import SignupForm

def signup(request):
    form = SignupForm()
    return render(request, "signup.html", context={"form": form}) 
```

Dans le fichier `signup.html` :
```html
<!doctype html>
<html lang="fr">
<head>
     <title>Inscription</title>
</head>
<body>
    <form method="POST">
        {% csrf_token %}
        {{ form }}
        <input type="submit" value="S'inscrire">
    </form>
</body>
</html>
```

Vous remarquerez que le formulaire n'inclue pas de base la balise `<form>` ni le bouton pour soumettre le formulaire. Il faut donc les ajouter nous-même et entourer la variable `form` passée en contexte.

Vous noterez également l'ajout d'une balise `{% csrf_token %}`. Cette balise ajoute un champ caché qui contient un jeton CSRF, nécessaire pour des raisons de sécurité. Ce jeton est utilisé automatiquement par Django lors de la soumission d'un formulaire pour s'assurer de la bonne provenance du formulaire.

## Récupérer les données dans la vue

Pour récupérer les données soumises par un utilisateur dans une vue, on utilise le dictionnaire `request.POST`. On peut vérifier le type de requête effectué dans la vue avec l'attribut `method` disponible sur l'objet `request` :
```python
from blog.forms import SignupForm

def signup(request):
    if request.method == "POST":
        form = SignupForm(request.POST)
    else:
        form = SignupForm()

    return render(request, 'signup.html', context={"form": form})
```

Une fois les données récupérées, on peut valider le formulaire avec la méthode `is_valid` et récupérer les données 'nettoyées' dans l'attribut `cleaned_data` :
```python
from blog.forms import SignupForm

def signup(request):
    if request.method == "POST":
        form = SignupForm(request.POST)
        if form.is_valid():
            print(form.cleaned_data)
    else:
        form = SignupForm()

    return render(request, 'signup.html', context={"form": form})
```

> Attention, l'attribut `cleaned_data` n'est créé qu'après avoir appelé la méthode `is_valid`. Vous êtes donc obligé de valider votre formulaire pour avoir accès aux données 'nettoyées'.

## Les formulaires liés

Pour créer un formulaire lié (à un modèle) on utilise la classe `ModelForm` du module `forms`. Grâce à une classe `Meta` à l'intérieur de notre formulaire, on peut ensuite spécifier quel modèle on souhaite relier au formulaire et les champs du modèle à afficher :
```python
from django import forms
from blog.models import BlogPost

class SignupForm(forms.ModelForm):
    class Meta:
        model = BlogPost
        fields = ("title", "slug", "published", "date", )
```

On peut choisir à la place de l'attribut `fields` d'utiliser l'attribut `exclude` pour inclure par défaut tous les champs sauf ceux indiqués dans cet attribut :
```python
from django import forms
from blog.models import BlogPost

class SignupForm(forms.ModelForm):
    class Meta:
        model = BlogPost
        exclude = ("slug", )  # On inclue tous les champs sauf le slug.
```

Pour inclure tous les champs, on peut également utiliser la chaîne de caractères `"__all__"` :
```python
from django import forms
from blog.models import BlogPost

class SignupForm(forms.ModelForm):
    class Meta:
        model = BlogPost
        fields = "__all__"
```

## Sauvegarder un formulaire

Avec un formulaire lié, il est possible de sauvegarder le formulaire pour créer une instance du modèle lié dans la base de données. Pour ce faire on utilise la méthode `save` sur le formulaire après avoir validé les données soumises par l'utilisateur :
```python
from blog.forms import SignupForm

def signup(request):
    if request.method == "POST":
        form = SignupForm(request.POST)
        if form.is_valid():
            form.save()  # On sauvegarde le formulaire
    else:
        form = SignupForm()

    return render(request, 'signup.html', context={"form": form})
```

Si on souhaite uniquement récupérer le modèle à partir du formulaire sans sauvegarder celui-ci dans la base de données (pour par exemple modifier un champ avant de le sauvegarder) on peut utiliser le paramètre `commit` et sauvegarder directement l'instance avec la méthode `save` (sur l'instance) :
```python
from blog.forms import SignupForm

def signup(request):
    if request.method == "POST":
        form = SignupForm(request.POST)
        if form.is_valid():
            model = form.save(commit=False)
            model.cgu_accept = True
            model.save()
    else:
        form = SignupForm()

    return render(request, 'signup.html', context={"form": form})
```

## Renseigner des valeurs par défaut

Pour initialiser un formulaire avec des valeurs par défaut, on peut utiliser le paramètre `initial` :
```python
from blog.forms import SignupForm

def signup(request):
    form = SignupForm(initial={"email": "hello@docstring.fr", "cgu_accept": True})

    return render(request, 'signup.html', context={"form": form})
```

On passe un dictionnaire à ce paramètre avec en clé le nom du champ et en valeur la valeur initiale souhaitée pour ce champ.
