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

|Notation|Signification|
|---|---|
|\n|Saut de ligne (newline)|
|\t|Tabulation (indentation)|
|\\|Au sens littéral \|
|\"|Guillemets à l’intérieur d’une chaîne|

### Fonctions String

Le type `String` possède ces propres fonctions.

|Fonction|Description|Exemple de résultat|
|---|---|---|
|```java<br>str.length()<br>```|Longueur de la chaîne|```java<br>"abc".length() → 3<br>```|
|```java<br>str.toUpperCase()<br>```|En majuscules|```java<br>"abc".toUpperCase() → "ABC"<br>```|
|```java<br>str.toLowerCase()<br>```|En minuscules|```java<br>"ABC".toLowerCase() → "abc"<br>```|
|```java<br>str.trim()<br>```|Supprimer les espaces en début et fin|```java<br>"  x y  ".trim() → "x y"<br>```|
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