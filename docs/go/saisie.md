# SAISIE

## fmt.Scan - lire une valeur depuis l'entree standard

La fonction `Scan` accepte des pointeurs en argument et retourne deux valeur : `count` et `err`.

- `count` : indique combien de valeurs ont reellement pu etre reconnues et ecrit dans la variables.
- `err` : contient l'erreur si la fonction rencontre une erreur. `nil` si aucune erreur n'est rencontrer

L'utilisation des pointeur se fait via `&` qui vient passer l'adresse mémoire d'une variable. On viendras créer une variable avant d'utiliser la méthode.

Elle récupère la saisie depuis l'entrée standard.

```go
package main

import "fmt"

func main() {
	// recuperation d'une saisie
	var n int // on prepare la variable qui viendras stocker la valeur
	fmt.Scan(&n) // lecture de la saisie
	fmt.Println(n) // affichage de la saisie

	// recuperation de plusieurs saisie
	var a, b int // on prepare deux variables
	fmt.Scan(&a, &b) // on recupere la saisie
	sum := a + b // calcul avec les valeurs saisie
	fmt.Println(sum) // affichage du resultat

	// traitement des valeurs retourne
	var x int
	count, err := fmt.Scan(&x) // on recupere les valeurs retourner

	fmt.Println("count:", count) // par exemple : count: 1
	fmt.Println("err:", err)     // en cas de succes : err: <nil>
	fmt.Println("x:", x)         // si vous avez saisi 42, alors : x: 42

	// gestion d'erreur => la seconde valeur n'est pas saisie
	var a, b int
	count, err := fmt.Scan(&a, &b)

	fmt.Println("count:", count) // probablement : count: 1
	fmt.Println("err:", err)     // ne sera pas <nil>, il y aura une description de l'erreur
	fmt.Println("a:", a)         // a: 10
	fmt.Println("b:", b)         // b: 0 (restera a sa valeur zero)
}

// mini calculatrice
func main() {
	// read
	var price int
	var qty int
	fmt.Scan(&price, &qty) // lecture des saisies

	// compute
	total := price * qty

	// print
	fmt.Println(total) // si vous avez saisi 120 3, cela affichera : 360
}
```

---

## fmt.Fscan - entrée avec source explicite

La méthode `Fscan` prends en premier argument, la source d'entree qui sera recuperer. On passe ensuite les pointeurs des variables qui contiendront les saisies.

`os.Stdin` représente l'entrée standard.

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

## Conversion de saisie

Généralement, on récupère une chaine depuis la méthode `Scan`. Il sera nécessaire de la convertir pour utiliser les nombre et réaliser des calculs par exemple.

```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	// read: on lit l'ID comme une chaine (par exemple : 100)
	var idStr string
	fmt.Scan(&idStr)

	// parse: on essaie de convertir la chaine en int
	id, err := strconv.Atoi(idStr)

	// compute: on calcule le prochain ID (si err n'est pas nil, id vaudra 0)
	nextID := id + 1

	// print: on affiche tout, y compris err, pour comprendre ce qui s'est passe
	fmt.Println("id =", id)         // par exemple : id = 100
	fmt.Println("nextID =", nextID) // par exemple : nextID = 101
	fmt.Println("err =", err)       // <nil> ou texte de l'erreur
}
```
