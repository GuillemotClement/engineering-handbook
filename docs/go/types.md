# Types 

## Nombre 

Les operation entre `float64` et `int` sont interdite. 

### int 

`int` est le type pour les nombres entier. Il prends en charge les operations et les comparaison. Pour l'operation `/`, la partie decimal est supprime

La `zero value` est `0`. 

### float64 

`float64` est le type par defaut pour les nombres decimaux.

La `zero value` est `0.0`

### Protection valeur max 

La valeur zero pour les nombre peut etre un nombre si aucune valeur n'est suprerieur a zero. Pour eviter ce probleme, on peut utiliser un pattern flag. On viens verifier que la valeur a bien ete initialiser avec des donnees 

```go 
package main 

import "fmt"

func main(){
  var max int 
  var hasMax bol // false, c'est le flag

  for i := 0; i < 3; i++ {
    var x int
    fmt.Scan(&x)

    // on check le flag pour set la valeur max initial
    if !hasMax || x > max {
      max = x 
      hasMax = true // a la premiere iteration on set le flag 
    }
  }
  fmt.Println(max) // correct meme avec des valeurs negative.
}
```

## String 

`string` est le type pour les chaines de caracteres.

La `zero value` est `""`

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

### Verifier une chaine vide 

```go 
package main 

import "fmt"

func main(){
  var title string // zero value ""
  fmt.Println(title == "") // true 
}
```

## Boolean 

Permet de stocker des valeurs `true` ou `false`. Le resultat de toute comparaison en Go est un `bool`.

La `zero value` est `false`


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
```