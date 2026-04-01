# Golang 

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

// affectation ===========================
a = 7

// syntaxe courte => méthode standard ====
appName := "SuperApp"
```


