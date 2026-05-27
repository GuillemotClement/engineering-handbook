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

En Go, un `string` est une suite d'octets et est immuable. Cela signifie qu'une fois creer, on ne peut plus modifier ces elements internes, on peut seulement creer une nouvelle chaine a partir de l'ancienne.
Elle a pour zero value `""`.

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

### Indexation de chaine

Lorsque l'on utilise l'expression `s[i]`, Go ne retourne pas un caractere de la string, mais un octet qui represente cette sequence. Le type sera `uint8`, soit un `byte`.
Pour voir le caractere, on utilise `%c`.

```go
func main(){
  s := "Go"

  fmt.Println(s[0], s[1]) // 71 111
  fmt.Prinf("%T\n", s[0]) // uint8
  fmt.Printf("%c\n", s[0]) // G
}
```

### Modifier une chaine

Une chaine etant immutable, on ne peux pas modifier un caractere d'une chaine en venant l'indexer. Pour faire une modification, il est necessaire de creer une nouvelle chaine depuis celle initiale.

```go
func main(){
  s := "go"

  s = "G" + s[1:]
  fmt.Println(s) // Go
}
```

### Tranche de chaine

Les chaine fonctionne sur le meme principe que les slice. On utilise aussi les demi-intervale. Etant donne que cela travaille avec des octects, on risque de couper une "lettre".

```go
func main(){
  s := "golang"

  fmt.Println(s[:2]) // go
  fmt.Println(s[2:]) // lang
  fmt.Println(s[1:4]) // ola
}
```

Avant d'indexer une chaine avec un indice variable, il faut s'assurer que l'indice est valide.

```go
func main(){
  s := "Go"
  i := 1

  if i >= 0 && i < len(s) {
    fmt.Printf("%c\n", s[i]) // o
  }
}
```

### Normaliser une commande

On souhaite aue la premiere lettre d'une commande dans une application CLI soit passer en majuscule.

```go
func titleASCII(s string) string {
  // check de la longueur de la chaine fournis
  if len(s) == 0 {
    return s
  }

  // on recupere le premier caractere
  b := s[0] // byte
  // on realise la transformation
  if b >= 'a' && b <= 'z' {
    b = b - ('a' - 'A')
  }

  // convertis b en string et ajoute la chaine initiale a la suite
  return string(b) + s[1:]
}

func main(){
  fmt.Println(titleASCII("add")) // Add
}
```

### Parcourir une chaine

Lorsque l'on utilise un `range` pour parcourir une chaine, Go parcourt la chaine comme du texte, et a chaque iteration, retourne un caractere sous forme de `rune`.

```go
func main(){
  s := "Hi, World"
  fmt.Println("len bytes:", len(s)) // len bytes: 10

  for i, r := range s {
    fmt.Printf("i=%d r=%c", i, r) // ex: i=0 r=H
  }
}
```

### Compter la longeur en caractere

```go
func main(){
  s := "Hi, World"
  n := 0

  for range s {
    n++
  }

  fmt.Println("runes:", n) // runes: 6
}
```

### Recuperer un caractere

L'utilisation de `s[n]` ne garantis pas la recuperation du caractere. Il faut utiliser un `range` pour recuperer reellement le bon caractere.

```go
func firstRune(s string) (rune, bool) {
  for _, r := range {
    return r, true
  }
  return 0, false
}

func main(){
  // le ok permet de gerer correctement les string vide
  r, ok := firstRune("World")
  fmt.Println(ok, string(r)) // true m
}
```

### Limiter la longeur du texte en caractere

```go
func isTooLongTitle(title string, maxRunes int) bool {
  n := 0
  for range title {
    n++
  }
  return n > maxRunes
}

func main(){
  fmt.Println(isTooLongTitle("buy milk", 20)) // false
}
```

### Reccupere N premiere runes

`range` donne le `i` indice en octect de debut de la rune courante. Si on compte les runesm alors lorsque l'on atteinds N-eme, on connais la frontiere en octets

```go
func prefixRunes(s string, n int) string {
  if n <= 0 {
    return ""
  }

  count := 0
  cut := len(s)

  for i := range s {
    if count == n {
      cut = i
      break
    }
    count++
  }
  return s[:cut]
}

func main(){
  fmt.Println(prefixRunes("Hi, World!", 5)) // Hi, world
}
```

### Conversion []byte et []rune

Lorsque l'on souhaite modifier une chaine, on creer une nouvelle chaine. Pour modifier facilement le contenu, on convetit en general temporairement la chaine dans une forme plus adapte :

- `[]byte` : lorsque l'on travail avec des octects (caractere ASCII, tabulation, retour a la ligne, donnee binaire)
- `[]rune` : lorsque l'on veux travailler avec des caractere unicode (lettre)

La conversion creer une nouvelle valeur, la chaine reste intacte.

```go
func main(){
  s := "go"
  b := []byte(s)
  b[0] = 'G'

  fmt.Println(s) // go
  fmt.Println(string(b)) // Go
}
```

---

### Modifier une chaine via []byte

Une chaine arrive de l'entreem et elle contient des caracteres technique. On souhaite normaliser cette chaine. Ici, on veux remplacer les tabulation `\t` par un espace.
On utilise les operation sur octets pour faire cette normalisation.

Cette approche est correct pour des modifications d'octets ASCII technique. Pour les autres types, c'est dangereux car ils peuvent potentiellenment occuper plus d'octets.

```go 
func replaceTabsASCII(s string) string {
  b := []byte(s) // on convertis la chaine en byte 

  // on parcours les bytes de la chaine
  for i := 0; i < len(b); i++ {
    // on verifie si le caractere match 
    if b[i] == '\t' {
      b[i] = ' ' // on remplace par un espace 
    }
  }

  return string(b) // permet de creer une nouvelle chaine a partir du slice d'octet
}

func main(){
  fmt.Println(replaceTabsASCII("A\tB\tC")) // A B C
}
```

On peut egalement utiliser cette approche pour modifier une string. 

```go 
func wrapWithBrackets(s string) string {
  var b []byte
  b = append(b, '[') 
  b = append(b, s...) // la string est expanded en bytes
  b = append(b, ']')
  return string(b)
}

func main(){
  fmt.Println(wrapWithBrackets("go") // [go]
}
```

---

### []rune() - travail sur les caracteres

`[]rune()` prends une chaine UTF-8, et la decode en une sequence de caractere. Elle permet de simplifier le travail lorsque l'on souhaite modifier des caractere dans une chaine 

```go 
// remplace la premiere lettre d'une chaine => les deux doivent etre sous forme de rune
func replaceFirstRune(s string, newFirst rune string {
  r := []rune(s)
  if len(r) == 0 {
    return s
  }
  r[0] = newFirst // on modifie la premiere lettre de la chaine 
  return string(r)  
}

func main(){
  fmt.Println(replaceFirstRune("hello", 'H')) // Hello
}
```

---

### Package strings

Package dans la blibliotheque standard Go qui contient des fonction pour travailler avec des `string`.

```go
package main

import (
  "fmt"
  "strings"
)

func main(){
  // nettoyage des espaces
  raw := "  go, strings, utf8  "
  clean := strings.TrimpSpace(raw) // "go, strings, utf8"

  // decouper une chaine => prends un separateur et retourne un slice de mots
  s := "go, strings, utf-8"
  parts := strings.Split(s, ",") // [go strings utf-8]

  // fusion []string => prends le slice et le separateur qui sera ajouter
  out := strings.Join(parts, ", ") // "go, strings, utf-8

  // accumuler une chaine => concatenation  
  var b strings.Builder

  b.WriteString("Tags: ")
  b.WriteString("go")
  b.WriteString(", ")
  b.WriteString("strings")

  fmt.Println(b.String()) // Tags: go, strings
}
```

On creer une fonction permettant de nettoyer une chaine

```go
package main 

import (
  "fmt"
  "strings"
)

func main(){
  raw := "  go,  strings,  utf-8   "
  raw = strings.TrimSpace(raw) // nettoyage de la string 

  parts := string.Split(raw, ",") // transformation en slice
  for i := 0; i < len(parts); i++ {
    parts[i] = strings.TrimSpace(parts[i]) // nettoayge chaque mot 
  } 

  cleanStr := strings.Join(parts, ", ") // go, strings, utf-8

  // on viens ensuite ajouter un nouveau mot en debut de chaine 
  var b strings.Builder
  b.WriteStrings("Tags: ")
  b.WriteStrings(cleanStr)

  fmt.Println(b.String()) // Tags: go, strings, utf-8
}
```

---

### Package bytes

Il contient plus ou moins les meme fonctions que le package `strings`. 

```go
package main 

import (
  "bytes"
  "fmt"
)

func main(){
  // tranformer en slice de byte
  b := []byte("go,strings,utf-8") // transforme la chaine en byte 
  parts := bytes.Split(b, []byte[","]) // [[103  111] [115 116 114 110 103 115]...]

  // fusionne les bytes de slice 
  out := bytes.Join(parts, []byte(" + ")) 
  fmt.Println(string(out)) // go + bytes

  // accumuler des bytes 
  var buf bytes.Buffer 

  buf.WriteString("ID=")
  buf.WriteString("42")

  fmt.Println(buf.String()) // ID=42
}
```

Comme pour l'exemple de string, on met en place une fonction de nettoyage de chaine 

```go 
package main 

import (
  "bytes"
  "fmt"
)

func main(){
  raw := []byte{"  go,  bytes,  utf-8  "}
  raw := bytes.TrimSpace(raw)

  parts := bytes.Split(raw, []byte(","))
  for i := 0; i < len(parts); i++ {
    parts[i] = bytes.TrimSpace(parts[i])
  }

  var buf bytes.Buffer
  buf.WriteString("Tags: ")
  buf.Write(bytes.Join(parts, []byte(", ")))

  fmt.Println(bug.String()) // Tags: go, bytes, utf-8
}
```
---

### Debug de chaine 

Une entree utilisateur peut potentiellement contenir des caracteres innatendu comme des tab, des retour a la ligne, etc
Pour afficher le contenu reel d'une chaine, on peut utiliser `%q` avec `Printf()`.

```go 
func main(){
  s := "add\r\r"
  fmt.Prinln(s) // add 
  fmt.Printf("%q\n", s) // add\r\n
}
```

Il existe egalement des situations ou `%q` montre quelque chose d'etrange. Dans ce cas, on viens afficher la valeur en octet exact :

```go 
func main(){
  a := "A B"
  b := "A\tB"

  fmt.Printf("%q bytes=%v\n", a, []byte(a)) // "A B" bytes=[65 32 66] => 32 correspond a espace
  fmt.Printf("%q bytes=%v\n" b, []byte(b)) // "A\tB" bytes=[65 9 66] => 9 correspond a tabulation
}
```

Lorsque la chaine contient des caractere non ACSII, les octets ne sont plus vraiment adapter, et le debug est plus facile en utilisant des rune.

```go 
func main(){
  s := "add\r\n"
  for i, r := range s {
    fmt.Printf("i=%d r=%q\n", i , r)
  }
  // i=0 r='a'
  // i=1 r='d'
  // i=2 r='d'
  // i=3 r='\r'
  // i=4 r='\n'
}
```

### Nettoyage de chaine 

Une application CLI enregistre des tasks. On souhaite normaliser la saisie pour eviter les probleme d'affichage par la suite.

```go 
package main 

import (
  "bufio"
  "fmt"
  "os"
  "strings"
)

func main(){
  r := bufio.NewReader(os.Stdin)
  tasks := make([]string, 0) // slice qui va contenir les saisie

  line, _ := r.ReadString('\n') // lecture saisie
  title := strings.TrimSpace(line) // nettoyage de la saisie

  tasks = append(tasks, title) // ajout dans le slice 
  fmt.Println("saved: %q\n", tasks[0]) // save: "..."
}
```







---

## Rune

`rune` est un entier qui represente un caractere unicode. On utilise `''` pour definir un caractere.
Un rune est un nombre. On peut l'afficher en tant que caractere ou de nombre

```go
func main(){
  r := 'World'
  fmt.Println("%T\n", r) // int32
  fmt.Println("%d\n", r) // 19990
  fmt.Println("%c\n", r) // World
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
