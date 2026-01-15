# Programme Java

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