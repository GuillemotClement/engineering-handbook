# Golang 

## FONCTIONNEMENT 

En Go, le compilateur Go transforme le texte source directement en code machine, spécifique à un système d'exploitation et à une architecture de processeur données (Windows, Linux, etc).

Il dispose également d'une gestion automatique de la mémoire. Le garbage collector, le planificateur de goroutines et d'autres mécanismes de service constituent le `Go Runtime`. 
Lors de la compilation, ce Runtime est simplement incorporé à l'intérieur du fichier binaire.

### Go SQK 

Pour transformer le code source en fichier exécutable, l'IDE à besoin d'un ensemble d'outils `Go SQK`. 
C'est un répertoire sur le pc qui contient : 
- des utilitaires pour la compilation et la construction : `go build`, `go run`
- des outils de formatage et de test : `gofmt`, `go test`
- la bibliothèque standard

Le dossier racine dans lequel Go SQK est installé s'appelle **GOROOT**. L'IDE l'utilise comme "source de vérité". Si le SDK n'est pas connecté, l'IDE ne peut pas comprendre la syntaxe de la version actuelle du langage. 

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


## STRUCTURE 

Dans les projets Go, il n'y a pas de hiérarchie stricte de dossier. Il n'existe pas de dossier `src` obligatoire pour les sources.

Pour les petits utilitaires et micro services, les fichiers sources se trouvent directement à la racine du projet, à côté du fichier `go.mod`.

A mesure que le projet grandit, le code est organiser en dossier, chacun devenant un package distinct. Un package étant un dossier physique dans le système de fichier.

```tree
// juste un package main 
tasker/
├── .idea/           # fichiers de service de GoLand, ne pas toucher
├── go.mod           # fichier du module (dependances et version de Go)
├── main.go          # point d'entree (package main)
├── banner.go        # fichier auxiliaire (egalement package main)
|-- logger/          # package logger
|---- format.go
└── README.md
```

Dans les package (dossier), on peut créer des fichiers `.go` comme on le souhaite, il n'y a pas de contrainte fixe liée au nom du package. Par exemple, dans le dossier `logger`, on peut créer un fichier `format.go` dans lequel on vient placer la logique de formatage du texte.

Par convention, le nom du package doit correspondre au nom du dossier. Si le dossier s'appelle `logger`, alors sur toute première ligne des fichier `.go` du package, on retrouve `package logger`. Tous les fichiers du package doivent obligatoirement appartenir à ce package.

Le compilateur fusionne les fichiers d'un même package au moment de la compilation. Cela signifie que des constantes déclarer dans un fichier d'un package, sont également disponible dans un autre fichier du package.

Les import fonctionne dans un fichier donné, et non pour tout le package.

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

```go 
// déclaration de variable 
var x int
var y = 10
z := 10 

// affectation 
z = 20
```

L'utilisation de `:=` n'est autorisé que lorsqu'au moins une des variables est déclarées dans le bloc.
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

En Go, les `{ }` définissent une portée. C'est la zone de code l'intérieur de laquelle les noms de variables sont accessibles.

```go
main {
    x  // lives throughout main
    if {
        y // lives only inside if
    }
    // here y no longer exists
}
```

- `y` n'existe que dans le bloc `if`
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

Une constante désigne une valeur qui est définie une fois et qui ne change jamais. Elle doit également être **connue au moment de la compilation**. 
On utilise généralement les constantes pour des limites strictes, ou des réglages qui ne devraient pas changer.

```go 
const minAge = 18 // déclaration de la constante 

// déclaration groupée
const (
	minAge = 18
	maxUsers = 100 
	appName = "DoorGuard"
)
```
###  untyped 

C'est une constante sans type fixé. La constante contient une valeur, mais celle ci n'est pas encore typer.
Le type sera appliquer au moment de l'utilisation.

```go 
func main() {
	const n = 5 // untyped

	var a int = n
	var b int64 = n

	fmt.Printf("a=%v (%T)\n", a, a) // a=5 (int)
	fmt.Printf("b=%v (%T)\n", b, b) // b=5 (int64)
}
```
### typed

Constante qui possède un type explicite.

```go 
func main() {
	const n = 5     // untyped
	const m int = 5 // typed

	var a int64 = n        // ok
	var b int64 = int64(m) // conversion nécessaire

	fmt.Println(a, b) // 5 5
}
```
### iota - numérotation de constante

Les iota permettent de numéroter les valeurs de façon uniforme pour que le code garde un sens et reste maintenable.

Un `ioat` n'existe que dans un groupe de `const`, commence à `0` sur la première ligne du bloc, et augmente de `1` pour chaque ligne de ce groupe.

Il repart à `0` dans chaque nouveau bloc

```go 
func main() {
	const (
		StatusNew = iota			// 0
		StatusInProgress			// 1
		StatusDone						// 2
	)

	fmt.Println(StatusDone) // 2
}
```

#### Enum - valeur unique 

En Go, pour créer une énumération, on créer un type numérique ( par exemple `type Status int`) et un ensemble de constante de ce type. On obtient un nombre, mais le code se lit comme un nom parlant.

Le compilateur n'autorise pas de mélanger plusieurs types.

```go 
type TaskStatus int // création du type 

// enum
const (
	StatusNew TaskStatus = iota // 0
	StatusInProgress // 1
	StatusDone // 2
)

func main() {
	var s TaskStatus = StatusInProgress
	
	switch s {
	// les case corresponde à l'enum
	case StatusNew:
		fmt.Println("status: new") // status: new
	case StatusInProgress:
		fmt.Println("status: in progress")
	case StatusDone:
		fmt.Println("status: done") // status: done
	default:
		fmt.Println("status: unknown")
	}
}
```

#### Bitmasks - conbinaison

Un enum répond à la question "quelle variante est choisie". Mais parfois il est nécessaire de stocker un ensemble de caractéristiques indépendante.

Par exemple, une tâche peut avoir plusieurs options (urgente, avec notification, archivée, etc), et elles peuvent se combiner dans n'importe quelle configuration.

Pour cela, on utilise un flag : un seul nombre dans lequel chaque bit représente une case à cocher.

Le modèle le plus courante ressemble à `1 << iota`: cela signifie "prends le nombre 1, et décale le bit à 1 de `iota` position".
On obtient alors des puissance de deux : `1`, `2`, `4`, `8` ...

```go
type TaskFlag uint

const (
	FlagUrgent TaskFlag = 1 << iota
	FlagNotify
	FlagArchived
)

func main() {
	var flagsCode uint
	
	fmt.Scan(&flagsCode)

	flags := TaskFlag(flagsCode)

	if flags&FlagUrgent != 0 {
		fmt.Println("flag: urgent")
	}
	if flags&FlagNotify != 0 {
		fmt.Println("flag: notify")
	}
	if flags&FlagArchived != 0 {
		fmt.Println("flag: archived")
	}
}
```

#### Saut et démarrage non nul 

Parfois, on souhaite que la valeur `0` signifie "inconnu", et que les vraies valeurs commence à `1`. C'est généralement ce qu'on réalise sur des valeurs venant de l'extérieur.

avec `iota`, on peut donnez explicitement le premier élément, ou bien sauter la première ligne.

```go
type Level int

// on spécifie la première valeur
const (
	LevelUnknown Level = 0
	LevelLow     Level = iota // iota ici vaut 1, car c'est la deuxieme ligne
	LevelHigh                 // 2
)

func main() {
	fmt.Println(LevelUnknown, LevelLow, LevelHigh) // 0 1 2
}

// AVEC SAUT DE LIGNE 
const (
	_ = iota // nous avons saute 0
	One      // 1
	Two      // 2
)

func main() {
	fmt.Println(One, Two) // 1 2
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

---
## STRING 

En Go, une chaîne est une tableau d'octect. Elle est immuable. On ne peut pas modifier un des éléments interne, on peut seulement créer une nouvelle chaîne à partir de l'ancienne.

### len() 

Retourne la taille en octet de la chaîne. On ne peut pas compter le nombre de caractère via cette méthode.

```go 
func main() {
	s := "Go"
	fmt.Println(len(s)) // 2
}
```

### Indexation de chaine 

Lorsque l'on vient sélectionner un élément de la string, on ne retourne pas un caractère, mais un octect de cette séquence.
Le type est `uint8` ou `byte`.

```go 
func main() {
	s := "Go"

	fmt.Println(s[0], s[1])  // 71 111
	fmt.Printf("%T\n", s[0]) // uint8
	
	fmt.Printf("%c\n", s[0]) // G
	fmt.Printf("%c\n", s[1]) // o
}
```

### Modifier une chaîne 

Pour changer une string, on vient en créer une nouvelle.

```go 
func main() {
	s := "go"

	// on prend "G" et on ajoute la fin de la chaine a partir de l'octet 1
	s = "G" + s[1:]
	fmt.Println(s) // Go
	
	s = "golang"
	fmt.Println(s[:2])  // go
	fmt.Println(s[2:])  // lang
	fmt.Println(s[1:4]) // ola
}
```

### strings 

Package de la librairie standard qui fournit des méthodes pour travailler sur des string.

#### strings.TrimSpace 

Permet de nettoyer le début et la fin de la chaine des espaces.

```go
func main() {
	raw := "   go,  strings, utf-8   "
	clean := strings.TrimSpace(raw)

	fmt.Printf("%q\n", raw)   // "   go,  strings, utf-8   "
	fmt.Printf("%q\n", clean) // "go,  strings, utf-8"
}
```

#### strings.Split 

Permet de découper une string selon un séparateur. Retourne un slice 

```go 
func main() {
	s := "go,strings,utf-8"
	parts := strings.Split(s, ",") //

	fmt.Println(parts) // [go strings utf-8]
}
```

#### strings.Join 

Permet de fusionner les éléments d'un slice . On ajoute un séparateur en deuxième argument.

```go 
func main() {
	parts := []string{"go", "strings", "utf-8"}
	out := strings.Join(parts, ", ")

	fmt.Println(out) // go, strings, utf-8
}
```





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

---

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

On peut également vérifier une condition, et directement affecter le résultat de la comparaison dans une variable boolean 

```go
var canEnter bool = isAdmin && isCodeOK
```

### Valeurs par défaut 

A la déclaration d'une variable, elle prends automatiquement une valeur par défaut.

- `int` : 0
- `float64` : 0.0
- `boolean`: false 
- `string` : ""

### Type personnalisé sur les nombres 

Ce sont des types crée en interne, stocké comme un nombre mais ayant un sens distinct et des règles de compatibilité distinct.

Il aura la même représentation interne qu'un `int`, mais pour le compilateur, c'est une autre entité et il empêchera de la mélanger avec n'importe quel `int`.

Pour déclarer un nouveau type :

```go 
func main() {
	type Status int // on déclare le nouveau type 

	var s Status = 1
	fmt.Printf("%T %v\n", s, s) // main.Status 1
}
```

Si on tente de lui affecter ensuite une valeur différentes de son type, le compilateur empêche la compilation.

```go 
func main() {
	type Status int

	var code int = 2
	var s Status = code // impossible : types différents

	fmt.Println(code) // 2
}
```

Pour réaliser une conversion explicite et pouvoir affecter une valeur au nouveau type : 

```go 
func main() {
	type Status int

	var code int = 2
	var s Status = Status(code) // conversion explicite

	fmt.Printf("code=%d (%T)\n", code, code) // code=2 (int)
	fmt.Printf("s=%d (%T)\n", s, s)          // s=2 (main.Status)
}

// conversion inverse 
func main() {
	type Status int

	var s Status = 2
	code := int(s) // on reconvertit en int

	fmt.Printf("code=%d (%T)\n", code, code) // code=2 (int)
}
```

Pour garder un sens au code, il utilise alors des constante pour connaitre la signification de chaque valeur.

```go 
func main() {
	// déclartion du nouveau type 
	type Status int
	// déclaration des constante pour stocker la signification
	const (
		StatusNew        Status = 0
		StatusInProgress Status = 1
		StatusDone       Status = 2
	)
	
	var s Status = StatusDone // on affecte la valeur de la const
	fmt.Println(s) // 2
}
```



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

La conversions d'un type large vers un type plus étroit comme `int64` vers `int32` peut provoquer des problème.

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

## OPERATEURS

### Opérateur arithmétique 

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

Les opérations sont typé, par exemple, des opérations sur des `int` produisent des `int`.

#### Division entière

L'opérateur `/` de faire réaliser des division entière. Si deux `int` sont utilisé, on aura également un `int` pour le résultat. La partie décimale est ignoré.

```go
func main() {
	fmt.Println(7 / 2) // 3
	fmt.Println(9 / 3) // 3
	fmt.Println(1 / 2) // 0
}
```

#### Division des nombres négatifs 

La division entière se fait vers zéro.

```go 
func main() {
	fmt.Println(-7 / 2) // -3
	fmt.Println(-1 / 2) // 0
}
```
#### Modulo

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

#### Calcul de pourcentage 

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

### Opération binaire 

Un nombre peut être perçu comme un ensemble de bits. Cette approche est utile lorsqu'il faut stocker plusieurs attributs indépendants dans un seul nombre, et vérifier rapidement ce qui est actif.

Généralement, on utilise les flags (ensemble d'options, d'autorisation ou de mode), et pour cela on garde tout dans un seul nombre et on manipule les bits.

#### Ecriture binaire 

La représentation binaire permet d'écrire des masque directement dans le code pour les lire.

Pour cela, on utilise des préfixe : 
- `ob...` - écriture binaire 
- `0x...` : hexa
- `0o` : octal 

```go 
const (
	a = 12       // decimal
	b = 0b1100   // binaire
	c = 0x0C     // hex
	d = 0o14     // octal
)
```

L'écriture binaire est adapté pour les drapeaux, car on peut directement voir quel bits sont actif.

Pour simplifier les masques longs, on peut utiliser `_` pour regrouper les bits 

```go 
const mask = 0b1111_0000_0011_0101
```

On regroupe ici 4 groupes de bits.

Un masque est un nombre dans lequels certains bits nous interessent. Souvent un masique contient exactement un bit active, il représente alors un drapeau.

`%b` permet d'afficher un nombre binaire avec le `fmt.Printf`

#### Décalage à gauche - << 

Permet de déplacer les bits d'un nombre vers la gauche.  La valeur à droite indique de combien de colonne on décale vers la gauche

```go 
1 << 0 => 0000 0001 | 1 
1 << 1 => 0000 0010 | 2
1 << 3 => 0000 1000 | 8 
```

On peut changer rapidement la valeur binaire.

```go 
func main() {
	var mask uint = 1 << 3
	fmt.Printf("mask=%b (%d)\n", mask, mask) // mask=1000 (8)
}
```

#### OR binaire - |  - Activer

Lorsque l'on stocke un ensemble de drapeau dans un seul nombre, l'opération "activer un drapeau" se fait avec `|` 

```go 
func main() {
	var a uint = 1 << 0 // 0001
	var b uint = 1 << 2 // 0100

	// démarre à 0 (0000)
	var flags uint = a | b // on active le 1 et le 3 ref a et b
	fmt.Printf("%b\n", flags) // 101
}
```

#### AND binaire - &  - Vérification 

Permet de faire une vérification d'octets actif. 

```go
func main() {
	const (
		read  uint = 1 << 0 // 001
		write uint = 1 << 1 // 010
	)

	var perms uint = read // lecture seule

	// check si le deuxieme octet est actif 
	fmt.Println(perms&write != 0) // false
	// 
	fmt.Println(perms&read != 0)  // true
}
```

#### AND NOT binaire - &^ - Désactiver 

Permet de désactiver 

```go 
func main() {
	const (
		read  uint = 1 << 0 // 001
		write uint = 1 << 1 // 010
		exec  uint = 1 << 2 // 100
	)

	var perms uint = read | write | exec // 111
	perms = perms &^ write // on passe le 2 bit à 0

	fmt.Printf("%b\n", perms) // 101
}
```

#### ^ - bascule et inversion 

- `a ^ b`: XOR ou exlusif 
- `^a` : inversion 

On utilise `XOR` lors que l'on souhaite tout toggle. 

```go 
func main() {
	var x uint = 0b1010
	var y uint = 0b1100

	fmt.Printf("%b\n", x^y) // 110
}

func main() {
	const debug uint = 1 << 0

	var flags uint = 0
	flags = flags ^ debug // debug active
	flags = flags ^ debug // debug desactive

	fmt.Println(flags) // 0
}
```

#### Décalage à droite >> - extraction 

```go 
func main() {
	var flags uint = 0b10100 // le bit 2 est active, le bit 4 est active

	bit2 := (flags >> 2) & 1
	bit3 := (flags >> 3) & 1

	fmt.Println(bit2, bit3) // 1 0
}
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

### Boucle for 

En Go, il n'existe qu'une seule boucle `for`. 

```go
for init; cond; post {
	// corps de la boucle 
}
```


```go 

func main(){
	for i := 0; i < 3; i++ {
		fmt.Println("Itération :", i)
	}
}
```

- `init`: création et initialisation d'une variable compteur. Elle se fait une fois au début de la boucle.
- `cond` : condition d'arrête. Elle est vérifié avant chaque itération de la boucle.
- `post` : étapes entre itération. S'exécute après le code de la boucle. Par exemple, augmenter ou diminuer la variable compteur.

#### Boucle while 

La boucle s'exécute tant que la condition est `true`.

```go 
for cond {
	// code de la boucle 
}
```

```go
func main() {
	x := 1
	for x < 100 {
		x = x * 2
	}
	fmt.Println(x) // 128
}
```

#### Boucle infinie

Pour une CLI par exemple, on attends la saisie de l'utilisateur tant qu'il ne souhaite pas sortie explicitement de la boucle.
Dans ce cas, on peut utiliser une boucle infinie : 

```go 
func main(){
	balance := 0 
	
	for { 
		var x int 
		-, err := fmt.Scan(&x) // récupère la saisis

		if err != nil {
			break
		}
		
		// sortie de la boucle 
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

L'instruction `continue` permet de passer à l'itération suivante. 

### range - parcourir une séquence

Cette instruction permet de parcourir les éléments d'une séquence. La fonction retourne deux valeurs. (index, et value)

```go 
for index, value := range s {
	// code de la boucle 
}
```

- `index` : la position de l'élément dans la séquence 
- `value` : la valeur en cours d'itération 

#### Parcourir une chaîne 

```go 
func main(){
	message := "Go"
	
	// on parcour la chaîne 
	for i, v := range message {
		fmt.Printf("Index: %v\n", i)
		fmt.Prinf("Valeur: %v\n", v)
	}
}

```

#### Vérification de caractère 

Pour vérifier un caractère unique, on utiliseras un `rune`.

```go
func main() {
	s := "go#lang"

	hasHash := false
	for _, v := range s {
	// on compare avec une rune
		if v == '#' {
			hasHash = true
			break
		}
	}

	fmt.Println(hasHash) // true
}
```

#### Remplacement de caractères 

```go 
package main

import "fmt"

func main() {
	var levelRow string
	fmt.Scan(&levelRow)

	var fixedRow string // contient la chaîne corriger
	var replacedCount int // cumul des caractères remplacer 

	for _, v := range levelRow {
		if v == '#' {
			fixedRow += "*" // on ajoute dans la string corriger 
			replacedCount++ // on incrémente le compteur 
		} else {
			fixedRow += string(v) // on ajoute le caractère de la string original
		}
	}

	fmt.Println(fixedRow)
	fmt.Println(replacedCount)
}
```

### Protection contre les boucle infinie 

Si une boucle provoque potentiellement une boucle infinie, on peut utiliser ce pattern pour log ce qui se passe, et ajouter une limite pour le debug.

```go 
package main

import "fmt"

func main() {
	i := 0
	limit := 20

	for i < 5 {
		i++
		fmt.Println("i =", i) // i = 1, i = 2, ...

		limit--
		if limit == 0 {
			fmt.Println("stop: safety limit reached")
			break
		}
	}
}
```

### Pattern de boucle 

#### Accumulateur 

Débute à zéro, et cumule les valeurs. La variable stocke le résultat intermédiaire, et chaque itération de la boucle, met à jour la variable.

```go 
func main(){
	var n int  
	fmt.Scan(&n)
	
	sum := 0 // accumulateur 
	for i := 0; i < n; i++ {
		var x int 
		fmt.Scan(&x) // récupère une valeur saisis
		sum = sum + x // cumul à chaque tour de boucle 
	}
	
	fmt.Println(sum)
}
```

#### Valeur maximale

L'idée principale est la suivante : pour le maximum il est important de savoir d'où vient la valeur initiale.

##### Initialisation par le premier élément 

```go 
func main(){
	var n int 
	fmt.Scan(&n)
	
	// pour une entrée vide, on affiche 0 
	if n <= 0 {
		fmt.Prinln(0)
		return 
	}
	
	// le premier élément devient le max
	var max int 
	fmt.Scan(&max)
	
	for i := 1; i < n; i++ {
		var x int 
		fmt.Scan(&x)
		// si plus grand, on change la valeur max 
		if x > max {
			max = x
		}
	}
}
```

##### Valeur max et index 

PArfois, on as besoin de connaitre la valeur maximal, mais également sa postition. 

```go 
func main() {
	var n int
	fmt.Scan(&n)

	if n <= 0 {
		fmt.Println(-1) // pas d'éléments, donc pas d'indice
		return
	}

	var max int
	fmt.Scan(&max)
	maxIndex := 0 // stocke l'index de la valeur max

	for i := 1; i < n; i++ {
		var x int
		fmt.Scan(&x)
		if x > max {
			max = x
			maxIndex = i
		}
	}

	fmt.Println(maxIndex) // par exemple : 4
}
```

#### Recherche avec drapeau 

La recherche c'est lorsque l'on souhaite savoir si quelque chose apparaît dans les données. On utilise un flag, et si lever, on `break`.

```go 
// Recherche au flag 
func main() {
	var n, target int
	fmt.Scan(&n, &target)

	found := false // drapeau init a false 
	for i := 0; i < n; i++ {
		var x int
		fmt.Scan(&x)
		// si on trouve la donnée, on leve le drapeau 
		if x == target {
			found = true
			break
		}
	}

	fmt.Println(found) // true ou false
}

// Recherche du premier qui match 
func main() {
	var n int
	fmt.Scan(&n)

	found := false
	firstNeg := 0

	for i := 0; i < n; i++ {
		var x int
		fmt.Scan(&x)
		if x < 0 {
			firstNeg = x
			found = true
			break
		}
	}

	if found {
		fmt.Println(firstNeg) // par exemple : -7
	} else {
		fmt.Println(0) // convenons que s'il n'y a pas de nombres négatifs, on affiche 0
	}
}
```

---

## PACKAGE 

Un package est un dossier contenant le code source sur le disque. Il n'existe pas d'espaces de noms abstraits détachés du système de fichiers.


### Package et espace de nom 

Les package Go permettent de grouper le code par sens, et donner des règles sur qui peut voir quoi et l'utiliser.

La ligne `package` en haut du fichier permet de définir le package auquel le fichier appartient.

Un fichier n'est pas a lui seul un "module du programme". Le module c'est le package, et le fichier n'est qu'une morceau du module.

```go
// définition du package du fichier
package main  
```

Cette ligne indique que le fichier appartient au package `main`. Tous les fichiers appartenant au même package dans un même dossier partage le même espace de visibilité.

La frontière est le `package` est pas le fichier.

### package main 

Le package `main` est spécial. C'est le point d'entrée d'une application. Le programme démarre à partir de celui ci.

Dans ce package, on retrouve la fonction `main()`, qui est le point d'entrée par lequel  le programme se lance. C'est dans cette fonction que l'on viens connecter les différents composant de l'application.

On retrouve ce package dans le cas d'une application exécutable. 

### Règle d'exportation 

En Go, pour définir si un élément (constante, type, variable, fonction )est exporté ou non c'est la première lettre qui définit ce comportement : 
-  majuscule : il est exporté et accessible depuis d'autre package 
- minuscule : il est non exporté, accessible uniquement à l'intérieur de son package.

L'export d'élément n'as aucune incidence sur la sécurité. Il s'agit uniquement d'un contrat et d'une promesse.
On indique juste que cet élément est disponible en dehors du package.

On exporte uniquement les éléments intéressant pour en dehors du package. La logique interne au package reste privée, il n'y a aucun intérêt à rendre cela public.

### Import path 

L'import path permet au compilateur et aux outils d'aller chercher le code nécessaire. Il doit pointer sur un package unique.

```go 
package main

import (
	"fmt"
	"math/rand"
)

func main() {
	// on fait référence au package math/rand
	fmt.Println(rand.Intn(10)) // par exemple : 7
}
```

Pour utiliser ensuite les fonction du package, on fait référence au package, puis la fonction que l'on souhaite utiliser.

Généralement, le `package name` correspond au dernier segment du chemin. 

#### Package avec le même nom 

```go 
package main 

import (
	"crypto/rand"
	"math/rang"
)

func main(){}
```

Ce code ne compileras pas car dans un fichier, il ne peut pas avoir deux package avec le même nom d'import

### Imports 

`import` permet d'ajouter une dépendance dans le fichier, et ainsi d'utiliser des fonctions d'un package.
L'instruction indique au compilateur quels packages est nécessaire à ce **fichier précis**

```go 
// import seul 
import "fmt"

// import groupé
import (
	"fmt"
	"strconv"
)
```

#### Alias d'import 

Dans le cas ou l'on souhaite importer plusieurs package ayant le même nom, ou si le nom de package n'est pas idéale, on peut venir créer un alias. 
Cela permet de renommer un package dans un fichier.

```go
// améioration de la lisibilité 
package main 

import ( 
	"fmt"
	s "strings" // création d'un alias 
)

func main(){
	var title string 
	fmt.Scan(&title)
	
	title := s.TrimSpace(title) // on fait référence à l'alias du package 
	fmt.Println("task:", title)
}

// =================================
// conflit de nom 
package main 

import (
	crand "crypto/rand"
	"fmt"
	mrand "math/rand"
)

func main(){
	_ := crand.Reader
	fmt.Println(mrand.Intn(10))
}
```

#### Blank import 

Le blank import est un lorsque l'on importe un package sans introduire son nom dans le code. 
On l'utilise dans le cas où le package doit être chargé et exécuter sans utiliser ces fonctions ou type.

En go, des package peut avoir du code d'initialisation, qui s'exécute automatiquement au démarrage du programme. Par exemple, pour les pilotes de base de donnée. On les appelles pas directement, mais il sont enregistrée à l'intérieur de `database/sql`.

```go 
package main

import (
	"fmt"
	_ "math"
)

func main() {
	fmt.Println("started") // started
}
```

### init()

Fonction spécial qui est appelée automatiquement, et s'exécute avant `main()`. Le runtime Go exécute l'initialisation du paquet (mise en place des variables globales, et les appels à `init()`), puis ensuite passe à `main()`.

La fonction ne prends aucun paramètres et ne retourne aucune valeur de retour.

Si plusieurs fonctions `init()` sont déclarée, ils s'exécutent tous avant.

```go 
package main 

import "fmt"

func init(){
	fmt.Println("init")
}

func main(){
	fmt.Println("main")
}
```

Ce système provoque des effet secondaire et doit être utiliser avec précaution.

#### init explicite 

Lorsqu'une fonction `init()` commence à grossir, on peu venir créer une initialisation explicite, pour rendre le code plus explicite.

On créer une fonction, par exemple `initApp()`, que l'on appelle ensuite dans le `main()`

```go 
package main 

import (
	"fmt"
	"time"
)

func initApp() (string, time.Time){
	prefix := "stats: "
	if prefix == ""{
		return "", errors.New("empty prefix")
	}
	return prefix, nil
}

func main(){
	// appel de la fonction d'initialisation
	prefix, err := initApp()
	if err != nil {
		fmt.Println("init error:", err)
		return
	}
	fmt.Println(prefix + "ready")
}
```

### Anti-patterns de package 

En Go, un package est une **frontière de responsabilité**, et un **contrat pour les autres parties du code**.

#### Etat global et config global 

Lorsque l'on déclare une variable dans la portée globale, elle vit plus longtemps que ce que l'on pense, et elle est modifiable depuis de nombreux endroits, le comportement peut devenir floue (changement de valeur innatendu, ...)

```go 
package main 

var lastResult int // état global => n'importe qui peut modifier

func addToLast(x int){
	lastResult += x
}
```

Pour conserver un état "global", on préfère la déclarer près de `main()`, et la transmettre explicitement. 

```go
package main

func add(a, b int) int {
	return a + b
}

func main() {
	last := 0 // variable "global"
	last = add(last, 10)
	_ = last // ici, on peut ensuite imprimer last via fmt.Println
}
```

Attention, cela reste une mauvaise pratique. L'import de ce paquet dépend de l'historique des appels, et pas seulement des paramètres d'entrée des fonctions. 

Pour garder une bonne pratique, le mieux est d'utiliser une constante pour une variable qui ne doit pas changer de valeur, et si la valeur doit changer, la définir près de la logique

L'état mutable doit être le plus local et explicite.

```go 
package main 

const appName = "CalcCLI"

func main(){
	_ := appName
}

func formatResult(n int, debug bool) string {
	if debug {
		return "DEBUG: result=" 
	}
	return "result="

}

```

#### Dépendances inutiles et imports qui s'étendent 

Les imports peuvent être "utile", mais conceptuellement inutiles. Par exemple `fmt` dans la logique pour un `Print()` de débug.

```go
// anti-pattern 
package main 

import "fmt"

func add(a, b int) int {
	fmt.Println("adding ...") // debug temporaire qui devient un comportement de la fonction 
	return a + b
}
```

En plus du "bruit", la fonction `add` fait maintenant deux choses : calcule et imprime. 

Pour améliorer, on peut utiliser ce pattern 

```go 
package main 

import "fmt"

// responsable uniquement de la logique 
func add(a, b int) int {
	return a + b
}

func main(){
	fmt.Println(add(2, 3)) // ici on fait le log 
}
```

#### Fuite des détails d'implémentation via l'API 

Si le package renvoie ou accepte dans ses fonctions publiques des types qui sont des détails d'implémentation, on lie le code externe à ces détails.
Pour faire évoluer le code, cela peut devenir compliquer.

Les fonction publiques d'un paquet doivent communiquer avec le monde exterieur via des types simples et attendus (`string`, `int`, `bool`) et des résultats clairs.

#### Package utils 

On peut retrouver dans les projet Go, des package `uils`, `helpers` ou `common` et y mettre tout et n'importe quoi. 

Pour corrige cela, on vient nommer correctement en se basant sur le sens. 

| Nom frequent | Pourquoi c'est peu lisible           | Alternative plus previsible                                |
| ------------ | ------------------------------------ | ---------------------------------------------------------- |
| `utils`      | « il y a tout » = « rien de precis » | parse, format, calc (selon le role)                        |
| `common`     | commun a quoi ?                      | config, constants (si c'est vraiment de cela qu'il s'agit) |
| `helpers`    | aider qui ?                          | nom selon le domaine : tasks, users                        |
| `data`       | les donnees, c'est trop large        | storage, input, output (selon l'objectif)                  |
#### Architecture propre simple 

Pour garder un projet maintenable, on peut utiliser ce type d'architecture.

```tree
calccli/
├── main.go     // entree/sortie, communication avec l'utilisateur
├── parse.go    // analyse des nombres et des commandes
└── ops.go      // arithmetique (fonctions pures)
```

Chaque fichier possède sa responsabilité. 

Par exemple, le fichier `ops.go` est charger d'implémentation les fonctions responsables des calculs 

```go 
package main

func apply(op string, a, b int) int {
	if op == "add" {
		return a + b
	}
	return a - b
}
```

Dans le fichier `main.go`, on viens orchestrer l'ensemble 

```go 
package main

import "fmt"

func main() {
	var op string
	var a, b int
	fmt.Scan(&op, &a, &b)
	fmt.Println(apply(op, a, b))
}
```

---

## FONCTION 

Un fonction doit être déclaré, puis appeler. A l'appel, le code dans la fonction est exécuté.

```go
// syntaxe 
func <name>(<param> <type>, <param2> <type>) <type_retour> {
	// code exécuter à l'appel de la fonction 
}
```

- Les paramètre passés à la fonction doivent être typés
- Le retour de la fonction doit être typé

```go 
package main 

import "fmt"

func printIfPositive(x int){
	if x <= 0 {
		return 
	}
	fmt.Println("positive:", x)
}

func add(a int, b int) int {
	return a + b
}

// si plusieurs paramètres ont le même type 
func reactArea(width, height int) int {
	return width * height
}

func main(){
	// appel de la fonction 
	printIfPositive(3)
	
	fmt.Println(add(2, 3)) // 5
	
	fmt.Println(rectArea(3, 4)) // 12
}
```

### Contrat de fonction 

Chaque fonction doit avoir un contrat clair : qu'attends la fonction, qu'est ce qui est retourné

Les nom des paramètres doit également être clair, de cette manière, cela améliore la lisibilité du code.

### Retour de fonction multiple 

En Go, une fonction peut retourner plusieurs valeurs. Dans la signature, ces résultats s'écrivent entre parenthèses.

Une fonction renvoie un résultat utile, soit indique que quelque chose n'a pas marché via une `error`.

`error` est une interface avec la méthode `Error() string`, qui signifie que toute erreur doit pouvoir s'expliquer en texte.

```go
package main 

import "fmt"

// la fonction retourne deux string 
func swap(a, b strin) (string, string){
	return b, a
}

func main() {
	// on prépare deux variables pour récupérer les valeurs de la func 
	left, right := swap("L", "R")
	fmt.Println(left, right) // R L
}
```

### Ignorer une valeur retourner 

Toutes les valeurs retourner par une fonction ne sont pas forcément utiliser. Dans ce cas, on utilise `_` pour l'ignorer.

```go 
package main 

import "fmt"

func pair() (int, int) {
	return 10, 20
}

func main() {
	// on ignore la seconde valeur retourné
	x, _ := pair()
	fmt.Println(x) // 10
}
```

### Gestion d'erreur 

Les fonctions qui peuvent échouer, retourne une `err`. 

Pour créer une erreur, on utilise `errors.New("message")`. On lance cette erreur, et on viens la traiter à l'endroit où la fonction est appelée.

Dans le cas ou une erreur arrive dans la fonction, la valeur retourné devras être celle par défaut : 
- `0` pour un `int`
- `""` pour une `string`
- `0.0` pour un `float`
- `false` pour un `bool`

```go 
package main 

import (
	"errors"
	"fmt"
)

func safeDiv(a, b int) (int, error) {
	if b == 0 {
		// on lance une erreur 
		// on retourne 0 pour la valeur 
		// la nouvelle erreur 
		return 0, errors.New("division by zero")
	}
	// on retourne la valeur 
	// et nil pour indiquer l'absence d'erreur 
	return a / b, nil 
}

func main(){
	q, err := safeDiv(10, 0)
	// check résultat de la fonction 
	if err != nil {
		fmt.Println("error:", err) // on affiche l'erreur 
		return // termine l'exécution 
	}
	
	fmt.Println("q =", q)
}
```

### Resultat nomme

On peut directement nommer les retour de la fonction depuis la signature. Ces variables seront directement disponible dans le corps de la fonction. Elles sont directement initialiser avec leur `zero value`.

Un `return` sans valeur est autorise uniquement sur les résultats sont nomme. 

```go 
package main 

import (
	"fmt"
)

// les variable min et max sont dispo dans la fonction
func minMax(a, b int) (min int, max int) {
	if a < b {
		min = a
		max = b
		// les variable min et max sont automatiquement retourner
		return
	}
	
	min = b
	max = a
	return
}
```

Il est toujours possible de retourner explicitement les resultats lors du `return`

```go
func parseExpr(aS, opS, bS string) (a int, op string, b int, err error){
	if opS != "+" && opS != "-" && opS != "*" && opS != "/" {
		return 0, "", ), errors.New("unknow operator")
	}
	
	return
}
```

### Fonction variadiques 

Fonctions qui prends un nombre indéterminé de paramètres. Le paramètre variadiques est toujours le dernier dans la liste des paramètres.

Pour déclarer une paramètre variadiques, on utilise `<name> ...T`. Lorsque l'on appelle la fonction, on pourras passer autant d'arguments que souhaité dans la fonction.

```go 
func sum(nums ...int) int {
	total := 0 
	// on parcours les argument reçus 
	for _, v := range nums {
		total += v
	}
	return total 
}

func main(){
	fmt.Println(sum(1, 2, 3)) 
}
```

Pour passer des arguments multiples dans une fonctions variadiques, on utilise `...args` :

```go 
func sum(nums ...int) int {
	var sum int
	for _, num := range nums {
		sum += num
	}
	return sum
}

func printSum(prefix string, nums ...int) {  
	// on passe les différents valeurs à la fonction variadiques 
    fmt.Printf("%s %d\n", prefix, sum(nums...))  
}
```



### Fonction anonymes 

Une fonction anonyme est une fonction sans nom. Elle permet de transmettre un comportement.

```go 
func (x int) int {
	return x * 2
}
```

#### Appel immédiat (IIFE)

Parfois, il peut être intéressant d'exécuter un bloc de code directement (IIFE).

Les parenthèses à la fin permette de déclencher l'appel de la fonction anonyme.

```go 
func (){
	fmt.Println("=== MiniCalc ===")
	fmt.Println("Entrez: a op b rule")
}()
```

On peut également passer un paramètre et un résultat à la fonction anonyme 

```go 
res := func(x int) int {
	return x * x
}(5) // on appel la fonction anonyme et on lui passe 5

fmt.Println(res) // 25
```

#### Calculer puis assigner 

Parfois, le calcul exige un `if` mais on ne souhaite pas encombrer `main` avec des variables temporaire.

```go 
x := -10

sign := func(n int) string {
	if n < 0 {
		return "negative"
	}
	
	if n == 0 {
		return "zero"
	}
	
	return "postive"
}(x)

fmt.Println(sign) // negative
```

#### Fonction comme valeur 

On peut venir stocker une fonction dans une variable.

```go 
f := func(x int) int {
	return x + 1
}

fmt.Println(f(10)) // 11
```

#### Type de fonction 

On peut venir définir un type pour les fonction. 

```go 
type Rule func(int) int 
```

#### Fonction d'ordre supérieur 

Fonction qui accepte une autre fonction et l'utilise à l'intérieur. 

```go 
// type de fonction 
type Rule func(int) int

func apply(x int, r Rule) int {
	return r(x) 
}

// utilisation avec une fonction nommée
func double(x int) int { return x * 2}

fmt.Println(apply(10, double)) // 20

// utilisation avec une fonction anonyme 
fmt.Println(apply(10, func(x int) int { return x * x})) // 100 
```

Le deuxième paramètre de la fonction `apply` est une fonction. Elle retourne le résultat de la fonction passé en paramètre.

Un autre exemple avec une fonction qui prends un nombre de répétition et une action 

```go 
func repeat(n int, action func()) {
	for i := 0; i < n; i++ {
		// chaque itération on appelle la fonction passé
		action()
	}
}

// utilisation 
repeat(3, func(){
	fmt.Println("hi")
})
```

### Closure 

Une closure est une fonction qui utilise des variables de la portée externe. 

Par exemple, pour implémenter un compteur. La fonction `makeCounter` retourne une fonction qui incrémente un nombre interne à chaque appel 

```go
func makeCounter() func () int {
	n := 0
	return func() int {
		n++
		return n
	}
}

c := makeCounter()
fmt.Println(c()) // 1
fmt.Println(c()) // 2
fmt.Println(c()) // 3
```

---
## TABLEAU  

Le tableau permet de contenir un ensemble de valeur de taille fixe connu à l'avance. Les données sont stockées côte à côte, et on y accède via un indice.
Le premier élément est placé à l'index `0`
### Création d'un tableau 

Pour déclarer une tableau, on utilise `[N]T` ou `N` est le nombre d'éléments du tableau, et `T` le type des éléments de ce tableau.

```go 
func main(){
	week := [3]int{10, 20, 30} // création et initialisation d'un tableau
	week[1] = 99 // on modifie l'élément à l'index 1 => le deuxième.
}
```

### Copie de tableau 

Lorsque l'on réalise une affectation, tous les éléments du tableau sont **copiés**. Chaque copie possède sa propre référence.

```go 
func main(){
	a := [3]int{1, 2, 3}
	b := a // on copie le tableau dans la variable => les deux sont indépendnants
	b[0] = 00
	
	
	fmt.Println("a:", a) // a: [1 2 3]
	fmt.Println("b:", b) // b: [99 2 3]
}
```

### Passage de tableau à une fonction 

```go
// ma fonction attends un tableau de 7 entier 
func sumWeek(week [7]int) int {
	total := 0
	for _, v := range week {
		total += v
	}
	return total
}

func main() {
	week := [7]int{100, 0, 50, 20, 10, 0, 70}
	// on passe le tableau de 7 entier 
	fmt.Println(sumWeek(week)) // 250
}
```

### Comparaison de tableau 

On peut utiliser `==` pour comparer deux tableau, étant donné que c'est une copie d'éléments 

```go 
func main() {
	a := [2]int{1, 2}
	b := [2]int{1, 2}
	fmt.Println(a == b) // true
}
```

---
## SLICE 

Un slice est une "fenêtre" qui pointe sur un tableau. Il se comporte comme un tableau. Le slice est une vue partielle d'un tableau, et différents slice peuvent regarder les même données.

### Création de slice 

Pour créer un slice, on ne définit pas le nombre d'éléments.

```go 
func main(){
	s := []int{1, 2, 3}
	s[0] = 99
	
	fmt.Println(s) // [99 2 3]
}
```

### Affectation de slice 

Lorsque l'on fait une affectation de slice, on ne copie pas les éléments, mais on copie la description du slice. Les éléments eux même restent partagés. 

La modification d'un slice qui partage le même tableau, entraine la modification pour tous les slices partageant le même tableau.

```go 
func main(){
	s := []int{1, 2, 3}
	t := s // copie de la référence 
	t[0] = 99 // modif pour les deux slices 
	
	fmt.Println("s:", s) // s: [99 2 3]
	fmt.Println("t:", t) // t: [99 2 3]
}
```

### Passage de slice au fonction 

```go 
// la fonction attends un slice de int 
func sumDays(days []int) int {
	total := 0
	for _, v := range days {
		total += v
	}
	return total
}

func main() {
	days := []int{100, 0, 50, 20}
	// on passe le slice à la fonction 
	fmt.Println(sumDays(days)) // 170
}
```

### Comparaison de slice 

On ne peut pas utiliser de `==` pour comparer deux slice 

```go 
func main() {
	s := []int{1, 2}
	_ = s
	fmt.Println(s == s) // erreur de compilation : les slices ne peuvent pas etre comparés ainsi
}
```

### Slice Header 

Un **slice header** est une petite structure descriptive indiquant ou se trouvent les éléments et combien sont disponible.
La valeur d'un slice n'est pas le "conteneur avec des éléments", mais une description d'une fenêtre permettant de voir un ensemble d'éléments.

#### len - nombre d'élément du slice 

`len(s)` permet de connaitre combien d'éléments sont accessibles à travers le slice. Il indique combien d'éléments peuvent être lire et écrit.

- premier indice : `0`
- dernier indice : `len(s) - 1`
- `len(s)` : hors limite, et provoque une panique lors de l'accès

```go 
func main() {
	s := []int{10, 20, 30}
	fmt.Println(len(s)) // 3 - le nombre d'élément dans le slice 

	fmt.Println(s[0]) // 10 - élément à l'indice 1 
	fmt.Println(s[2]) // 30 - élément à l'indice 3
	fmt.Println(s[3]) // panic: index out of range
}
```

#### cap - réserve du slice 

La capacité `cap` définit combien d'éléments peuvent être stocker dans ce segment mémoire, à partir du début actuel du slice, sans déplacement ailleurs.
C'est la réserve qui permet d'agrandir le slice.

Il ne donne pas droit d'accéder par indice, l'indexation dépend de `len`

```go 
func main() {
	s := make([]int, 2, 5) // len=2, cap=5
	fmt.Println(len(s), cap(s)) // 2 5
	fmt.Println(s)              // [0 0]
}
```

- `len = 2` : deux éléments sont disponible dans le slice. Initialiser à zéro 
- `cap = 5` : jusqu'à 5 éléments peuvent tenir dans ce bloc mémoire.

#### pointer 

Dans un slice, se trouve une information indiquant où se trouve le premier élément (l'adresse du début des données). Deux valeurs de type slice différentes peuvent référence un même ensemble d'éléments.

```go 
func main() {
	s := []int{1, 2, 3}
	t := s       // we copy the header, not the elements
	t[0] = 99

	fmt.Println(s) // [99 2 3]
	fmt.Println(t) // [99 2 3]
}
```

- `t := s` : copie uniquement le header du slice ( pointer / len / cap)
- le `pointer` de `t` et celui de `s` pointe vers le même tableau sous jacent (backing array)
- la modification de `t[0]` modifie aussi les données pour `s` car c'est le backing array qui est modifié. 
### nil-slice 

Pour un slice `[]T`, la valeur zéro est `nil`, il n'y a pas de slice, il ne pointe vers aucun élément.

```go 
func main() {
	// initialisation du slice 
	var nums []int // valeur nil => il ne pointe sur rien 

	fmt.Println(nums == nil)          // true
	fmt.Println(len(nums), cap(nums)) // 0 0
	fmt.Println(nums)                 // []
}
```

### make - création de conteneur 

`make` permet de créer des conteneur (slices, maps, canaux). 
Avec le make, on peut définir de manière explicite le nombre d'éléments souhaité, et la capacité du slice.

```go 
tasks := make([]string, 0)
```

Cet instruction permet de créer un slice de `string` de longueur `0`, sans éléments. Le slice existe, et peut être transmis, retourne par des fonctions et étendu via `append`

Pour les slice `make` prends deux formes :

```go
make([]T, n) // longueur x: les éléments existe et sont remplis par la zero valu
make([]T, n, cap) // longueur n et cap >= n
```

Par exemple, pour créer un slice de 3 élément nul 

```go 
func main(){
	// création d'un slice de 3 int nul 
	a := make([]int, 3)
	fmt.Println(a, len(a), cap(a)) // [0 0 0] 3 3
}

func main() {
	// création d'un slice de 0 int avec une capa de 3
	b := make([]int, 0, 3)
	fmt.Println(b, len(b), cap(b)) // [] 0 3
}
```

### Slice vide 

Un slice vide est un slice pour lequel `len == 0` mais qui n'est pas égale à `nil`. C'est un slice de zéro élément.

Il existe deux manière d'obtenir un slice vide 

#### littéral 

Cette manière indique clairement que c'est un slice vide mais qui existe.

```go
func main() {
	tasks := []string{}

	fmt.Println(tasks == nil)           // false
	fmt.Println(len(tasks), cap(tasks)) // 0 0
	fmt.Println(tasks)                  // []
}
```

#### make 

Avec cette manière on définis explicitement la taille du slice 

```go 
func main() {
	tasks := make([]string, 0)

	fmt.Println(tasks == nil)           // false
	fmt.Println(len(tasks), cap(tasks)) // 0 0
}
```

### Tester les slices 

#### Tester si le slice est vide 

```go 
if len(tasks) == 0 {
	fmt.Println("Il n'y a pas encore de taches") // Il n'y a pas encore de taches
}
```

#### Tester si le slice est initialisé

```go 
func main() {
	var a []int  // nil
	b := []int{} // empty

	fmt.Println(len(a) == 0, a == nil) // true true
	fmt.Println(len(b) == 0, b == nil) // true false
}
```

### Append - ajouter un élément 

La fonction `append` permet d'ajouter à la fin, et si la place manque, agrandir le stockage.
Elle retourne le slice mis à jour.

Le résultat de la fonction doit toujours être conservé.

Lorsque l'on tente d'ajouter un élément dans un slice, mais que la `cap` est trop petite, alors Go viens prendre un nouvel emplacement mémoire, et y transfère tout le contenu et y place le nouvel élément.

```go
// syntaxe
<slice> := append(<slice>, <valeur>)

func main() {
	s := []int{1, 2}
	s = append(s, 3) // ajout dans le slice la valeur 3
	fmt.Println(s) // [1 2 3]
}
```

Avec des fonctions, il faut bien faire attention à retourner correctement le nouveau slice 

```go 
func addOne(s []int) []int {
	s = append(s, 999)
	return s
}

func main() {
	s := []int{1, 2}
	s = addOne(s)

	fmt.Println(s) // [1 2 999]
}
```

#### append et slice nil 

On peut venir ajouter des éléments avec `append` dans un slice qui est `nil`. C'est très utiliser pour accumuler des données.

```go 
func main() {
	var s []int // nil

	s = append(s, 10)
	s = append(s, 20)

	fmt.Println(s, s == nil) // [10 20] false
}
```

#### exemple 

On réaliser une application. Une fonction permet d'ajouter des tâches 

```go 
package main

import "fmt"

// permet d'ajouter des tâches
func addTask(tasks []string, title string) []string {
	tasks = append(tasks, title)
	return tasks
}

func main() {
	// création du slice 
	var tasks []string

	// on viens ajouter les tâches
	tasks = addTask(tasks, "Faire des exercices")
	tasks = addTask(tasks, "Caresser le chat (le chat ne se caresse pas tout seul)")

	fmt.Println(tasks) // [Faire des exercices Caresser le chat (le chat ne se caresse pas tout seul)]
}
```

### Préallocation de la capacité d'un slice 

La préalloction c'est lorsque l'on créer un slice de longueur nulle, mais avec une capacité définie à l'avance en utilisant un `make([]T, 0, <taille_attendu>)`.

Cela permet d'optimiser le slice lorsque l'on ajoute plusieurs éléments dans le slice avec une boucle.

```go 
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {
	in := bufio.NewReader(os.Stdin)

	var n int
	fmt.Fscan(in, &n)

	// préalocation du slice 
	tasks := make([]string, 0, n) // len=0, cap=n
	for i := 0; i < n; i++ {
		var t string
		fmt.Fscan(in, &t)
		tasks = append(tasks, t)
	}

	fmt.Println(tasks) // [buy_code sleep repeat]
}
```

### Découpage de slice 

```go 
part := s[a:b]

func main() {
	s := []int{10, 20, 30, 40, 50}
	part := s[1:4] // Du deuxième au 3 eme élément
	fmt.Println(part) // [20 30 40]
}
```

- `a` : borne de départ 
- `b`: borne de fin non inclut.

Cela signifie de récupérer tous les éléments entre l'indice `a` et `b` non inclut.

Si la borne de fin est supérieur à la limite, cela provoque une `panic`. La borne supérieur est comparée à `cap`, cette borne doit être inférieur à la valeur du `cap`. 

#### Reslice 

```go
func main() {
	s := make([]int, 0, 3) // len=0, cap=3

	fmt.Println(s[0]) // panic: l'index 0 n'est pas disponible quand len=0

	t := s[:1]             // ok: b=1 <= cap=3
	fmt.Println(t, len(t)) // [0] 1
}
```

#### Prédire le len et cap du résultat 

Le slice `s[a:b]` retourne un nouveau slice ayant sont propre `len` et `cap`.

- `len(s[a:b]) == b - a`
- `cap(s[a:b]) == cap(s) - a` : généralement ainsi parce que le début de la fenêtre se déplace vers la droite.
`cap` diminu de `a` car la capacité c'est "combien d'espace il reste à droite du début de la fenêtre jusqu'a la fin du backing array". Si on décale le début de la fenêtre, il y a moins de place à droite.

```go 
func main() {
	s := make([]int, 5, 10) // len=5 cap=10
	part := s[2:5]          // on prend 3 elements

	fmt.Printf("len=%d cap=%d\n", len(part), cap(part)) // len=3 cap=8
}
```

#### Omission de bornes 

Il existe des syntaxe simplifier pour les découpage de slice.

| Comportement souhaité                            | Code           |
| ------------------------------------------------ | -------------- |
| Sélection d'éléments                             | `s[a:b]`       |
| Tout jusqu'a `n`                                 | `s[:n]`        |
| Tout à partir de `n`                             | `s[n:]`        |
| Les `k` derniers                                 | `s[len(s)-k:]` |
| Inclure l'indice `i` dans le jusqu'a i           | `s[:i+1]`      |
| Prendre exactement `n` positions à partir de `i` | `s[i:i+n]`     |


```go
func main() {
	s := []int{10, 20, 30, 40, 50}

	fmt.Println(s[:2]) // [10 20] => les deux premiers
	fmt.Println(s[3:]) // [40 50] => à partir du 3eme élément
	fmt.Println(s[:])  // [10 20 30 40 50]
}
```

#### Protection du découpage 

```go 
func main() {
	tasks := []string{"learn Go", "write code", "drink water", "sleep"}

	var from, to int
	fmt.Scan(&from, &to)

	a := from - 1
	b := to
	// on protège contre les bornes incorrects pour le découpage
	if a < 0 || b < 0 || a > b || b > len(tasks) {
		fmt.Println([]string{}) // []
		return
	}

	fmt.Println(tasks[a:b])
}
```

### Expression complète de slice 

Le slice possède trois indices : `s[a:b:c]`. On définit la `len`, mais aussi la limite de `cap` du résultat.

- `a` : len 
- `b` : cap 
- `c` : end of cap 

Pour interdire la croissance vers la droite, et empêcher des conneries sur les backing array, on peut venir forcer la taille du `cap` pour que les `append` ne vienne pas casser.

```go 
func main() {
	tasks := []string{"a", "b", "c", "d", "e"}

	mid := tasks[1:3:3] // start=1, endLen=3, endCap=3
	fmt.Println(mid)                // [b c]
	fmt.Println(len(mid), cap(mid)) // 2 2
}
```

### Copy

La méthode copie les éléments de la `source` vers la `destination` et retourne le nombres d'éléments copiés.

Pour le slice de destination, il faut définis le nombres d'éléments à copier.

```go
copy(<destination>, <source>)

func main() {
	src := []int{10, 20, 30}
	dst := make([]int, 2) // len=2 => préparation de la destination

	n := copy(dst, src) // copie du slice source dans la destination
	fmt.Println(n)   // 2
	fmt.Println(dst) // [10 20]
}
```

Dans ce code, `dst` à une longueur de 2, il y aura donc 2 éléments de copiés.
#### copie indépendante 

Pour créer des slice totalement indépendant, on utilise ce pattern 

```go 
// la fonction permet de créer un clone du slice indépendant
func cloneInts(src []int) []int {
	dst := make([]int, len(src))
	copy(dst, src)
	return dst
}

func main() {
	a := []int{1, 2, 3}
	b := cloneInts(a) // on récupère une copie indépendante du premier slice 

	b[0] = 99
	fmt.Println(a) // [1 2 3]
	fmt.Println(b) // [99 2 3]
}
```

#### copie de sous slice 

```go 
func clonePart(src []int, a, b int) []int {
	part := src[a:b] // on récupère une partie du slice 
	dst := make([]int, len(part)) // on prépare la destination 
	copy(dst, part) // on copie 
	return dst
}

func main() {
	s := []int{10, 20, 30, 40}
	x := clonePart(s, 1, 3) // [20 30] copie indépendante 

	x[0] = 999 
	fmt.Println(s) // [10 20 30 40]
	fmt.Println(x) // [999 30]
}
```

### clear 

Fonction qui permet de reset les valeurs d'un slice. Il permet également de supprimer la référence vers les données.

```go 
func main() {
	s := []int{10, 20, 30}
	clear(s)

	fmt.Println(s)      // [0 0 0]
	fmt.Println(len(s)) // 3
}
```

#### Déplacement des éléments 

`copy` permet également de réaliser des déplacement au sein d'un slice.

Par exemple, on as une liste de tâches, et on souhaite retirer un élément du milieux. On peut venir déplacer la queue vers la gauche.

```go
copy(s[i:], s[i+1])

func main(){
	s := []int{1, 2, 3, 4}
	copy(s[1:], s[2:])
	
	fmt.Println(s) // [1 3 4 4]
}
```

Le trous entre 2 et 3 est fermé, mais un doublon apparait à la fin.

On peut également faire des déplacement vers la droite. Par exemple, pour insérer un élément au mileu. On ne peut pas déplacer vers la droite si il n'y a pas de place en longueur.
La première étape est d'augmenter `len`.

```go 
func main(){
	s := []int{10, 20, 30}
	
	i := 1
	s = append(s, 0) // len+1 
	copy(s[i+1], s[i:]) // déplacement vers la droite de 1 
	s[i] = 99
	
	fmt.Println(s) // [10 99 20 30]
}
```

Exemple d'un gestionnaire de tâche 

```go 
package main 

import "fmt"

// cloneStrings crée une copie indépendante du slice 
func cloneStrings(src []string) []string {
	dst := make([]string, len(src))
	copy(dst, src)
	return dst 
}

// déplacement vers la gauche
func shiftLeftByOne(s []string, i int){
	if i < 0 || i >= len(s)-1 {
		return 
	}
	copy(s[i:], s[i+1:])
}

func main() {
	tasks := []string{"buy milk", "read book", "write code"}
	
	view := cloneStrings(tasks)
	view[0] = "buy coffee"
	
	fmt.Println("tasks:", tasks) // tasks: [buy milk read book write code]
	fmt.Println("view: ", view)  // view:  [buy coffee read book write code]

	shiftLeftByOne(tasks, 1)
	fmt.Println("shift:", tasks) // shift: [buy milk write code write code]
}
```


Cette méthode permet de créer une copie d'un slice. `copy(dst, src)` copie les éléments de `src` vers `dst` et retourne le nombre d'éléments copiées.

```go 
func main(){
	src := []int{10, 20, 30}
	dst := make([]int, 2) // len 2 => création d'une fenêtre de 2 éléments
	
	n := copy(dst, src)
	fmt.Println(n) // 2 
	fmt.Println(dst) // [10 20]
}
```

#### Création d'un backing array 

On alloue un nouveau buffer, on y copie les éléments, on travail ensuite avec. `a` et `b` sont indépendants, ils ont des backing array différents.

```go
// clone un slice en entier 
package main

import "fmt"

func cloneInts(src []int) []int {
	dst := make([]int, len(src))
	copy(dst, src)
	return dst 
} 

func main(){
	a := []int{1, 2, 3}
	b := cloneInts(a)
	
	b[0] = 99
	fmt.Println(a) // [1 2 3]
	fmt.Println(b) // [99 2 3]
}
```

### Copier une partie de slice 

On choisit un intervalle, ensuite on lui crée son propre buffer.

```go 
package main 

import "fmt"

func clonePart(src []int, a, b int) []int{
	part := src[a:b]
	dst := make([]int, len(part))
	copy(dst, part)
	return dst
}

func main(){
	s := []int{10, 20, 30, 40}
	x := cloneParts(s, 1, 3) // [20 30]
	
	x[0] = 999
	fmt.Println(s) // [10 20 30 40]
	fmt.Println(x) // [999 30]
}
```

### clear 

Fonction qui permet de réecrire la zero value pour les éléments de la plage.

```go
func main() {
	s := []int{10, 20, 30}
	clear(s)

	fmt.Println(s)      // [0 0 0]
	fmt.Println(len(s)) // 3
}
```


