# BOUCLE

En Go, il n'existe qu'**une seule forme de boucle**. On utilise `for` mais avec plus ou mois d'arguments.

## for

Elle prends trois arguments dans sa forme de base :

- `init`: initialisation de la valeur et réaliser en début de boucle
- `cond` : condition d'arrêt vérifier a chaque itération
- `post` : incrémentation, applique a chaque itération de la boucle

Par convention, on utilise `i` pour les variable compteur.

```go

func main(){
	// syntaxe
	for init; cond; post {
    // corps de la boucle
	}

	// exemple compteur
	for i := 0; i < 3; i++ {
		// executer a chaque itereation
		fmt.Println("Itération :", i)
	}

	// repetition et affichage
	var n int
	fmt.Scan(&n) // recupere la nombre d'iteration

	for i := 1; i <= n; i++ {
		var title string
		fmt.Scan(&title) // recupere la saisie utilisateur
		fmt.Println(i, "-", title) // exemple : 1 - buy_milk
	}

	// boucle de calcul des carre
	var n int
	fmt.Scan(&n)

	for i := 1; i <= n; i++ {
		fmt.Println(i, "->", i*i) // si n=3 : 1->1, 2->4, 3->9
	}
}
```

---

## for while

On peut mettre en place des boucle `while` en Go. La boucle viens s'exécuter tant que la condition est évaluer a `true`.

```go


func main() {
	// syntaxe
	for cond {
		// code de la boucle
	}

	// double un nombre tant qu'il est < 100
	x := 1
	for x < 100 {
		x = x * 2
	}
	fmt.Println(x) // 128
}
```

---

### for infinie

Il est parfois nécessaire de faire tourner une boucle de manière infinie. Pour une CLI par exemple, on attends la saisie de l'utilisateur tant qu'il ne souhaite pas sortie explicitement de la boucle.

Il faut obligatoirement ajouter un point de sortie lorsqu'une condition est atteinte.

```go
func main(){
	// exemple
	for {
	    // on fait quelque chose
	    // si il faut terminer — break
	}

	// exemple : lire jusqu'a zero
	balance := 0

	// on lance une boucle infinie
	for {
		var x int
		-, err := fmt.Scan(&x) // récupère la saisis

		// si err on sort de la boucle
		if err != nil {
			break
		}

		// si on atteind zero on sort de la boucle
		if x == 0 {
			break
		}

		// si valeur trop grande on skip
		// on utilise une syntaxe plus lisible pour ecrire le nombre
		if x > 1_000_000 || x < -1_000_000 {
			fmt.Println("montant trop grand, j'ignore")
			continue
		}

		balance = balance + x
	}
	fmt.Println(balance)
}
```

---

## break

L'instruction `break` permet de sortir de la boucle.

---

## continue

L'instruction `continue` permet de passer à l'itération suivante. Attention au placement de l'instruction.

```go
package main

import "fmt"

func main() {
	i := 1
	for i <= 5 {
		if i == 3 {
			i++ // on fait l'etape avant continue
			continue
		}
		fmt.Println(i)
		i++
	}
}
```
---

## range - parcourir une séquence

Permet de parcourir une séquence. Elle retourne deux valeurs :

- `index` : la position de l'élément dans la séquence
- `value` : la valeur en cours d'itération

La valeur correspond a la valeur numérique du caractère lorsque l'on parcourt une chaine.

```go
func main(){
	// syntaxe
	for index, value := range s {
		// code de la boucle
	}

	// parcourir une chaine
	s := "Go"

	for i, v := range s {
		fmt.Println(i, v)
	}
	// 0 71		  71 - code du caractère 'G'
	// 1 111	 111 - code du caractère 'o

	// compter le nombre de caracteres
	s := "gopher"

	count := 0
	for _, v := range s {
		if v == 'o' {		// 'o' symbole
			count++
		}
	}

	fmt.Println(count) // 1

	// detection de caractere interdit
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

### Remplacement de caractères

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

---

## Pattern

### Accumulateur

1. Initialisation d'une variable a zéro.
2. Cumul des valeurs dans la variable initial
3. Affiche le résultat final après les itération qui représente le cumul de valeur

```go
func main(){
	// definit le nombre d'iteration a faire
	var n int
	fmt.Scan(&n)

	sum := 0 // initialisation de la variable accumulateur
	for i := 0; i < n; i++ {
		// on demande une saisie utilisateur a chaque iteration
		var x int
		fmt.Scan(&x) // recupere la valeur saisis
		sum = sum + x // incrementation dans la variable accu
	}

	fmt.Println(sum) // affichage somme total
}
```

### Somme sous condition

On souhaite additionner les nombres seulement sous certaines condition, par exemple les nombres paires.

On garde le même pattern que pour l'accumulateur, mais on ajoute un `if` permettant de d'incrémenter seulement avec les nombres qui vérifie la condition.

```go
package main

import "fmt"

func main() {
	var n int
	fmt.Scan(&n)

	sumEven := 0
	for i := 0; i < n; i++ {
		var x int
		fmt.Scan(&x)
		// ajoute de la condition
		if x%2 == 0 {
			sumEven = sumEven + x
		}
	}

	fmt.Println(sumEven) // par exemple : 12
}
```

### Compter les caractères dans une chaine

Ici, on cherche a savoir combien de fois un caractère est présent dans une chaine.

```go
package main

import "fmt"

func main() {
	s := "bananas"

	countA := 0
	for _, ch := range s {
		// on verifie le caractere rechercher
		if ch == 'a' {
			countA++
		}
	}

	fmt.Println(countA) // 3
}
```

### Valeur maximale

La recherche du maximum (et minimum) peut être problématique lorsque l'on rencontre des valeurs négative et que la valeur max est initialiser a zéro.

Pour que la recherche soit fiable, on peut venir utiliser la premier valeur, et se baser dessus en itérant sur les valeurs suivantes :

```go
package main

import "fmt"

func main() {
	var n int
	fmt.Scan(&n)

	if n <= 0 {
		fmt.Println(0) // convenons que pour une entrée vide, on affiche 0
		return
	}

	var max int
	fmt.Scan(&max) // le premier élément devient le maximum de départ

	for i := 1; i < n; i++ {
		// x contient la saisis utilisateur
		var x int
		fmt.Scan(&x)
		if x > max {
			max = x
		}
	}

	fmt.Println(max) // par exemple : 42
}
```

### Valeur maximal et position

On as parfois besoin de connaitre la valeur maximal et sa position.

```go
package main

import "fmt"

func main() {
	var n int
	fmt.Scan(&n)

	if n <= 0 {
		fmt.Println(-1) // pas d'éléments, donc pas d'indice
		return
	}

	var max int
	fmt.Scan(&max)
	maxIndex := 0

	for i := 1; i < n; i++ {
		var x int
		fmt.Scan(&x)
		if x > max {
			max = x
			maxIndex = i // stocke l'index ou le max est trouver
		}
	}

	fmt.Println(maxIndex) // par exemple : 4
}
```

### Recherche

La recherche c'est lorsque l'on souhaite savoir si quelque chose apparaît dans les données. On utilise un flag, et si lever, on `break`.

1. On initialise un flag a `false`
2. on itère, si un match arrive, on passe le flag a `true` et on stop la boucle

```go
// recherche parmis des nombres
func main() {
	var n, target int
	fmt.Scan(&n, &target) // on recupere la valeur rechercher

	found := false // drapeau init a false
	for i := 0; i < n; i++ {
		var x int
		fmt.Scan(&x) // on recupere la saisie
		// si on trouve la donnée, on leve le drapeau et on stop la boucle
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
