# Python 
## Commentaire 

```python
# commentaire one line

"""
  commentaire multi ligne 
"""
```


## Log 

```python
print("Quelque chose")

# calcul puis affichage
print(40 + 2)

# affiche la valeur d'une variable
print(maVariable)
```

---

# Variables

Les variables permettent de stocker de la donnée et permet de venir les utiliser dans un programme. La valeur d'une variable peut évoluer pendant l'exécution du script.

On peut utiliser n'importe quel nom pour une variable, mais celle ci doit être descriptive, et utiliser `_`si elle est composer de plusieurs nom.

```python
# créer une variable
my_height = 100
my_name = "Lane"

# réassignation 
my_height = 175

# utilisation
print(my_name)
```

## Déclaration multiple 

```python 
sword_name, sword_damage, sword_length = "Excalibur", 10, 200
```

## Type de variable 

### Strings 

Permet de stocker des chaîne de caractères. On peut utiliser un `'` ou un `"`

```python
name_with_single_quotes = 'boot.dev' # not so good
name_with_double_quotes = "boot.dev" # so good
```

#### F-Strings

Permet de déclarer une string qui contient des variables.
```python
num_bananas = 10
bananas = f"You have {num_bananas} bananas"
print(bananas)
# You have 10 bananas
```

On commence par ajouter un `f`au début de la déclaration de la chaîne de caractères.
On utiliser ensuite des `{ }` autour de la variable à afficher. On place ensuite le nom de la variable. Elle sera ensuite remplacer par la valeur de la variable lors de l'affichage de la chaîne de caractères

#### Concaténation 

En utilisant l'opérateur `+` on peut fusionner plusieurs string. 
```python
first_name = "Lane "
last_name = "Wagner"
full_name = first_name + last_name
print(full_name)
# prints "Lane Wagner"
```

### Number 

On peut y stocker un `int` ou bien un `float` 

```python
x = 5 # positive integer
y = -5 # negative integer

x = 5.2
y = -5.2
```

### Boolean 
Peut contenir un `True`ou un `False`.

```python 
is_tall = True
is_short = False
```

### None

Lorsqu'une variable ne contient pas de valeur, on utilise `None`. C'est une valeur spécial qui représente une absence de valeur. 

On peut l'utiliser pour décalrer une variable qui sera utilisé par la suite.

```python
my_mental_acuity = None
```

La valeur de la variable est `None` tant que l'on lui affecte pas de valeur.

```python 
my_none = None # this is a None-type
my_none = "None" # this is a string with the value "None"
```

## Retourner le type d'une variable 

```python 
player_health = 100
player_has_magic = True


print("player_health is a/an", type(player_health).__name__)
print("player_has_magic is a/an", type(player_has_magic).__name__)
```



---

## Mathematique 

### Opérateur 

- `+`: addition 
- `-`: soustraction 
- `*` : multiplication 
- `/`: division 

On peut également utiliser les parenthèses pour définir un ordre de priorité.

Il est également possible d'utiliser des valeurs négative

```python
my_negative_num = -420
```

