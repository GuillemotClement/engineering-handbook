# Variable

En Go, une variable a une type, un nom et une valeur. Lorsque la variable est decalrer, le type est definis et il n'est pas possible de le changer.

En Go des qu'une variable est creer, celle ci recoit automatiquement la valeur par defaut de son type. Cette valeur est la `zero value`. Elle n'est pas nulle, mais contient la valeur elementaire du type.
Cela permet de lire immediatement une variable apres sa declaration, sans risque d'obtenir des comportement innatendus.

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

Pour utiliser l'operateur, il faut aussi qu'un moins une des variables soit declarer pour la premiere fois 

```go 
package main 

import "fmt"

func main(){
  x := 10 
  y := 20 

  x, z := y, 30 // z est un nouveau nom
  fmt.Println(x, y, z) // 20 20 30
}
```

## Affectation 

L'operateur `=` permet d'affecter une valeur a une variable. La variable doit etre declarer pour permettre d'affecter une valeur (local scope ou global scope).

Il est impossible d'affecter une valeur differente du type de la variable.

### Affectation multiple 

```go
a, b = 10, 20
```

- `a` prend la valeur 10
- `b` prend la valeur 20

Le côté droit est évalué entièrement, puis les résultats sont répartis dans les variables de gauche.

```go
func main() {
	x := 1
	y := 2

	x, y = y, x+y
	fmt.Println(x, y) // 2 3
}
```

### Permutation de valeurs 

Cela peut être utile pour permuter les valeurs sans utiliser de variables temporaire

```go
func main() {
	from := 0
	to := 0

	fmt.Scan(&from, &to)

	if from > to {
		from, to = to, from // permutation
	}

	fmt.Printf("range: %d..%d\n", from, to) // par exemple : range: 3..10
}
```

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
```

## Shadowing

Le shadowing est la situation ou dans un bloc de code interne, on creer une nouvelle variable avec le meme nom que dans le bloc externe. Dans le bloc, on accede a la nouvelle variable, mais dans le bloc externe, c'est toujours l'ancienne valeur qui est stocker.

```go 
package main 

import "fmt"

func main(){
  x := 10 

  if x > 0 {
    x := 99 
    fmt.Println("inside: ", x) // 99
  }

  fmt.Println("outside: ", x) // 10 
}
```

### if avec init statement 

En Go, il existe une forme populaire de `if`. On peut utiliser une declaration courte avant la condition. Cela peut provoquer des comportements innatendus 

```go 
package main 

import "fmt"

func main(){
  result := 100

  // dans le bloc, la valeur est maintenant 5
  if result := 5; result < 10 {
    fmt.Println("inside: ", result) // 5 
  } 

  fmt.Println("outside: ", result) // 100 
}
```

## Variable globale

Go permet de declarer des variables dans la scope du package, ce sont des globales. Ces variables sont declarer en dehors des fonctions.

- `:=` n'est pas utilisable pour declarer une variable globale 
- les variables globales sont visible pour toutes les fonctions du package

Il est facile de faire un shadowing avec les variables globales, si on vient redefinir avec `:=` une variable globale.

```go 
package main 

import "fmt" 

var appName = "Range Normalizer" // variable globale 

func normalize(from, to int) (int, int) {
  if from > to {
    from, to = to, form 
  }
  return form, to
}

func main(){
  form, to := 10, 3
  from, to = normalize(from, to)
  fmt.Println(appName, from, to) // Range Normalizer 3 10 
}
```

## Blank Identifier 

Permet d'ignorer une valeur retourner par une fonction 

```go 
package main 

import "fmt"

func main(){
  a := 0 
  b := 0 

  _, _ := fmt.Scan(&a, &b)
  fmt.Println("a=%d b=%d\n", a, b) // a=3 b=10
}
```