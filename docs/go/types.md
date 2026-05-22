# Types 

## Nombre 

Les operation entre `float64` et `int` sont interdite. 

### int 

`int` est le type pour les nombres entier. Il prends en charge les operations et les comparaison. Pour l'operation `/`, la partie decimal est supprime

### float64 

`float64` est le type par defaut pour les nombres decimaux.

## String 

`string` est le type pour les chaines de caracteres.

### Concatenation 

```go 
package main 

import "fmt"

func main(){
  first := "Go"
  second := "lang"

  full := first + second // concatenation 
  fmt.Println(full)
}
```

### Comparaison de chaine 

```go 
package main 

import "fmt"

func main(){
  cmd := "add"

  fmt.Println(cmd == "add") // true 
  fmt.Println(cmd != "delete") // true
}
```

## Boolean 

Permet de stocker des valeurs `true` ou `false`. Le resultat de toute comparaison en Go est un `bool`.

```go 
package main 

import "fmt"

func main(){
  // comparaison 
  age := 20 
  isAdult := age >= 18 

  fmt.Println(isAdult) // true 

  hasTicket := true 
  
  canEnter := age >= 18 && hasTicket // si premier vrai on evalue la second et retourne le boolean
  fmt.Println(canEnter) // true 

}
