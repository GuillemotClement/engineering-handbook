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
```

---

## VARIABLES

En Go, lorsque l'on réalise une affectation, on vient **copier** la valeur dans une nouvelle variable.

Avec la syntaxe courte, Go détermine lui même le type de la variable depuis sa valeur.

```go
// déclaration de variable
var <name> <type>

var a int // entier
var s string // chaîne de caractère
var c float64 // nombre décimaux

// déclaration multiple 
var a, b, c int; var d int

// affectation ===========================
a = 7

// syntaxe courte => méthode standard ====
appName := "SuperApp"
```


---

## TYPE 

### int 

Stocke les nombres entiers sans la partie décimale. Il prends les négatif et les positifs.

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


### Conversion 

#### strconv.Itoa() - int -> str 

Permet de convertir un entier en chaîne 

```go 
package main 

import (
	"fmt"
	"strconv"
)

func main(){
	x := 333
	s = "Amigo" + strconv.Itoa(x)
	fmt.Println(s)
}
```

---

## MATH 

### Division entière

L'opérateur `/` de faire réaliser des division entière. Si deux `int` sont utilisé, on aura également un `int` pour le résultat. La partie décimale est ignoré.

```go
package main

import "fmt"

func main() {
	a := 7
	b := 2

	fmt.Println(a / b) // 3
	
	// pourcentage 
	planned := 10
	done := 3
	
	percent := (done * 100) / planned
	fmt.Println(percent) // 30
}
```

### Modulo

L'opérateur `%` permet de calculer le reste d'une division.

```go
package main

import "fmt"

func main() {
	candies := 17
	kids := 5

	each := candies / kids
	left := candies % kids

	fmt.Println(each) // 3
	fmt.Println(left) // 2
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