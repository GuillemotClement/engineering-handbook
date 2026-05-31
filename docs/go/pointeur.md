# Pointeur 

Un pointeur est une valeur qui stocke l'adresse d'une autre valeur. On peut l'affecter, le comparer à `nil`, le passer à des fonctions. 
Une adresse correspond au coordonnées d'une variable en mémoire. 

Le type `*T` correspond à **un pointeur vers T**. 

- `int`: entier ordinaire -> la valeur 
- `* int`: pointeur vers un entier -> une valeur qui stocke une adresse mémoire ou se trouve un `int`

Il est possible de trouver deux pointeurs qui pointe sur une même valeur. Dans ce cas, on modif sur la valeur d'un pointeur affecte aussi la valeur de l'autre pointeur, car les deux pointe sur la même valeur mémoire.


## Opérateur `&` - stocker une adresse mémoire

L'opérateur `&x` s'appelle **la prise d'adresse**. Il prend l'adresse d'une valeur adressable et retourne un pointeur.

L'opérateur `&` ne peut être utilisé que sur de vrai case mémoire dont l'adresse à un sens. en général : 

- une variable
- un élément de slice 
- champ de structure 

```go 
func main() {
	x := 10
	p := &x // p has type *int

	fmt.Println(x) // 10
	_ = p
}
```

La variable `p` ne copie pas la valeur de `x`, elle stocke l'adresse mémoire de `x`.

--- 

## Déférencement `*p` - récupérer la valeur à l'adresse mémoire

`*p` est un déreferencement, et permet de récupérer la valeur stocker à l'adresse mémoire. Il indique "prends la valeur qui se trouve à cette adresse.
Le déreferencement peut se trouver à gacuhe de `=`, ce qui permet d'écrire une nouvelle valeur sur l'adresse mémoire.

```go 
func main() {
	x := 10
	p := &x // contient l'adresse mémoire de la variable x 

	fmt.Println(*p) // permet d'accéder à la valeur placer sur l'adresse mémoire stocké dans p => 10

  *p = 42 // on écrit sur l'adresse mémoire 
  fmt.Println(x) // 42
}
```

## Pointeur `nil`

La zero value d'une pointeur est `nil`. Cette valeur signifie qu'il n'y a pas d'adresse, ne pointe nul part.

Si on tente de déréférencer un pointeur `nil`, cela provoque une `panic`. Si on vient travailler avec un pointeur qui est potentiellement nul, il faut venir checker sa valeur.

```go 
func main() {
	var p *int // zero value => nil
	fmt.Println(p == nil) // true

  fmt.Println(*p) // panic

  // sécurisation 
  if p != nil {
    fmt.Println(*p) // panic
  }
}
```

--- 

## `%p` - afficher l'adresse mémorire 

Pour afficher une valeur de pointeur, c'est à dire l'adresse mémoire de la valeur, on utilise `%p` 

```go 
func main() {
	x := 7
	p := &x

	fmt.Printf("p=%p\n", p)   // p=0x.... affichage de l'adresse
	fmt.Printf("*p=%d\n", *p) // *p=7 => affichage de la valeur 
}
```

---

## Pointeur dans une fonction 

Les pointeurs sont utiles lorsque l'on utilise des fonctions. Il sera possible de modifier une valeur qui existe en dehors de la fonction.

On as une application qui permet de calculer un solde `balance` et on souhaite appeler une fonction `deposit()` pour l'augmenter

```go
// la fonction prend un pointeur, balance qui est la solde du compte 
// en deuxième argument, on passe la valeur à ajouter  
func deposit(balance *int, amount int) {
  // on ajoute une protection pour éviter un pointeur nil 
  if balance == nil {
    return 
  }

	*balance += amount // *balance permet d'accéder à la valeur en mémoire pointer par le pointeur 
}

func main() {
	balance := 0
	deposit(&balance, 50)
	fmt.Println(balance) // 50
}
```

---

## Pointeur et slice 

Avec les slices, on peut venir récupérer l'adresse mémoire d'un élément du slice 

```go 
func main() {
	nums := []int{10, 20, 30}
	p := &nums[1] // on vient récupérer l'adresse mémoire de l'élément du slice 

	*p = 99 // on modifie la valeur de l'adresse mémoire, l'élément placé dans le slice 
	fmt.Println(nums) // [10 99 30]
}
```

---

## `new(T)` et `make()`


On utilise `new(T)` lorsque l'on veux obtenir un pointeur sur une valeur `T `: 

- passer et modifier la même valeur depuis différents endroits 
- renvoyer un gros object d'une fonction sans le copier intégralement 
- avoir la possibilité de signaler "pas d'object" avec `nil`

- `make(...)`: créer une valeur prête à l'emploi avec un état interne. Il retourne `T`, et permet de créer des `slice`, `map`, `chan`

### `new(T)` - obtenir un pointeur vers la zero value 

Lorsque l'on utiliser `new(T)`, Go alloue de la mémoire pour une valeur de type `T`, y écrit la zero value, puis retourne l'adresse de cet emplacement mémoire. Il retourne un pointeur.

```go 
func main() {
	p := new(int)    // p: *int, contient 0 a l’interieur =/ créeation d'un pointeur vers un *int 
	fmt.Println(*p)  // 0
	*p = 10 // on set une valeur à l'emplement mémoire
	fmt.Println(*p)  // 10
}
```

**`new` avec un struct**

Une struct est une valeurs avec des champs.

```go 
type Task struct {
	Title string
	Done  bool
}

func main() {
	t := new(Task)               // t: *Task, champs = zero values
	fmt.Println(t.Title, t.Done) // "" false
}
```

On souhaite créer une application qui stocke des tâches en mémoire, et qui peut en ajouter plusieurs et les afficher 

```go 
package main

// création d'un struct pour décrire la tâche 
type Task struct {
	ID    int
	Title string
	Done  bool
}

// fonction qui permet de créer une nouvelle task
// on utilise new() pour initialiser la struct, puis on lui set les valeurs
func NewTask(id int, title string) *Task {
	t := new(Task) // *Task avec zero values
	t.ID = id
	t.Title = title
	return t
}

// on utilise le map pour stocker les différentes tasks
func main() {
	tasks := make([]*Task, 0, 8) // le slice viens stocker les task
	byID := make(map[int]*Task) // le map permet de les ranger par id 

	// création des nouvelles tasks
	t1 := NewTask(1, "Apprendre new vs make")
	t2 := NewTask(2, "Ne pas confondre nil-map et map vide")

	// ajoute dans le slice des tasks
	tasks = append(tasks, t1, t2)
	// ajout des task dans le map [index]tasks
	byID[t1.ID] = t1
	byID[t2.ID] = t2

	// affichage 
	fmt.Println(tasks[0].Title) // Apprendre new vs make
	fmt.Println(byID[2].Title)  // Ne pas confondre nil-map et map vide
}
```

---

## Escape analysis 

L'espace analysis, c'est à dire l'analyse du compilateur, décide si une valeur peut vivre localement dans la cadre de l'appel d'une fonction, ou est ce qu'elle doit vivre plus longtemps.

Escape analysis, ou analyse de fuite, est l'idée du compilateur: comprendre si une valeur s'échappe au dela des limites de l'appel de fonction courante.

Si une valeur ne s'échappe pas, on peut la placer de maniere à ce qu'elle vive le temps nécessaire, généralement sur la stack.

Si une valeur s'échappe, c'est à dire que l'on renvoie un pointeur vers elle, ou si on stocke ce pointeur quelque part à l'extérieur, le compilateur place les données de façon a ce qu'elles vivent plus longtemps. Elle ira sur la heap.

En Go, c'est le compilateur qui gère cette partie.

Pour Go, ce n'est pas quel moyen qui est utilisé pour obtenir l'adresse, mais ou cette adresse est allée et combien de temps elle sera nécessaire pour que le compilateur définisse ou cette variable sera placer.

### Stack 

Les données sur la stack vivent tant que la fonction s'exécute.

### Heap 

Le heap (tas) est une zone mémoire pour les données qui peuvent vitre plus longtemps que l'appel de fonction courantes. Si une fonction créer une valeur et retourne un pointeur vers elle, cette valeur doit exister après la fin de la fonction.

En Go, pour que les données ne s'accumulent pas sur la heap, le garbage collector (GC) s'en occupe. Il parcourt le graphe des objets présent sur la heap en partant des racines (globals, valeurs sur la stack, etc), puis supprime ce qui est devenu inaccessible. Cette idée est la vision de base du GC dans Go.

### `return x`est sur 

En Go, il est possible de retourner l'adresse d'une variable locale depuis une fonction.

Le compilateur voit que l'on retourne l'adresse vers l'exterieur. La variable doit donc vivre plus longtemps que l'appel de la fonction. Le compilitaeur organise alors le placement de sorte que le pointeur soit valide après le return.

```go 
func NewInt(v int) *int {
	x := v
	return &x
}

func main() {
	p := NewInt(10)
	fmt.Println(*p) // 10
}
```

--- 

### Ancrache 

Une valeur peut exister ou ne pas exister. Par exemple, un utilisateur ajoute un article à sa liste de course, et la quantité est facultative. `names[] string` contient les noms, et `qtys[] *int` contient la quantiité ou le slice contient des pointeur vers `int` et `nil` pour une valeur non spécifié.

```go 
import (
	"fmt"
	"strconv"
)

// la fontion créee une variable local v et retourne son adresse via le pointeur => la variable doit survivre à la fonction 
func parseOptionalInt(s string) (*int, error) {
	// la quantite n'es pas definis 
	if s == "-" {
		return nil, nil
	}

	v, err := strconv.Atoi(s)
	if err != nil {
		return nil, fmt.Errorf("bad number %q: %v", s, err)
	}

	return &v, nil // retourne le pointeur (l'adresse mémoire) de la variable local v 
}

// la fonction permet de realiser le formatage
func formatQty(q *int) string {
	// si la valeur est nul, on returne une string 
	// on protege contre un pointeur nil 
	if q == nil {
		return "(sans quantite)"
	}
	// sinon on retourne la valeur du l'adresse en mémoire => valeur de l'adresse du pointeur 
	return fmt.Sprintf("%d", *q)
}

// la foonction ajoute le nouvel item => les slices sont retourner 
func addItem(names []string, qtys []*int, name string, qty *int) ([]string, []*int) {
	// ajout du nom dans la slice 
	names = append(names, name)
	// ajout de la quantité 
	qtys = append(qtys, qty)
	// retourne les deux slices mis à jour 
	return names, qtys
}

// permet d'afficher les valeurs des slices 
func printItems(names []string, qtys []*int) {
	for i := range names {
		fmt.Printf("%d) %s %s\n", i+1, names[i], formatQty(qtys[i]))
		// exemple: "1) milk 2"
		// ou:     "2) bread (sans quantite)"
	}
}
```

--- 

## Pointeur dans un `range` 

On as un slice qui contient une liste de tâche. On souhaite trouver les tâches qui possède une sous chaine en particulier, et renvoyer des pointeurs vers ces chaines afin de les modiier directement dans la liste d'origine.

### `&v` dans le `range`

Dans la portée du `range`, la variable `v` est une variable de boucle, dans laquelle la valeur de l'élément est déposée à chaque itération. Cette valeur peut être une copie, et quand on prends `&v`, on prends l'adresse de cette varaible précise, et pas l'adresse de l'élément du tableau dans le slice. On obtient alors un lien vers cette copie, et pas vers la valeur dans le slice.

### Pointeurs identiques à cause d'une variable réutilisé

`v` est une variable avec une seule adresse. Dans la boucle, on vient réecrire la valeur en cours d'itération dessus. A la fin, de l'itération, la valeur `v` contient la dernière valeur itéré.

---

## Adresse d'un élément par indice `&tasks[i]`

On parcours les indices et on prends l'adresse du véritable élément dans le tableau du slice. 

```go 
func main() {
	tasks := []string{"buy milk", "learn go", "sleep"}

	ptrs := make([]*string, 0, len(tasks)) // slice qui contient des pointeurs
	for i := range tasks {
		ptrs = append(ptrs, &tasks[i]) // on ajoute les pointeurs dans le slice
	}

	*ptrs[1] = "LEARN GO (done)" // on modifie la valeur de l'adresse
	fmt.Println(tasks[1]) // LEARN GO (done)
}
```

On viens créer une fonction `FindTaskPtrs` qui cherche des taches par sous chaine, et retourne un pointeur vers les éléments du slice d'origine.

```go 
func FindTaskPtrs(tasks []string, query string) []*string {
	found := make([]*string, 0) // création du slice de pointeur 

	// on parcours la liste de tasks
	for i := range tasks {
		// on fait la recherche de sous chaine 
		if strings.Contains(tasks[i], query) {
			found = append(found, &tasks[i]) // on ajoute l'adresse dans le slice de poiteur 
		}
	}
	return found // on retourne le slice de pointeur 
}

func main() {
	tasks := []string{"buy milk", "learn go", "sleep", "go to gym"}

	ptrs := FindTaskPtrs(tasks, "go") // on cherche parmis les tasks celle contenant la sous chaine go 
	fmt.Println(len(ptrs)) // 2

	// on modifie la valeur des éléments du slice 
	*ptrs[0] = strings.ToUpper(*ptrs[0])
	*ptrs[1] = strings.ToUpper(*ptrs[1])

	fmt.Println(tasks) // [buy milk LEARN GO sleep GO TO GYM]
}
```