
# Core

## Compilateur

Programme qui vient traduire le code Go en code machine. Il prends le code Go, le décompose, le vérifie pour détecter les erreurs et le transforme en code machine

## Lecture d'erreur

`main.go:7:6: undefined: something`

- `main.go` : fichier qui provoque l'erreur
- `7` : ligne
- `6` : colonne
- `undefined: something` : message d'erreur

Il existe plusieurs type d'erreurs :

- `undefined` : nom introuvable
- `cannot use ... as ...` : type ne correspondent pas
- `declared and not used`: une variable n'est pas utilise
- `imported and not used`: importer mais non utiliser
- `syntax error` : erreur de syntaxe

## Go SQK

L'ensemble des outils de l'IDE pour transformer le code source en un fichier exécutable. C'est un répertoire sur l'ordinateur qui content :

- les utilitaire pour la compilation et la construction : `go build`, `go run`
- les outils de formatage et de test : `gofmt`, `go test`
- la bibliothèque standard

Le dossier racine dans lequel Go SDK est installer s'appelle `GOROOT`. L'IDE utilise celui ci comme source de vérité.

---

## Modélisation de données 

### Invariants des données 

Un invariant est une règle qui doit rester vraie pour une valeur valide. Par exemple, l'id d'une tâche est toujours supérieur à zéro. Ils doivent être vérifié à la frontière du système, c'est à dire à l'endroit où les données arrivent de l'exterieur.
A l'intérieur du programme, il faut utiliser des données correctes et prévisibles.

### Responsabilité des types 

On peut créer des types spécifique pour renforcer le sens du code. De cette manière, il n'est plus possible de permuter un `TaskID` avec un `ProjectID`. 

```go
package main

import "fmt"

type TaskID int // création du type personnalisé 

func main() { 
	var id TaskID = 10 // ajout du sens via le type à la variable
	fmt.Println(id) // 10
}
```

### Fonctions de construction et normalisation 

Une fonction de construction et de normalisation est une fonction qui reçoit des entrée brute, la normalise, la vérifie et retourne soit la valeur correcte, soir une erreur.

```go 
package main

import (
	"errors"
	"strconv"
	"strings"
)

type TaskID int // création d'un type personnalisé 

func ParseTaskID(s string) (TaskID, error) {
  // convertis l'entrée en int     
	n, err := strconv.Atoi(strings.TrimSpace(s))
	if err != nil || n <= 0 {
		return 0, errors.New("task id must be a positive integer")
	}
  // on convertis la valeur dans le type personnalisé 
	return TaskID(n), nil
}

type TaskTitle string // création du type personnalisé 

func NewTaskTitle(s string) (TaskTitle, error) {
  // on normalise la chaine 
	parts := strings.Fields(strings.TrimSpace(s))
  // vérification si la chaine est correct 
	if len(parts) == 0 {
		return "", errors.New("title is empty")
	}
  // on retourne la chaine normalisé
	return TaskTitle(strings.Join(parts, " ")), nil
}
```

### Normalisation à l'entrée 

```go 
package main

import (
	"fmt"
	"strings"
)

func main() {
	// découpage de la chaine 
  parts := strings.Fields("  add   BUY   milk  ")
  // récupération de la première chaine et stockage dans une variable 
	cmd := strings.ToLower(parts[0])
  // récupération du restes des éléments de la chaîne 
	title := strings.Join(parts[1:], " ")
  // affichage de la chaine normalisé
	fmt.Println(cmd, title) // add BUY milk
}
```

### Stockage et frontière de validation 

On construit un modèle de stockage des tâches de manière à ce qu'il soit prévisible et n'exige pas de magie.

```go 
package main

import "fmt"

type Status int // définition d'un type personnalisé 

// type pour les éléments d'une tasks 
type TaskID int // représente l'id de la task
type TaskTitle string // représente le titre de la task => string 
type TaskTitles map[TaskID]TaskTitle // stockage des tasks => la clé est l'id de la task, et la valeur sera le titre 

type TaskStatuses map[TaskID]Status // stockkage des status des tasks => clé est l'id de la task, la valeur est le status

// création de constante pour le status 
const (
	StatusUnknown Status = iota // 0
	StatusTodo // 1
	StatusDone // 2
)

// fonction permet d'ajouter une nouvelle tâches 
func AddTask(
	order []TaskID,
	titles map[TaskID]TaskTitle,
	st map[TaskID]Status,
	id TaskID,
	t TaskTitle,
) []TaskID {
  // on affiche dans la map des tasks l'id et le title 
	titles[id] = t
  // on passe dans le map des status l'id de la task et le status associé avec la constante 
	st[id] = StatusTodo
  // on retourne le slice permettant de stocker l'ordre des tasks avec la nouvelle valeur 
	return append(order, id)
}

func main() {
	fmt.Println(StatusTodo, StatusDone) // 1 2
}