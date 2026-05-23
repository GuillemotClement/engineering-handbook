# Operateurs 

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

	// calcul de pourcentage decimal
	planned := 10
	done := 3

	percent := (done * 100) / planned
	fmt.Println(percent) // 30
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

---

### Opérateur conditionelle

On ne peut faire des comparaison que sur des types compatibles.

- `==` : vérifie une égalité
- `!=` : vérifie une différence
- `<` : inférieur à
- `<=` : inférieur ou égale à
- `>` : supérieur à
- `>=` : supérieur ou égale à

```go
func main(){
	x := 5
	isFive := x == 5
	isNotFive := x != 5

	fmt.Println(isFive) // true
	fmt.Println(isNotFive) // false

	// comparaison de chaine
	role := "admin"
	isAdmin := role == "admin"
	isGuest := role != "admin"

	fmt.Println(isAdmin) // true
	fmt.Println(isGuest) // false

	// comparaison taille de chaine
	login := "gopher"
	isEmpty := len(login) == 0
	isLong := len(login) >= 8

	fmt.Println(isEmpty) // false
	fmt.Println(isLong)  // false

  // comparaison avec condition
	score := 82
	isHight := score >= 90
	isMedium := score >= 75

	fmt.Println(isHight) // false
	fmt.Println(isMedium) // true
}
```

---

### Opérateur logique

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

#### Short circuit

Les opérateurs `&&` et `||` ont un comportement spécifique :

- `A && B` : la partie B n'est pas évalué si A est `false`
- `A || B` : la partie B n'est pas évalué si A est `true`

##### Protection division par zero

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

##### Check role

```go
func main(){
	role := "admin"
	hasPaid := false

	canUseFeature := role == "admin" || hasPaid
	fmt.Println(canUseFeature) // true
}
```

Si l'user est admin, la condition sera vraie.

---


