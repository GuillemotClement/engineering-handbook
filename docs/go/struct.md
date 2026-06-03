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

---

## Comparaison de struct 

Une struct est comparaible avec `==` si chacun des champs des deux structs sont aussi comparable avec `==`. Si au moins un champ ne respecte pas la règle, alors la struct ne peut pas être comparer. Cette règle est récursive.

Un slice, un map ou une fonction ne peuve pas être comparer.

### Comparaison `==` 

L'application dispose d'une struct `Task`qui contient des champs comparables. 

```go 
type Task struct {
	ID    int
	Title string
	Done  bool
}

func main() {
  // deux tasks contiennent les meme valeurs de champs
	a := Task{ID: 1, Title: "Read spec", Done: false}
	b := Task{ID: 1, Title: "Read spec", Done: false}

	fmt.Println(a == b) // true
}
```

On peut facilement implémenter une fonction utilitaire permettant de chercher des doublons :

```go 
type Task struct {
	ID    int
	Title string
	Done  bool
}

func containsTask(tasks []Task, x Task) bool {
  // on parcourt le slice de struct Task
  // pour chaque, on vérifie si un doublon est présent
	for _, t := range tasks {
		if t == x {
			return true
		}
	}
	return false
}

func main() {
	tasks := []Task{{ID: 1, Title: "Read spec"}}
	fmt.Println(containsTask(tasks, Task{ID: 1, Title: "Read spec"})) // true
}
```

---

### Comparaison par sens 

Lorsqu'une comparaison n'est pas comparable, ce n'est plus à l'opérateur d'effectuer la comparaison, mais au code et du domaine métier.

Généralement, les entité ont un identifiant unique, comme un `ID` permettant des les identifier de manière unique.

```go 
type Task struct {
	ID    int
	Title string
	Done  bool
}

// on compare les struc avec leur ID 
func sameTaskByID(a, b Task) bool {
	return a.ID == b.ID
}

func main() {
	a := Task{ID: 1, Title: "Write"}
	b := Task{ID: 1, Title: "Write (edited title)"}

	fmt.Println(sameTaskByID(a, b)) // true
}
```

---

### Comparaison avec pointeurs 

La comparaison des pointeurs compare les adresse mémoire, et pas la valeurs de la struct.

```go 
type Task struct {
	ID    int
	Title string
	Done  bool
}

func main() {
	p1 := &Task{ID: 1, Title: "Same data"}
	p2 := &Task{ID: 1, Title: "Same data"}

	fmt.Println(p1 == p2)   // false: different addresses
	fmt.Println(*p1 == *p2) // true: data are the same
}
```

---

## Struct tags 

Les tags permettent d'ajouter une "etiquette" sur les champs d'un struct. Celle ci sont utiliser pour pour "montrer" ces données vers l'exterieur (json, ..).
Il se compose d'une paire clé valeur, la clé est le nom de la convention, et la valeur la règle de cette convention. Il s'écrit dans le champ de la struct, après le type.
Il est possible de stocker plusieurs paires, séparé par un espace 

```go 
type Task struct {
	Title string "json:\"title\""
	Done  bool   `json:"done"` // syntaxe à utilise car plus clair 
  Title string `json:"title" db:"task_title"` // tag multiple 
}

func main() {}
```

Dans cet exemple, les tags se lisent de cette manière: "pour JSON, ce champ s'apelle 'title', pour une base de donnée hypothétique 'task_title'. 

### omitempty 

En ajoutant cette valeur dans le tag, si la valeur du champ est vide, celle ci est exclu dans le résultat JSON. Il n'indique pas une valeur optinel, pour uniquement le fait d'exlure cette valeur dans le json.

```go 
type Task struct {
	Title string `json:"title,omitempty"` // si le title n'est pas de valeur, il est exlut 
}

func main() {}
```

---

### ignorer un champ 

Le JSON dispose d'un caractère spécial dans les situation ou un champ d'une struct est utiliser pour du debug par exemple, et ne doit jamais apparaitre dans le JSON.

```go 
type Task struct {
	ID       int    `json:"id"`
	Title    string `json:"title"`
	Done     bool   `json:"done"`
	internal string `json:"-"` // le champ n'est pas exporté et est en plus ignoré
}

func main() {}
```

---

## Invaraiants du modèle

Une struct est un conteneur de champ, et un modèle est un conteneur + les règles. Ces règles sont appelées invarariants.
Un invariant est une condition qui doit être vraie pour chaque valeur correcte d'un type. Par exemple, si on as une tache, alors il y a de forte chance qu'un titre vide ne soit pas une tache.
La zero value d'une struct est techniquement valide, mais du point de vue du modèle, elle peut être fausse. `Task{}` se compile et existe mais cela ne veut pas dire qu'il faut la stocker en mémoire ou la montrer à l'utilisateur.

On as une application de console de liste de tâche. On ajoute une tâche, on affiche la liste, on marque une tache comme terminée. On souhaite prôtégé les données.

- un `ID` doit être positif
- le titre ne doit pas être vide
- le status doit appartenir aux valeurs autorisées 

On viens valider les données à la frontière, c'est à dire, le moment ou les données entrent dans le système. Dans une application CLI, la frontière est l'entrée utilisateur et toute source de données externe. On vérifie donc les données lorsque l'on viens récupérer ces entrées. Plus la données est vérifier tôt, plus le risque de bug lié à la validation est réduit.

**Pattern contrat de validation**
En Go, on utilise des fonction permettant de vérifier les valeurs. Cette fonction retourne une `error` si une validation échoue. De cette manière, on peut savoir ce qui pose problème. 

**Pattern créer et vérifier**
On peut vérifier si une struct est déjà construire, et organiser le processus de création de manière à rendre difficile l'obtention accidentelle d'une mauvaise tâche. Pour cela, on créer une fonction constructeur qui permet de créer une valeur et de la valider.

**Read -> Parse -> Validate -> Build**
Une fois le modèle et ses règles implémenter, on les appliques à la frontière. C'est une CLI classique, le pattern consiste à lire une chaîne, la parser et vérifier.

**Erreur de validation utile**
Une erreur de validation est un petit message permettant de savoir quel erreur à fait échouer la validation.
Le message doit être court et précis: `title is empty` par exemple.

```go 
package main

import "fmt"

// définition d'un type pour le status
type Status int

// définis des constante pour le status
const (
	StatusTodo Status = iota + 1
	StatusDone
)

// défintion de la struct de la task 
type Task struct {
	ID     int
	Title  string
	Status Status
}

// fonction de validation => permet de valider les données entrantes
// elle retourne un erreur dans le cas ou une validation échoue
// chaque validation retourne un message d'erreur explicite
func ValidateTask(t Task) error {
	if t.ID <= 0 {
		return fmt.Errorf("id must be positive")
	}
	if strings.TrimSpace(t.Title) == "" {
		return fmt.Errorf("title is empty")
	}
	if t.Status != StatusTodo && t.Status != StatusDone {
		return fmt.Errorf("unknown status")
	}
	return nil
}

// pattern créer et valider 
// la fonction prends les valeurs à ajouter dans la struct 
// la fonction retourne la nouvelle struct et une error
func NewTask(id int, title string) (Task, error) {
  // initialisation de la struct
  // on viens normaliser les valeurs 
	t := Task{
		ID:     id,
		Title: strings.TrimSpace(title),
		Status: StatusTodo,
	}
  // on appelle la fonction de validation en passant la nouvelle struct et si la validation echoue, on retourne une erreur 
	if err := ValidateTask(t); err != nil {
    // récupère et transmet l'erreur de validaiton 
		return Task{}, err
	}

	return t, nil
}

// la fonction permet d'update le status d'une task 
// on lui passe un pointeur vers la task à modifier
func MarkDone(t *Task) error {
	// si le pointeur est nil, la task n'existe pas, on protege contre une panique
  if t == nil {
		return errors.New("task is nil")
	}
  // on modifie le status
	t.Status = StatusDone
  // on retourne la valeur après validation 
	return ValidateTask(*t)
}

func main() {
	in := bufio.NewReader(os.Stdin) // on prépare la récupération 

  // on viens récupérer les saisies
	fmt.Print("id: ")
	idLine, _ := in.ReadString('\n')

	fmt.Print("title: ")
	titleLine, _ := in.ReadString('\n')

  // on viens convertir l'id en int 
	id, err := strconv.Atoi(strings.TrimSpace(idLine))
	if err != nil {
		fmt.Println("invalid id:", err) // example: invalid id: strconv.Atoi: parsing "x": invalid syntax
		return
	}

  // on appelle la fonction permettant de créer une nouvelle task
	t, err := NewTask(id, titleLine)
	if err != nil {
		fmt.Println("invalid task:", err)
		return
	}
  
  // si la création à réussie, on obtient la nouvelle tasks
	fmt.Printf("created: %+v\n", t)

  // on créer une nouvelle tasks = exemple ==========================
  task := Task{ID: 1, Title: "finish lecture", Status: StatusTodo}
  // on viens mettre à jour la task
  _ = MarkDone(&task)
	fmt.Println(task.Status) // 2

}
```

---

## Méthodes Go 

### Receiver 

Une méthode est une fonction dont le paramètre spécial est écrit entre parenthèses avant le nom de la fonction: **le receiver**. 
Le receiver se passe de cette manière: `func (t Task) Lavel()`, puis l'appel d'une méthode, se fait en faisant référence au struct auquel la méthode est lié: `t.Label()`. 

Le receiver est une variable dans la portée de la fonction. Les règles habituelle de visibilité s'applique dessus. Le nom du receiver doit rester court et respecter ces bonnes pratiques:

- le receiver est nommé avec un ou deux lettres, et ce nom est conserver dans toutes les méthode du stuct 
- Pour `Task`, on utilise `t`, `User` `u`, etc 
- pour un nom composé: `TaskStore` on utilise `s` ou `st`
- le nom du receiver ne doit pas se confondre avec une autre variable

Le receiver d'une méthode peut être autre chose qu'une sutrct. Par exemple, on peut déclarer une méthode sur un type fonction, et le receiver sera alors logiquement appelé `fn`.

```go 
type Task struct {
	ID    int
	Title string
	Done  bool
}

// méthode qui prends le receiver de Task
func (t Task) Label() string {
	return fmt.Sprintf("#%d %s", t.ID, t.Title)
}

func main() {
	t := Task{ID: 1, Title: "Understand methods"}
	fmt.Println(t.Label()) // #1 Understand methods
}
```

---

### Nommage de méthode 

En Go, on attend souvent qu'un appel de méthode ressemble à une phrase naturelle: `tast.Done` est un champ, alors que `taskt.MarkDone()` est une action. 

```go 
type Task struct {
	ID    int
	Title string
	Done  bool
}

// méthode qui check si le champ est valide 
func (t Task) IsDone() bool { // question — réponse booléenne
	return t.Done
}

// méthode qui update le status 
func (t *Task) MarkDone(){
  t.Done = true 
}
```

**Renvoie de champ**
En Go, lorsqu'une méthode retourne un champ du struct, on le nom du champ comme nom de méthode.

```go 
type Task struct {
	id int
}

// retourne le champ ID du struct 
func (t Task) ID() int {
	return t.id
}
```

**Type du receiver**
Un type doit avoir un style de méthode prévisible. Pour garder de la cohérence, il faut choisir un style et s'y tenir.

- Une méthode lecture seront sur `Task`
- méthode de modification seront sur `*Task`


### Pattern constructeur 

En Go, il n'y a pas de constructeur comme dans un language Object. A la place, on utilise une fonction `NewX()`.

```go 
type Task struct {
	ID    int
	Title string
	Done  bool
}

// fonction constructeur 
func NewTask(id int, title string) Task {
	return Task{ID: id, Title: title, Done: false}
}

func main() {
	t := NewTask(1, "Write NewTask")
	fmt.Println(t.Done) // false
}
```

### Exemple

On ajoute plusieurs méthodes pour l'application de task

```go 
type Task struct {
	ID    int
	Title string
	Done  bool
}

// la fonction permet de renommer une task
func (t *Task) Rename(title string) {
	t.Title = title
}

// la fonction vient créer une chaîne pour afficher la task
func (t Task) Label() string {
	status := "TODO"
	if t.Done {
		status = "DONE"
	}
	return fmt.Sprintf("[%s] #%d %s", status, t.ID, t.Title)
}

// permet d'update le status à Done d'une task
func (t *Task) MarkDone() {
	t.Done = true
}

func main() {
	t := NewTask(1, "Understand method naming")
	fmt.Println(t.Label()) // [TODO] #1 Understand method naming

	t.MarkDone()
	fmt.Println(t.Label()) // [DONE] #1 Understand method naming
}
```

---

## Value & Pointer receiver 

- pointer: dès qu'on viens modifier l'objet 
- value: uniquement pour de la lecture et un petit objet 

### Value receiver `(T)`: la méthode recoit une copie 

Utile lorsque la méthode se contente de lire des données, on passe une copie de l'objet. Cette apporoche peut être lourde, car l'objet entier est copier à chaque appel.

```go 
type Task struct {
	Title string
	Done  bool
}

func (t Task) IsDone() bool {
	return t.Done
}

func main() {
	task := Task{Title: "Buy milk", Done: true}
	fmt.Println(task.IsDone()) // true
}
```

### Pointer receiver 

Nécessaire lorsque la méthode vient modifier l'objet.

```go
type Task struct {
	Title string
	Done  bool
}

// pointeur receiver => modifier le champ de l'objet 
func (t *Task) MarkDone() {
	t.Done = true
}

func main() {
	task := Task{Title: "Write tests", Done: false}
	task.MarkDone()
	fmt.Println(task.Done) // true
}
```

**Pointeur pour champ slice**

Pour travailler sur des slice au sein de la méthode, on utilise un pointeur 

```go 
type Task struct {
	Title string
	Tags  []string
}

func (t *Task) AddTag(tag string) {
	t.Tags = append(t.Tags, tag)
}

func main() {
	task := Task{Title: "Learn Go", Tags: []string{}}
	task.AddTag("study")
	fmt.Println(task.Tags) // [study]
}
```

**Adressabilité**
Un pointeur receiver exige que le receiver soit adressage, c'est à dire que la valeur est une vrai adresse qu'on peut prendre.

Pour qu'un constructeur fonctionne correctement avec des méthode pointeurs, on le fait retourner un pointeur: `*Task`

```go
type Task struct {
	Done bool
}

func (t *Task) MarkDone() {
	t.Done = true
}

// le constructeur retourne le pointeur de l'objet créer 
func NewTask() *Task {
	return &Task{Done: false}
}

func main() {
	task := NewTask() // cr"ation de la task avec la fonction constructeur 
	task.MarkDone()
	fmt.Println(task.Done) // true
}
```

---

## Method set et interface 

Une interface est un type qui décrit quelles méthodes doivent exister sur une valeur pour qu'elle puisse être utilisée à un endroit donné. Un type convient à une interface s'il possède tous les méthodes requise.

Le **method set** d'un type correspond à l'ensemble des méthode que le type possède dans le contexte de la vérification des interfaces.

- `T`: méthode déclarée avec un `(t T)` 
- `*T`: méthode déclarée avec un receiver `(t T)` et `(t *T)` 

Un type `*T` voit plus de méthodes que `T`. Pour une interface, la logique est stricts: ça convient où ça ne convient pas.

**Pourquoi `t.M()` peut fonctionner mais l'interface n'accepte pas `t`**

Lorsqu'une variable est adressable `t`, et que le type à une méthode avec un pointeur `func (t *Task) M ()`, alors l'appel `t.M()` peut etre compilé. Dans ce cas, le compilateur fera: `(&t).M()`. 
Cette insertion automatique ne fonctionne que pour l'appel avec un point, et seulement quand la valeur est adressable. Cela ne signifie pas que `T` à obtenu cette méthode dans son method set, et la compatibilité est vérifiée par celui ci strictement.

On as une `Task` et on souhaite pouvoir la marquer comme terminée.

```go
// déclaration du type Task
type Task struct {
	ID    int
	Title string
	Done  bool
}

// création d'une interface "peut etre terminé"
type Completer interface {
	MarkDone() // l'interface attends que le type passer implemente la methode 
}

// la fonction accepte n'importe quel valeur qui implemente l'interface Completer
func Complete(c Completer) {
	c.MarkDone()
}

// la methode appartient au type *Task
func (t *Task) MarkDone() {
	t.Done = true
}

func main() {
	t := Task{ID: 1, Title: "Learn method sets"}
	// Complete(t)   // ne compile pas car une valeur du type Task n'implemente pas MarkDone 
	Complete(&t) // fonctionne car la valeur est de type *Task et implemente MarkDone
	fmt.Println(t.Done) // true
}
```

### Travailler avec un range 

Avec un range, l'adresse memoire sera celle de la valeur dans le scope du `range`, et pas l'adresse de la valeur dans un slice.
Pour obtenir la bonne adresse de l'élément du slice, on travil avec les indices et pas la copie de la valeur

```go
type Task struct {
	ID   int
	Done bool
}

func (t *Task) MarkDone() { t.Done = true }

func main() {
	tasks := []Task{{ID: 1}, {ID: 2}}

	for i := range tasks {
		tasks[i].MarkDone()
	}

	fmt.Println(tasks[0].Done, tasks[1].Done) // true true
}
```

### Stratégie de choix des receiver

Quand on conçoit un type pour une application, il faut tenir compte d'une règle simple: le type doit etre pratique et previsible, à la fois seul mais également avec les interfaces.

Si une méthode modifie une valeur de l'objet, un pointeur receiver est logique.

Si une méthode viens lire de la donnée et retourner un résultat dérivé (`IsDone() bool` ou `Title()`), alors une value receiver est plus pratique car il sera automatiquement disponible pour `Task` et `*Task`.

---

## Interface `fmt.Stringer` 

Le paquet `fmt` possède une convention: si une valeur sait se convertir elle-meme en chaine, alors `fmt` l'utilise. Il correspond alors au contrat :

```go
type Stringer interface {
    String() string
}
```

### `String()`

La méthode `String()` doit retourner une chaine. Elle ne doit jamais modifier l'objet, et est utiliser uniquement pour l'affichage. On lui définis un value receiver.
`fmt` viens utiliser automatiquement la `String()` définit pour l'affichage.

Lorsqu'il y a des status, on utilise ce pattern. On viens définir les types comme base sur `int` et des constantes. Pour afficher la valeur, on utilise ensuite `String()`
Pour vérifier quel status est utiliser, et retourner la bonne chaine, on utilise alors un `switch`.


```go 
package main

import "fmt"

// déclaration des status
type Status int

const (
	StatusTodo Status = iota
	StatusDone
)

// implémentation de switch pour gérer l'affichage du status => lië au type Status
func (s Status) String() string {
	switch s {
	case StatusTodo:
		return "todo"
	case StatusDone:
		return "done"
	default:
		return fmt.Sprintf("Status(%d)", int(s))
	}
}

type Task struct {
	ID     int
	Title  string
	Status Status // le status est de type Status, ce qui permet d'utiliser son String() pour son affichage
}

// implémentation de String() pour gérer l'affichage des tasks
// se base sur le type Task
func (t Task) String() string {
	return fmt.Sprintf("#%d %q [%s]", t.ID, t.Title, t.Status)
}

func main() {
	// on passe une constante pour dëfinir le status
	t1 := Task{ID: 1, Title: "Comprendre Stringer", Status: StatusTodo}
	t2 := Task{ID: 2, Title: "Se rejouir", Status: StatusDone}

	fmt.Println(t1) // #1 "Comprendre Stringer" [todo]
	fmt.Println(t2) // #2 "Se rejouir" [done]
}
```

**Affichage réelle**

`%+v`: permet d'afficher les cle valeur d'un map par exemple
`%#v`: permet d'afficher une représentation complète

```go
package main

import "fmt"

type Task struct {
	ID    int
	Title string
	Done  bool
}

func (t Task) String() string {
	return fmt.Sprintf("Task#%d %q", t.ID, t.Title)
}

type rawTask Task

func main() {
	t := Task{ID: 10, Title: "voir les champs", Done: false}

	fmt.Println(t)                  // Task#10 "voir les champs"
	fmt.Printf("%+v\n", rawTask(t)) // {ID:10 Title:voir les champs Done:false}
}
```

**Bonne pratique String()**

- pas d'effet de bord, `String()` ne doit pas modifier les champs, écrire dans un fichier, incrémenter un compteur
- pas de `panic`, on retourne proprement une erreur comme `<invalid>`
- ne pas utiliser le formatage de soi-meme via `%v`, sinon un recursion apparait => cela viens appeller de nouveau `String()`

---

## Méthodes sur les types slice: `type Items[] Item`

Il existe une astuce simple: créer un type nommé au dessus du slice `type Items[] Item`. On peut ensuite déclarer des méthodes et obtenir une API lisible: `items.Add(...)`, `items.MarkDone()`, ...
Cela permet d'obtenir un code plus pratique: les opérations sur la collections vivent à coté et s'appellent de la meme maniere.

On ne peut pas déclarer des méthodes sur le type pur `[]Item` car ce n'est pas un type nommé. Elles ne se déclarent que sur des types nommés, definis dans le package.

### Pattern: `Item` et collection `Items`

On souhaite mettre en place une liste de taches intelligente avec un type et des méthodes.

`Items` est un type disctinct. En interne, c'est un slice, mais à l'extérieur, on peut exposer des méthodes propres.

```go
package main

type Item struct {
	ID    int
	Title string
	Done  bool
}

type Items []Item

// retourne la taille de la collection
func (it Items) Len() int {
	return len(it)
}

// recherche pas id 
func (it Items) FindByID(id int) (Item, bool) {
	for _, x := range it {
		if x.ID == id {
			return x, true
		}
	}
	return Item{}, false
}

// mutation 
func (it Items) MarkAllDone() {
	for i := range it {
		it[i].Done = true
	}
}

// ajout element 
// style value 
func (it Items) Add(x Item) Items {
	return append(it, x)
}
// utilisation
items = items.Add(Item{ID: 1, Title: "Learn Go"})

// style pointer 
func (it *Items) Add(x Item) {
	*it = append(*it, x)
}
// utilisation 
items.Add(Item{ID: 2, Title: "Write tests"})


// fonction constructeur 
func (it *Items) Add(x Item) {
	*it = append(*it, x)
}

// change le status 
func (it Items) MarkDone(id int) bool {
	for i := range it {
		if it[i].ID == id {
			it[i].Done = true
			return true
		}
	}
	return false
}

// suppression stable 
func (it *Items) DeleteByID(id int) bool {
	s := *it
	for i := 0; i < len(s); i++ {
		if s[i].ID != id {
			continue
		}

		// Décaler le suffixe d'un cran vers la gauche.
		copy(s[i:], s[i+1:])

		// Mettre à zéro le dernier élément (utile pour le GC).
		s[len(s)-1] = Item{}

		// Tronquer le slice d'un élément et sauvegarder via le pointeur.
		*it = s[:len(s)-1]
		return true
	}
	return false
}

func main() {
	// creation du slice avec le type personnalise
	items := Items{}

	// creation de deux nouvel tasks
	items.Add(Item{ID: 1, Title: "Learn Go"})
	items.Add(Item{ID: 2, Title: "Drink water"})

	// passe la deuxieme task en status Done
	ok := items.MarkDone(2) 

	fmt.Println("marked:", ok)       // marked: true
	fmt.Println("len:", items.Len()) // len: 2
	fmt.Println(items[1].Done)       // true
}
```

---

## Embedding 

L'embedding permet d'intégrer un type à l'interieur d'un autre pour que ces champs et méthodes soit accessibles. 
Cela permet d'acceder a des champs d'un type intégré dans un premier type. On peut de manière réaliser des compositions.

L'utilisation des struct composés permet d'isoler les champs communs dans une struct séparé, qui sont ensuite intégrés dans une autre struct.

Les champs du struct intégrés remonte automatiquement.

```go
// intégré dans le struct Task
type Meta struct {
	ID      int
	Version int
}

type Task struct {
	Title string
	Done  bool
	Meta // champ composé 
}

type Stats struct {
	Total int
	Done  int
}

type Project struct {
	Name string
	Stats // integre le struct Stats
}

func main() {
	t := Task{Title: "Refactor model", Done: false, Meta: Meta{ID: 5, Version: 1}} // inialisation des champs composés
	fmt.Println(t.ID, t.Version, t.Meta.ID) // 5 1 5 - affichage 

	p := Project{Name: "home", Stats: Stats{Total: 12, Done: 3}}
	fmt.Println(p.Done, "/", p.Total) // 3 / 12
}
```
---

## Composition comme style d'héritage 

En Go, il n'existe pas d'héritage comme dans d'autre langage. On ne peut pas faire de `extends`. 
Pour retrouver cette logique, on étend le comportement via le contient.
 
En Go, on se pose la question: de quelle petites parties assembler un type afin qu'il fasse son travail et que le code montre clairement la responsabilié de chaque partie.

### Composition de données: une struct composé de parties

Dans un gestionnaire de tache, on peut avoir une `Task`, avec des métadonnée comme l'auteur, ou le projet auquel la tache appartient.

```go 
type Meta struct {
	Author  string
	Project string
}

type Task struct {
	Title string
	Done  bool
	Meta  Meta // composition: Task contient Meta
}

func main() {
	t := Task{
		Title: "Read Go spec", 
		Meta: Meta{
			Author: "Ann", 
			Project: "study"
		}
	}

	fmt.Println(t.Meta.Author) // Ann
}
```

`Meta` est un bloc sémantique séparé, on peut venir le réutiliser dans d'autres entités sans recopier les champs.

---

### Composition du comportement: petits types et délégation 

Les méthodes appartiennent au types. Cela signifie que l'on peut étendre un comportement. Il est avantageux de créer de petits types responsables de petits morceaux de logique, puis de les brancher via des champs. Cela permet de réutiliser des methodes.

Par exemple, on souhaite gérer proprement le status d'une tache. On peut utiliser `Done` comme un `bool`, mais dès que l'on souhaite ajouter des status supplémentaires, `bool` est trop limité.

On viens donc créer un type séparé `Status`, puis on ajoute des méthodes.
On utilise la méthode `Task.Done()` comme méthode d'enrobage. Elle n'est pas obligé de révéler à l'extèrieur la façon dont le status est constuit. Elle délègue la décision à `Status`, mais l
API de la tache reste pratique: "La tache est=elle faite ?"

```go 
package main

// ajout du type personnalisé
type Status string 

// déclaration des différents status avec des const
const (
	StatusTodo Status = "todo"
	StatusDone Status = "done"
)

// la fonction permet de savoir si la task est terminé
func (s Status) IsDone() bool {
	return s == StatusDone 
}

// on ajoute le nouveau type dans la struct de Task 
type Task struct {
	Title  string
	Status Status
}

// la fonction appelle la methode de Task pour verifier si la task est terminé
func (t Task) Done() bool { 
	return t.Status.IsDone() 
}

func main() {
	t := Task{Title: "Write code", Status: StatusDone}
	fmt.Println(t.Done()) // true
}
```

La composition du comportement ressemble souvent a "champ + methode d'enrobage". Cela augmente la verbosite du code, mais on garde le controle sur ce que l'on souhaite exposer.

On pourrait potentiellement avec autre d'entité que `Task` et on pourrais venir reutiliser la méthode pour verifier si autre chose est terminé

### Embedding et controle de l'API 

**Embedding comme accélérateur d'accès** 

Lorsque l'on utilise la composition, on peut écrire dans une struct un champ sans nom, et les champs/méthodes du type imbriqué remontent et son directement accessible.

```go
type Meta struct {
	Author  string
	Project string
}

type Task struct {
	Title string
	Meta // embedded field
}
```

Désormais, `t.Author` est une forme courte pour `t.Meta.Author`.
Lorsque l'on utilise la composition, on indique au lecteur du code que les champs/methode de `Meta` font partie de la surface API de `Task`.

**Méthodes d'enrobage: controler la surface du type**

Au début, on fait de la composition car c'est plus court, puis le temps passe, et on se rend compte que les méthodes/champs ont fui vers l'exèrieur alors qu'il n'aurait pas du faire partie de l'API publique du type.

On passe alors au duo champ + méthode d'enrobage.

Dans notre application de todo, on veux stocker l'auteur et le projet, mais leur modification ne doit etre possible que via des fonction séparées, par exemplem avec des validations.

```go 
type Meta struct {
	author  string
	project string
}
// les deux méthodes permet d'afficher la valeur du status
func (m Meta) Author() string  { return m.author }
func (m Meta) Project() string { return m.project }

type Task struct {
	Title string
	meta  Meta
}
// le deux methodes viennent chercher la donnees en passant pas les methodes du type Meta
func (t Task) Author() string  { return t.meta.Author() }
func (t Task) Project() string { return t.meta.Project() }
```

Avec cette approche, on controle désormait un point de controle unique. On peut modifier la structure de `Meta` sans casser les appels exterieur. 

**Composition ou champs: un choix pragmatique**

Il existe un raisonnement pratique. 

Si on vient imbriquer via la composition, on fait automatiquement remonter les champs et méthodes de la struct imbriqué. Cette approche est utile dans les cas ou le type intégré est réellement un morceau de logique de l'objet lui meme.

En revanche, si le type intéré est une dépendance (logger, client, configuration), la composition dégrade la lisibilité. 

```go
import "fmt"

type Logger struct{}

func (Logger) Debug(msg string) {
	fmt.Println("DEBUG:", msg) 
}

type ServiceA struct{ 
	Logger  // embedding: Debug va "déborder" vers l'extérieur
}       
type ServiceB struct{ 
	logger Logger // field: access controlled
} 

func main() {
	ServiceA{}.Debug("hi") // DEBUG: hi
}
```

Dans `ServiceA`, la méthode `Debug()` fait maintenant partie de la surface du service, alors que le service peut concerner des taches, et pas la logging.
Dans `ServiceB`, on créer une méthode propre `LogDebug`, ou un logger interne, sans l'exposer depuis l'extérieur.

### Exemple 

Ce pattern se retrouve très souvent dans les applications réelle: il existe une entitg métier "tache", et il existe une "tache dans le stockage", a laquele s'ajout un `ID`.

En Go, on fait "StoredTask contient Task"

**Option 1: champ nommé**
```go 
type Task struct {
	Title  string
	Status Status
}

type StoredTask struct {
	ID   int
	Task Task // composition explicite
}

// utilisation =====
type Status string

const (
	StatusTodo Status = "todo"
)

type Task struct {
	Title  string
	Status Status // type personnalisé
}

type StoredTask struct {
	ID   int
	Task Task // champ nommé
}

func main() {
	st := StoredTask{ID: 10, Task: Task{Title: "Buy milk", Status: StatusTodo}}
	fmt.Println(st.Task.Title) // Buy milk => creation avec la méthode du type Task
}
```

**Option 2: composition**

```go 
package main

type StoredTask struct {
	ID int
	Task // embedded
}
```

Alors `st.Title` fonctionne directement. 

---

### Composition de services: un objet composé de plusieurs composants

La composition est utile pour les modèles de donnéés, mais aussi pour les objets de services qui exécutent des opérations. 
On peut écrire un code lisible: **un type = une responsabilité**. Le comportement complexe est ensuite assemblé à partir de plusieurs petites parties.

Par exemple, on implémente un validateur de titre de tache, Il n'est pas nécessaire de connaitre toute la `Task`, il ne fait que vérifier la chaine:

```go 
type TitleValidator struct{}

func (TitleValidator) Validate(title string) error {
	if len(title) == 0 {
		return fmt.Errorf("title is empty")
	}
	return nil
}

// implémentation du service de tache qui contient le validateur comme champ:
type TaskService struct {
	validator TitleValidator // utilise le type TitleValidator
}

func (s TaskService) NewTask(title string) (Task, error) {
	// on peut accéder à la validation via le struct
	if err := s.validator.Validate(title); err != nil {
		return Task{}, err
	}
	return Task{Title: title, Status: StatusTodo}, nil
}

// assemblage dans le main 
func main() {
	// initialisation du service
	svc := TaskService{validator: TitleValidator{}}
	t, err := svc.NewTask("Learn Go") // utilisation de la methode, qui appelle le validator
	fmt.Println(t.Title, err) // Learn Go <nil>
}
```

`TaskService` n'hérite pas du validateur, il l'utilise. ce type de code est plus simple car les détails ne sont pas caché par la "magie" de l'héritage.
