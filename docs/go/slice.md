# Tableau et slice 

## Tableau

Le tableau permet de stocker des valeurs. Il possede une taille fixe, et les valeurs doivent etre de meme type. Chaque valeur est stocker a un index. La taille du tableau fait partie de son type.
Il se declare sous la forme `[n]T` ou `n` est le nombre d'element que stocke le tableau, et `T` le type de ses valeurs.

### Creation de tableau 

```go 
package main 

import "fmt"

func main(){
  week := [3]int{10, 20, 30} // creation et initialisation du tableau 
  week[1] = 99 // on set une valeur au deuxieme emplacement 

  fmt.Println(// [10, 99, 30])
}
```

### Copie de tableau 

Lorsque l'on vient copier un tableau, tous les elements sont sotcker, on obtient une copie independante.

```go 
func main(){
  a := [3]int{1, 2, 3}
  b := a 
  b[0] = 99 

  fmt.Println("a: ", a) // a: [1, 2, 3]
  fmt.Println("b: ", b) // b: [99, 2, 3]
}
```

---

## Slice 

Le slice est un tableau sans longueur fixe. C'est l'outil principale pour utiliser une liste. Il fonctionne comme un tableau mais avec une taille flexible.
Un slice est une vue partielle d'un tableau, et differents slice peuvent regarder les meme donnees.

### Creation de slice 

Un slice est creer avec `[]T{...}`, ou `T` est le type des elements du slice.

```go
func main(){
  s := []int{1, 2, 3} // creation du slice 
  s[0] = 99 // affectation 

  fmt.Println("s: ", s) // s: [99, 2, 3]
}
```

### Copie de slice 

Lorsque l'on vient copier un slice, on vient faire la copie de la desriptiion du slice et les elements restent inchange. On copie le slice header.

```go 
func main(){
  t := []int{1, 2, 3}
  s := s 
  t[0] = 99 

  fmt.Println("t: ", t) // t: [99, 2, 3]
  fmt.Println("s: ", s) // s: [99, 2, 3]
}
```

Lorque l'on fait une copie de slice, on fait juste une copie de ce slice header, ce qui est beaucoup moins lourds que de faire une copie de tableau, ou dans ce cas, on copie les elements du tableau.

---

## Passage a une fonction 

En go tout est transmis par valeur. Cela signfie qu'au moment de l'appel, Go copie la valeur de l'argument dans le parametre.

Pour un tableau, la valeur ce sont tous les elements.
Pour un slice, la valeur est une structure de description qui reference les elements stocker ailleurs.

```go
// la fonction prends un tableau comment argument 
func setFirstArray(a [3]int){ 
  a[0] = 100
}

// la fonction prends en slice comme argument 
func setFirstSlice(s []int){
  s[0] = 100
}

func main(){
  a := [3]int{1, 2, 3} // declaration d'un tableau 
  s := []int{1, 2, 3} // declaration du slice 

  setFirstArray(a)
  setFirstSlice(s) 

  fmt.Println("array:", a) // array: [1, 2, 3]
  fmt.Println("slice: ", s) // slice: [100, 2, 3]
}
```

Avec le tableau, `setFirstArray()` recoit une copie du tableau, et modifie la copie. L'original n'est pas copier.
Avec le slice, `setFirstSlice()`, c'est une copie de la description du slice qui est recu, et cette description pointe vers les elements du slice. La fonction modifie le slice.

### Exemple - analyseur de depense 

Une mini application qui analyse les depenses. On souhaite stocker les depenses par jours et calculer la somme. Il y a deux scenario :

- premier scenario: on calcule les depenses pour 7 jours. On utilise un tableau `[7]int` : la taille est fixe, les depenses sont toujours complete
- deuxieme scenario : on calcule les depenses pour un nombre arbritaire de jours. On utilise alors un slice.

```go 
package main

import "fmt"

// scenario 1 - utilisation avec tableau 
func sumWeek(week [7]int) int {
  total := 0
  // on parcourt le tableau 
  for _, v := range week {
    total += v // on calcul la somme pour tous les jours de la semaine 
  }
  return total
}

// scenario 2 - utilisation avec slice 
func sumDays(days []int) int {
  total := 0
  for _, v := range days {
    total += v
  }
  return total
}

func main(){
  // le tableau stocke les depenses par jours (7) pour une semaine
  week := [7]int{100, 0, 50, 20, 10, 0, 70}
  fmt.Println(sumWeek(week)) // 250

  // le slice contient les depenses par jours pour un nombre variable de jours 
  days := []int{100, 0, 50, 20}
  fmt.Println(sumDays(days)) // 170
}
```

--- 

## Comparaison 

- les tableaux peuvent etre comparer avec `==` si tous les elemnts sont comparable
- les slice ne peuvent pas etre comparer avec `==` sauf pour une valeur `nil`

```go 
func main(){
  // comparaison de tableau 
  a := [2]int{1, 2}
  b := [2]int{1, 2}
  fmt.Println(a == b) // true 

  // comparaison de slice 
  s := []int{1, 2}
  _ = s
  fmt.Println(s == s) // erreur de compilation
}
```

---

## Slice header
 
Un slice ne contient pas les elements eux meme, mais une structure descriptive qui indique ou se trouve les elements, et combien sont disponible. Ce modele est le `slice header`. Cette description comporte trois parties `pointer`,  `len`, `cap`

### pointer - reference du slice 

`pointer` permet de referencer l'emplacement memoire du premier element du slice, c'est a dire l'adresse du debut des donnees. Ainsi, deux valeurs slice differentes peuvent referencer un meme ensemble d'elements.

```go 
func main(){
  s := []int{1, 2, 3}
  t := s // copie du slice header 
  t[0] = 99 // modifie la valeurs dans les deux slices

  fmt.Println(s) // [99, 2, 3]
  fmt.Println(t) // [99, 2, 3]
}
```

Lorsque l'on fait une copie de slice `t := s`, on copie le slice header (`pointer / len / cap`). Le pointer de `t` et celui de `s` pointe sur le meme tableau sous jacent (`backing array`). La modification de l'un affecte egalement l'autre.



### len - longueur visible et limites d'indexation 

`len(s)` permet de connaitre combien d'element du slice sont actuellement accessible a travers cete fenetre (le slice). Ce nombre definis combien on peut lire/ecrire en toute securite par indice.
Un depacement de limite de slice en Go provoque une panic.

```go 
func main(){
  s := []int{10, 20, 30}
  fmt.Println(len(s)) // 3

  fmt.Println(s[0]) // 10
  fmt.Prinln(s[2]) // 30
  fmt.Println(s[3]) // panic: index out of range
} 
```

Dans un scenario ou l'on connait a l'avance le nombre d'elements que le slice viens stocker, on peut fixer la `len` a la creation du slice.

```go 
func main(){
  var n int 
  fmt.Scan(%n)

  nums := make([]int, n) // on fixe la longueur du slice 
  for i := 0; i < len(nums); i++ {
    fmt.Scan(&nums[i]) // on ecris la valeur dans le slice 
  }

  fmt.Println(nums) // [5 10 15]
}
```




### cap - capacite et reserve 

`cap(s)` definis combien d'elements peuvent tenir dans ce segment memoire, a partir du debut du slice, sans deplacement ailleurs. 
C'est une "reserve" qui permet d'agrandir un slice. Le `cap` ne permet pas d'acceder au indice, il gere uniquement le mecanisme interne et les performances.

```go 
func main(){
  // make permet de creer un slice et de definir le slice header
  s := make([]int, 2, 5) // len=2 cap=5
  fmt.Println(s) // [0, 0]
}
```

Dans ce code : 

- `len = 2` signifie que deux elements sont disponibles, tous deux valent pour l'instant `0`
- `cap=5` : signifie que jusqu'a 5 elements peuvent tenir dans ce bloc memoire 

### Affichage de l'etat du slice 

Il est possible d'afficher les valeurs de slice header pour du debugage.

```go
func printSliceState(name string, s []int){
  fmt.Println("%s: len=%d cap=%d data=%v\n", name, len(s), cap(s), s)
}

func main(){
  a := make([]int, 2, 5)
  printSliceState("a", a) // a: len=2 cap=5 data=[0 0]
}
```

---

## Slice nil et empty slice  

Une liste peut etre vide mais pour deux raison : elle n'est pas encore initialiser ou elle existe mais contient `0` elements. 
Ces deux etats ont une grosse influences sur le resultat des tests, des comparaison et la facon de lire le code.

| Etat du slice | Implementation | s == nil | len(s) | cap(s) | Affichage |
| ---           | ---            | ---      | ---    | ---    | --- |
| slice `nil` | `var s []int` | `true` | 0 | 0 | [] |
| empty slice | `s := []int{}` | `false` | 0 | 0 | [] |
| empty avec reserve de capa | `s := make([]int, 0, 10)` | `false` | 0 | 10 | [] |


## Slice nil 

Pour un slice, la zero value est `nil`. Elle signfie que le slice n'existe pas encore, il ne pointe vers aucune adresse memoire.
On retrouve cette forme lors d'une declaration ordinaire :

```go
func main(){
  var nums []int // declaration du slice 

  fmt.Prinln(nums == nil) // true 
  fmt.Println(len(nums), cap(nums)) // 0 0
  fmt.Println(nums) // [] afficher mais valeur est nil
}
```

---

## Make 

`make` permet de creer des conteneurs pret a l'emplois : slices, maps, canaux.
L'utilise du `make` permet de specifier la forme du slice en fournissant la longueur et la capa

```go 
// syntaxe de make
make([]T, n) // longueur n; les elements existe et sont remplis par la zero value 
make([]T, n, cap) // longueur n et capacite cap (cap >= n)

tasks := make([]string, 0) // creer un slice de string de longeur 0
```

Ce code vient creet un nouveau slice de string de longeur 0. Il n'y a pas encore d'element mais le slice existe et peut etre utiliser.

```go 
func main(){
  // creation avec value zero 
  a := make([]int, 3) // creation du slice de int de 3 elements avec la zero value
  fmt.Println(a, len(a), cap(a)) // [0 0 0] len=3 cap=3

  // creation slice vide 
  b := make([]int, 0, 3)
  fmt.Println(b, len(b), cap(b)) // [] len=0 cap=3 
}
```
---

## Slice vide 

Un slice vide est un slice ou `len` == 0 mais qui n'est pas egale a `nil`. Le slice existe et contient zero element. Il existe deux maniere d'obtenir un slice vide 

### litteral []T{}

```go 
func main(){
  tasks := []string{} // creation litteral du slice vide 

  fmt.Println(tasks == nil) // false
  fmt.Println(len(tasks), cap(tasks)) // 0 0 
  fmt.Println(tasks) // []
}
```

### make([]T, 0)

```go 
func main(){
  tasks := make([]string, 0) 

  fmt.Println(tasks == nil) // false
  fmt.Println(len(tasks), cap(tasks)) // 0 0 
}
```

---

## Tests 

Lorsque l'on test des slice, il est important de savoir ce que l'on souhaite savoir. 

### Tester la longueur du slice 

Pour savoir coment d'elements sont stocker dans le slice, il faut utiliser `len` 

```go 
if len(tasks) == 0 {
  fmt.Println("Il n'y a pas encore de tasks")
}
```

### Tester si un slice est initialiser 

Pour verifier si un slice est initialiser, on utilise `tasks == nil`. 

```go 
func main(){
  var a []int // nil 
  b := []int{} // empty 

  fmt.Println(len(a) == 0, a == nil) // true true 
  fmt.Println(len(b) == 0, b == nil) // true false
}
```

### Utilisation du s == nil 

On souhaite une fonction qui collecte des donnees de maniere optionnelle. S'il n'y a pas de donnees, on souhaite retourner un `nil` pour montrer que la valeur est absente. Mais si l'utilisateur a activer l'option mais qu'il n'y a pas de resultat, alors on veut retourner un empty pour montrer que la valeur existe mais qu'elle est vide.

```go 
package main 

import "fmt"

func buildHints(enabled bool) []string {
  if !enabled {
    return nil // indices desactiver => le slice est non initaliser
  }
  return []string{} // slice initialiser et empty 
}

func main(){
  a := buildHints(false)
  b := buildHints(true)

  fmt.Println(a == nil, len(a)) // true 0 
  fmt.Println(b == nil, len(b)) // false 0
}
```

## Initialisation propre 

Lorsque l'on construit une liste avec `append` et qu'il n'y a pas d'exigence de preallocation, alors `var s []T` est adapter. C'est court, lisible et cela n'oblige pas a penser trop tot a `make` 

Si on souhaite montrer que c'est bien un collection meme si vide, alors `s := []T{}` est une bonne option. On rencontre souvent cette syntaxe lorsque l'on retourne ue valeur depuios une fonction et que l'on souhaite garantir que l'appellant recevras quelque chose de non-nil 

Si on sait a l'avance que l'on va ajouter `n` elements, et que l'on ne souhaite pas que `append` agrandisse le tampon interne, alors `make([]T, 0, n)` est adapter.

```go 
// construction d'une liste avec append et pas d'exigencde de preallocation 
var s []T 

// liste vide 
s := []T{}

// liste avec preallocation fixe pour eviter l'agrandissement de zone memoire 
s := make([]T, 0, n)
```

## Utilisation d'un nil slice 

Go est conciliant avec un slice `nil`. On peut l'utiliser sans danger dans de nombreux cas.

### Utilisation dans une boucle 

```go
func main(){
  var nums []int // nil 

  sum := 0 
  for _, v := range nums {
    sum := v
  }

  fmt.Println(sum) // 0
}
```

Puis `len(nilSlice) == 0`, la boucle ne s'execute pas. 

### Indexation s[0] n'est pas sur quand len(s)==0

L'indexation ne fonctionne que dans l'intervalle `0 .. len(s) - 1`. Par consequent, un `nil` comme un empty sont dangereux pour `s[0]` si la longueur est null

```go 
func main(){
  var nums []int // nil 

  if len(nums) > 0 {
    fmt.Println(nums[0])
  } else {
    fmt.Println("Il n'y a pas d'element") 
  }
}
```

---

## Exemple - liste de tache 

Un programme console qui lit des taches et les affiches. Pour le stockage des tasks, il est logique d'utiliser des slices.

On implemente une fonction qui affiche les taches. Elle doit fonctionnement aussi bien opur un `nil` que pour un `empty`

```go 
package main 

import "fmt"

func printTasks(tasks []string) {
  // protection contre un slice vide
  if len(tasks) == 0 {
    fmt.Println("la liste de taches est nul")
  }

  // parcours le slice et affichage
  for i, t := range tasks {
    fmt.Printf("%d %s\n", i+1, t) // i+1 pour afficher l'emplacement de la task dans la liste = index debut a 0 pour emplace 1
  }
}

func main(){
  var tasks []string // nil 
  printTasks(tasks) // la liste de taches est nul

  tasks = append(tasks, "Rendre la lecon") // ajoute une tasks dans la liste 
  printTasks(tasks) // 1 Rendre la lecon
}
```
--- 

# append 

L'ajout des elements a un slice en Go n'est pas magique. Un slice est definit par un nombre d'element qui existent maintenant `len` et une reserve d'espace pour l'avenir `cap`. Ajouter un element n'est pas simplement une affectation par indice. Parfois il y a assez de place, et parfois non et un demenagement vers une memoire plus grand est necessaire.

La fonction `append` permet d'ajouter a la fin d'un slice, et si la `cap` n'est pas suffisante, agrandis le stockage et retourne le nouveau slice mis a jour. **Le resultat d'un append doit toujours etre conserve**. L'ajout d'un element peut potentiellement changer l'adresse memoire du slice.


```go 
func main(){
  s := []int{1, 2}
  s = append(s, 3) // on ajoute au slice "s" la valeur 3 
  fmt.Println(s) // [1 2 3]
}
```

La fonction viens ecrire dans le backing array du slice et alloue un nouveau backing array si la `cap` n'est pas suffisante. Dans les deux cas, la fonction retourne un nouveau slice header mis a jour. 
Le fait de ne pas utiliser le retour de append peut provoquer des probleme et des comportement innatendu car on ne travail plus avec le bon slice header contenant l'etat actuel.

```go 
// anti pattern
func addOne(s []int){
  append(s, 999) // resultat non retourner et ignorer 
}

func main(){
  s := []int{1, 2}
  addOne(s)

  fmt.Prinln(s) // [1 2] => resultat de la fonction ignorer car non retourner
}

// bon pattern
// on retourne toujours le nouveau slice header retourner par append 
func addOne(s []int) []int {
  s = append[s, 999]
  return s
}

func main(){
  s := []int{1, 2}
  s = addOne(s)

  fmt.Prinln(s) // [1 2 999]
}
```

### Croissance du slice et reallocation 

Lorsque l'on ajoute des elements au slice, `len` grandit de facon previsible : on ajoute un element, la longueur augmente de 1.
Avec `cap`, Go essaie d'allouer la memoire de sorte que les futurs `append` soient moins couteux, la capacite augmenet souvent par bond.

Lorsque la capacite manque, Go alloue un nouveau tableau plus grand et y copie les elements. Si on as un autre slice qui pointe vers l'ancien backing array, apres reallocation, celui ci garde la reference vers l'ancien.

### append et slice nil 

En Go, un slice `nil` est un etat initial correct pour accumuler des donnees. `append` sait demarrer a partir d'un `nil`, il allouera le backing array lui meme 

```go 
func main(){
  var s []int // nil 

  s = append(s, 10)
  s = append(s, 20)

  fmt.Println(s, s == nil) // [10 20] false
}
```

### Exemple: liste de tache 

On creer une "memoire" pour stocker les taches et ajouter de nouveaux elements

```go 
package main 

import "fmt"

// fonction responsable de l'ajout de tasks
func addTasks(tasks []string title string) []string{
  tasks = append(tasks, title)
  return tasks
}

func main(){
  var tasks []string // nil-slice => depart normal 

  tasks = addTasks(tasks, "Faire des exo") // ajout d'une nouvelle task
  tasks = addTasks(tasks, "Apprendre append") // ajout d'une autre task

  fmt.Println(tasks) // [ "faire des exo" "apprendre append"]
}
```

---

## Preallocation de slice

La preallocation de la `cap` du slice permet d'optimiser `append` dans une boucle pour eviter des copies inutiles ou d'allocations memoire inutiles.
Cette approche est realisable lorsque l'on connait a l'avance le nombre d'elements qui seront ajouter dans le slice. Cela permet de rendre le code plus previsible et moins couteux.

```go 
func main(){
  a := make([]int, 5) // pas de prealloc, on remplis avec 5 0 
  b := make([]int, 0, 5) // preallocation on fixe la capa a 5 elements

  fmt.Println(a, len(a), cap(a)) // [0 0 0 0 0] 5 5 
  fmt.Println(b, len(b), cap(b)) // [] 0 5 -> slice empty
}
```

On peut realiser une petite application de gestion de taches 

```go 
package main 

import (
  "bufio"
  "fmt"
  "os"
)

func main(){
  in := bufio.NewReader(os.Stdin)

  var n int 
  fmt.Scan(in, &n)

  tasks := make([]string, 0, n) // preallocation du slice 
  for i := 0; i < n; i++ {
    var t string 
    fmt.Fscan(in, &t)
    tasks = append(tasks, i)
  }

  fmt.Println(tasks) // [buy_code sleep repeat]
}

