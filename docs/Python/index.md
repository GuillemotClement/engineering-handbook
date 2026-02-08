# Python 

Les programmes ecrit en Python sont execute par `py.exe` qui est le Python Interpreter. Un programme special capable d'executer des programmes Python.

Python contient des concepts de base :
- Chaque commande est ecrit sur une nouvelle ligne 
- L'indentation est importante
- Les commandes sont regroupees en bloc a l'aide de l'indentation. Si plusieurs commandes ont le meme niveau d'indentation, elles sont consideres comme appartenant au meme bloc

## ENVIRONNEMENT DE DEVELOPPEMENT 

### venv 

`venv` est l'environnement virtuel. C'est un module Python qui permet de créer différents environnement virtuel légers et isolés pour des proejts Python.
C'est un outil qui permet de gérer les dépendances d'un projet, en les isolant des lib systèmes.

Chaque environnement virtuel a ses propres exécutables Python, et un emplacement pour installer des libs. Ce qui garantit l'indépendance des projets les uns des autres.

C'est utile lorsque différents projets nécessitent différentes version d'une même bibliothèques, ou quand il faut éviter les conflits entre les lib système et les lib pour le projet.

---

## AFFICHAGE 

### print()
Cette fonction permet d'afficher quelque chose 
```python
print("Quelque chose")
# Quelque chose

print("un", "deux", "trois")
# un deux trois
```

---

## VARIABLE
En Python, les variables sont des noms attribues a des objets, utilises pour stocker des donnees, les modifier, et y acceder dans le programme.

Python est un langage a typage dynamique, cela signifie que le type de la variable est defini au moment ou elle recoit une valeur, et ce type peut changer au cours de l'execution du programme.

En Python, les variables ne necessitent pas de declaration pour reserver de la memoire. L'affectation de valeurs aux variables reserve automatiquement de la memoire et determine leur type.

Les nom des variables doit etre descriptifs et respoecter les regles de Python pour les identifiants, par exemple ne pas commencer par des chiffres et ne pas contenir de caracteres speciaux sauf underscore.

Les variables sont des references a des objets. Cela signife qu'une variable se voit attribuer une valeur, une référence à l'objet contenant cette valeur est en réalité créée. Si les variables `y` et `x` pointent vers le même objet, toute modification faite via `x` sera également refltée dans `y` car les deux variables font référence au même objet en mémoire.

Python gère automatiquement la mémoire des variables à l'aide du garbage collector qui surveille et libère la mémoire inutilisée.

Certain types de données en Python comme les chaînes et les tuples sont immuables. Cela signifie que leur contenu ne peut pas être modifé après leur création. Toute tentative de modification de ces données entraînera la création d'un nouvel objet.

### Création d'une variable 

En Python, on peut attribuer n'importe quel valeur a n'importe quelle variable. 

Pour déclarer une nouvelle variable :
```python
nom = valeur 

# affectation multiple 
x, y, z = 1, 2, 3
```

### Type de données 

Python supporte plusieurs types de données de base utilisés pour stocker et manipuler des données dans les programmes. 

| Type de données | Description                           | Exemple d'utilisation     |
| --------------- | ------------------------------------- | ------------------------- |
| `int`           | Nombres entiers                       | x = 10                    |
| `float`         | Nombres à virgule flottante           | y = 3.14                  |
| `complex`       | Nombres complexes                     | z = 1 + 2j                |
| `str`           | Chaînes                               | s = "Hello, world!"       |
| `bool`          | Type booléen                          | is_valid = True           |
| `None`          | Absence de valeur                     | result = None             |
| `bytes`         | Séquence immuable d'octets            | b = bytes([50, 100, 76])  |
| `bytearray`     | Séquence mutable d'octets             | ba = bytearray([50, 100]) |
| `memoryview`    | Représentation des données en mémoire | mv = memoryview(b'abc')   |

#### int 

Le type `int` est utilisé pour représenter les nombres entiers sans partie décimale. Il peut stocker du positif comme du négatif.

#### str 

Le type `str` est utilisé pour stocker des chaîne de caractères. Les string sont entourés de `'` ou `"`.

En python, on ne peut concaténer que des string.  On utilise `+`
```python
nom = "Alex" + "Alex"
```
#### float 
Permet de représenter des nombres à virgule.

#### complex
Représente les nombres complexe (composés d'une partie réel et imaginaire)

#### bool 
Représente un boolean. Il prends une valeur `True` ou `False`. 

#### None
Type spécial représentant l'absence de valeur 

#### bytes 
Séquence immuable d'octects 

#### bytearray

Séquence mutable d'octets

#### memoryview 
Objet permettant de travailler avec des données sous forme binaire sans les copier 

#### Collection 
Les collections permettent de stocker des groupes entiers d'objets. 

Elles permettent de représenter différentes structures de donnée pour stocker, gérer et traiter des groupes d'éléments.
- `list` : ensemble ordonné et modifiables d'éléments 
- `tuple`: séquence ordonné et immuable d'éléments
- `range`: séquence de nombres souvent utilisé dans les boucles 
- `set` : collection non ordonnées d'élément unique 
- `dict`: collection de clé-valeur avec des clé unique 
- `frozenset`: variable immuable de l'ensemble `set`

| Type de collection | Description                             | Exemple                             |
| ------------------ | --------------------------------------- | ----------------------------------- |
| `list`             | Mutable, éléments pouvant se répéter    | my_list = [1, 2, 3]                 |
| `tuple`            | Immuable, éléments pouvant se répéter   | my_tuple = (1, 2, 3)                |
| `range`            | Séquence immuable de nombres            | my_range = range(1, 10)             |
| `set`              | Ensemble non ordonné d'éléments uniques | my_set = {1, 2, 3}                  |
| `dict`             | Paires clé-valeur, clés uniques         | my_dict = {'a': 1, 'b': 2}          |
| `frozenset`        | Ensemble immuable d'éléments uniques    | my_frozenset = frozenset([1, 2, 3]) |

Chacune de ces collections a ses propres propriété et méthodes unique ce qui les rends adaptées à différentes tâches de programmation.

#### Classes et objet
Python permet de déclarer ses propres types - les classes.

Elles offrent un moyen d'emballer des fonctions et des données liées. De plus, elles permettent de modéliser des objets réel ou abstraits avec un comportement et des propriétés spécifiques.

Une classe est créer à l'aide du mot clé `class`. Cette instruction crée un nouveau type d'objet et permet à la nouvelle classe d'hériter des attributs et méthodes d'une autre classe.

Les classes ont leur propre fonction intégrées.




### Convention de nommage 

Le nom d'une variable doit refléter les données qu'elle contient, de manière à ce que le code soit facilement lisible.

Il est recommander d'utiliser des minuscules avec des underscore pour les noms des variables ( par exemple `ma_var`).

Pour les variables globales, on privilégie des nom longs et descriptifs pour que leur but soit clair.

---

## COMMENTAIRE 
Les commentaire ne sont pas pris en compte par l’interpréteur. Le commenter n'est pas exécuter.

```python
# ligne 

""" commentaire 
```

### docstrings
Chaine de caractere sur plusieurs lignes qui se trouve generalement au debut des modules, classes, methodes et fonction pour decrire leur but.
Elles sont encadrees par trois paire de guillemets double, et utilisee pour generer automatiquement de la documentation 
```python
def add(a, b): """ Fonction pour additionner deux nombres. :param a: premier terme :param b: second terme :return: somme de a et b """ return a + b 
```

---

## OPERATEUR 

### Opérateur mathématique 

#### Division 
Le résultat d'une division sera toujours un `float`
```python
# division 
result = 5 / 2 # 2.5
```

#### Division entière 
Divise le premier nombre par le second. Le résultat sera un entier 
```python
result = 7 // 2 # 3
```

#### Modulo
Retourne le reste de la division du premier nombre par le second 
```python
result = 5 % 3 #2
```

#### Puissance 
Élevé un nombre à la puissance 
```python
result = 5 ** 3 # 125
```

### Opérateur d'assignation abrégés
En python, on retrouve une notation abrégée 
#### Addition et assignation 
Augmente la valeur de la variable par l'opérande de droite 
```python 
x = 5
x += 1 # 6 
```

#### Soustraction et assignation 
Diminue la valeur de la variable par l'opérande de droite 
```python
x = 5
x -= 3 # 2
```

#### Multiplication et assignation 
Multiplie la valeur par l'opérande de droite 
```python 
x = 5
x *= 3
```

#### Division et assignation 
Divise la variable par l'opérande de droite, le résultat est `float`
```python
x = 5
x /= 2 # 2.5
```

#### Division entière et assignation 
Divise la variable par l'opérande de droite, le résultat est un entier 
```python
x = 5
x //= 3 # 2
```

#### Modulo et assignation 
Assigne à la variable le reste de la division par l'opérande de droite 
```python
x = 5
x %= 3 # 2
```

#### Elévation et assignation 
Elève et assigne 
```python
x = 5
x **= 3 # 125
```

---

### Opérateur de comparaison 

Les opérateurs de comparaisons comparent deux valeurs et retournent une valeur `True` ou `False` en fonction du résultat de la comparaison.

#### Egalité 
Vérifie si les deux valeurs sont égale
```python
print(5 == 5) # True 
```

#### Inégalité 
Vérifier si les deux valeurs sont différentes 
```python 
print(5 != 5) # False 
```

#### Plus grand et plus petit 
Compare les deux valeurs pour déterminer si une valeur est plus grande qu'une autre, ou plus petite 
```python
print(5 > 3) # True 
print(5 < 3) # False 
```

#### Plus grand ou égal & plus petit ou égale
Compare deux valeurs pour vérifier si une valeur est plus grande ou égale à une autre, et inversement.
```python
print(5 >= 5) # True 
print(5 <= 4) # False
```

---

## CONSOLE 

La console est une interface utilisateur textuelle qui permet d’interagir avec l'ordinateur à l'aide de commandes saisies au clavier. 

La console est utilisée pour entrer et sortir des données lors de l'exécution du programme.

L'interaction avec la console est souvent utilisée dans les scripts pour automatiser des tâches.

### print() 

La sortie de données vers la console est le processus d'affichage d'information dans la console. En python, on utilise la fonction`print()` qui peut afficher du texte, des nombres et d'autres objets.

```python
print("Mon message à afficher")
```

#### sep 

`sep` : définit le caractères de la chaine qui sera utilisée pour séparer plusieurs valeurs. Par défaut, c'est `espace`. Cela signie que si on passe plusieurs valeur, c'est ce caractère qui sera utilisé comme séparateur.

```python
print("Hello", "World")
print("Hello", "world", sep=", ") #Hello, world

print(1, 2, 3, 4, sep=",\n") # ajoute une nouvelle ligne 
```

#### end 
Permet de définit ce qui sera imprimé après toutes les valeurs passées. Par défaut `\n` qui ajoute une nouvelle ligne.

```python
print("Hello", end=" ")
print("world")
```




### format()

Permet de combiner des chaînes et d’afficher les données à l'écran de manière plus simple et compréhensible.

Elle permet d'insérer des valeurs dans une chaîne à des position spécifiques. 
```python
"Modèle de chaîne de {} à {}".format(valeur1, valeur2,…)
```

Cette fonction est une fonction fille de la chaîne, elle est appelée sur une chaîne.

Elle remplit le modèle de la chaîne avec les valeurs passée. elle convertit les valeurs passées en chaîne, et les insère dans les emplacement désignés par les `{}`.

```python
welcome_message = "Salut {}, bienvenue à {}"
print(welcome_message.format("Anna", "notre magasin"))

output = "Données: {0:.2f} et {1:.2f}".format(3.1415, 2.7158)
print(output) # Données: 3.14 et 2.72

output = "{name} travaille chez {company}"
print(output.format(name="Serguei", company="Google"))
```

### f-string 
Ces chaînes améliorent la lisibilité du code et fonctionnent également plus rapidement que les autres méthodes de formatage.

Pour les utiliser on place la lettre `f` ou `F` devant la chaîne. Les expressions à insérer sont placées entre accolades `{}`

```python
force = "Côté Obscur"
message = f"Que la force soit avec toi {force}"
print(message) # Que la force soit avec toi Côté Obscur

age = 28
message = f"J'ai {age} ans"
print(message)

birth_year = 1985
current_year = 2024
message = f"J'ai {current_year - birth_year} ans"
print(message) # J'ai 39 ans
```

On peut indiquer des variables disponible dans le score actuel, et Python les convertit automatiquement en chaîne et les insère au bon endroit





### input() 
Cette fonction permet de récupérer des saisie utilisateur via la console. La fonction permet d'intéragir avec l'utilisateur dans les applications console.

Les données saisie sont toujours interprétées comme une chaîne . Il sera donc nécessaire de convertir les données si on souhaite traiter des nombres.

```python
name = input("Saisir un nom: ")
print("Salut ", name)
```

Dans cet exemple, `name` récupère la saisie de l'utilisateur. On affiche ensuite cette variable dans le `print()`.

On peut utiliser la fonction sans argument, lorsque l'on souhaite que l'utilisateur appuie sur entrée.

```python 
name = input() # attendre la saisie du texte et enter 
```

#### Saisie de nombre depuis la console 

Pour travailler avec des nombre depuis la console, il sera nécessaire de convertir la chaîne en type numérique approprié avec la fonction `int()` ou `float()`.

```python
age = input("Saisir votre âge :")
age = int(age) # conversion de la saisis en int 
print("Dans 10 ans, vous aurez " + str(age + 10) + " ans.")
```

On peut utiliser une syntaxe courte ;
```python 
age = int(input("Saisir votre âge: ")) # contient le nombre 
print("Dans 10 ans, vous aurez " + str(age + 10) + " ans.")
```

Même principe pour les nombres décimaux :
```python
age = float(input("Saisir votre âge : "))
print("Dans 10 ans vous aurez " + str(age + 10) + " ans.")
```

L'utilisation de `input()` peut provoquer des erreurs si une saisie incorrecte est traiter. Essayer de convertir en entier une chaîne qui ne peut pas être interprétée comme un nombre déclencheras une erreur `ValueError`

---

## CONVERSION DE TYPE 

La conversion de type permet de convertir une valeur d'un type à un autre. 

### type() 
Cette fonction permet de déterminer le type d'un objet. Elle retourne le type de l'objet spécifié, utile pour le debug, la validation de donnée ou l'implémentation d'une logique dépendant des types de données

```python 
x = 1
print(type(x)) # <class 'int'>

x = "Salut"
print(type(x)) # <class 'str'>

x = [1, 2, 3]
print(type(x)) # <class 'list'>

# vérifier qu'une variable contient un nombre 
arg = 123
if type(arg) == int:
	print(arg + 10) # 133
	
# vérifier le type de la variable 
arg = "123"
if type(arg) == int:
	print(arg + 10)
elif type(arg) == str:
	print(arg + "monde") 
else:
	print("type inconnu")
```
### int() 
Cette fonction permet de convertir une valeur en nombre entier.
```python 
num_str = "43"
num_int = int(num_str) # convertion en int
print(num_int) # 43
```

Si la valeur converti n'est pas un nombre, il y aura une erreur `ValueError`.

#### Conversion d'un float en int 
```python
num_float = 42.9
num_int = int(num_float)
print(num_int) # 42
```

L'arrondi des float dans ce type de conversion se fait toujours vers le bas. Seule la partie entière est conservée. 

#### Conversion boolean en int 
```python
true_bool = True
false_bool = False 
print(int(true_bool)) # 1
print(int(false_bool)) # 0
```

---

### str()
Cette fonction permet de convertir une valeur en chaîne de caractère. 

#### Conversion int en str 
```python
num_int = 42
num_str = str(num_int)
print(num_str) # "42"
```

#### Conversion float en str 
```python
num_float = 42.9
num_str = str(num_float)
print(num_str) # "42.9"
```

#### Conversion bool en str 
```python 
true_bool = True 
false_bool = False 
print(str(true_bool)) # "True"
print(str(false_bool)) # False 
```

---

### float()
Permet de convertir une valeur en nombre à virgule 

#### Conversion str en float 
```python 
num_str = "42.9"
num_float = float(num_str)
print(num_float) # 42.9
```

#### Conversion int en float 
```python 
num_int = 42
num_float = float(num_int)
print(num_float) # 42.0
```

#### Conversion bool en float 
```python
true_bool = True 
false_bool = False 
print(float(true_bool)) # 1.0
print(float(false_bool)) # 0.0 
```

---

### tuple()
Convertit une séquence en tuple 
```python
t = tuple([1, 2, 3]) # devient (1, 2, 3)
```

---

### list()
Convertis un objet itérable en liste. Un objet ittérable est un objet qui peut être parcourur séquentiellement comme des chaîne, des listes ou des tuples
```python
l = list("abc") # ['a', 'b', 'c']
```

---

### dict()
Permet de créer un dictionnaire à partir d'une séquence clé valeur 
```python 
d = dict([(1, 'a'), (é, 'b')]) # {1: 'a', 2: 'b'}
```





## INSTRUCTION CONDITIONNELLE 

### if 

Exécute la commande si la conditon est évalué à `True`. Si la condition est évalué à `false`, alors le programme poursuit son exécution.
```python
if conditon:
	commande

# exemple 
age = int(input("Saisir votre age : "))
if age >= 21:
	print("Voici votre bière")
```

---

### if else 

```python 
if <condition>:
	commande1
else:
	commande2
```

Si la condition est évalué à `true`, alors la `commande1` s'exécute. Sinon la `commande2` sera exécuté.

Les commandes ne sont jamais exécuté simultanément, c'est l'un ou l'autre.

```python 
y = 4
if y > 5:
	print("y est plus grand que 5")
else: 
	print("y est plus petit que 5")
```

--- 

### if elif else 

`elif` permet de vérifier une condition supplémentaire

```python
if consition1:
	commande1
elif condition2:
	commande2
else:
	commandeElse
	
# exemple 
x, y = 5, -8
if x > 0 and y > 0:
	print("Premier quart")
elif x < 0 and y > 0:
	print("Deuxieme quart")
elif x < 0 and y < 0:
	print("troiseme quart")
else:
	print("quatrieme quart")
```

### Opérateur ternaire 

Les ternaire est une syntaxe simplifié pour les `if else`.

```python
variable = valeur1 if condition else valeur 2
```

Si la condition est vraie, alors la variable est assigné à `valeur1` sinon `valeur2`

---

## BLOC DE CODE 

Un bloc de commande est un regroupement de code. Les blocs de code sont définis par des indentations, ce qui rend la structure du code claire et lisible.

Python utilise des indentations pour délimiter les séquences d'instructions.

La convention Python indique d'utiliser 4 espaces pour un niveau de d'indentation.

### Structure des blocs 

Un bloc de code commence par une instruction, suivie de deux points et une indentation à la ligne suivante.
Toutes les instructions avec le même niveau d'indentation sont considérées comme faisant partie d'un même bloc.

---
## BOUCLE 
###  FOR 

La boucle `for` permet d'exécuter des instructions sous certaines condition.

```python 
for variable in liste_valeurs:
	commande
```

Cette boucle est adapté pour parcourir des liste de valeurs : liste, chaîne, dictionnaire ... 

`variable` prends la valeur en cours d'itération, et pour chacune elle exécute les commandes.

```python 
fruits = ["apple", "banana", "cherry"]
for fruit in fruits:
	print(fruit)
```

Dans ce code, on parcourt la liste, et on affiche la valeur. La boucle parcourt la liste, et affiche chacune des valeurs. 

### range()
Cette fonction permet de générer une séquence de nombres.

Elle peut s"utiliser de manière différentes selon les besoin : 
- **un argument**: génère une séquence de 0 à n-1. Utiliser pour faire une action un certain nombre de fois 
```python 
for i in range(5):
	print(i) #affiche de 0 à 4
```

- **deux arguments** : génère une séquence de `start` à `end-1`. 
```python
for i in range(1, 6):
	print(i) # affiche 1 à 5
```

- **trois arguments** : ajoute un `step` qui permet de définir le pas entre les nombres dans la séquence 
```python
for i in range(0, 10, 2):
	print(i) # affiche les nombres paire de 0 à 8
```

#### Boucle inversé 

```python
for i in range(10, 0, -1):
	print(i) # affiche de 10 à 1
```

### Itérer une liste 

```python
for variables in [val1, val2, ...]:
	commande
```

La boucle vient itérer une liste, et exécuter les commandes pour chaque valeur de la liste.

### while 

La boucle `while` permet d'exécuter un bloc de commande tant que la condition évaluer est `True`. Attention à toujours prévoir une condition de sortie pour ne pas créer de boucle infinie.

```python
while condition:
	commande

# exemple 
count = 0
while count < 5:
	print(count)
	count += 1

# CLI 
user_input = ""
while user_input != "exit":
	user_input = input("Saisir une entrée :")
	print(user_input)
```

### break 

L'opérateur `break` est utiliser pour stoper immédiatement l'exécution d'une boucle `while` ou `for`

Il permet de sortir d'une boucle avant sa fin normal en fonction d'une condition.
```python
for num in range(10):
	if num == 5:
		break # stop l'exécution de la boucle 
	print(num)

while True:
	response = input("Saisir exit pour quiter"):
	if response == "exit":
		break;

elements = [1, 2, 3, -99, 5]
for element in elements:
	if element < 0:
		print("Elément négatif trouvé :" , element)
		break
```

### continue 

L'opérateur `continue` permet de sauter l'itération en cours de la boucle et passer à l'itération suivante.
```python
for i in range(10):
	if i % 2 = 2 # si paire 
		continue
	print(i)
	
# filtrage de donnée 
data = ["apple", "banana", "", "cherry", "date"]
for fruit in data:
	if not fruit:
		continue # saute les chaine vide
	print(fruit.capitalize)
	
scores = [92, 85, 99, 78, 82, 100, 67, 88]
for score in scores:
	if score < 80:
		continue # saute les scores bas
	print("Félicitation, votre score : ", score)
```

### else 

L'opérateur `else` peut être utiliser dans une boucle. Il s'exécute après la fin de la boucle, mais uniquement si la boucle s'est terminé normalement. 
Cela permet de venir faire des vérification si la boucle s'est terminé prématurément 

```python
for in in range(3):
	password = input("Saisir le mot de passe : ")
	if password == "secret":
		print("Mot de passe accepté.")
		break
else:
	print("Mot de passe incorrect")
```

Dans ce code, si l'utilisateur à saisis un mot de passe incorrect trois fois, le `else` s'affiche.

```python
n = 5
while n > 0:
	print(n)
	n -= 1
else:
	print("La boucle est terminé")
```

L'utilisattion des `else` dans une boucle est utile dans les algo de recherche, où il est nécessaire de déterminer si la recherche à réussie. Par exemple, lors de la recherche d'un élément dans une liste qui n'existe pas, on peut utiliser `else` pour afficher un message d'échec après la fin de la boucle 

---

### Boucle imbriquées 

Une boucle imbriquée signifie qu'une boucle est présente à l'intérieur d'une autre. Cela permet de traiter des tableaux à dimensions multiples, des listes de liste ou d'autres sutrctures de données imbriquées.

Elle se compose d'une boucle externe et d'une ou plusieurs boucle interne. Chaque fois que la boucle externe accomplit une itération, la boucle interne s'exécute entièrement 
```python
for i in range(3):
	for j in range(3):
		print(f"{i}, {j})
		
		
# affiche une table de multiplication
n = 5
for i in range(1, n + 1):
	for j in range(1, n + 1):
		print(f"{i} * {j} = {i * j}", end="\t")
	print()
```