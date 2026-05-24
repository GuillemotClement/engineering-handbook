# Golang

---

## STRUCTURE

### Architecture de projet

Dans les projets Go, il n'y a pas de hiérarchie stricte de dossier. Il n'existe pas de dossier `src` obligatoire pour les sources.
Pour les petits utilitaires et micro services, les fichiers sources se trouvent directement à la racine du projet, à côté du fichier `go.mod`.
A mesure que le projet grandit, le code est organiser en dossier, chacun devenant un package distinct.

Un package étant un dossier physique dans le système de fichier.

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


## STRING

### Indexation de chaine

Lorsque l'on vient sélectionner un élément de la string, on ne retourne pas un caractère, mais un octet de cette séquence.
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

---

## BOOLEAN

Le type `bool` peut prendre une valeur `true` ou `false`. Il permet de réaliser des comparaison

```go
package main

import "fmt"

func main() {
	isWeekend := true // boolean
	fmt.Println(isWeekend) // true
}
```

Une comparaison est une expression dont le résultat est toujours `bool`. On utilise les operateur de comparaison pour comparer deux valeurs.

```go
package main

import "fmt"

func main() {
	a := 10
	b := 7

	greater := a > b // comparaison
	fmt.Println(greater) // true
}
```

Pour garder le code propre, on viens utiliser des nom de variable pour les boolean qui reflète leur but

```go
package main

import "fmt"

func main() {
	age := 20
	hasTicket := true

	isAdult := age >= 18
	canEnter := isAdult == true // pour l’instant sans operateurs logiques

	fmt.Println(isAdult)   // true
	fmt.Println(canEnter)  // true
	fmt.Println(hasTicket) // true
}
```

---

---

---



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

Prends des nombre entier positif et négatif. C'est le type par défaut pour les entiers.
La taille de `int` dépend de la plateforme (32 ou 64).

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

Prends les nombre a virgule postifi et negatif.

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

---

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

---

## ERREUR

### Vérification des erreurs

En go, de nombreuses fonctions retourne `error`, et cette valeur est soit `nil` soit non `nil`.
Lorsque la fonction s'est bien exécuté, la valeur de `error` sera `nil`.

Si une fonction peut retourner une erreur, alors on la traite directement. De cette manière, on traite directement les cas d'erreur, et on ne garde pas des valeurs potentiellement mauvaise.

En Go, on utilise ce pattern : `appel -> check -> use` :

1. Appelle de la fonction
2. Vérifier `err`
3. Utiliser le résultat de la fonction

```go
package main

import "fmt"

func main() {
	var x int
	_, err := fmt.Scan(&x)

	if err != nil {
		fmt.Println("impossible de lire le nombre") // par exemple, si l'on a saisi "abc"
		return		// sortie de main()
	}

	fmt.Println("x =", x)
}
```

On peut utiliser les `if` avec initialiseur en utilisant les variables temporaire pour simplifier le code.
De cette manière, les variables ne sont que temporaire.

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

#### Mini app : calculatrice

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

---

---


---


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

---
