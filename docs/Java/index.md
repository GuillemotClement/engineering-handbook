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

---

### Affichage 

#### System.out

C'est un objet spécial qui affiche du texte à l'écran. On l'appelle aussi la console.

Cette console est le moyen le plus répandus d'interaction entre un programme et un humain.

Lorsque l'utilisateur tape des commandes au clavier, et que le programme lui répond par du texte à l'écran, les développeur disent que le programme travaille avec la console.

Travailler avec la console, c'est afficher du texte à l'écran et saisir des données au clavier.
#### System.out.println

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

#### System.out.print

Cette commande permet d'afficher du texte, mais n'ajoute pas de nouvelle ligne.

```java
System.out.print("Amigo");
System.out.print("The");
System.out.print("Best");

//AmigoTheBest
//
```



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
## TYPE 

Number :
`byte` : 1 octect 
`short`: 2 octects
`int`: 4 octect 
`long`: 8 octect 

Nombre à virgule 
`float` : 4 octet 
`double` : 8 octect

---
## BOOLEAN

Peut prendre une valeur `true`ou `false`.

```java
int age = 20;
boolean isAdult = age >= 18;   // true (20 est supérieur ou égal à 18)
boolean isTeenager = age >= 13 && age < 18; // false (20 n'est pas inférieur à 18)
boolean isEven = age % 2 == 0; // true (20 est divisible par 2)
```



---
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

Il n'est pas possible d'effectuer des opération arithmétique avec le type `String`, même si la chaîne ne contient que des nombres.

### Int => String

Il n'est pas possible d'effectuer des opérations des variables de type `String`, même si la chaine ne contient pas de chiffres.

Il existe plusieurs façon de convertir en entier en chaîne de caractère.

```java
// valueOf() => méthode principale
int number = 42;
String str = String.valueOf(number);  // str == "42"

// concaténation avec une string vide
int number = 42;
String str = "" + number; // "42"

// concaténation avec une string
int a = 5;
String name = "Anya" + a; //"Anya5"         
```

### String => Int

Dans le cas où une chaîne ne contient que des nombres, il est possible de la convertir. La méthode `Integer.parseInt()` permet de réaliser la conversion.

```java
int x = Integer.parseInt(stroka);

String str = "123";
int number1 = Integer.parseInt(str); // 123
```

---
## SAISIE CLAVIER 

### Scanner 
La classe `Scanner` (`java.util.Scanner`) permet de lire des données à partir de différentes sources : console, fichiers, internet.

Elle prend en paramètre la source des données. 

#### Récupérer une saisie clavier 

```java
void main()
{
	// instanciation de l'objet avec le clavier en source
    Scanner console = new Scanner(System.in);
    // permet de lire une string
    String name = console.nextLine();
    // permet de lire un nombre
    int age = console.nextInt();

    System.out.println("Name: " + name);
    System.out.println("Age: " + age);
}
```

A l'instanciation, on définit le type `Scanner`, puis son nom et enfin la classe qui est instancier.

Le code `new Scanner(System.in)` créer un nouvel objet de type `Scanner`, et on lui passe en parmètre la source à partir de laquelle l'objet lira les données - `System.in` qui correspond au saisie clavier.

On peut ensuite lire les données depuis le clavier.

`scanner.nextLine()` est la méthode permettant de lire la saisie issue du clavier. Lorsque le programme atteins cette ligne, il attends la saisie et à l'appuie sur `Enter` enregistre ce qui a été saisie.

- `scanner.nextLine()`: permet de récupérer des string 
- `scanner.nextInt()`: permet de récupérer des int 
- `scanner.nextDouble()`: permet de récupérer des float

Si le type attendu n'est pas respecté, une erreur est provoquée.

---
## CONDITION

### if else 

```java
if (condition1)
{
    // S’exécute si condition1 est vrai
}
else if (condition2)
{
    // S’exécute si condition2 est vrai, et condition1 — faux
}
else
{
    // S’exécute si aucune des conditions ci-dessus ne convient
}
```

### Comparaison 

```java
if (number == 10) // égalité
if (age != 18) // différend de 
if (temperature > 36) // supérieur
if (balance <= 0) // inférieur ou égale

// comparaison chaîne
String password = console.nextLine();
if (password.equals("qwerty"))            // on appelle la méthode equals() et on lui passe la deuxième chaîne
{
    System.out.println("Vous êtes connecté !");
}

(0 < a) && (a < 100) // ET
(!a) && (!b) // OU
!(!a || !b) // NOT

```

### Ternaire 

Les deux valeurs d'une opération ternaire doivent être du même type.

```java
int age = 25;

// syntaxe if else 
int money;
if (age > 30)
   money = 100;
else
   money = 50;
   
// ternaire 
int money = age > 30 ? 100 : 50;
```

---

## BOUCLE 

### While 

```java
int i = 1;
while (i <= 5)
{
    System.out.println("Étape n° " + i);
    i++; // N’oubliez pas d’incrémenter le compteur !
}

// boucle de saisie
String password = "";
while (!password.equals("qwerty"))
{
    System.out.print("Entrez le mot de passe : ");
    password = console.nextLine();
}
System.out.println("Bienvenue !");
```