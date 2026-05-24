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

---

## Fonction variadiques

Fonctions qui prends un nombre indéterminé de paramètres de meme type. Le paramètre variadiques est toujours le dernier dans la liste des paramètres.
Pour déclarer une paramètre variadiques, on utilise `<name> ...T`. 
Lorsque l'on appelle la fonction, on pourras passer autant d'arguments que souhaité dans la fonction.

Dans une fonction variadique, les parametres optionnelles arrive en dernier. 

Pour propager les arguments variadique, on utilise la syntaxe `args...` qui permet de passer l'ensemble des arguments lors de l'appel de la fonctions.

```go 
package main 

import (
	"fmt"
	"errors"
)

func printReport(name string, scores ...int) error {
	if len(scores) == 0 {
		return errors.New("no scores for report")
	}
	avg, _ := average(scores) // on skip l'erreur car deja gerer avec le check precedent
	fmt.Println("%s: count%d avg=5.2f\n", name, len(scores), avg)
	return nil
}

func average(nums ...int) (float64, error){
	if len(nums) == 0 {
		return 0, errors.New("no number to average")
	}
	return float64(sum(nums...)) / float64(len(nums)), nil // on transmet l'ensemble des arguments variadique
}

func sum(nums ...int) int {
	total := 0 
	for _, v := range nums {
		total += v
	}
	return total
}

func main(){
	_ = printReport("Anna", 10, 20, 30) // Anna: count=3 avg=20,00
}
```
---

## Fonction anonymes

On souhaite parfois passer a une fonction des donnes mais egalement des infstruction. Par exemple on donne une pomme (la donnee), et on indique ce que l'on souhaite (lave, coupe, manger) le comportement.
Au lieu d'utiliser un ensemble de `if/else`, on peut passer une fonction anonyme qui s'occupe de transmettre les instructions.

```go 
// declaration fonction anonyme => sans nom
func(x int) int {
	return x * 2
}
```

Des que l'on ecrit `fun(...){ ... }`, on creer une valeur. Ce n'est pas encore l'execution.


Parfois, il peut être intéressant d'exécuter un bloc de code directement (IIFE). Les parenthèses à la fin permette de déclencher l'appel de la fonction anonyme.

```go
// fonction anonyme qui print les deux lignes
func (){
	fmt.Println("=== MiniCalc ===")
	fmt.Println("Entrez: a op b rule")
}() // les parentheses a la fin signifie un appel 
```

On peut également passer un paramètre et un résultat à la fonction anonyme et l'executer directement pour que la variable recoive la valeur retourner par celle ci.

```go
res := func(x int) int {
	return x * x
}(5) // on appel la fonction anonyme et on lui passe 5

fmt.Println(res) // 25
```

Parfois le calcul exisge des `if` mais on ne souhaite pas encombrer le code avec des variables temporaire 

```go 
x := -10 

// recupere la valeur de la fonction anonyme
sign := func(n int) string {
	if n < 0 {
		return "negatif"
	}
	if n ==  0 {
		return "zero"
	}
	return "positive"
}(x)

fmt.Prinln(sign) // negative
```

### Fonction comme valeur

Une variable peut stocker une fonction. 

```go 
f := func(x int) int {
	return x + 1
}

fmt.Println(f(10)) // 11
```
Dans ce code, on declare une variable qui stocke une fonction anonyme. On appelle ensuite cette fonction en utilisant le nom de variable `f`.

---


### Fonction nil 

Une fonction decarer via `var` vaut `nil` par defaut 

```go 
var rule func(int) int // nil
```

---

### Nommer un type de fonction 

Il est possible de declarer des types pour une fonction. Cela permet d'ameliorer la lisibilite 

```go 
type Rule func(int) int
```

### Fonction d'ordre supérieur

Une fonction d'ordre superieur est une fonction qui accepte une autre fonction et l'utilise a l'interieur. Elle peut parfois retourner egalement une fonction.

```go
// type de fonction
type Rule func(int) int

// `r` est la fonction qui est passer en parametre a une autre fonction.
func apply(x int, r Rule) int {
	return r(x)
}

// utilisation avec une fonction nommée
func double(x int) int { 
	return x * 2 // retourne le parametre doubler
} 
fmt.Println(apply(10, double)) // 20

// utilisation avec une fonction anonyme
fmt.Println(apply(10, func(x int) int { return x * x})) // 100
```

`apply` ne sait rien doubler. Elle sait seulement qu'on lui donne `x` et la regle `r`. De cette maniere, on envoie une valeur et un comportement a la fonction.

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
	n := 0 // visible dans la fonction anonyme
	return func() int {
		n++ // retourne la variable dans la portee superieur 
		return n
	}
}

c := makeCounter()
fmt.Println(c()) // 1
fmt.Println(c()) // 2
fmt.Println(c()) // 3
```
