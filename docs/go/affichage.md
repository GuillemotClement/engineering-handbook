# Affichage 

En Go, le package `fmt` permet d'utiliser les methodes pour l'affichage.

## fmt.Print()

Permet d'afficher quelque chose 

```go
package main

import "fmt"

func main(){
	// affichage sans nouvelle ligne 
	fmt.Print("Hello")
}
```

## fmt.Println()

Permet d'afficher en ajoutant les espaces et le retour a la ligne.

```go
package main

import "fmt"

func main(){
	/// affichage avec saut de ligne	
	fmt.Println("Mon age :", 28)
	fmt.Println("Nous sommes en", 2026, "annee.")
	
	// affichage avec concatenation
	fmt.Println("Ami" + "go") // Amigo
}
```

## fmt.Printf()

Permet d'utiliser des espaces reserver pour afficher des valeurs.

On utilise des specificateurs pour affichers les valeurs selon leur type.

```go
package main

import "fmt"

func main(){
	x := 10 
	fmt.Printf("x=%v type=%T\n", x, x) // x=10 type=int 
}
```

- `%v` : spécificateur universel 
- `%T` : affiche le type de valeur 
- `%d` : entier 
- `%s` : string 
- `%%` : affiche le symbole `%`
- `%b` : binaire 
- `%q` : affiche une string avec des " "
- `%c` : affiche les caractere pour les type `rune`