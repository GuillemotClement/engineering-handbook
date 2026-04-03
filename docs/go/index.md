# Golang 

## FONCTIONNEMENT 

### Compilateur 

C'est un programme spécial qui compile le code. Il traduit le code Go en code compréhensible pour la machine.

Il prend le texte en Go, le décomposer, le vérifie pour détecter les erreurs et le transforme en code machine.

Le compilateur peut également optimiser le code, signaler les erreur, etc.

![[Pasted image 20260401113513.png]]

### Lire les erreurs de compilateur 

Le message d'erreur contient en général :
- le fichier 
- la ligne 
- la colonne
- text de l'erreur 

Par exemple `main.go:7:6: undefined: something`

---

## COMMENTAIRE 

```go
// commentiare one line 
fmt.Println("Bonjour, monde !") // Ceci affiche une salutation a l'ecran

/*
commentaire multiligne
*/
```

---
## AFFICHAGE 

### fmt

C'est le package permettant d'utiliser les fonction lié à l'affiche. Il est nécessaire de l'importer avant d'utiliser les méthodes.

```go
import "fmt"
```

### fmt.Print()

Cette fonction affiche du texte à l'écran sans ajouter de retour à la ligne
```go
package main 

import "fmt"

func main(){
	fmt.Print("Hello")
}
```
### fmt.Println()

Cette fonction affiche du texte a l'écran et ajoute un retour à la ligne.

```go
// afficher une string
fmt.Println("hello world")
// afficher une rune
fmt.Println('A')
// afficher un number 
fmt.Println(1)
// afficher plusieurs valeurs 
fmt.Println("Mon age :", 28)
```

### fmt.Printf()

Cette fonction permet d'ajouter des espaces réservé. 

L'utilise de `%s` permet d'ajouter une string, `%d` permet d'ajouter un nomber.

```go
fmt.Printf("Name: %s, Score: %d\n", "Alice", 42)

func main(){
	x := 10 
	fmt.Printf("x=%v type=%T\n", x, x) // x=10 type=int 
}
```

- `%v` : spécificateur universel 
- `%T` : affiche le type de valeur 
- `%d` : entier 
- `%s` : string 

---

## VARIABLES

### var 

Permet de créer une variable. Par défaut, Go lui affecte la valeur par défaut pour ce type si on ne lui affecte rien.

On l'utilise pour préparer une variable qui va recevoir une valeur. Par exemple, pour utiliser avec `fmt.Scan`, ou après une vérification de condition.

```go 
func main() {
	var count int // declared
	count = 3     // assigned
	fmt.Println(count) // 3
	
	var name = "Gopher" // inférence 
	var age = 10
	fmt.Println(name, age) // Gopher 10
}
```

### := 

Syntaxe courte pour déclarer une variable.

```go
func main() {
	x := 10        // declared + initialized
	x = x + 5      // updated an existing variable
	fmt.Println(x) // 15
}
```

L'utilisation de `:=` n'est autorisé que lorsqu'au moins une des variable est déclaré dans le bloc.
- si tous les noms existent déjà, alors l'utilisation est interdite.
- si une partie des variables existe déjà, mais qu'un moins un nouveau apparait, alors on peut l'utiliser, et les variables existante recevront la nouvelle valeur.

```go 
func main() {
	x := 10
	y := 20

	x, z := y, 30 // z est un nouveau nom, donc := est autorise
	fmt.Println(x, y, z) // 20 20 30
}
```

### Shadowing 

Le shadowing, ou le masquage de variable est lorsque dans un bloc, on créer une nouvelle variable avec le même nom qu'une variable d'une bloc externe. Dans le bloc, la nouvelle variable, prends cette valeur, et celle du bloc exterene ne change pas.

### Variable globale

Il est possible de déclarer des variables au niveau du package. Ces variables se déclarent en dehors des fonctions

On ne peut pas utiliser `:=`, il faut utiliser `var` ou `const`. 

Les variables globales sont visibles dans toutes les fonctions de ce package.

```go 
package main

import "fmt"

var appName = "Range Normalizer" // variable globale (package-level)

func normalize(from, to int) (int, int) {
	if from > to {
		from, to = to, from
	}
	return from, to
}

func main() {
	from, to := 10, 3
	from, to = normalize(from, to)
	fmt.Println(appName, from, to) // Range Normalizer 3 10
}
```



### Affectation 

L'affectation c'est lorsque l'on passe une nouvelle valeur à une variable. La variable doit être déclaré au préalable dans la portée courante.

```go 
func main() {
	var total int // déclaration 
	total = 100 // affectation 
	total = total - 30
	fmt.Println(total) // 70
}
```

### Portée des blocs 

Les accolades définissent la portée, une zone de code à l'intérieur de laquelles les noms de variables existent et sont accessibles.

```go 
func main() {
	x := 10

	if x > 0 {
		y := x * 2
		fmt.Println("inside:", y) // inside: 20
	}

	fmt.Println("outside:", x) // outside: 10
}
```

### Assignation multiple 

Il est possible d'assigner plusieurs valeurs à plusieurs variables en une seule fois : 

```go 
a, b = 10, 20
```

- `a` prend la valeur 10 
- `b` prend la valeur 20 

Le côté droit est évalué entièrement, puis les résultats sont répartis dans les variables de gauche.

```go 
func main() {
	x := 1
	y := 2

	x, y = y, x+y
	fmt.Println(x, y) // 2 3
}
```

Cela peut être utile pour permuter les valeurs sans utiliser de variables temporaire 

```go 
func main() {
	from := 0
	to := 0

	fmt.Scan(&from, &to)

	if from > to {
		from, to = to, from // permutation
	}

	fmt.Printf("range: %d..%d\n", from, to) // par exemple : range: 3..10
}
```

---
## CONSTANTE

### Déclarer une constante

Une constante désigne une valeur qui est définie une fois et qui ne change jamais. Elle doit également être **connue au moment de la compilation**. 

```go
const maxUsers = 100
```

Après cela, la constante ne peut plus être réassigné. 

On utilise généralement les constantes pour des limites strictes, ou des réglages qui ne devraient pas changer.

```go 
const minAge = 18

if age >= minAge {
    fmt.Println("welcome")
}
```

### Groupe de constante 

Les constantes sont généralement déclarée dans des groupes. 

```go 
const (
	minAge = 18
	maxUsers = 100 
	appName = "DoorGuard"
)
```

### Constante untyped 

Ce type de constante est sans type fixé. 

```go 
const n = 5 
```

La constante peut prendre un type `int`, `int64` ou `uint`. On pourras recevoir un type plus tard selon l'endoit où la constante est utilisée.

```go 
func main() {
	const n = 5 // untyped

	var a int = n
	var b int64 = n

	fmt.Printf("a=%v (%T)\n", a, a) // a=5 (int)
	fmt.Printf("b=%v (%T)\n", b, b) // b=5 (int64)
}
```

Ici c'est la variable qui reçoit le type. La constante se glisse dans le type attendu.

### Constante typées 

```go 
const m int = 5 
```

Il s'agit d'une constante `typed`. Elle possède un type et obéit au même règle que les valeurs ordinaire de ce type.

```go 
func main() {
	const n = 5     // untyped
	const m int = 5 // typed

	var a int64 = n        // ok
	var b int64 = int64(m) // conversion nécessaire

	fmt.Println(a, b) // 5 5
}
```

---

## TYPE 

### int

Stocke les nombres entiers sans la partie décimale. Il prends les négatif et les positifs. 

Il est possible de savoir combien de bits représente le `int`

```go 
func main() {
	fmt.Println("int size:", strconv.IntSize, "bits") // par exemple: int size: 64 bits
}
```

### int64 

Utiliser lorsque l'on souhaite travailler avec une taille fixe, et une grand plage. C'est utiliser lorsque l'on stocke des nombres potentiellement grand, ou lorsque le format doit être identiques sur différentes machines.

- argent en unité minimales, par exemple en centimes. Généralement, pour le stockage de valeur numéraire, on préfère stocker en centimes pour éviter les erreur de précision.
- compteur et accumulations 
- les nombres venant de l'extérieur (numéro de transactions, Id user)

### uint 

Peut prendre que des nombre positif. 
- travail avec les masques de bits et des aspects de bas niveau 
- lorsque l'api exige ce type spécifique 
- lorsque l'on travail avec des identifiant 

```go 
func main(){
	u := uint(17)
}
```

### float64 

Permet de stocker des valeurs décimale. C'est le type par défaut pour les nombres à virgule.

Attention à la précision avec les float.
```go 
func main() {
	x := 0.1
	var y float32 = 0.1

	fmt.Printf("x=%v, type=%T\n", x, x) // x=0.1, type=float64
	fmt.Printf("y=%v, type=%T\n", y, y) // y=0.1, type=float32
}
```

La comparaison entre nombre flottant peut engendrer des résultats inattendu à cause de la précision.


### string 

#### Caractères spéciaux 

- `\n` : saut de ligne 
- `\t` : tabulation 
- `\"` : échappement guillemet
- `\`: échappement anti slash

#### Concaténation 

Le signe `+` permet de faire des concaténation.

```go
package main 

import (
	"fmt"
	"strconv"
)

func main(){
	// concaténation 
	s := "Amigo" + " the best"
	fmt.Println(s)
	
	// conversion int => str 
	x := 333
	s = "Amigo" + strconv.Itoa(x)
	fmt.Println(s)
}
```

Il n'est pas possible de mélanger une chaîne et des numbers pour réaliser des concaténation.

#### len() - Longueur d'une chaîne 

Cette fonction retourne la longueur d'une chaîne.

```go
package main

import "fmt"

func main() {
    s := "golang"
    fmt.Println(len(s)) // 6
}
```


### bool 

Le type  `bool` ou boolean, contient une valeur `true` ou `false`. 

Pour le nom des variables boolean, on utilise généralement ce type de nom `isAdult`, `hasTicket`, etc.

```go 
package main 

import "fmt"

func main(){
	isWeekend := true 
}
```

### Valeurs par défaut 

A la déclaration d'une variable, elle prends automatiquement une valeur par défaut.

- `int` : 0
- `float64` : 0.0
- `boolean`: false 
- `string` : ""



---

## CONVERSION

Le package `strconv` permet de convertir les types. 

### strconv.Atoi - str -> int 

Conversion `str` vers `int`. Retourne deux valeurs.

```go
package main 

import (
	"fmt"
	"strconv"
)

func main(){
	s := "42"
	n, err := strconv.Atoi(s) // on fait la conversion 
	
	fmt.Println("n =", n) // 42
	fmt.Println("err =", err) // err = nil
}
```
### strconv.Itoa - int -> str

Convertion `int` vers `str`. 

```go
package main 

import (
	"fmt"
	"strconv"
)

func main(){
	nextID := 101 
	msg := "next id = " + strconv.Itoa(nextID) // conversion 
	fmt.Println(msg) // "next id = 101"
}
```

### Conversion explicite 

Go utilise une syntaxe de conversion `T(x)` ou `T` est le type cycle, et `x` la valeur à convertir.

```go 
func main() {
	var n int64 = 42
	m := int(n) // conversion du int64 en int 

	fmt.Printf("n=%v (%T)\n", n, n) // n=42 (int64)
	fmt.Printf("m=%v (%T)\n", m, m) // m=42 (int)
}
```

### Débordement à la conversion d'entier 

La convesion d'un type large vers un type plus étroit comme `int64` vers `int32` peut provoquer des problème.

Il peut y avoir des perte de donnée (débordement de plage), et le programme ne plante pas forcément.

```go 
func main() {
	var big int64 = 130
	var small int8 = int8(big)

	fmt.Println(big)   // 130
	fmt.Println(small) // -126 // débordement
}
```

### Conversion signed to unsigned 

```go 
func main() {
	i := -1
	u := uint(i)

	fmt.Println(i) // -1
	fmt.Println(u) // un très grand nombre (dépend de la taille de uint)
}
```

### float -> int 

Une conversion d'un flottant vers un entier entraine une troncature de la valeur. La partie décimal n'est pas conservé.

La conversion peut également entrainer une perte de précision sur les grands nombres.

```go 
func main() {
	x := 3.99
	n := int(x)

	fmt.Println(n) // 3
}
```

Pour obtenir un résultat de division décimal, il faut convertir les opérandes en `flat64` avant la division, sinon la partie décimal ne sera pas conservée.

```go 
func main() {
	a := 7
	b := 2

	fmt.Println(a / b)                   // 3
	fmt.Println(float64(a) / float64(b)) // 3.5
	fmt.Println(float64(a/b) + 0.0)      // 3 (d’abord en entier, puis en float)
}
```



---

## ARITHMETIQUE 

### Opérateur arithmétique 

- `+` : addition 
- `-` : soustraction 
- `*` : multiplication 
- `/` : division entière 
- `%` : modulo (reste d'une division)

Les opérations sont typé, par exemple, des opérations sur des `int` produisent des `int`.

### Division entière

L'opérateur `/` de faire réaliser des division entière. Si deux `int` sont utilisé, on aura également un `int` pour le résultat. La partie décimale est ignoré.

```go
func main() {
	fmt.Println(7 / 2) // 3
	fmt.Println(9 / 3) // 3
	fmt.Println(1 / 2) // 0
}
```

### Division des nombres négatifs 

La division entière se fait vers zéro.

```go 
func main() {
	fmt.Println(-7 / 2) // -3
	fmt.Println(-1 / 2) // 0
}
```
### Modulo

L'opérateur `%` permet de calculer le reste d'une division. Fonctionne que sur des `int`

```go
func main() {
	a := 17
	b := 5
	fmt.Println(a/b, a%b) // 3 2
	fmt.Println((a/b)*b + (a%b)) // 17
}

// ====
// calculer de temps 
package main

import "fmt"

func main() {
	totalMinutes := 135

	hours := totalMinutes / 60
	minutes := totalMinutes % 60

	fmt.Println(hours, minutes) // 2 15
}

// monétaire 
// on passe en centime pour éviter les problème de précision 
func main() {
	totalCents := 12345

	euros := totalCents / 100
	cents := totalCents % 100

	fmt.Printf("%d euros %02d cents\n", euros, cents) // 123 euros 45 cents
}
```

- `%2d` : format d'affichage. On affiche deux nombres après la virgule

### Calcul de pourcentage 

```go 
func main() {
	var priceK int64
	var qty int64
	var discountPercent int64

	fmt.Scan(&priceK, &qty, &discountPercent)

	subtotal := priceK * qty
	// on calcul la remise à appliquer
	discount := (subtotal * discountPercent) / 100
	total := subtotal - discount

	fmt.Println(total)
}
```

### Opération arithmétique 

```go 
x := 10

x += 5  // x = 15
x -= 3  // x = 12
x *= 2  // x = 24
x /= 4  // x = 6

// concaténation 
s := "Hello"
s += " World" // "Hello World"
```

---

## ARRONDIS ET FORMATAGE 

Le formatage ne modifie pas la valeur, mais l'arrondis viens modifier la valeur.

### Formatage 

Le formatage permet par exemple d'afficher de manière lisible une valeur par exemple 

```go 
func main() {
	x := 2.0 / 3.0

	fmt.Printf("Pretty: %.2f\n", x)   // Pretty: 0.67
	fmt.Printf("Raw:    %.17f\n", x)  // Raw:    0.66666666666666663
	fmt.Printf("Still x: %.17f\n", x) // Still x: 0.66666666666666663
}
```

### Formatage des float 

Lorsque l'on affiche un `float`, Go essaie d'affiche un nombre raisonable. Il est possible de forcer l'affichage pour définir le nombre de chiffres après la virgule à afficher.

- `%f` : affiche un float 
- `%.2f` : affichage 2 chiffres après la virgule avec un arrondis pour l'affichage 
- `%.0f` : affichage sans la partie décimal 

```go 
func main() {
	x := 1.0 / 3.0

	fmt.Printf("x as %%f   = %f\n", x)   // x as %f   = 0.333333
	fmt.Printf("x as %%.2f = %.2f\n", x) // x as %.2f = 0.33
	fmt.Printf("x as %%.0f = %.0f\n", x) // x as %.0f = 0
}
```

### Arrondis 

On vient faire un arrondis lorsque l'on souhaite : 
- calculer une somme, puis utiliser la valeur arrondie 
- appliquer un arrondis 
- tronquer la partie décimale 

Pour cela, on utilise le package `math`. Ces méthode retourne une nouvelle valeur

- `math.Round(x)` : arrondi à l'entier le plus proche 
- `math.Floor(x)` : arrondi vers le bas 
- `math.Ceil(x)` : arrondi vers le haut 

```go 
func main() {
	fmt.Println(math.Round(2.5)) // 3
	fmt.Println(math.Floor(2.9)) // 2
	fmt.Println(math.Ceil(2.1))  // 3
}
```

### Arrondis a deux chiffres 

Pour obtenir une valeur à deux chiffres :

```go
func main() {
	x := 12.3456
	// on multiple par 100
	// on arrondi l'entier 
	// on divise de nouveau 
	y := math.Round(x*100) / 100

	fmt.Printf("x = %.10f\n", x) // x = 12.3456000000
	fmt.Printf("y = %.10f\n", y) // y = 12.3500000000
}
```

### Gestion des float 

```go 
func main() {
	x := 0.1 + 0.2

	fmt.Printf("x = %.17f\n", x) // x = 0.30000000000000004
	fmt.Printf("x = %.2f\n", x)  // x = 0.30
}
```



---

## SAISIE DE DONNEES

En Go, pour lire des valeurs depuis l'entrée standard `stdin` et afficher le résultat sur la sortie standards `stdout` se fait via la librairie standard `Scan`

### fmt.Scan - lire une valeur 

La fonction prends un pointeur, et retourne deux valeurs `count`, et `err`
Elle permet de récupérer la saisie depuis la `stdin`, la console.

- `count`: indique combine de valeurs ont réellement pu être reconnues et écrite dans les variables. Si une valeur n'as pas pu être lu, `err` contiendras alors une valeur non `nill` qui descrit l'erreur.

Scan prends des pointeur. On lui passe l'adresse des variables créer au préalable pour qu'il puisse venir stocker dans ces variables les valeurs issue de la `stdin`.

- `&a` : transmet l'adresse mémoire de la variable 
- `a` : transmet la valeur de la variable

```go 
package main 

import "fmt"

func main(){
	var a, b int // on prépare les variables 
	fmt.Scan(&a, &b) // récupération depuis la stdin
	
	sum := a + b
	fmt.Println(sum)
}

// exemple 
package main

import "fmt"

func main() {
	var x int
	count, err := fmt.Scan(&x) // on récupère les valeurs retourné

	fmt.Println("count:", count) // par exemple : count: 1
	fmt.Println("err:", err)     // en cas de succes : err: <nil>
	fmt.Println("x:", x)         // si vous avez saisi 42, alors : x: 42
}
```

### Ignorer une valeur de retour d'une fonction 

Dans le cas où une valeur retournée n'est pas utile, on peut l'ignorer avec un **blank identifier** `_`.

Par exemple, il n'est pas forcément nécessaire d'utiliser la variable `count`, on peut alors l'ignore 

```go 
package main 

import "fmt"

func main(){
	var n int 
	-, err := fmt.Scan(&n)
	
	fmt.Println("err:", err) 
	fmt.Println(n)
}
```

### Panic 

Parfois dans un programme Go, il peut s'arrêter brutalement. C'est ce qu'on appelle une `panic`.

C'est un signal qui dit que quechose de potentiellement dangereux est arrivé. 

### fmt.Fscan - entrée avec source explicite

Lorsque l'on souhaite lire des entrée depuis une autre source que la `stdin` (fichier, chaîne, réseau), on peut venir utiliser cette méthode.

```go
package main 

import (
	"fmt"
	"os"
)

func main(){
	var a, b int
	fmt.Fscan(os.Stdin, &a, &b) // on passe explicitement la stdin
	
	fmt.Println(a * b)
}
```

---

## CONDITION 

### Comparaison 

La comparaison est une expression qui retourne un `bool`. 

```go 
package main 

import "fmt"

func main(){
	a := 10 
	b := 7 
	
	greater := a > b // la variable contient la valeur boolean 
	fmt.Println(greater) // true 
}
```

### Opérateur de comparaison 

On ne peut faire des comparaison que sur des types compatibles.

- `==` : vérifie une égalité 
- `!=` : vérifie une différence 
- `<` : inférieur à 
- `<=` : inférieur ou égale à 
- `>` : supérieur à 
- `>=` : supérieur ou égale à 

```go 
package main 

import "fmt"

func main(){
	x := 5 
	
	isFive := x == 5
	isNotFive := x != 5 
	
	fmt.Println(isFive) // true 
	fmt.Println(isNotFive) // false 
	
	// ============================
	
	score := 82 
	
	isHight := score >= 90
	isMedium := score >= 75 
	
	fmt.Println(isHight) // false 
	fmt.Println(isMedium) // true 
}
```

### If, else, else if

```go 
func main(){
	age := 12 
	
	if age < 7 {
		fmt.Println("Entrée gratuite")
	} else if age < 18 {
		fmt.Println("Billet enfant")
	} else {
		fmt.Println("Billet adulte")
	}
}
```

En Go, on ne peut pas faire une vérif directement sur une variable non nul `if age { }`, il faut obligatoirement utiliser ce code : 

```go 
if age != 0 {
	...
}
```

### Portée de variable 

Une variable peut exister uniquement dans une conditon `if`.

Dans le code, `x` est déclaré dans l'initialisateur de `if`. La variable est donc accessible dans les deux blocs de la condition.

```go 
func main(){
	if x := 10; x%2 == 0 {
		fmt.Println("x est pair :", x) // x est pair 
	} else {
		fmt.Println("x est impair :", x)
	}
}
```

### if avec initialiseur 

Go permet d'écrire un `if` avec une valeur initialisateur. La variable est disponible que pour cette condition.

```go
if init; cond {
	...
} else {
	...
}
```

Par exemple, pour gérer les erreurs de conversion de type 

```go 
package main 

import (
	"fmt"
	"strconv"
)

func main(){
	ageStr := "21"
	
	if age, err := strconv.Atoi(ageStr); err == nil {
		fmt.Println("Age : ", age)
	} else {
		fmt.Println("Age incorrect")
	}
}
```

Les variable `age` et `err` n'existent que dans le bloc de conditions.

### Opération logique 

- `&&` : les deux condition doivent être `true` 2
- `||` : une des conditions doit être `true` 3
- `!` : inversion - 1

```go 
// && exemple 
func main(){
	age := 20
	hasTicket := true 
	
	if age >= 18 && hasTicket {
		fmt.Println("welcome")
	} else {
		fmt.Println("no entry")
	}
}

// || exemple 
func main(){
	role := "editor"
	
	isAdmin := role == "admin"
	isEditor := role == "editor"
	
	canEdit := isAdmin || isEditor
	fmt.Println(canEdit)
}

// ! exemple 
func main(){
	name := "user"
	
	ifEmpty := name == ""
	fmt.Println(isEmpty) // false 
	fmt.Println(!isEmpty) // true
}
```

### Short circuit 

Les opérateurs `&&` et `||` ont un comportement spécifique : 
- `A && B` : la partie B n'est pas évalué si A est `false`
- `A || B` : la partie B n'est pas évalué si A est `true`

```go 
func main(){
	a := 10 
	denom := 0
	
	// partie droit non exécuté sur denom == 0
	ok := denom != 0 && (a/denom > 1)
	flt.Println(ok) // false
}
```

Si la valeur est `0`, on passe directement à `false`. Sinon, le calcul est effectué 

```go 
func main(){
	role := "admin"
	hasPaid := false 
	
	canUseFeature := role == "admin" || hasPaid
	fmt.Println(canUseFeature) // true 
}
```

Si l'user est admin, la condition sera vraie.

### return 

L'opérateur `return` termine immédiatement l'exécution de la fonction courante.

De cette manière, on peut éviter les imbrications profondes de `if`

```go
if isBanned {
	fmt.Println("denied: banned")
	return 
}

if isVIP || (isAdult && hasTicket) {
	fmt.Println("welcome")
}
```

### Exemple DoorGuard 

Programme qui vient vérifier si un utilisateur à les droits d'accès.

```go 
package main 

import "fmt"

func main(){
	// déclaration des variables
	var age int 
	var ticketStr string 
	var vipStr string 
	var bannedStr string 
	
	// récupération saisie stdin 
	fmt.Scan(&age, &ticketStr, &vipStr, &bannedStr)
	
	// conversion texte en boolean 
	hasTicket := ticketStr == "yes"
	isVIP := vipStr == "yes"
	isBanned := bannedStr == "yes"
	isAdult := age >= 18

	// vérification 
	if isBanned {
		fmt.Println("denied: banned")
		return 
	}
	
	if isVIP || (isAdult && hasTicket) {
		fmt.Println("welcome")
	} else {
		fmt.Println("denied: need ticket and age 18+")
	}
}
```

### Switch 

```go
package main

import "fmt"

func main() {
	op := "*" // variale checker dans le switch

	switch op {
	// check plusieurs valeurs 
	case "+", "add":
		fmt.Println("addition")
	case "-":
		fmt.Println("soustraction")
	case "*":
		fmt.Println("multiplication")
	default:
		fmt.Println("operation inconnue")
	}
}
```

- `default` : c'est le cas qui s'exécute si aucune valeur ne match avec les différents case. Il n'est pas obligatoire.

Par défaut, chaque case est `break` automatiquement.. `fallthrough` permet d'empêcher ce comportement.

---

## ERREUR 

### Vérification des erreurs

En go, de nombreuses fonctions retourne `error`, et cette valeur est soit `nil` soit non `nil`.
Lorsque la fonction s'est bien exécuté, la valeur de `error` sera `nil`. 

En Go, on utilise ce pattern : `appel -> check -> use` :
1. Appelle de la fonction 
2. Vérifier `err`
3. Utiliser le résultat de la fonction 

Chaque appel d'une fonction pouvant retourner une erreur doit être gérer directement.

```go 
package main

import (
	"fmt"
	"strconv"
)

func main() {
	var billStr, tipStr string
	_, err := fmt.Scan(&billStr, &tipStr)
	if err != nil {
		fmt.Println("erreur d'entree : 2 valeurs sont necessaires")
		return
	}

	bill, err := strconv.Atoi(billStr)
	if err != nil {
		fmt.Println("le montant doit etre un entier")
		return
	}
	if bill < 0 {
		fmt.Println("le montant ne peut pas etre negatif")
		return
	}

	tipPercent, err := strconv.Atoi(tipStr)
	if err != nil {
		fmt.Println("le pourboire doit etre un entier")
		return
	}
	if tipPercent < 0 {
		fmt.Println("le pourcentage de pourboire ne peut pas etre negatif")
		return
	}

	total := bill + bill*tipPercent/100
	fmt.Println("total :", total)
}
```

Dans le cas ou la valeur doit être uniquement dans un endroit unique, il est possible d'utiliser la syntaxe avec initialiseur. Dans ce cas, les variables n'existe que dans le bloc `if/else` 

```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	if n, err := strconv.Atoi("100"); err != nil {
		fmt.Println("erreur de parsing :", err)
	} else {
		fmt.Println(n + 1) // 101
	}
} 
```

--- 

## BOUCLE 

En Go, il n'existe qu'une seule boucle `for`. 

### Boucle for

```go
for init; cond; post {
	// corps de la boucle 
}

func main(){
	for i := 0; i < 3; i++ {
		fmt.Println("Itération :", i)
	}
}
```

- `init`: création et initialisation d'une variable compteur. Elle se fait une fois au début de la boucle.
- `cond` : condition d'arrête. Elle est vérifié avant chaque itération de la boucle.
- `post` : étapes entre itération. S'exécute après le code de la boucle. Par exemple, augmenter ou diminuer la variable compteur.

### Boucle while 

Pour les boucle while, on utilise également `for` sous une autre forme. 

```go 
for cond {
	// code de la boucle 
}
```

La boucle s'exécute tant que la condition est `true`.

```go
package main

import "fmt"

func main() {
	x := 1
	for x < 100 {
		x = x * 2
	}
	fmt.Println(x) // 128
}
```

### Boucle infinie

```go 
package main 

import "fmt"

func main(){
	balance := 0 
	
	for { 
		var x int 
		-, err := fmt.Scan(&x) // récupère la saisis

		if err != nil {
			break
		}
		
		if x == 0 {
			break
		}
		
		// si valeur trop grande on skip 
		if x > 1_000_000 || x < -1_000_000 {	
			fmt.Println("montant trop grand, j'ignore")
			continue
		}
		
		balance = balance + x
	}
	fmt.Println(balance)
}
```

### break 

L'instruction `break` permet de sortir de la boucle. 

### continue 

Passer à l'itération suivante 

### range 

Cette instruction permet de parcourir les éléments d'une séquence. 

La fonction retourne deux valeurs. (index, et value)

```go 
func main(){
	s := "Go"
	
	for i, v := range s {
		fmt.Println(i, v)
	}
}

// détecteur de caractères 
func main() {
	s := "go#lang"

	hasHash := false
	for _, v := range s {
		if v == '#' {
			hasHash = true
			break
		}
	}

	fmt.Println(hasHash) // true
}
```

- `i` position de l'élément en court d'itération 
- `v` : caractères unicode du caractère de type `rune`

---

## PACKAGE 

Un package est un dossier physique contenant le code source. 

Pour créer un nouveau package, on créer un nouveau dossier dans le projet.