# Java

## MAIN()

Un programme, peut se composer d'un ou de plusieurs fichiers. Chaque fichiers contient des commandes regroupée en fonction Java.

Un programme minimal en Java doit posséder au moins une méthode par laquelle commence son exécution.

Cette méthode s'appelle `main()`. Cette méthode est le point d'entrée du programme. C'est toujours par elle que l'exécution commence.

En Java 21 et plus, un programme minimal peut se composer d'une seule méthode `main()`, qui peut même ne contenir aucune commande.

Un programme minimal en Java 25 ressemble à cela :

```java
void main()
{
	// instruction de la méthode
}
```

Un programme Java est composé de commandes. Chaque commande décrit une action, et à la fin de chaque commande, on met un **point-virgule**

### Ordre d'exécution d'un programme

Le programme exécute les commandes de haut en bas, ligne par ligne. Lorsque les commandes sont terminées, le programme s'arrête.

```java
void main()
{
   System.out.println("Bonjour à tous !");
   System.out.println("Aujourd’hui, nous apprenons à programmer en Java !");
}
```

Le résultat du code sera :

```text
Bonjour à tous !
Aujourd’hui, nous apprenons à programmer en Java !
```

### System.out.println

Cette commande permet d'afficher du texte à l'écran et ajoute une passage à la ligne suivante.

```java
System.out.println("Amigo");
System.out.println("The");
System.out.println("Best");

//Amigo
//The
//Best
```

- `println`: `print` + `line`.

### System.out.print

Cette commande permet d'afficher du texte, mais n'ajoute pas de nouvelle ligne.

```java
System.out.print("Amigo");
System.out.print("The");
System.out.print("Best");

//AmigoTheBest
//
```

#### System.out

C'est un objet spécial qui affiche du texte à l'écran. On l'appelle aussi la console.

Cette console est le moyen le plus répandus d'interaction entre un programme et un humain.

Lorsque l'utilisateur tape des commandes au clavier, et que le programme lui répond par du texte à l'écran, les développeur disent que le programme travaille avec la console.

Travailler avec la console, c'est afficher du texte à l'écran et saisir des données au clavier.

## COMPILATEUR

Pour l'ordinateur, chaque instruction est encodée en code machine.

La Java permet d'écrire du code lisible par l'humaine, et le compilateur est un programme spécial qui traduit le texte d'un programme en un ensemble de codes machine.

Le développeur écrit un programme en java, puis lance le compilateur, qui viens traduire le code en un fichier unique en code machine (le programme final compilé).

| Programme C++ | ==> | Programme, composé de codes machine |

Le programme obtenu peut être exécuté immédiatement sur l'ordinateur. L'inconvénient de cette approche est que le code du programme obtenu dépend du processeur et du système d'exploitation.

Un programme compilé pour Windows ne fonctionnera pas sur un téléphone Android

Java utilise une autre approche :

| Programme Java | = Compilateur Java => | Programme composé de bytcode | = Java VM => | Programme code machine |

Le compilateur Java ne compile pas toutes les classes en un seul programme en codes machines. A la place, il compile chaque classe séparément, et pas en code intermédiaire spécial (bytecode). La compilation en code machine s'effectue au lancement du programme.

Pour cette opération, il existe un programme spécial appelé JVM - Machine virtuelle Java.
On la lance d'abord, puis le programme constitué de bytecode.
Et c'est la JVM qui compile en code machine.

### Erreur de compilation

Lors de la compilation, le programme vient chercher les erreurs. Il vérifie la correction du code, et ne laisse pas aller plus loin s'il trouve une erreur.

#### Erreur de type

On tente d'assigner un nombre à une variable qui stocke du texte

```java
String userName = 42; // Oups ! On ne peut pas faire ça.
```

Le compilateur signale immédiatement que les types ne correspondent pas : `Cannot implicitly convert type 'int' to 'string'.`

#### Faut de frappe

```java
System.out.printline("Salut !"); // Erreur dans le nom de la méthode
```

---

## Commentaires

```java
// commentaire one line
System.out.println("Bonjour, le monde !"); 	// Cela affiche un message de bienvenue à l'écran

/* commentaire
multi
ligne */
```

---

## VARIABLES

Les variables sont des éléments utilisées pour stocker de la données. Chaque variable est composé de :

- **nom**: permet de distinguer une variable d'une autre.
- **type**: le type de la variable détermine quel donnée, valeurs on peut stocker.
- **valeur**: la donnée stocké dans la variable.

Il existe quelques règles concernant le nom des variables

- Il ne peut pas commencer par un chiffre et ne doit pas être utiliser par un mot réservé
- Il ne peut pas contenir de symboles spéciale sauf `$` et `_`
- La casse est importante
- Par convention, on utilise le `camelCase`

### Déclaration de variable

```java
int a;
String s;
double c;

// déclaration multiple 
int var1, var2, var3;

// déclaration et affectation multiple 
int imya1 = var1, imya2 = var2, imya3 = var3;
```

### Affectation de variable

Pour ajouter une valeur à une valeur, on utilise l'opérateur d'affectation. Elle copie la valeur de la variable dans une autre.

```java
int i;			// La variable i est créée
int a, b;		// Les variables a et b sont créées
int x;			// La variable x est créée

i = 3;			// La valeur 3 est placée dans la variable i.

a = 1;			// La valeur 1 est placée dans la variable a.
b = a + 1;		// La valeur 2 est placée dans la variable b.

x = 3;			// La valeur 3 est placée dans la variable x.
x = x + 1;		// À cette ligne, la valeur de x augmente de 1, x vaut maintenant 4.
```

---

# Types Java

## INT

Le type `int` est le type adapté pour stocker des nombres entiers positif ou négatif.

```java
int monIntier = 7;
```

### Calcul d'expression entières

A droite de l'opérateur d'affectation, il est possible d'avoir n'importe quel expression. On peut de cette manière, faire une opération et ou le résultat est affecté à la variable.

```java
int a = 1;			// La variable a vaudra 1
int b = 2;			// La variable b vaudra 2
int c = a * b + 2;	// La variable c vaudra 4
```

### Division de nombre entier

En Java, la division d'un entier par un entier produit toujours un entier. Le reste de la division es tronqué, la partie fractionnaire sera ignorée.

| Commande          | Résultat de la division | Remarque                |
| ----------------- | ----------------------- | ----------------------- |
| `int a = 5 / 2;`  | 2,5                     | La variable a vaudra 2  |
| `int b = 20 / 3;` | 6,6667                  | La variable b vaudra 6  |
| `int c = -6 / 5;` | -1,2                    | La variable c vaudra -1 |
| `int d = 1 / 2;`  | 0,5                     | La variable d vaudra 0  |

### Modulo

Le module permet d'obtenir le reste d'une division. On utilise le `%.

| Commande          | Résultat de la division | Remarque               |
| ----------------- | ----------------------- | ---------------------- |
| `int a = 5 % 2;`  | 2 et reste 1            | La variable a vaudra 1 |
| `int b = 20 % 4;` | 5 et reste 0            | La variable b vaudra 0 |
| `int c = 9 % 5;`  | 1 et reste 4            | La variable c vaudra 4 |
| `int d = 1 % 2;`  | 0 et reste 1            | La variable d vaudra 1 |

#### Nombre pair

Cet opérateur est utilise pour savoir si un nombre est pair par exemple. Il suffit de faire un modulo 2 et de comparer le reste à 0. Si 0, alors le nombre pair.

```java
(a % 2) == 0
```

### Incrémentation & décrémentation

```java
// incrémentation
a++;
// décrémentation
a--;
```

---

## STRING

Le type `String` permet de stocker des chaîne de caractères. Ils disposent également d'une multitude de méthode avec lesquelles ont peut faire de nombreuses choses.

Tous les objets Java peuvent être convertis en type `Sring`, ils renvoient leur réprésentation textuelles.

Les chaînes doivent être placé entre guillemets double

```java
String imya;

imya = "ma chaîne";
```

### Concaténation

La concaténation permet de fusionner des chaînes.

```java
String name = "Anya";                // name contient la chaîne Anya
String city = "New York";           // city contient la chaîne New York
String message = "Hello!" + city + name + city;
```

### Echappements

```java
String quote = "Il a dit: \"Bonjour!\"";
System.out.println(quote); // Il a dit: "Bonjour!"
```

| Notation | Signification                         |
| -------- | ------------------------------------- |
| \n       | Saut de ligne (newline)               |
| \t       | Tabulation (indentation)              |
| \\       | Au sens littéral \|                   |
| \"       | Guillemets à l’intérieur d’une chaîne |

### Fonctions String

Le type `String` possède ces propres fonctions.

| Fonction                        | Description                           | Exemple de résultat                       |
| ------------------------------- | ------------------------------------- | ----------------------------------------- |
| `java<br>str.length()<br>`      | Longueur de la chaîne                 | `java<br>"abc".length() → 3<br>`          |
| `java<br>str.toUpperCase()<br>` | En majuscules                         | `java<br>"abc".toUpperCase() → "ABC"<br>` |
| `java<br>str.toLowerCase()<br>` | En minuscules                         | `java<br>"ABC".toLowerCase() → "abc"<br>` |
| `java<br>str.trim()<br>`        | Supprimer les espaces en début et fin | `java<br>"  x y  ".trim() → "x y"<br>`    |

Chacune des fonctions retourne une nouvelle chaîne, la chaîne d'origine n'est pas modifée.

#### Longueur d'une chaîne

```java
String name = "Andrey";
int length = name.length();
System.out.println(length); // 6, car il y a 6 lettres
```

#### Transformer une chaîne en majuscule ou minuscule

```java
String original = "Bonjour";
System.out.println(original.toUpperCase()); // BONJOUR
System.out.println(original.toLowerCase()); // bonjour
```

#### Supprimer les espaces

```java
String messy = "   hello   ";
System.out.println(messy.trim()); // "hello"
```

---

## CONVERSION DE TYPE

Il est possible de convertir une valeur dans un autre type. Si cette conversion n'est pas effectuée, cela provoquera une erreur.

### Conversion int vers string

Il n'est pas possible d'effectuer des operations des varaibles de type `String`, meme si la chaine ne contient pas de chiffres.

#### String.valueOf()

C'est la méthode principale et la plus répandue

```java
int number = 42;
String str = String.valueOf(number);  // str == "42"
```

La méthode convertit un nombre en une chaîne qui correspond au type de l'objet passé en argument.

#### Concaténation avec une chaîne vide

```java
int number = 42;
String str = "" + number;
```

#### Conversion implicite en chaîne

Cette conversion se produit automatiquement lorsque l'on concaténe un `String` avec un autre type

```java
int a = 5;
String name = "Anya" + a;            //  name contient la chaîne Anya5

int a = 5;
String city = a + "New York" + a;   //  city contient la chaîne 5New York5

int number = 10;
String code = "Yo";
String message = "Hello! " + number + code; //  message contient la chaîne Hello! 10Yo
```

### Conversion String => int

Dans le cas ou la string ne contient que des nombres, on peut venir la convertir.

#### Integer.parseInt()

```java
int x = Integer.parseInt(stroka);
```

`x` est la declaration de la variable, et `stroka` est un nombre donne sous forme de string.

```java
String str = "123";
int number1 = Integer.parseInt(str);        //  number1 contient le nombre 123;

int number2 = Integer.parseInt("321");      //  number2 contient le nombre 321

int number3 = Integer.parseInt("321" + 0);  //  number3 contient le nombre 3210

int number4 = "321"; //  Ne se compile pas : variable de type int, mais valeur de type String
```

---

## ADRESSAGE MEMOIRE ET VARIABLE

### Organisation de la memoire

Chaque ordinateur dispose d'une memoire vive
