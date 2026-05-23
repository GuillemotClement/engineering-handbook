# Fonction

## Declaration et utilisation 

Une fonction en Go doit avoir les parametres types et le retour. Lorsqu'une fonction ne retourne rien, il n'y a pas de typage pour le retour.
Une variable declarer dans une fonction n'existre que dans le bloc de la fonction.

Chaque fonction doit avoir un contrat clair qui definit ce qu'attends la fonction en entree et ce qu'elle retourne.

Le nom d'une fonction doit etre clair, et indiquer ce qu'elle realise, par exemple `add()`. Les arguments doivent aussi representer clairement leur utiliser : `height`, `width` par exemple.


```go
// syntaxe
func <name>(<param> <type>, <param2> <type>) <type_retour> {
	// code exécuter à l'appel de la fonction
}

// fonction qui ne retourne rien 
func printHeader(){
	fmt.Println("Header")
}

// fonction qui retourne une valeur => le retour doit etre typer 
func returnHeader() string {
	return "Header"
}

//fonction avec parametre  
func add(a int, b int) int {
	return a + b
}

// si plusieurs paramètres ont le même type
func reactArea(width, height int) int {
	return width * height
}

func main(){
	// appel de la fonction 
	printHeader() // Header
}
```

---

## Fonction main()

La fonction `main()` est le point d'entree. Elle se charge d'appeller differents fonction afin d'orchestrer l'application. Elle est responsable de l'organisation des differentes operation realiser.

Chaque fonction realise une action, et tend a etre le plus petit possible pour faciliter la lecture du code.

```go 
package main 

import "fmt"

func sum(a, b int) int {
	return a + b
}

func mul(a, b int) int {
	return a * b
}

func main(){
	var a, b int 
	fmt.Scan(&a, &b)

	fmt.Println("sum =", sum(a, b)) // on affiche le resultat obtenu par la fonction 
	fmt.Println("mul = ", mul(am b)) 
}
```

---

## Retour multiple 

Un fonction en Go retourne generalement soit un resultat et une erreur. L'erreur est presente si quelque chose ne se passe pas comme prevu sinon la valeur est `nil`.
On viens traiter directement a l'appel l'erreur pour realiser une action des que quelque chose ne passe pas comme prevu.

`error` est une interface avec une methode `Error() string`.

Lorsque l'on parle de retour multiple, cela signifie que la fonction peut retourner plusieurs valeurs via un seul `return`. Dans la signature, les type de retour sont indiquer entre parentheses.

Lorqu'une fonction retourne plusieurs valeurs, on peut les recuperer directement via une affectation a l'endroit ou la fonction a ete appeller. Pour gerer le cas ou une fonction retourne une valeur qui ne serait pas utiliser, on utilise `_` pour skip cette valeur.

```go
package main 

import "fmt"

func swap(a, b string) (string, string){
	return a, b // on retourne deux valeurs 
}

func pair() (int, int){
	return 10, 20
}

func main(){
	left, right := swap("L", "R")
	fmt.Println(left, right) // R L
	
	x, _ := pair() // on skip la deuxieme valeur retourner
}
```

---

## Gestion d'erreur

En Go, il existe un syntaxe pour traiter les erreurs. Dans le cas ou elle echoue, celle ci retourne l'erreur, et on peut traiter cela depuis l'endroit ou la fonction est appeler.

Pour cela, il existe une forme standard `(T, error)` ou `T` est le resultat utile et `error` peut contenir soit `nil`, soit un `non-nil`, c'est a dire une erreur. Si le retour est non nil, cela signifie qu'une erreur est survenue dans l'execution de la fonction 

```go 
package main 

import (
	"errors" // package pour creer une erreur 
	"fmt"
)

func safeDiv(a, b int) (int, error) {
	if b == 0 {
		// si erreur, on retourne la zero value et un message d'erreur 
		return 0, errors.New("division by zero")
	}
	// sinon on retourne le resultat et nil pour l'error
	return a / b, nil 
}

func main(){
	q, err := safeDiv(10, 0) // on recupere le resultat et l'erreur potentiel 

	// on check pour verifier si une erreur est survenu
	if err != nil {
		fmt.Println("error: ", err) // error: division by zero
		return // on termine l'execution du script
	}

	fmt.Println("q =", q)
}
```

---

## Retour nomme

En Go, il est possible de nommer les retour de fonction dans la signature. Lorqu'un retour est nommer, des variables sont automatiquement creer et utiliser pour le `return`.
Cette pratique permet d'ameliorer la lisibilite du code, on voit directement a quoi sert la valeur retourner.

```go
func minMax(a, b int) (min int, max int){

	// ajouter implicitement
	// min := 0
	// max := 0

	return // min et max sont retourner automatiquement
}
```

Lorsque des retour sont nommer, il est possible de faire un `return` sans ajouter les valeurs. Elles sont automatiquement retourner.

```go 
package main 

import "fmt"

func minMax(a, b int) (min int, max int) {
	if a < b {
		min = a 
		max = b 
		return // min et max sont implicitement ajouter 
	}
	min = b 
	max = a 
	return 
}

func main(){
	lo, hi := minMax(10, 3) // 3 10
}
```

Il est toujours possible de retourner explicitement les resultats lors du `return`

```go
func parseExpr(aS, opS, bS string) (a int, op string, b int, err error){
	if opS != "+" && opS != "-" && opS != "*" && opS != "/" {
		return 0, "", ), errors.New("unknow operator")
	}

	// ...
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