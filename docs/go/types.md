# Types

## Nombre

Les operation entre `float64` et `int` sont interdite.

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

### int

`int` est le type pour les nombres entier. Il prends en charge les operations et les comparaison. Pour l'operation `/`, la partie decimal est supprime.
Il prend des postifs et negatif.

La `zero value` est `0`.

#### strconv.IntSize

Permet de connaitre le nombre de bit utiliser pour le type `int`

```go
package main

import (
  "fmt"
  "strconv"
)

func main(){
  fmt.Println("int size: ", strconv.IntSize, "bits") // int size: 64 bits
}
```

### int64

`int64` permet d'utiliser de stocker des nombres plus grandsm ou lorsque le format des donnes doit etre identique sur differentes machines.
Ce type est adapter dans ce type de cas :

- argent en unite minimal, par exemple en centimes. L'argent est generalement stocker sous forme d'entier pour eviter les probleme de precision des `float`.
- grand compteur et accumulation
- nombres provenant de l'exterieur comme les id utilisateur, numero de transactions

```go
package main

import "fmt"

func main(){
  // on traite des valeurs monetaire stokcer en centimes
  var incomeCents int64
  var expenseCents int64

  fmt.Scan(&incomeCents, &expenceCents)

  balanceCents := incomeCents - expenseCents
  fmt.Println(balanceCents) // 10501
}
```

### uint

Le type `uint` permet de stocker des nombre positif. Sa taille en bit depant de la plateforme de la machine. Il peut etre utile dans ce type de cas :

- travail avec des masque de bits et des aspects de bas niveau
- lorsque l'API exige ce type
- lorsque l'on modelise explicitement un identifiant non negatif et qu'on est sur de n'avoir aucune valeur negative dans le calculs

```go
package main

import "fmt"

func main(){
  u := uint(17)
  fmt.Printf("%v (%T)\n", u, u) // 17 (uint)
}
```

---

Les nombres reel dans l'ordinateur ne sont pas precis. Il ne faut pas effectuer de calcul en utilisant des type `float`. Ils sont stockes sous forme binaire, et certaines fractions decimale ne peuvent pas etre representer.

### float32 & float64

`float32` occupe 32 bits et `float64` occupe 64 bits. Plus il y a de bits et plus la precision est grande. Par defaut, c'est le `float64` qui est utiliser.

La `zero value` est `0.0`

```go
package main

import "fmt"

func main(){
  x := 0.1
  var y float32 = 0.1

  fmt.Printf("x=%v, type=%T\n", x, x) // x=0.1 type=float64
  fmt.Printf("y=%v type=%T\n", y, y) // y=0.1 type=float32
}
```

### Comparaison de float

Les fractions du type `0.1` dans le systeme binaire sont infinies (`1/3` par exemple : 0.33333 ...). Le nombre est stocke de maniere aproximative.

Pour faire des compairaison de `float`, on integre une tolerance pour que l'evaluation de la condition soit fiable.

### Debugage de float

```go
package main

import "fmt"

func main(){
  x := 0.1 + 0.2
  fmt.Printf("x=%v\n", x) // x=0.3000000000004
  fmt.Printf("x=%.17f\n", x) // x=3.00000000004
  fmt.Printf("x=%T\n", x) // type=float64
}
```

---

## Formatage des nombres

Le formatage ne modife pas le nombre, il permet d'ameliorer l'affichage.

### Formatage des float

Par defaut, Go essaie de produit un nombre raisonable. Avec `Printf`, il est possible de definir combien de chiffre apres la virgule afficher, quel format choisirm etc

- `%f` : affiche un nombre a virgule flottante en notation decimal
- `%.2f` : affiche deux nombre apres la virgule en arronissant l'affichage
- `%.0f` : affiche sans partie decimal en arrondissant pour l'affichage
- `%.3f` : trois chiffres apres la virgule
- `%g` : forme normal ou exposant
- `%.3g` : chiffre significatif

```go
func main(){
  x := 1.0 / 3.0

  fmt.Printf("%f\n", x) // 0.33333
  fmt.Printf("%.2f", x) // 0.33
  fmt.Printf("%.0f", x) // 0

  a := 12345.6789
  b := 0.000012345

  fmt.Printf("%g", a) // 12345.6789
  fmt.Prinf("%g", b) // 1.2345e-05
  fmt.Prinf("%.3g", a) //1.23e+04
}
```

---

## Arrondis

Pour faire des arrondis, on utilise le package `math` qui fournis des fonctions utile pour faire differents types d'arrondis.

L'arrondis viens modifier la valeur.

```go
import (
  "fmt"
  "math"
)

func main(){
  // arrondis a l'entier proche
  fmt.Println(math.Round(2.5)) // 3

  // arrondis vers le bas
  fmt.Println(math.Floor(2.9)) // 2

  // arrondis vers le haut
  fmt.Println(math.Ceil(2.1)) // 3
}
```

### Arrondis a deux chiffres comment valeur

Le formatage avec `%.2f` ne modifie par la valeur elle meme. Pour obtenir reellement une valeur a deux chiffres, on utilice ce code

```go
import (
  "fmt"
  "math"
)

func main(){
  x := 12.3456
  y := math.Round(x * 100) / 100

  fmt.Printf("%.10f", x) // 12.345600000
  fmt.Printf("%.10f", y) // 12.350000000 => la valeur est modifier avec l'arrondis
}
```

---

## String

`string` est le type pour les chaines de caracteres. 

En Go, une chaîne est une tableau d'octet. Elle est immuable. On ne peut pas modifier un des éléments interne, on peut seulement créer une nouvelle chaîne à partir de l'ancienne.

La `zero value` est `""`

### Concatenation

Les chaines peuvent être concaténer avec `+`. Il n'est pas possible de realiser des concatenation de type different, il sera nécessaire de faire une conversion avant de pouvoir l'utiliser.

```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	s := "Amigo" + " the best" // s contiendra Amigo the best
	fmt.Println(s)

	s = "" // s contiendra une chaîne vide — sans aucun caractère.
	fmt.Println(s)

	x := 333
	s = "Amigo" + strconv.Itoa(x) // s contiendra Amigo333
	fmt.Println(s)

	// concatenation abrege
	s := "Hello"
	s += " World" // "Hello World"

	// affichage mixte sans concatenation
	name := "Ann"
    age := 20
    fmt.Println("Name:", name, "Age:", age) // Name: Ann Age: 20
}
```

### Caractere speciaux

- `\n` : saut de ligne
- `\t` : tabulation
- `\"` : guillemet double échapper
- `\\` : antislash

### len()

Retourne la taille en octet de la chaîne en type `int`.
On ne peut pas compter le nombre de caractère via cette méthode.

```go
func main() {
    s := "golang"
    fmt.Println(len(s)) // 6
    fmt.Println(len("Hello"))  // 12 (probablement)

    // mini app
	firstName := "John"
    lastName := "Doe"
    role := "Junior Gopher"

    fullName := firstName + " " + lastName

    fmt.Println("=== BADGE ===")                  // === BADGE ===
    fmt.Println("Name:", fullName)               // Name: John Doe
    fmt.Println("Role:", role)                   // Role: Junior Gopher
    fmt.Println("Name length:", len(fullName))   // Name length: 11
}

/*
Representation reel
"Go"      -> [ 'G' ][ 'o' ]                 -> len = 2 octets
"Hello"  -> [ P ][ r ][ i ][ v ][ e ][ t ] -> chaque bloc ≈ 2 octets -> len ≈ 12 octets
*/
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

### Conversion

Package qui permet de faire des conversion de string.

#### strconv.Atoi - string => int

Cette methode prendre une chaine et tente de la convertir en `int`. Elle retourne deux valeur : `num` et `err`.

L'utilisation d'une chaine vide un d'un entier plus grand que ce que peux contenir un `int` provoque une erreur.

```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	// utilisation basique
	s := "42" // chaine qui contient un nombre
	n, err := strconv.Atoi(s) // on tente la conversion

	fmt.Println("n =", n)     // n = 42
	fmt.Println("err =", err) // err = <nil>

	// conversion echouer
	s := "42x" // contient un x donc echec de la conversion
	n, err := strconv.Atoi(s)

	fmt.Println("n =", n)     // n = 0
	fmt.Println("err =", err) // err = strconv.Atoi: parsing "42x": invalid syntax
}
```

#### strconv.Itoa - nombre => string

Cette méthode prend un nombre et le convertis en chaine.

```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	nextID := 101
	msg := "next id = " + strconv.Itoa(nextID) // concatenation possible car convertis
	fmt.Println(msg) // next id = 101
}
```

---

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

---

## Conversion

### Conversion de nombre

Go interdit de melanger differents types numerique dans une meme expression sans conversion explicite.Par exemple, il n'est pas possible de faire une operation avec un type `int` et `int64`.

```go
func main(){
  a := 10
  var b int64 = 20

  fmt.Println(a + b) // impossible: type differents
  fmt.Println(int64(a) + b) // on vient convertir le int
}
```

### Conversion explicite

Go utilise la syntaxe `T(x)` pour convertir de maniere explicite une valeur dans un type compatible.

- `int64(a)` : convertis `a` en `int64`
- `float64(qty)` : convertis `qty` en `float64`
- `int(x)` : contis `x` en `int`

```go
func main(){
  var n int64 = 42
  m := int(n) // conversion en int
}
```

### Overflow

Le debordement peut arriver lorsque l'on convertis un type vers un type plus restrictif. Il est possible d'avoir une perte de donnee dans ce cas et le programme peut continuer a fonctionner avec un mauvais nombre.

Si on essaie d'affecter une constante / un litteral qui ne tient pas dans le type, le compilateur se plaindra. L'erreur est detecter a la compilation.

Dans le cas d'une variable, le compilateur autorise la conversion.

```go
func main(){
  var big int64 = 130
  var small int8 = int8(big) // on convertis dans un type trop restrictif

  fmt.Println(big) // 130
  fmt.Println(small) // -126 = overflow
}
```

Le debordement peut egalement arriver lors d'operation arithmetique. Lorsque l'on doit realiser des calcul, bien faire attention au type utiliser.

### Conversion signed et unsigned

Ce type de conversion peut provoquer des erreurs. Les nombres negatifs deviennent de grand positifs car le motif binaire est conserver.

```go
func main(){
  i := -1
  u := uint(i) // conversion en non signer

  fmt.Println(i) // 1
  fmt.Println(u) // un grand nombre qui depend de la taille de uint
}
```

Mis a part sur les operation bas niveau, l'utilisation du `uint` est a eviter.

### Conversion int et float

La conversion `int(float64)` provoque une troncature, la partie decimal n'est pas conserver.

```go
func main(){
  x := 3.99
  n := int(x)

  fmt.Println(n) // 3
}
```

`float64` ne permet pas de conserver les grand entiers, on peut perdre la valeur exact.

---

## Type personnalise de nombre 

On peut venir creer un nouveau type, qui en interne est stocke comme un nombre mais qui a un sens distinct et des regles de compatibilite disctinct.

Par exemple, un nouveau type `Status` qui sera stocker comme un `int`. Ce n'est pas une variable, ni une constante, c'est un nouveau type.
Il aura la meme representation interne que `int` mais pour le compilateur, c'est un type specifique, et il cessera de malanger ce nouveau type avec un `int` classique.

```go 
func main(){
  type Status int // declaration du nouveau type 

  var s Status = 1 // declaration d'une variable avec le type Status 
  fmt.Printf("%T %v\n", s, s) // main.Status 1 

  // exemple failed
  var code int = 2 
  var s Status = code // impossible, le type ne correspond pas

  var s Status = 2 
  var code int = s // impossible le type ne correspond pas
}
```

Le type s'affiche comme `main.Status` et non comme `int`. 

### Conversion explicite des type personnaliser de nombre 

La conversion explicite fonctionne de la meme maniere que pour les type de base.

```go
func main(){
  type Status int 

  // int -> status
  var code int = 2 
  var s Status = Status(code) // conversion explicite 

  // Status -> int 
  var s Status = 2 
  code := int(s) // conversion explicite 
}
```

