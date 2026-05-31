# Error

En Go, les erreurs sont considerer comme des valeurs que l'on peut affecter, retourner depuis des fonctions, et afficher.
On verifie de maniere explicite `err` pour traiter au plus pres les cas d'erreur. Ce style permet de rendre le flux de controle plus previsible.

En pratique, si une fonction peut echouer, elle renvoie generalement une secondes valeurs de type `error`. Cette valeur est ensuite traiter de maniere explicite au niveau de l'endroit ou la fonction est appeler.

## Type error

`error` est un type d'interface. Il est definit de cette maniere :

```go
type error interface {
  Error() string
}
```

L'interface est minimal : si une valeur possede la methode `Error()` qui retourne une `string`, alors cette valeur peut etre utilisee comme `error`.

- `error` n'est pas une chaine, mais peut fournir une chaine avec la methode `Error()`.

Il est possible de creer un type error personnaliser pour ajouter des elements sur l'erreur

```go
package main

import "fmt"

// creation du type personaliser
type myError struct{}

// implemente le contrat error => lier au type struct
func (myError) Error() string {
  return "something went wrong"
}

func main(){
  var err error = myError{}
  fmt.Println(err) // something went wrong
}
```

On peut declarer une variable de type `error` pour preparer avant utilisation

```go
func main(){
  var err error // permet de stocker une erreur
}
```

---

## Traitement des erreur

`nil` dans `error` signifie un succes. Une valeur `non=nil` signifie qu'une erreur s'est produit. Par defaut, `error` prends une zero value de `nil`.

```go
package main

import "fmt"

func main(){
  var err error // zero value => error = nil
  fmt.Println(err == nil) // true
  fmt.Println(err) // <nil>

  // traitement normal
  n, err := strcon.Atoi("42")
  fmt.Println(n) // 42
  fmt.Println(err == nil) // true

  // traitement d'une erreur
  n, err := strcon.Atoi("4x")
  fmt.Println(n) // 0 // zero value pour un int
  fmt.Println(err) // strconv.Atoi: parsing "4x": invalid syntax
}
```

---

## err.Error()

Lorsque l'on as besoin d'afficher le message d'erreur et le concatener dans une autre chaine

```go
func main(){
  _, err := strcon.Atoi("x")
  if err != nil {
    fmt.Println("message:", err.Error()) // message: strconv.Atoi: parsing "x": invalid syntax
  }
}
```

---

## Traitement des erreurs dans un programme

Le programme vient lire le nombre d'enregistre `n`, puis `n` paires categorrie de montant. Le montant arrive sous forme de `string` et il faut le convertir.

On retrouve une premiere fonction responsable de la lecture des saisie. Elle retourne la valeur et l'erreur.

```go
package main

import "fmt"

// fonction viens lire le token entrant
func readToken() (string, error) {
	var s string
	_, err := fmt.Scan(&s) // on recupere l'erreur
	return s, err // on retourne la valeur + err
}
```

On retrouve ensuite une fonction de lecture de montant. Elle lit le token sous formne de chaine et tente de le convertir en `int`

Si une erreur survient, on retourne `0` et l'erreur.

```go
package main

import (
	"strconv"
)

func readAmount() (int, error) {
	token, err := readToken()
	if err != nil {
		return 0, err
	}
	return strconv.Atoi(token), nil
}
```

On viens ensuite mettre en place le programme

```go
package main

import (
	"fmt"
)

func main() {
	n, err := readAmount()
	if err != nil {
		fmt.Println("failed to read n:", err)
		return
	}

	totals := make(map[string]int)

	for i := 0; i < n; i++ {
		cat, err := readToken()
		if err != nil {
			fmt.Println("failed to read category:", err)
			return
		}

		amount, err := readAmount()
		if err != nil {
			fmt.Println("failed to read amount:", err)
			return
		}

		totals[cat] = totals[cat] + amount
	}

	fmt.Println("totals:", totals)
}
```

---

## errors.New(s) - creer une erreur simple

Permet de creer une nouvelle erreur. Elle prends une chaine et retourne une valeur de type `error`.

```go
package main

import (
  "errors"
  "fmt"
)

func Divide(a, b int) (int, error) {
  if b == 0 {
    // on creer une nouvelle erreur dans le return
    return 0, errors.New("division by zero")
  }
  return a / b, nil
}

func main(){
  q, err := Divide(10, 0) // on recupere la valeur + le message d'erreur creer dans la fonction appeler
  fmt.Println(q, err) // 0 "division by zero"
}
```

---

## fmt.Errorf() - creer un erreur avec formatage

Formate une chaine selon les regles de `fmt.Printf` et la retourne comme une `error`. Permet d'obtenir des message d'erreur plus precis qu'avec un simple `errors.New()`

```go
package main

import "fmt"

func Percent(part, total int) (int, error) {
  if total == 0 {
    return 0, fmt.Errorf("percent: total is 0 (part=%d)", part)
  }
  return part * 100 / total, nil
}

func main(){
  p, err := Percent(5, 0)
  fmt.Println(p, err) // 0 percent: total is 0 (part=5)
}
```

---

## Calculatric avec validation

L'application vient lire l'entree, parser, calculer puis afficher. On utilise des fonction qui retourne les valeurs et les erreurs. Chaque fonction as sa propre responsabilite, et est responsable du traitement d'erreur. L'erreur est remonter pour etre traiter dans le flux.

`parseInt()` est une fonction helper qui vient convertir en chaine en int. L'utilisation de `%q` permet d'afficher la valeur en guillemets.

```go
package main

import (
	"fmt"
	"strconv"
)

func parseInt(s string) (int, error) {
	n, err := strconv.Atoi(s) // on parse et on traite le resultat
	if err != nil {
		return 0, fmt.Errorf("parse int %q: %v", s, err) // on detaille le message d'erreur
	}
	return n, nil
}
```

La fonction `compute()` permet de faire le calcul. Elle prends les deux valeur `int` et l'operation en argument.

```go
package main

import (
	"errors"
	"fmt"
)

func compute(a, b int, op string) (int, error) {
	switch op {
	case "+":
		return a + b, nil
	case "/":
		if b == 0 {
			return 0, errors.New("division by zero") // on creer un message simple avec errors.New()
		}
		return a / b, nil
	default:
		return 0, fmt.Errorf("unknown operation %q", op) // on ajoute les details dans le message d'erreur
	}
}
```

On construit ensuite le programme dans `main()`. Chaque etape du programme est verifier pour s'assurer qu'il n'y a pas une erreur dans le traitement du programme.

```go
package main

import "fmt"

func main() {
	var aStr, op, bStr string
	fmt.Scan(&aStr, &op, &bStr)

	a, err := parseInt(aStr)
	if err != nil {
		fmt.Println("error:", err)
		return
	}

	b, err := parseInt(bStr)
	if err != nil {
		fmt.Println("error:", err)
		return
	}

	res, err := compute(a, b, op)
	if err != nil {
		fmt.Println("error:", err)
		return
	}

	fmt.Println("result:", res)
}
```

---

## Pattern run()

Pour organiser le code pour que la gestion des erreurs ne soient pas etalee dans tout `main()`, un procede courant est de creer une fonction `run() error`, dans laquelle se trouve tout la logique. `main()` devient alors une enveloppe qui apelle `run()`, verifie l'erreur et affiche un message et se termine.

On retrouve alors ce pattern :

```go
package main

import "fmt"

func run() error {
  // logique
  return nil
}

func main(){
  if err := run(); err != nil {
    fmt.Println("error:", err)
    return
  }
}
```

Par exemple, pour une application CLI qui lit depuis la stdin, et execute une seule etape. L'application supporte une commande `add <title>` (ajout de tache en memoire) et `get <id>` (affiche d'une tache).

```go
package main

import (
  "fmt"
  "errors"
  "strconv"
)

// ajout d'une task
func AddTask(tasks []string, title string) ([]string, error){
  // test validite du title 
  if len(title) == 0 {
    return nil, errors.New("task title is empty")
  }

  tasks = append(tasks, title) // ajout dans le slice si title valide
  return tasks, nil
}

// afficher un tasks 
func GetTask(tasks []string, id int) (string, error) {
  // verification si id est valide
  if id < 0 || id >= len(tasks){
    return "", fmt.Errorf("tasks id out of range: %d", id)
  }
  return tasks[id], nil
}

// fonction orchestrateur
func run() error {
  tasks := []string{"Learn Go"} // donnee initiale

  var cmd, arg string 
  if _, err := fmt.Scan(&cmd, &arg); err != nil {
    return err
  }

  if cmd == "add" {
    var err error 
    
    tasks, err := AddTasks(tasks, arg)
    if err != nil {
      return err
    }

    fmt.Println("ok")
    return nil
  }
  
  if cmd == "get" {
    id, err := strconv.Atoi(arg)
    if err != nil {
      return fmt.Errorf("parse id %d: %v", arg err)
    }

    task, err := GetTask(tasks, id)
    if err != nil {
      return err
    }

    fmt.Prinln(task)
    return nil
  }

  return fmt.Errorf("unknown command %q", cmd)
}

func main(){
  if err := run(); err != nil {
    fmt.Println("error:", err)
    return
  }
}
```

---

## Pattern omit valeur et traitement err

Parfois une fonction renvoie une valeur utile et un `error`, mais la valeur n'est pas utile pour la suite. Par exemple, on affiche quelque chose, ou on realise une action pour effet. 
Dans ce cas, on peut utiliser le `_` et la declaration courte de `err` directement dans le `if`

```go 
func PrintHeader() error {
  if _, err := fmt.Println("=== TaskBok ==="); err != nil {
    return err
  }
  return nil
}
```

---

## Style de base des messages d'erreurs

Les messages d'erreurs s'ecrivent en minuscule, sans point final et sans prefix `"Error:"`. Une erreur doit etre considerer comme une description technique du probleme, facile a lire dans les logs et facile a enrichir a chaque niveau d'abstraction.

| Situation | Mauvais | Mieux |
| --- | --- | --- |
| age incorrect | `Error: Invalid age.` | `invalid age` |
| titre vide | `Erreur: Nom de tache vide.` | `title is empty` | 
| impossible d'analyser un nombre | `ERROR! Can't parse id` | `parse id: invalid syntax` |

Par convention, on utilise ce pattern : `fmt.Errorf("decompress %v: %v", name, err)` => on ajoute a gauche ce que l'on souhaite faire et a droite, la cause initial via deux point. 

Chaque niveau d'abstraction ajoute un contexte d'operation ce qui permet d'obtenir des message d'erreur clair.

Lorsque l'on ecrit une erreur, il faut penser : "quelle operation a echouer et pourquoi". Le message devient alors structurer, meme si ce n'est qu'une chaine.

- `"operation arg=value: readon"` 
- `"parse age: "4x": invalid syntaxe"`

### Detail a ajouter a l'erreur 

Les details sont interessant lorsqu'il reponde a la question : "qu'est ce qui a casser" => action + valeur problematique.

L'utilise de `%q` permet d'afficher la valeur de chaine entre guillemets, ce qui permet de voir directement la chaine qui pose probleme, et si c'est une chaine vide.

```go
func main() {
	input := "  "
	_, err := strconv.Atoi(input)
	if err != nil {
		fmt.Printf("parse int %q: %v\n", input, err)
		// parse int "  ": invalid syntax
	}
}
```

Si c'est un nombre qui fait echouer la validtion, il faut l'inclure. 

```go 
func ValidateLimit(limit int) error {
	if limit < 1 {
		return fmt.Errorf("limit must be >= 1, got %d", limit)
	}
	return nil
}
```

### Exemple 

```go 
// verifie la saisie => remonte la raison de l'erreur
func validateTitle(title string) error {
	if strings.TrimSpace(title) == "" {
		return errors.New("title is empty")
	}
	return nil
}

func addTask(title string) (int, error) {
	if err := validateTitle(title); err != nil {
		return 0, fmt.Errorf("add task: %v", err) // ajout du context
	}
	return 1, nil
}
```

`validateTitle()` retourne une erreur avec la raison qui as fait echouer la validation : `"title is empty"`.
`addTask()` connait le context, et ajoute `"add task: ..."` sur le message d'erreur. 
Cette approche permet d'obtenir un message d'erreur explicite, on connait alors la raison de l'erreur + le contexte dans lequel cette erreur est apparu

```go 
func parseID(s string) (int, error) {
	id, err := strconv.Atoi(s)
	if err != nil {
		return 0, fmt.Errorf("parse id %q: %v", s, err)
	}
	if id < 1 {
		return 0, fmt.Errorf("id must be >= 1, got %d", id)
	}
	return id, nil
}
```

Ici, le message indique la raison de l'echec, avec la valeur qui provoque l'erreur. Le message d'erreur peut etre contatener avec un niveau plus haut, par exemple `"mark done: parse id "x": invalid syntax"`

Lorsqu'une tache n'est pas trouver, il faut utiliser une formulation technique clair : `"task {id} not found"`.

```go 
func markDone(titles map[int]string, done map[int]bool, id int) error {
	if _, ok := titles[id]; !ok {
		return fmt.Errorf("task %d not found", id)
	}
	done[id] = true
	return nil
}
```

---

## Contrat de fonction : error, bool, (T, bool)

Un bon contrat rend le code de l'appelant simple, un mauvais contrat oblige a ecrire de verification etrange, stocker des valeurs magique comme `-1` et deviner ce que l'auteur de la fonction voulait dire.

En Go, le style du langage favorise les contrats explicite et un flux de controle previsible. Des que l'on choisit quoi retourner, on "signe" un contrat avec celui qui vient appeler la fonction.

- `bool` : c'est oui/non 
- `(T, bool)` : c'est trouve/non trouve
- `error` : cela n'a pas marcher et la raison compte 

### bool 

`bool` convient lorsque la fonction repond a une question de propriete : un nombre est pair, une chaine vide, un entre match, etc.
Dans ces cas, `false` est une reponse a la question.
Si on retourne une `error`, cela rend le code plus compliquer, chauq `false` peut potentielement etre une erreur critique.

```go
// retourne juste un boolean
func IsValidID(id int) bool {
	return id > 0
}

func main() {
	fmt.Println(IsValidID(10))  // true
	fmt.Println(IsValidID(-5))  // false
	fmt.Println(IsValidID(0))   // false
}
```

Dans ce code, il n'y a aucune raison de retourner `error`. On ne vient pas traiter pourquoi l'id n'est pas valide au niveau de la logique metier.

---

### (T, bool)

Le contrat `(T, bool)` est un excellent compromis pour les operations : obtenir une valeur si elle existe.
La valeur `bool` permet de savoir si la valeur a bien ete trouver, meme si la valeur est la zero value.

```go
// la valeur est retourner et une valeur bool pour indiquer si celle ci est trouver
func FindIndex(xs []string, target string) (int, bool) {
	for i, x := range xs {
		if x == target {
			return i, true
		}
	}
	return 0, false
}

func main() {
	words := []string{"go", "rust", "java"}

	i, ok := FindIndex(words, "go")
	fmt.Println(i, ok) // 0 true

	i, ok = FindIndex(words, "python")
	fmt.Println(i, ok) // 0 false
}
```

---

### error

`error` est necessaire lorsque l'echec a du sens et que les raison compte. Lorsque des operations qui peuvent echouer pour plusieurs raison : parsing, validation, calcul sous contrainte,  ..

Un package bien concut decrit sur quoi on peut compter en cas d'erreur.

```go
func ValidateTitle(title string) error {
	if len(title) == 0 {
		return errors.New("title is empty")
	}
	if len(title) > 50 {
		return fmt.Errorf("title is too long: %d chars", len(title))
	}
	return nil
}
```

Ici, `error` indique ce qui provoque l'erreur.

---

## Wrapping d'erreur - %w

Le `%w` se comporte comme si c'etait un `%v` de l'exterieur, mais en interne, il vient creer un enrobage et permet de retourner la case racine via la methode `Unwrap()`.
Le wrapping permet de creer une nouvelle erreur contenant l'ancienne.

On passera pour le `%w`, l'erreur que l'on souhaite enrober, et ba juste une valeur.


```go
// utilisation de %v 
func main() {
	base := errors.New("bad input")
	w := fmt.Errorf("parse: %v", base)

	fmt.Println(w)                 // parse: bad input
	fmt.Println(errors.Unwrap(w))  // <nil>
}

// utilisation de %w
func main() {
	base := errors.New("bad input") // creer une nouvelle erreur 
	w := fmt.Errorf("parse: %w", base) // viens ajouter un contexte => on passe base qui contient un err
 
	fmt.Println(w)                // parse: bad input => affiche l'erreur complete
	fmt.Println(errors.Unwrap(w)) // bad input => affiche l'erreur de base
}
```

---

### Enrobage multiple 

Dans une application, un erreur remonte generalement a travers plusieurs niveau, et chaque niveau ajoute son contexte.
`errors.Unwrap()` retire une seule couche. Si `unwrap` n'est pas pris en charge, il retourne `nil`

```go
func main() {
	base := errors.New("bad input")
	w1 := fmt.Errorf("parse: %w", base)
	w2 := fmt.Errorf("run: %w", w1)

	fmt.Println(w2)                // run: parse: bad input
	fmt.Println(errors.Unwrap(w2)) // parse: bad input
}
```

### Fonction d'affichage des couches d'erreur 

```go 
func printChain(err error) {
	for err != nil {
		fmt.Println("-", err)
		err = errors.Unwrap(err)
	}
}

func main() {
	base := errors.New("bad input")
	err := fmt.Errorf("parse: %w", base)
	err = fmt.Errorf("run: %w", err)

	printChain(err)
	// - run: parse: bad input
	// - parse: bad input
	// - bad input
}
```

---

## Verification d'erreur 

- `errors.Is` : dans la plupart des cas, on a pas besoin des details d'un type d'erreur precis. 
- `errors.As` : lorsque l'on a besoin de construire un comportement ou un message pour un type precis d'erreur 

### `errors.Is` - verifier la cause d'une erreur 

`errors.Is(err, target)` permet de verifier si une "`err` ou une des cause dans la chaine est-elle la meme erreur que `target`.
La fonction parcourt elle meme la chaine des cause.

```go 
var ErrBadInput = errors.New("bad input")

func main() {
	err := fmt.Errorf("parse: %w", ErrBadInput)

	fmt.Println(err == ErrBadInput)          // false
	fmt.Println(errors.Is(err, ErrBadInput)) // true
}
```

### `errors.As` - extraire une erreur du type voulu 

`errors.As` viens parcourir le wrapping d'erreur et essaie de trouver l'erreur du type souhaite. Si trouver, elle l'ecrit dans la variable et on peut l'utiliser comme un objet normal.

La fonction prends en deuxieme argument un pointeur. La fonction doit ecrire l'erreur trouver dans la variable `target` -> le deuxieme argument, pour que la fonction puisse modifier la variable il faut lui fournir l'adresse memoire de celle ci.

```go
import (
	"errors"
	"fmt"
	"strconv"
)

func main() {
	_, err := strconv.Atoi("12x")
	err = fmt.Errorf("parse id: %w", err)

	var numErr *strconv.NumError // declare la varible cible  => on indique le type rechercher
	if errors.As(err, &numErr) {
		fmt.Println("bad number:", numErr.Num) // bad number: 12x
	}
}
```

---

## Sentinel errors 

Une sentinel erreur est le cas ou l'on vient creer une valeur d'erreur unique et on l'utilise comme etiquette. Le code appelant vient verifier cette "etiquette" et traite l'erreur selon celle ci.
Elles sont declarer au niveau du package, puis reutilisees.
On utilise alors `errors.Is`

```go
package main

import (
	"errors"
	"fmt"
)

var ErrPermission = errors.New("permission denied") // delcaration de la sentinel => etiquette

func checkPermission(ok bool) error {
  // on ajoute l'etiquette dans l'erreur 
	if !ok {
		return fmt.Errorf("check permission: %w", ErrPermission)
	}
	return nil
}

func main() {
	fmt.Println(checkPermission(false)) // check permission: permission denied
}
```

---

## Types error 

Permet de reconnaitre une erreur avec son type. Cette aproche est adapte lorsque l'on doit traiter des details technique que l'on souhaite recuperer sans analyser la chaine. On utilise ensuite `errors.As` pour la traiter.

```go
package main

import (
	"errors"
	"fmt"
	"strconv"
)

func main() {
	_, err := strconv.Atoi("12x")
	err = fmt.Errorf("parse id: %w", err)

	var numErr *strconv.NumError
	if errors.As(err, &numErr) {
		fmt.Println("typed cause:", numErr.Error()) // typed cause: strconv.Atoi: parsing "12x": invalid syntax
	}
}
```

---

### Pattern 

On donne a l'exterieur une `class` via un sentinel, et a l'interieur on conserve une cause technique sous forme d'erreur typee.

```go
package main

import (
	"errors"
	"fmt"
	"os"
	"strconv"
)

// sentinel: classe de situation "age hors des limites autorisees"
var ErrAgeOutOfRange = errors.New("age out of range")

func ParseAge(s string) (int, error) {
	age, err := strconv.Atoi(s)
	if err != nil {
    // erreur technique => on retourne err de la fonction, et on ajoute le contexte 
		return 0, fmt.Errorf("parse age %w", err)
	}

	if age < 0 || age > 130 {
    // erreur metier => on utilise la sentinel pour donner la raison de l'echec 
		return 0, fmt.Errorf("parse: %w", ErrAgeOutOfRange)
	}

	return age, nil
}

func main() {
	var s string
	fmt.Fscan(os.Stdin, &s)

	age, err := ParseAge(s)
  // la fonction s'est bien derouler => on affiche l'age 
	if err == nil {
		fmt.Printf("AGE: %d\n", age)
		return
	}

  // on verifie si l'erreur est du au parsing => echec pour raison technique 
	var numErr *strconv.NumError
	if errors.As(err, &numErr) {
		fmt.Println("BAD_INPUT")
		return
	}

  // on verifie si l'erreur est metier avec la sentinel
	if errors.Is(err, ErrAgeOutOfRange) {
		fmt.Println("OUT_OF_RANGE")
		return
	}

  // autre cas d'erreur 
	fmt.Printf("ERROR: %v\n", err)
}
```

---

## `errors.Join`

Cette methode permet de regrouper plusieurs erreurs. Au lieu d'obtenir une erreur, on obtient le rapprt d'erreur complet.
Par exemple, un formulaire qui attends plusieurs valeurs. Au lieu de saisir, valider avoir une erreur pour un champ, puis relancer avec une erreur pour un autre champ, on retourne directement l'ensemble des raisons d'erreurs. Ou encore dans l'import de donnee, on souhaite connaitre toutes les lignes qui pose probleme.

`errors.Join(err1, err2)` prends plusieurs errurs, et retourne une seule. Elle enveloppe un ensemble de cause qui peut etre traiter par `errors.Is` et `errors.As`.
Si on lui passe que `nil`, le resultat sera `nil`. La representation textuel d'une erreur groupee ressemble a plusieurs message separer par des retour a la ligne.

```go
package main

import (
	"fmt"
	"strconv"
	"strings"
  "errors"
)

// sentinels
var (
	ErrTitleRequired      = errors.New("title required")
	ErrPriorityOutOfRange = errors.New("priority out of range")
)

func validateTask(title string, priority int) error {
	var errs []error // creation d'un slice pour contenir les erreurs

  // chaque erreur est ajouter dans le slice 
	if title == "" {
		errs = append(errs, fmt.Errorf("title: %w", ErrTitleRequired)) // on ajoute du contexte en plus pour obtenir des erreurs clair 
	}
	if priority < 1 || priority > 5 {
		errs = append(errs, fmt.Errorf("priority: %w", ErrPriorityOutOfRange))
	}

  // on passe le slice dans la fonction pour regrouper l'ensemnble des erreurs
	return errors.Join(errs...) // on utilise ... pour passer l'ensenble des elements du slice
}

// la fonction analyse une ligne
func parseTaskLine(line string) (string, int, error) {
	title, prioStr, ok := strings.Cut(line, ";")
	if !ok {
		return "", 0, fmt.Errorf("bad format: %q", line)
	}

	priority, err := strconv.Atoi(prioStr)
	if err != nil {
		return "", 0, fmt.Errorf("priority is not int: %w", err)
	}

	return title, priority, nil
}

// permet de parcourir les lignes et placer chaque probleme dans le slice d'errs
func importTasks(lines []string) error {
	var errs []error

  // on parcours chaque lignes et on creer le rapport d'erreur avec la ligne de l'erreur 
	for i, line := range lines {
		title, prio, err := parseTaskLine(line)
		if err != nil {
			errs = append(errs, fmt.Errorf("line %d: %w", i+1, err))
			continue
		}

    // on utilise la fonction de validation 
		if err := validateTask(title, prio); err != nil {
			errs = append(errs, fmt.Errorf("line %d: %w", i+1, err))
		}
	}

  // on retourne ensuite l'ensemble des rapport pour afficher chacune d'elle 
	return errors.Join(errs...)
}

func main() {
	lines := []string{";2", "Buy milk;10", "Read book;3"}

	err := importTasks(lines) // on check chaque ligne 
	fmt.Println(err)
	// line 1: title: title required
	// line 2: priority: priority out of range

  // on peut toujours utiliser les fonctions errors.Id sur le rapport 
	fmt.Println(errors.Is(err, ErrTitleRequired))      // true
	fmt.Println(errors.Is(err, ErrPriorityOutOfRange)) // true
}
```

---

## Taxinomie des erreurs d'application 

Une taxonomie des erreurs est un petit ensemble de categories que le niveau superieur de l'application sait distinguer, et a partir desquelles, prend des decision.

L'idee est que ces classes reglete le comportement. Le programme au niveau superieur (cli/serveur/script) affiche des messages differents et choisit des code de retour difference selon les categories.

| Classe | Signification | Comportement au niveau superieur |
| ------ | ------------- | -------------------------------- |
| validation | donnees d'entree invalide ou incomplete | indiquer a l'utilisateur de corriger l'entree |
| notfound | l'objet rechercher n'a pas ete trouver | signaler non trouver | 
| io | erreur entree/sortie | signaler "lecture/ecriture impossible" et parfois proposer de reassayer |
| internal | probleme inattendu a l'interieur du programme | signaler quelque chose a casser et conserver les detail de diag |  

On creer des etiquettes pour permettre de gerer les erreurs avec `errors.Is` qui agissent en tant que sentienel

```go
import "errors"

var (
	ErrValidation = errors.New("validation error")
	ErrNotFound   = errors.New("not found")
	ErrIO         = errors.New("i/o error")
	ErrInternal   = errors.New("internal error")
)
```

### Classe, Contexte et cause premiere

On separe les roles. Une erreur qui remonte doit :

- donner au code superieur une classe : permet de choisir le comportement 
- donner a l'humaine un contexte : comprendre ce qui etait fait 
- conserver la cause premier : ne pas perdre le diag

Cela est realiser en combinant les `errors.Join` et le wrapping `%w`

On assemble ce qui est important pour le code : classe + cause puis on ajoute ce qui est important pour l'humaine : contexte 

```go
package app

import (
	"errors"
	"fmt"
	"strconv"
)

var (
	ErrValidation = errors.New("validation error")
	ErrNotFound   = errors.New("not found")
	ErrIO         = errors.New("i/o error")
	ErrInternal   = errors.New("internal error")
)

// la fonction doit distinguer les mauvaise entree du reste.
func parseID(s string) (int, error) {
	id, err := strconv.Atoi(s)
	if err != nil {
		return 0, fmt.Errorf("parse id: %w", errors.Join(ErrValidation, err)) // ajout d'un contexte + raison premiere
	}
	if id < 0 {
		return 0, fmt.Errorf("parse id: %w", ErrValidation) // ajout du contexte + raison metier sentinel
	}
	return id, nil
}

// la fonction retourne une task si trouver ou une erreur 
func getTask(tasks []string, id int) (string, error) {
	if id < 0 || id >= len(tasks) {
		return "", fmt.Errorf("get task %d: %w", id, ErrNotFound) // ajout d'un contexte + raison metier sentinel 
	}
	return tasks[id], nil
}
```

---

### io dans readCommand 

La classe IO permet de lire des entrees, ecrire du resultat, travailler avec le reseau/fichier, etc 

```go
package app

import (
	"errors"
	"fmt"
)

func readCommand() (string, string, error) {
	var cmd, arg string
	_, err := fmt.Scan(&cmd, &arg)
	if err != nil {
		return "", "", fmt.Errorf("read command: %w", errors.Join(ErrIO, err)) // classe ErrIO + cause premiere err + contexte read command
	}
	return cmd, arg, nil
}
```

### internal dans firstTask 

`internal` signifie que le programme a rencontrer une situation non prevue. Elle indique une erreur du programme.

```go 
package app

import "fmt"

func firstTask(tasks []string) (string, error) {
	if len(tasks) == 0 {
		return "", fmt.Errorf("first task: %w", ErrInternal)
	}
	return tasks[0], nil
}
```

---

### Niveau superieur : choix du comportement 

On retrouve ensuite le niveau superieur, ou la fonction doit traiter les differents cas d'erreur 

```go 
package main

import (
	"errors"
	"fmt"

	"example.com/course/app"
)

func printUserMessage(err error) {
  // si erreur de validation 
	if errors.Is(err, app.ErrValidation) {
		fmt.Println("Entree incorrecte. Verifiez la commande et les arguments.") // exemple d'UX
		return
	}

  // si ressource n'est pas trouver 
	if errors.Is(err, app.ErrNotFound) {
		fmt.Println("Non trouve.") // ton calme
		return
	}

  // si l'erreur provient d'une operation exterieur 
	if errors.Is(err, app.ErrIO) {
		fmt.Println("Erreur d'entree/sortie. Reessayez.") // environnement externe
		return
	}

  // autre cas d'erreur 
	fmt.Println("Erreur interne de l'application.") // internal et tout le reste
}
```

---

### Wrapping d'erreur 

Lorsque l'on enveloppe tous, cela peut provoquer des soucis. Cela implique que le code d'un package externe est inclut dans notre code.

Vers l'exterieur, il faut toujours faire remonter la classe `validation|notFounf|io|internal`, et conserver la cause premiere pour le diag, sans encourager le code externe a construire sa logique dessus. Le moyen le plus simple est que le code externe le verifie que `ErrValidation` et ses semblabe, et que tous le reste soit du texte et du contexte pour l'humain.

--- 

## Panic 

Go propose deux manière de gérer les erreurs. Lorsque quelque chose ne passe mal mais est attendu comme une saisie incorrect, donnée non présente ou lorsque le code viole ses propres promesses et qu'il devient dangereux de continuer.

Go utilise les `error` pour les erreurs attendus. Si cela ne marche pas, on retourne l'erreur et l'appelant décide du traitement.

Lorsque le programme se retrouve dans un état qui ne devrait pas arriver selon la logique du code, cela mène à une **panic**. Le flux d'exécution s'arrête, les `defer` commencent à s'exécuter, la pile se déroule vers le haut, et si personne n'intercepte la panique, le programme plante.

Une `panic` indique que quelque chose est casser dans le programme. C'est un mécanisme intégré à Go qui arrête le flux normal d'exécution, lance le déroulement de la pile et garantit l'exéction des `defer` sur le chemin vers le haut. `panic` n'est pas un moyen de gére la logique métier.

Un cas "approprié" : on vérifie l'entrée, mais on as quand même obtenu un état impossible. Par exemple, la fonction accepte deux commandes ("add" et "div"). En amont une vérif à lieu. Si quelque chose de troisème arrive, cela signie que quelqu'un à modifier le code au point que l'invariant n'est plus vrai. 

```go 
func computeValidated(cmd string, a, b int) int {
	switch cmd {
	case "add":
		return a + b
	case "div":
		return a / b // ici, on suppose que b != 0 (verifie auparavant)
	default:
		panic("unreachable: command was validated earlier")
	}
}
```

Ici, l"utilisateur n'est pas coupable, c'est le code qui est cassé. Dans de tel endroits, `panic` peut se justifier, car plus honnête qu'une `error` étrange qui ne devrait pas se produire selon la conception.

## Panic runtime

Go peut provoquer des `panic` lorsque l'on enfrein les règles d'exécution, par exemple sortir des limite d'un slice.

**Dépassement de limites d'un slice**
```go 
package main

import "fmt"

func main() {
	s := []int{10, 20}
	fmt.Println(s[2]) // panic: index out of range
}
```

La bonne approche est d'ajouter une protection avant que la panic puisse se produit 

```go 
package main

import (
	"errors"
	"fmt"
)

var ErrNeedAtLeast3 = errors.New("need at least 3 numbers")

func third(s []int) (int, error) {
	if len(s) < 3 {
		return 0, ErrNeedAtLeast3
	}
	return s[2], nil
}

func main() {
	_, err := third([]int{10, 20}) // fonction permet de proteger contre la panic d'out of range 
	fmt.Println(err) // need at least 3 numbers
}
```

**Ecriture dans un map nil**
Un map `nil` peut être dangereux. On peut lire mais écrire. Pour pouvoir écrire dans un map, il faut le créer avec un `make`

```go 
package main

func main() {
	var m map[string]int
	m["x"] = 1 // panic: assignment to entry in nil map
}
```

Généralement, un map `nil` apparait après un oubli d'initialisation. Dans du code interne, cela se rappoche d'une situation de `panic`, mais dans du code extérieur, si le map apparait comme paramètre, il vaut mieux s'assurer de rendre l'API plus sur : soit initialiser le map, soit retourner un `error` clair

```go 
package main

import "errors"

var ErrNilMap = errors.New("map must be initialized")

func addCount(m map[string]int, key string) error {
	// protection contre le map nil 
	if m == nil {
		return ErrNilMap
	}
	m[key]++
	return nil
}
```

**Division par zéro**
Une division par zero est impossible, et provoque une `panic`. 

--- 

## Style Must 

On peut rencontre des fonction du type `MustXxx`: `template.Must`, `regexp.Mustcompile`, etc. 
L'idée est que si une erreur survient ici, on termine.

C'est un outil qui permet de figer l'hypothèse qu'ici l'erreur est impossible, et si cela arrive, cela signifie que le programme est mal construit.

```go 
package main

import "fmt"

func mustCompute(cmd string, a, b int) int {
	res, err := compute(cmd, a, b)
	if err != nil {
		panic(fmt.Errorf("mustCompute failed: %w", err))
	}
	return res
}
```

---

## `recover`

`recover` permet d'intercepter une panique et de la transformer en résultat controler pour que le programme ne plante pas. Il doit être placé dans un `defer`. Dans le flux normal `recover()` retourne `nil` et n'intercepte rien.

Lorsque Go rencontre un panic, il commence à remonter la pile et exécute les `defer`. A ce moment la, la fonction `deferred` à le temps d'appeller `recover()`, de récupérer la valeur de panic et d'arrêter l'avalanche. 
`recover` ne reprends pas l'exécution à partir du point de panic. Le code situer après la ligne ou le panic s'est produite ne sera pas exécuter. `recover` ne fait que transformer une sortie de ssecours en sortie normal de la fonction ou se trouvait le `defer` 

```go 
package main

import "fmt"

func main() {
	defer func() {
		if r := recover(); r != nil {
			fmt.Println("recovered:", r) // recovered: boom
		}
	}()

	panic("boom")
}
```

### Pattern save main

En Go, on utilise le pattern `save main`: `recover` se trouve au niveau supérieur, et le code principal travail selon un contrat `(T, error)`.
```go
package main

import "fmt"

func main() {
	if err := safeRun(); err != nil {
		fmt.Println("app error:", err) // app error: ...
	}
}
```

`main()` lance le travail et affiche le problème.

**safeRun** 
`safeRun()` est une frontière. Elle retourne un `err error` nommé. Cela permet à la fonction deferred d'assigner `err = ...` juste avant la sortie. Cette technique est une façon standard d'intervenir dans le résultat d'une fontion via `defer`.
```go 
package main

import "fmt"

func safeRun() (err error) {
	defer func() {
		if r := recover(); r != nil {
			err = fmt.Errorf("unexpected panic: %v", r)
		}
	}()

	return run()
}
```

La fonction métier `run()` vit dans le monte des gens normaux : 
```go 
package main

import "errors"

func run() error {
	return errors.New("not implemented yet")
}
```

Si dans la fonction `run()` et de ce qu'elle appelle, une panic se produit, on vient transformer un processus sale en `error` compréhensible à la frontière. On permet également à l'erreur de remonter.

---

### Valeur de panic 

La valeur d'une panic n'est pas forcément une chaine. Dans un `panic()`, on peut passer n'importe quel valeur, et elle arrivera dans le `recover()` sous la forme de `any`. 

Cette fonction helper permet de rendre `safeRun` plus propre. 
```go 
package main

import "fmt"

func panicToError(r any) error {
	if e, ok := r.(error); ok {
		return fmt.Errorf("panic: %w", e)
	}
	return fmt.Errorf("panic: %v", r)
}

func safeRun() (err error) {
	defer func() {
		if r := recover(); r != nil {
			err = panicToError(r) // on utilise la fonction helper
		}
	}()
	return run()
}
```

### Afficher la pile lors de recover 

Le résultat après un `recover` est de stopper l'opération courante et de renvoyer l'erreur vers le haut. Par défaut, lors d'une panic non interceptée, Go l'affiche. Si on intercepte la panic, on annule la sortie standard de la panic, et la pile ne sera plus imprimée automatiquement. 

Dans le cas ou afficher la pile est nécessaire (par exemple, pendant le développement), on peut prendre la pile depuis `runtime/debug`.

```go 
package main

import (
	"fmt"
	"runtime/debug"
)

func safeRun() (err error) {
	defer func() {
		if r := recover(); r != nil {
			fmt.Printf("panic stack:\n%s\n", debug.Stack())
			err = panicToError(r)
		}
	}()
	return run()
}
```

`recover` ne doit pas devenir un "aspirateur qui engloutit tous les problèmes". Si on attrape une panic, on rends la chute prévisible mais il faut toujours venir corriger la cause.

---

### Limite de l'application 

La limite de l'application c'est l'endoit ou le code rencontre le monde extérieur, et ou l'on doit transformer les problèmes internes en résultat compréhensible. Généralement dans le `main()` qui est le point d'entrée.
`recover` doit "si quelque chose d'impossible se produit, ne pas exposer l'utiliser à une stack d'erreur, mais terminer l'opération de façon controlée".

Il doit être placer à l'endroit ou est on prêt à terminer l'opération. Si on lancer un `recover` au milieu du code métier, on ne peut pas garantir que l'état est rester correct. La panique a pu arriver au milieu d'une modification de données, d'un calcul ou d'une chaine de plusieurs étapes.

Le code minimal de protection ressemble à ce pattern: on extrait le travail dans une fonction `run()`, puis on place une `safeRun()` qui permet d'intercepter une panique avec `defer` et retourne une erreur.

Après le `recover`, on essaie pas de terminer le travail. On indique que l'opération à rencontrer une erreur critique, voici l'erreur. Cela correspond à la mécanique: `recover` rend le controle, mais le code situé après l'endroit de la panique ne sera déjà plus exécuté.

```go 
package main

import (
	"fmt"
)

// intercepte une panic 
func safeRun() (err error) {
	defer func() {
		if r := recover(); r != nil {
			err = fmt.Errorf("unexpected panic: %v", r)
		}
	}()
	return run() // fonction qui réalise le travail
}

// point d'entrée
func main() {
	if err := safeRun(); err != nil {
		fmt.Println("error:", err) // error: unexpected panic: ...
	}
}
```

### Panic en interne, error à l'exterieur 

Il est parfois utile d'utiliser un `panic` dans une fonction comme sortie rapide d'une imbrication profonde, pour éviter de passer par `err` si plusieurs niveaux. Cette approche fonctionne uniquement dans les cas on l'on sait avec certitude que la panique sera intercepté en un seul endroit, et si on ne laisse pas fuir vers l'utilisateur.

```go 
package main

import (
	"fmt"
	"strconv"
)

func mustParseInt(s string) int {
	n, err := strconv.Atoi(s)
	if err != nil {
		panic(err)
	}
	return n
}

func parseInt(s string) (n int, err error) {
	defer func() {
		if r := recover(); r != nil {
			err = fmt.Errorf("parse int %q: %v", s, r)
		}
	}()
	return mustParseInt(s), nil
}
```

