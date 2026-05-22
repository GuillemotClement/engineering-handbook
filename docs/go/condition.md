# CONDITION

## If, else, else if

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

En Go, on ne peut pas faire une vérif directement sur une variable non nul `if age { }`, il faut utiliser une condition explicite.

```go
if age != 0 {
	...
}
```

## if avec initialiseur

Go permet d'écrire un `if` avec une valeur initialiseur. La variable est disponible que pour cette condition.

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

	// age et err n'existe que dans le if/else
	if age, err := strconv.Atoi(ageStr); err == nil {
		fmt.Println("Age : ", age)
	} else {
		fmt.Println("Age incorrect")
	}
}
```

---

## early return

On peut utiliser l'operateur `return` pour terminer directement dans une condition l'exécution du script courant.
Cela permet d'éviter des `if` imbriquer.

```go
if isBanned {
	fmt.Println("denied: banned")
	return
}

if isVIP || (isAdult && hasTicket) {
	fmt.Println("welcome")
}
```

---

## Switch

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

	// plusieurs execution pour un cas
	day := 7

	switch day {
		case 6, 7:
			fmt.Println("jour de repos")
		default:
			fmt.Println("jour ouvrable")
	}

	// switch sans expression
	// entre dans le premier case qui retourne true
	score := 82

	switch {
		case score >= 90:
			fmt.Println("A")
		case score >= 75:
			fmt.Println("B")
		default:
			fmt.Println("C")
	}

	// switch avec conditon
	n := -4

	switch {
	case n > 0 && n%2 == 0:
		fmt.Println("positif pair")
	case n > 0:
		fmt.Println("positif impair")
	default:
		fmt.Println("non positif")
	}
}
```

- `default` : c'est le cas qui s'exécute si aucune valeur ne match avec les différents case. Il n'est pas obligatoire.

Par défaut, chaque case est `break` automatiquement.. `fallthrough` permet d'empêcher ce comportement.

---

### Menu CLI

Le switch permet d'évaluer des actions utilisateurs, et exécuter du code selon la saisis. Par exemple, une mini calculatrice, ou on réalise le calcul selon le choix de l'utilisateur

```go
package main

import "fmt"

func main() {
	var op string
	var a, b int
	fmt.Scan(&op, &a, &b)

	switch op {
	case "add", "+":
		fmt.Println(a + b)
	case "sub", "-":
		fmt.Println(a - b)
	case "mul", "*":
		fmt.Println(a * b)
	case "div", "/":
		// on check le diviseur
		if b == 0 {
			fmt.Println("division by zero")
			return // terminer l'execution de la fonction main()
		}
		fmt.Println(a / b)
	default:
		fmt.Println("unknown op")
	}
}
```
