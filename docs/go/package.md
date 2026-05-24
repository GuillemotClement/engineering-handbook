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












### Import path

L'import path permet au compilateur et aux outils d'aller chercher le code nécessaire. Il doit pointer sur un package unique.

```go
package main

import (
	"fmt"
	"math/rand"
)

func main() {
	// on fait référence au package math/rand
	fmt.Println(rand.Intn(10)) // par exemple : 7
}
```

Pour utiliser ensuite les fonction du package, on fait référence au package, puis la fonction que l'on souhaite utiliser.

Généralement, le `package name` correspond au dernier segment du chemin.

#### Package avec le même nom

```go
package main

import (
	"crypto/rand"
	"math/rang"
)

func main(){}
```

Ce code ne compileras pas car dans un fichier, il ne peut pas avoir deux package avec le même nom d'import

### Imports

`import` permet d'ajouter une dépendance dans le fichier, et ainsi d'utiliser des fonctions d'un package.
L'instruction indique au compilateur quels packages est nécessaire à ce **fichier précis**

```go
// import seul
import "fmt"

// import groupé
import (
	"fmt"
	"strconv"
)
```

#### Alias d'import

Dans le cas ou l'on souhaite importer plusieurs package ayant le même nom, ou si le nom de package n'est pas idéale, on peut venir créer un alias.
Cela permet de renommer un package dans un fichier.

```go
// améioration de la lisibilité
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

#### Blank import

Le blank import est un lorsque l'on importe un package sans introduire son nom dans le code.
On l'utilise dans le cas où le package doit être chargé et exécuter sans utiliser ces fonctions ou type.

En go, des package peut avoir du code d'initialisation, qui s'exécute automatiquement au démarrage du programme. Par exemple, pour les pilotes de base de donnée. On les appelles pas directement, mais il sont enregistrée à l'intérieur de `database/sql`.

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

### init()

Fonction spécial qui est appelée automatiquement, et s'exécute avant `main()`. Le runtime Go exécute l'initialisation du paquet (mise en place des variables globales, et les appels à `init()`), puis ensuite passe à `main()`.

La fonction ne prends aucun paramètres et ne retourne aucune valeur de retour.

Si plusieurs fonctions `init()` sont déclarée, ils s'exécutent tous avant.

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

Ce système provoque des effet secondaire et doit être utiliser avec précaution.

#### init explicite

Lorsqu'une fonction `init()` commence à grossir, on peu venir créer une initialisation explicite, pour rendre le code plus explicite.

On créer une fonction, par exemple `initApp()`, que l'on appelle ensuite dans le `main()`

```go
package main

import (
	"fmt"
	"time"
)

func initApp() (string, time.Time){
	prefix := "stats: "
	if prefix == ""{
		return "", errors.New("empty prefix")
	}
	return prefix, nil
}

func main(){
	// appel de la fonction d'initialisation
	prefix, err := initApp()
	if err != nil {
		fmt.Println("init error:", err)
		return
	}
	fmt.Println(prefix + "ready")
}
```

### Anti-patterns de package

En Go, un package est une **frontière de responsabilité**, et un **contrat pour les autres parties du code**.

#### Etat global et config global

Lorsque l'on déclare une variable dans la portée globale, elle vit plus longtemps que ce que l'on pense, et elle est modifiable depuis de nombreux endroits, le comportement peut devenir floue (changement de valeur innatendu, ...)

```go
package main

var lastResult int // état global => n'importe qui peut modifier

func addToLast(x int){
	lastResult += x
}
```

Pour conserver un état "global", on préfère la déclarer près de `main()`, et la transmettre explicitement.

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

Attention, cela reste une mauvaise pratique. L'import de ce paquet dépend de l'historique des appels, et pas seulement des paramètres d'entrée des fonctions.

Pour garder une bonne pratique, le mieux est d'utiliser une constante pour une variable qui ne doit pas changer de valeur, et si la valeur doit changer, la définir près de la logique

L'état mutable doit être le plus local et explicite.

```go
package main

const appName = "CalcCLI"

func main(){
	_ := appName
}

func formatResult(n int, debug bool) string {
	if debug {
		return "DEBUG: result="
	}
	return "result="

}

```

#### Dépendances inutiles et imports qui s'étendent

Les imports peuvent être "utile", mais conceptuellement inutiles. Par exemple `fmt` dans la logique pour un `Print()` de débug.

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

#### Fuite des détails d'implémentation via l'API

Si le package renvoie ou accepte dans ses fonctions publiques des types qui sont des détails d'implémentation, on lie le code externe à ces détails.
Pour faire évoluer le code, cela peut devenir compliquer.

Les fonction publiques d'un paquet doivent communiquer avec le monde exterieur via des types simples et attendus (`string`, `int`, `bool`) et des résultats clairs.

#### Package utils

On peut retrouver dans les projet Go, des package `uils`, `helpers` ou `common` et y mettre tout et n'importe quoi.

Pour corrige cela, on vient nommer correctement en se basant sur le sens.

| Nom frequent | Pourquoi c'est peu lisible           | Alternative plus previsible                                |
| ------------ | ------------------------------------ | ---------------------------------------------------------- |
| `utils`      | « il y a tout » = « rien de precis » | parse, format, calc (selon le role)                        |
| `common`     | commun a quoi ?                      | config, constants (si c'est vraiment de cela qu'il s'agit) |
| `helpers`    | aider qui ?                          | nom selon le domaine : tasks, users                        |
| `data`       | les donnees, c'est trop large        | storage, input, output (selon l'objectif)                  |

#### Architecture propre simple

Pour garder un projet maintenable, on peut utiliser ce type d'architecture.

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