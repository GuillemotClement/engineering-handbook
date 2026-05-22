# Variable

En Go, une variable a une type, un nom et une valeur. Lorsque la variable est decalrer, le type est definis et il n'est pas possible de le changer.

## Declaration de variable 

Convention de nommage :
- ne peut pas commencer par un chiffre 
- ne peux pas contenir de caractère spéciaux hormis `_`
- `camelCase`

### var 

Mot cle permettant de declarer une nouvelle variable. Il permet de declarer une variable dans la portee globale, et permet de declarer une variable en precisant son type et pour preparer une variable qui sera affecter par la suite.

```go 
package main 

import "fmt"

func main(){
  var count int // declaration 
  count = 3 // affectation 
  fmt.Println(count) // 3

  // inference 
  var name = "Gizmo"
  var age = 10

  // preparation de variable 
  var city string
}
```

### := 

L'operateur `:=` est la syntaxe courte pour declarer une variable. Cette syntase est utilisable que dans un bloc de code.

```go 
package main 

import "fmt"

func main(){
  x := 10 // declaration et initialisation 
  x = x + 5 // update de la valeur 
  fmt.Println(x) // 15 
}
```

## Affectation 

L'operateur `=` permet d'affecter une valeur a une variable. La variable doit etre declarer pour permettre d'affecter une valeur (local scope ou global scope).

Il est impossible d'affecter une valeur differente du type de la variable.

## Portee 

### Bloc scope 

En Go, les `{}` definissent une portee : la zone de code a l'interieur de laquelle les noms de variables existent et son accessible.

```go
main {
  x // disponible dans le bloc 
  if {
    y // disponible que dans le if
  }
}
```

#### Bloc if 

Une variable declare dans un `if` n'est pas accessible de l'exterieur 

```go
package main 

import "fmt"

func main(){
  x := 10 

  if x > 0 {
    y := x * 2 // disponible que dans le bloc if
    fmt.Println("inside :", y) // inside: 20 
  }

  fmt.Println("outside :", x) // outside: 10
}
```

#### Bloc for 

Avec les boucles `for`, tout ce qui est declarer dans l'en-tete n'est disponible que dans la portee de la boucle.

Dans l'exemple, `i` n'est disponible que dans le bloc de la boucle.

```go 
package main 

import "fmt"

func main(){
  for i := 1; i < 3; i++ {
    fmt.Println(i) // 1, 2 ,3
  }
}
