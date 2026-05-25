# Package

Les packages en Go sont des espaces de nom. Ils permettent de grouper le code par sens et donner des regles sur qui peut accder au methodes et variable.

## Acces aux methodes et variable d'un package 

Pour acceder au element exporter d'un package, on utilise la notation point. On commence par faire reference au package, puis l'element a lequel on souhaite acceder.

```go
fmt.Println()
```

 - `fmt` : le package qui contient l'ensemble des methodes et variables
 - `Println()` : methode du package 

## Definir le package 

Dans les fichier Go, la premiere ligne est utiliser pour definir le package du fichier. Un fichier n'est pas un package, mais une unite de ce package.
Tous les fichiers ayant le meme dossier sont considerer comme faisant partie du meme package. Il partage le meme score au niveau du package.

```go 
package [package_name]
// ===================
package main 

import "fmt"

func main(){
  fmt.Prinln("hi")
}
```

Dans ce code, le fichier appartient au package `main`. 

### Package-level scope 

Dans un meme package, tous les fichiers voient les declarations des autres package. Il n'est donc pas necessaire d'importer des methodes et variables depuis un fichier du package.

Le code peut se structurer de cette maniere. Dans un premier temps, le fichier `main.go` qui permet d'organiser les appelles de methodes et d'orchestrer l'application.
```go 
// main.go 
package main 

import "fmt"

func main(){
  income, expense := readIncomeExpense() 
  balance := income - expense 

  fmt.Println(formatReport(income, expense, balance))
}
```

On retrouve ensuite un fichier `io.go`, responsable de la logique lier a la recuperation des saisies 

```go 
// io.go 
package main 

import "fmt"

func readIncomeExpense() (int, int) {
  var income int 
  var expense int 

  fmt.Scan(&income, &expense)
  return income, expense 
}
```

On retrouve un autre fichier `report.go`, responsable du formatage 

```go 
// report.go 
package main 

import "fmt"

func formatReport(income, expense, balance int) string {
  return fmt.Sprintf("income=%d expense=%d balance=%d", income, expense, balance)
}
```

Les trois fichiers font parties du meme package `main`. Ils peuvent appeler les fonctions declarer dans les differents fichiers du package.

---

## package main

Le package `main` est un cas special : le programme demarrer a partir de celui ci et le point d'entree est la fonction `main()`.

---

## Règle d'exportation

En Go, pour définir si un élément (constante, type, variable, fonction) est exporté ou non c'est la première lettre qui définit ce comportement :

- **majuscule** : il est exporté et accessible depuis d'autre package
- **minuscule** : il est non exporté, accessible uniquement à l'intérieur de son package.

L'export d'élément n'as aucune incidence sur la sécurité. Il s'agit uniquement d'un contrat et d'une promesse. On indique juste que cet élément est disponible en dehors du package.
On exporte uniquement les éléments intéressant pour en dehors du package. La logique interne au package reste privée, il n'y a aucun intérêt à rendre cela public.

```go
// report.go 
package main

import "fmt"

// fonction exporter 
func PrintBalanceReport(income, expense int) string {
  balance := income - expense
  return formatReport(income, expense, balance)
}

// fonction interne non exporter
func formatReport(income, expense, balance int) string {
  return fmt.Sprintf("income=%d expense=%d balance=%d", income, expense, balance)
}

// main.go 
package main 

import "fmt"

func main(){
  income, expense := readIncomeExpense()
  fmt.Println(PrintBalanceReport(income, expense))
}
```

---

## Import path

`Import path` c'est ce qui figure entre guillemets dans `import`. Il permet au compilateur d'aller recuperer le package pour utiliser les methodes et il doit pointer vers une adresse unique. On retrouve par exemple :

- `fmt`
- `strconv`
` math/rand`

--- 

## Package name 

C'est le nom donner au package. C'est celui ci qui sera pointer ensuite par `import` pour permettre au compilateur de recuperer le code necessaire.

Il ne correspond pas forcement a l'import, generalement, c'est le dernier segment du chemin.

- import path : `math/rand`
- package name : `rand`

```go
// package name 
package main

// import path 
import (
	"fmt"
	"math/rand" // on fait reference au dernier segment pour appeler les methode du package
)

func main() {
	// on fait référence au package math/rand
	fmt.Println(rand.Intn(10)) // par exemple : 7
}
```

---

### Package interne 

Dans la lib standard, les import path sont court et fixe : `fmt`, `strings`. Dans les projets, on retrouve des import du type `github.com/someone/projet/pkg/...`. Cela permet de rendre l'import path unique dans le monde.

Pour ces propres package, l'import path doit commencer par le prefixe du projet.

### StudyBuddy - exemple 

Le programme est un petit assistant qui demande un nom et le nomnre de tache resolu. Il affiche ensuite un petit rapport et une phrase de motivation.

```go
// main.go 
package main 

import (
  "fmt"
  "math/rand"
  "time"
)

func main(){
  rand.Seed(time.Now().UnixNano())

  var name String 
  var solved int 

  fmt.Print("Name and solved :")
  fmt.Scan(&name, &solved)

  fmt.Printf("%s solved %d tasks. %s\n", name, solved, pickPhrase())
}

// 
package main 

import (
  "math/rand"
)

func pickPhrase() string {
  phrases := []string{"Tenez bon !", "Vous assurez !", "Encore un petit effort !"}
  return phrases[rand.Intn(len(phrases))] // retourne une phrase aleatoire
}

// 
package main 

import (
  "math/rand"
  "time"
)

func initRandom(){
  rand.Seed(time.Now().UnixNano())
}
```

---

## Imports

Pour utiliser du code exterieur au package, on utilis `import`. L'import est valide uniquement pour le fichier. 
Il existe deux formes d'import : grouper ou seule :

```go
// import seul
import "fmt"

// import groupé
import (
	"fmt"
	"strconv"
)
```

### Alias d'import

Dans le cas ou l'on souhaite importer plusieurs package ayant le même nom, ou si le nom de package n'est pas idéale, on peut venir créer un alias.
Cela permet de renommer un package dans un fichier.

```go
// amélioration de la lisibilité
package main

import (
	"fmt"
	s "strings" // création d'un alias
)

func main(){
	var title string
	fmt.Scan(&title)

	title := s.TrimSpace(title) // on fait référence à l'alias du package
	fmt.Println("task:", title)
}

// =================================
// conflit de nom
package main

import (
	crand "crypto/rand"
	"fmt"
	mrand "math/rand"
)

func main(){
	_ := crand.Reader
	fmt.Println(mrand.Intn(10))
}
```

### Blank import _

Le blank import est un lorsque l'on importe un package sans introduire son nom dans le code. On l'utilise dans le cas où le package doit être chargé et exécuter sans utiliser ces fonctions ou type.
En go, des package peuvent avoir du code d'initialisation, qui s'exécute automatiquement au démarrage du programme. Par exemple, pour les pilotes de base de donnée. On les appelles pas directement, mais il sont enregistrée à l'intérieur de `database/sql`.

```go
package main

import (
	"fmt"
	_ "math"
)

func main() {
	fmt.Println("started") // started
}
```

---


## init()

Fonction spécial qui est appelée automatiquement, et s'exécute avant `main()`. Elle ne prend aucun paramètres et ne retourne aucune valeur de retour.
Elle fait partie du processus d'initialisation des paquets : le runtime Go execute l'initialisation du paquet (mise en place des variables globales, et appel a `init()`) puis il passe a l'execution de `main()`

```go
func init(){
  // initialisation automatique
}
```

L'ordre d'execution est le suivant :

1. les paquets importe sont initialiser (variables et leur `init()`)
2. le paquet courant est initialiser
3. `main()` est lancer depuis `package main`

```go
package main

import "fmt"

func init(){
	fmt.Println("init")
}

func main(){
	fmt.Println("main")
}
```

Si plusieurs `init()` sont present, il s'execute tous avant le `main()`. Mais attention, cela peut provoquer des comportements innatendu.


### Utilisation acceptable 

Il existe certain cas ou l'utilisable `init()` est utile : 

- ne peut pas planter avec une erreur normal 
- ne depend pas de l'entree utilisateur, fichiers ou reseau 
- ne cache pas la logique metier du demarrage de l'application 

Par exemple, la preparation d'un generateur de nombre pseudo-aleatoire pour des besoins interne. 

```go 
package main 

import (
  "fmt"
  "math/rand"
  "time"
)

var runID int // prepare la variable en globale

func int(){
  // lancer automatiquement et set la variable avec la valeur aleatoire
  runID = rand.New(rand.NewSource(time.Now().UnixNano())).Intn(10000)
}

func main(){
  fmt.Println("run id: ", runID ) // run id: 123 (exemple)
}
```

### init explicite

Il est preferable de creer une fonction d'initalisation explicite, ou on l'appelle depuis `main()`. Par exemple, dans une app, on souhaite obtenir des prefixe pour les affichages.

```go 
package main 

import (
  "fmt"
)

func initApp() (string, error) {
  prefix := "stats: " // definition du prefix
  if prefix := "" {
    return "", errors.New("empty prefix")
  }
  return prefix, nil
}

func main(){
  prefix, err:= initApp() // initialisation explicite
  if err != nil {
    fmt.Println("init error:" err)
    return
  }
  fmt.Println(prefix, "ready")
}
```

---

## Anti-patterns de package

En Go, un package est une **frontière de responsabilité**, et un **contrat pour les autres parties du code**. Tout ce qui est exporter depuis un package devient une partie de la "promesse" vers les autres. Si on modifie cette promesse, on casse les client. L'API publique d'un lib standard ne peut pas etre simplement deplacer, sinon les programmes externe qui en dependent casse.

### Etat global

Lorsque l'on déclare une variable dans la portée globale, elle vit plus longtemps que ce que l'on pense, et elle est modifiable depuis de nombreux endroits, le comportement peut devenir floue (changement de valeur innatendu, ...). **L'etat mutable doit etre le plus local et le plus explicite possible**.

Lorsqu'une variable globale se trouve dans un paquet, on ajoute une "memoire cacher" au package.


```go
// anti pattern
package main

var lastResult int // état global => n'importe qui peut modifier

func addToLast(x int){
	lastResult += x
}
```

L'ideal est de declarer l'etat au plus pres des besoin dans le `main()` et de la transmettre de maniere explicite.

```go
package main

func add(a, b int) int {
	return a + b
}

func main() {
	last := 0 // variable "global"
	last = add(last, 10)
	_ = last // ici, on peut ensuite imprimer last via fmt.Println
}
```

### Config global 

Pour definir des variable de config global, l'ideal est d'utiliser des `const`, soit comme une valeur que l'on transmet de maniere explicite la ou elle est necessaire.

```go
// declaration avec une const 
package pain 

const appName = "CalcCLI"

func main(){
  _ = appName // securisee : on ne peut pas modifier une constante 
}
```

Si la valeur doit changer, on la transmet 

```go 
package main 

func formatResult(n int, debug bool) string {
  if debug {
    return "DEBUG: result=" // puis ajout du nombre
  }
  return "result="
}
```

### Dépendances inutiles et imports qui s'étendent

Les imports doivent etre limiter le plus possible. Plus il y a d'import de declarer dans un fichier, et plus il est difficile de comprendre ce que fais le fichier.

Les imports peuvent être "utile", mais conceptuellement inutiles. Par exemple `fmt` dans la logique pour un `Print()` de débug, puis cet import est garder alors qu'il n'est plus necessaire.

```go
// anti-pattern
package main

import "fmt"

func add(a, b int) int {
	fmt.Println("adding ...") // debug temporaire qui devient un comportement de la fonction
	return a + b
}
```

En plus du "bruit", la fonction `add` fait maintenant deux choses : calcule et imprime.
Pour améliorer, on peut utiliser ce pattern

```go
package main

import "fmt"

// responsable uniquement de la logique
func add(a, b int) int {
	return a + b
}

func main(){
	fmt.Println(add(2, 3)) // ici on fait le log
}
```

### Fuite des détails d'implémentation via l'API

Si le package renvoie ou accepte dans ses fonctions publiques des types qui sont des détails d'implémentation, on lie le code externe à ces détails.
Pour faire évoluer le code, cela peut devenir compliquer.

Les fonction publiques d'un paquet doivent communiquer avec le monde exterieur via des types simples et attendus (`string`, `int`, `bool`) et des résultats clairs.

#### Package utils

On peut retrouver dans les projet Go, des package `uils`, `helpers` ou `common` et y mettre tout et n'importe quoi.

Pour corrige cela, on vient nommer de maniere explicite les packages en se basant sur le sens.

| Nom frequent | Pourquoi c'est peu lisible           | Alternative plus previsible                                |
| ------------ | ------------------------------------ | ---------------------------------------------------------- |
| `utils`      | « il y a tout » = « rien de precis » | parse, format, calc (selon le role)                        |
| `common`     | commun a quoi ?                      | config, constants (si c'est vraiment de cela qu'il s'agit) |
| `helpers`    | aider qui ?                          | nom selon le domaine : tasks, users                        |
| `data`       | les donnees, c'est trop large        | storage, input, output (selon l'objectif)                  |

#### Architecture propre simple

Pour garder un projet maintenable, on peut utiliser ce type d'architecture. L'idee est de separer le code par sens, au moins par fichier et de garder les dependance locale.

```tree
calccli/
├── main.go     // entree/sortie, communication avec l'utilisateur
├── parse.go    // analyse des nombres et des commandes
└── ops.go      // arithmetique (fonctions pures)
```

Chaque fichier possède sa responsabilité.
Par exemple, le fichier `ops.go` est charger d'implémentation les fonctions responsables des calculs

```go
package main

func apply(op string, a, b int) int {
	if op == "add" {
		return a + b
	}
	return a - b
}
```

Dans le fichier `main.go`, on viens orchestrer l'ensemble

```go
package main

import "fmt"

func main() {
	var op string
	var a, b int
	fmt.Scan(&op, &a, &b)
	fmt.Println(apply(op, a, b))
}
```