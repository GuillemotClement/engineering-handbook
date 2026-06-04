# Interface 

Une interface permet de définir un ensemble de comportement lié à un `struct`. Pour qu'un type soit valide, il doit implémenter les méthodes définis dans l'interface.
L'interface énumère des signatures de méthodes attendues par le consommateur.

```go 
type Greeter interface {
	Greet() string // signature de méthode 
}
```

`Greeter` est un type d'interface qui dit: "tout ce qui sait faire `Greet() string` convient".

On ne peut pas accéder aux champs via une interface. 

```go 
type User struct {
	Name string
}
```

Si `var g Greeter = User { Name: "Anna" }`, on ne pourras pas faire `g.Name` car `Greeter` est une interface, et qui sert uniquement à promettre des méthodes. L'interface doit protéger contre les détails d'implémentation.

## Implémentation implicite 

L'implémentation d'une interface se fait de manière implicite. L'implémentation n'as pas besoin de savoir qu'elle implémente une interface, on peut ajouter de nouvelles interface du coté du consommateur sans avoir à modifier les anciens types, et permet moins de couplage.

### Exemple: un type correspond automatiquement à une interface

```go 
// déclaration de l'interface 
type Greeter interface {
	Greet() string
}

// déclaration du type 
type User struct {
	Name string
}

// impléntation de la méthode Greet() liè au type User 
// User implément de manière implicite l'interface Greeter
func (u User) Greet() string {
	return "Hello, " + u.Name
}

func main() {
    // déclaration d'une variable avec le type Greeter qui correspond à l'interface 
    // User implémente la méthode Greet(), cela respect l'interface donc le typage est respecté
	var g Greeter = User{Name: "Anna"} // User matches Greeter
	fmt.Println(g.Greet())            // Hello, Anna
}
```

### Exemple: liste de tâches et interface de formatage

On a des tâches `Task`, et on les affiche. On vas maintenant afficher les tâches de plusieurs manières, sans réecrire toute la logique de sortie. 

```go 
package main 

import "fmt"

// déclaration du type 
type Task Struct {
    ID int 
    Title string 
    Done bool 
}

// déclaration de l'interface "qui sait formater une tâche"
type TaskFormatter interface {
    Format(t Task) string 
}

// Implémentation 1: formatteur simple 
// struct vide pour stocker le comportement
type PlainFormatter struct{} 

func (PlainFormatter) Format(t Task) string {
    status := " "
    
    if t.Done { 
        status = "x"
    }

    return fmt.Sprintf("[%s] %d: %s", status, t.ID, t.Title)
}

// Implémentation 2: Formatteur de débogage 
type DebugFormatter struct{}

func (DebugFormatter) Format(t Task) string {
    return fmt.Sprintf("Task{ID=%d}, Title=%q, Done=%v", t.ID, t.Title, t.Done)
}

// Fonction d'affichage, dépend de l'interface.
// La fonction qui affiche la liste n'as pas besoin de savoir quel formatteur on lui passe
// le second argument est une valeur qui doit implémenter l'interface TaskFormatter => ce qui signifie que la valeur peut accéder à une méthode Format()
func PrintTasks(tasks []Task, f TaskFormatter) {
    for _, t := range tasks {
        fmt.Println(f.Format(t)) 
    }
}

// utilisation 
func main(){
    tasks := []Task{
        {ID: 1, Title: "Buy Milk", Done: false},
        {ID: 2, Title: "Fix Bug", Done: true},
    }
    
    // appel de la fonction, en passant comme struct vide le comportement souhaité
    PrintTasks(tasks, PlainFormatter{})
    PrintTasks(tasks, DebugFormatter{})
}
```

### Interface comme point d'extension: ajout d'un nouveau comportement

Lorsque l'on écrit du code sans interface, l'extension ressemble rapidement à un ensmble de `if` ou de `switch` ce qui réduit la lisibilité du code. Les interfaces permettent de créer une logique de base qui accepte une interface, et l'extension se fait en ajoutant de nouveau type qui implémente cette interface. 

Par exemple, on souhaite afficher seulement les titres de tâches : 

```go
type TitleOnlyFormatter struct{}

func (TitleOnlyFormatter) Format(t Task) string {
	return t.Title
}
```

De cette manière, on as pas besoin de venir modifier `PrintTasks`.

---

## Interface dans le lib standard

Les interfaces sont une mécanique de base en Go.

### error 

`error` en Go est organisée de manière simple: pour qu'un type devienne une erreur, il doit posséder la méthode `Error() string`.

```go
// impléement l'interface Error car il est liè a la methode Error()
type ValidationError struct {
	Field string
	Msg   string
}

func (e ValidationError) Error() string {
	return fmt.Sprintf("%s: %s", e.Field, e.Msg)
}

func main() {
	var err error = ValidationError{Field: "title", Msg: "empty"}
	fmt.Println(err.Error()) // title: empty
}
```

---

## Valeur d'interface 

Une valeur d'interface se compose de deux parties. Le `nil` d'une interface n'est pas la même chose que le `nil` d'un pointeur.

Lorsque l'on écrit `var x error`, on obtient une variable d'interface. Au runtime, elle stocke deux choses: le type concret qui se trouve à l'intérieur, et quelle valeur concrète de ce type se trouve à l'intérieur.

Le type d'interface (ce qui est écrit à gauche: `error`, `Stringer`, `Greeter`) est un contrat. La valeur d'interface (la variable de ce type) est un conteneur qui se souvient d'où il se trouve.

S'il n'y a ni type, ni valeur dans la variable, on obtient un `nil d'interface`. Par exemple

Interface nil: `var err error = nil`, le type est absent et la valeur est absente. L'interface est `nil`
Typed nil dans une interface: `var p *T = nil; var err error = p`, le type est `*T`, et la valeur est `nil`.

### Interface nil et %T 

`g` est un vrai `nil` d'interface: il n'y a ni type dynamique, ni valeur.

```go
package main

import "fmt"

type Greeter interface {
	Greet() string
}

func main() {
	var g Greeter
	fmt.Println(g == nil) // true
	fmt.Printf("%T\n", g) // <nil>
}
```

### Typed nil: interface n'est pas nil et l'interieur est nil 

On place un pointeur `nil` à l'intérieur d'une interface.

```go
package main

import "fmt"

type Person struct {
	Name string
}

func (p *Person) String() string {
	if p == nil {
		return "<nil Person>"
	}
	return "Person(" + p.Name + ")"
}

func main() {
	var p *Person = nil

	var s fmt.Stringer = p
	fmt.Println(p == nil) // true
	fmt.Println(s == nil) // false parce qu'a l'interieur de s se trouve le type dynamique *Person. La valeur est nil mais le type est present

	fmt.Printf("%T %v\n", s, s) // *main.Person <nil Person>
}
```
---

## Piege error != nil 

Retourner `nil` créer une interface vide: sans type dynamique et sans valeur.

```go
package main

type ValidationError struct {
	Field string
	Msg   string
}

func (e *ValidationError) Error() string {
	if e == nil {
		return "<nil ValidationError>"
	}
	return e.Field + ": " + e.Msg
}

func validateTitle(title string) error {
	if title == "" {
		return &ValidationError{Field: "title", Msg: "empty"}
	}
	return nil // IMPORTANT : nil d'interface, sans type dynamique
}

func main() {}
```

### Exemple: gestionnaire de tâche et bug avec typed nil 

On veux améliorer l'application pour que le contrat `nil` = sucees ne soit pas cassé

```go 
package main

import "fmt"

type Task struct {
	Title string
}

type ValidationError struct {
	Field string
	Msg   string
}

func (e *ValidationError) Error() string {
	if e == nil {
		return "<nil ValidationError>"
	}
	return fmt.Sprintf("%s: %s", e.Field, e.Msg)
}

func NewTask(title string) (*Task, error) {
	if title == "" {
		return nil, &ValidationError{Field: "title", Msg: "empty"}
	}
	return &Task{Title: title}, nil
}

func main() {
	t, err := NewTask("buy milk")
	fmt.Println(t.Title, err == nil) // buy milk true
}
```

---

## Conception d'interface 

**Petites interface**

Une interface étant un contrat, si on ajoute une méthodes, on oblige toutes les implémentation à prendre cette méthode en charge, sinon le code cesse de compiler.
A mesure que l'on augmente le nombre de méthode d'une interface, on augmente le reste de casser l'application.

| Taille de l'interface | Coût | Résultat |
| --------------------- | ---- | -------- |
| 1 - 2 méthodes | implémentation facile à implémenter et remplacer | fléxibilité |
| 3 - 5 méthodes | correct, mais il faut commencer à réfléchir | devient vite "tout le monde doit tout faire" |
| 6+ | "machine à tout faire" | difficile à implémenter et à modifier |

**L'interface doit décrire le besoin du code**

L'interface nait où le code à besoin d'un certain comportement: le code dit "j'ai besoin de pouvoir ajouter une tâche", alors il faut une interface avec une seule méthode `Add()`. Le code dit "j'ai besoin de pouvoir obtenir la liste des tâches", une autre interface.

On implémente une application gestionnaire de tâches. On commence avec un modèle: 

```go 
package main

type Task struct {
	ID    int
	Title string
	Done  bool
}
```

On as une fonction qui permet d'afficher les tâches. Elle ne se soucie pas de comment les tâches sont stockées. Ce qui compte c'est le comportement: affichages des tâches. On peut implémenter une interface minimal:

```go 
package main

type TaskLister interface {
	List() []Task
}
```

Et la fonction consommatrice: 

```go 
package main

import "fmt"

func PrintTasks(l TaskLister) {
	for _, t := range l.List() {
		fmt.Println(t.ID, t.Title, t.Done) // 1 Acheter du pain false
	}
}
```

`PrintTasks()` ne demande pas savoir enregistrer, savoir exporter. Elle demande ce dont elle a besoin `List()`.

---

### Séparation des responsabilités

En Go, l'approche typique consiste à séparer le contrat par responsabilité. Cela ne veut pas dire que l'implémentation doit se fragmenter, au contraire. Une même implémentation peut prendre en charge plusieurs interfaces à la fois. Simplement, les consommateurs ne prennent que ce qui leur est nécessaire.

On définit plusieurs petites interfaces pour les tâches: 

```go 
package main

type TaskAdder interface {
	Add(title string) (Task, error)
}

type TaskCompleter interface {
	MarkDone(id int) error
}

type TaskFinder interface {
	FindByID(id int) (Task, bool)
}
```

Le code qui ajoute une tâche n'as pas besoin de savoir comment la marquer comme terminée. Le code qui la marque comme terminée n'as pas besoin de savoir chercher par son ID. On interdit pas aux implémentations de savoir faire plus, on interdit au consommateur d'exiger plus que ce qui est nécessaire.

```go 
AddTaskFlow  ---> TaskAdder
DoneTaskFlow ---> TaskCompleter
ShowTaskFlow ---> TaskFinder
```

Les consommateurs dépendent d'un contrat minimal. L'implémentation peut être la même, mais les dépendances deviennent plus fines.

---

### Nommages des interfaces

go utilise un style: le nom décrit un rôle ou une action, souvent avec un suffixe `-er`: (Reader, Writter, Stringer).

```go 
type TaskAdder interface {
	Add(title string) (Task, error)
}
```

---

### Composition d'interfaces 

Les interfaces peuvent être composées. C'est pratique dans le cas oû l'on as besoin d'un contrat plus large, mais que l'on souhaite conserver des briques petits et réutilisables.

Dans une application, il peut être utile d'avoir un stockage complet des tâches, capable d'ajouter, de lister, et de marquer comme terminée. Dans ce cas, on peut venir composer une interface.

```go 
type TaskStore interface {
	TaskAdder
	TaskLister
	TaskCompleter
}
```

`TaskStore` n'est pas l'interface principale, mais un assemblage pratique pour les endroits où l'on a seulement besoin de `TaskLister`, on est pas obligé de prendre `TaskStore`.

La composition est un moyen de ne pas dupliquer les signatures.

---

### Exemple: une implémentation et plusieurs interfaces 

On as un seul type qui possède tout ce qu'il faut, mais les consommateurs ne verront que les méthodes qu'ils ont demandées via de petites interfaces.

On retrouve une structure: 

```go 
package main

type InMemoryTasks struct {
	nextID int
	items  []Task
}
```

On ajoute ensuite ensuite les différentes méthodes: 

```go 
package main

import "errors"

func (s *InMemoryTasks) Add(title string) (Task, error) {
	if title == "" {
		return Task{}, errors.New("title is empty")
	}
	s.nextID++
	t := Task{ID: s.nextID, Title: title, Done: false}
	s.items = append(s.items, t)
	return t, nil
}

func (s *InMemoryTasks) List() []Task {
	return s.items
}

func (s *InMemoryTasks) MarkDone(id int) error {
	for i := range s.items {
		if s.items[i].ID == id {
			s.items[i].Done = true
			return nil
		}
	}
	return errors.New("task not found")
}
```

Dans `main()`, on retrouve un objet qui convient à des interfaces différentes: 

```go 
package main

import "fmt"

func main() {
	store := &InMemoryTasks{}

	_, _ = store.Add("Acheter du pain")
	_, _ = store.Add("Apprendre les interfaces (petites !)")

	PrintTasks(store)
	_ = store.MarkDone(1)
	fmt.Println("---")

	PrintTasks(store)
}
```
---

## Où déclarer les interfaces 





