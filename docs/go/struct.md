# Struct 

Un struct permet de décrire un nouveau type de donnée. C'est un "objet" qui contient des ensemble de valeurs.

### `type Task struct { }` - déclaration 

Lorsque l'on déclare une struct, chaque champs s'écrit sous la forme `Nom Type`. Par défaut, chaque champ aura sa `nul value`. Même si aucune valeurs n'est affecté aux champs du struct, elle possède la valeur zero.

```go 
package main

import "fmt"

// déclaration de la struct 
type Task struct {
	ID    int // 0
	Title string // ""
	Done  bool // false
}

func main() {
	var t Task // nouvelle variable avec ce type => t permet de faire référence à cette struct
	fmt.Printf("%T\n", t) // main.Task
}
```

---

### Accéder aux champs 

Pour accéder auxs champs d'un struct, on utilise `.`.

```go 
type Task struct {
	ID    int
	Title string
	Done  bool
}

func main() {
	var t Task
	t.ID = 1
	t.Title = "Read Go spec (or at least pretend)"
	t.Done = false

	fmt.Println(t.ID, t.Title, t.Done) // 1 Read Go spec (or at least pretend) false
}
```

---

### Structure et pointeur 

Si on souhhaite modifer une valeur depuis une fonction, on passe un pointeur `*Tasks`. L'accès au aux champs reste normal, le compilateur gère automatiquement le déférencement.

```go 
type Task struct {
	Title string
	Done  bool
}

// la fonction prends le pointeur vers la struct
func markDone(t *Task) {
  // on accède au champ normalement, le compilateur gère automatiquement le déréférencement.
	t.Done = true
}

func main() {
	var x Task
	x.Title = "Wash the dishes"
	markDone(&x) // on passe alors la struct comme pointeur à la fonction 

	fmt.Println(x.Title, x.Done) // Wash the dishes true
}
```

---

### Exportation des champs 

En Go, un nom qui commence par une majuscule indique qu'il est exporté (accessible depuis d'autres paquets). L'export est une frontière. En tant qu'auteur du code, on décide ce qui peut être modifié depuis l'extérieur, et ce qui ne le peut pas. Il est parfois utilie de masquer des champs pour empêcher qu'on puisse écrire directement n'importe quoi.

```go 
type Task struct {
	Title string // exporté
	done  bool // non exporté
}

func main() {
	var t Task
	t.Title = "Public title"
	t.done = true // a l'interieur du paquet main, c'est autorise

	fmt.Println(t.Title, t.done) // Public title true
}
```

---

### Exemple: une tâche comme une seul entité 

On créer une fonction qui fabrique une tâche et la renvoie comme valeur : 

```go 
package main

import "fmt"

// déclaration de la struct 
type Task struct {
	ID    int
	Title string
	Done  bool
}

// la fonction permet de créer une task
func makeTask(id int, title string) Task {
	var t Task // initialisation 
	t.ID = id // ajout de l'id 
	t.Title = title // ajout du titre 
	t.Done = false // ajout du status 
	return t // retourne la Task
}

func main() {
  // on créer une nouvelle task qu'on récupère dans une variable.
	task := makeTask(1, "Learn structs")
	fmt.Println(task.ID, task.Title, task.Done) // 1 Learn structs false
}
```

On obtient une valeur `taks` que l'on peut stocker dans un slice `[ ]Task`, transmettre, retourner depuis des fonctions, sans avoir à transporter de manière individuel chaque champs.

---

## Litteraux de struct

Un littéral de struct est une expression de forme `T{...}` ou `T` est le nom du type. A l'intérieur des accolades, on énumère les champs et valeurs. 
Il permet de créer une seule valeur du type de la struct en une fois et au même endroit. Cela permet d'améliorer la lisibilité, réduit le risque d'oublier un champ, et aide à mieux voir le modèle de donnée dans le code.

```go 
type Point struct {
	X int
	Y int
}

func main() {
	p := Point{X: 3, Y: 7} // creation du littéral de struct
	fmt.Println(p.X, p.Y) // 3 7
}
```

--- 

### Initialisation positionnelle

C'est lorsque l'on déclare les valeurs à la suite. Cette syntaxe est interessante lorsque l'on travail avec un struct simple. La valeur seron affecter aux champs en fonction de la place dans la déclaration. Go associe la première valeur au premier champ, et ainsi de suite.

Cette syntaxe peut être dangereuse car il n'y a pas de protection contre les erreurs d'insertion.

```go 
type Task struct {
	ID    int
	Title string
	Done  bool
}

func main() {
	t := Task{1, "Buy milk", false} // initialisation positionelle
	fmt.Println(t.ID, t.Title, t.Done) // 1 Buy milk false
}
```

--- 

### Initalisation nommée 

L'initialisation nommée consiste à écrire de manière explicite quel champ reçoit quelle valeurs. Cette syntaxe renforce la lisibilité et la robustesse.
L'odre des valeurs n'as plus d'importance, car elles sont explicitiement lié au champ à la déclaration.

```go 
type Task struct {
	ID    int
	Title string
	Done  bool
}

func main() {
	t := Task{ID: 1, Title: "Buy milk", Done: false}
	fmt.Println(t.ID, t.Title, t.Done) // 1 Buy milk false
}
```

---

### Initialisation partielle et zero value 

Il n'est pas obligatoire de set une valeurs à chaque champs d'un struct. Un champ non affecté aura la zero value par défaut.

```go 
type Task struct {
	ID    int
	Title string
	Done  bool
}

func main() {
	t := Task{Title: "Read Go spec"}
	fmt.Printf("%d %q %v\n", t.ID, t.Title, t.Done) // 0 "Read Go spec" false
}
```

---

### Pointeur sur un itteral : `&T{...}`

Il est parfois nécessaire de créer une struct et d'obtenir directement un pointeur vers celle ci. Par exemple, transmettre une "tâche" à plusieurs fonctions et modifier ces champs sans copier la valeur.

```go 
type Task struct {
	ID    int
	Title string
	Done  bool
}

func main() {
	p := &Task{ID: 1, Title: "Water the plants"}
	p.Done = true

	fmt.Println(p.Title, p.Done) // Water the plants true
}
```

---

### Litteraux de struct dans les slices 

L'utilisation de litteraux de struct avec slices est une pratique courante: fabriquer un jeu de données de démonstration, construire en mémoire une liste d'entités ...

On créer une fonction qui retourne la liste de départ des tâches.
```go 
type Task struct {
	ID    int
	Title string
	Done  bool
}

func demoTasks() []Task {
  // on retourne un slice avec des litteraux de struct pour des données initiales
	return []Task{
		{ID: 1, Title: "Buy milk", Done: false},
		{ID: 2, Title: "Read docs", Done: true},
		{ID: 3, Title: "Write some Go", Done: false},
	}
}

func main() {
	tasks := demoTasks()
	fmt.Println(len(tasks)) // 3
}
```

---

### Litteraux multiligne 

Dans les litteraux multilignes, après chaque éléments, une virgule est obligatoire.

```go 
type Task struct {
	ID    int
	Title string
	Done  bool
}

func main() {
	t := Task{
		ID:    10,
		Title: "Multiline literal",
		Done:  false,
	}
	fmt.Println(t.ID, t.Title, t.Done) // 10 Multiline literal false
}
```