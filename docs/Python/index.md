# Python 

Les programmes ecrit en Python sont execute par `py.exe` qui est le Python Interpreter. Un programme special capable d'executer des programmes Python.

Python contient des concepts de base :
- Chaque commande est ecrit sur une nouvelle ligne 
- L'indentation est importante
- Les commandes sont regroupees en bloc a l'aide de l'indentation. Si plusieurs commandes ont le meme niveau d'indentation, elles sont consideres comme appartenant au meme bloc

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

#### bool 
Représente un boolean. Il prends une valeur `True` ou `False`. 

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
