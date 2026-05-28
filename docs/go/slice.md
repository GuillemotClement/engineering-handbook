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

Lorsque l'on vient copier un tableau, tous les elements sont stocker, on obtient une copie independante.

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

Un slice est une `view` sur un `bakcing array`. Il contient un structure description le `slice header`
Celui ci se compose de trois elements qui decrive la structure :

- `pointer` : reference l'emplacement memoire du premier element du slice. Deux slice peuvent referencer le meme emplacement memoire
- `len` : nombre d'elements du slice accessible. `len()` retourne le nombre d'element du slice
- `cap` : definis la capacite memoire pour le slice. Cette reserve permet d'agrandir un slice sans realocation

### Creation de slice

Un slice peut etre vide mais pour deux raison : elle n'est pas encore initialiser ou elle existe mais contient `0` elements.
Ces deux etats ont une grosse influences sur le resultat des tests, des comparaison et la facon de lire le code.

| Etat du slice              | Implementation            | s == nil | len(s) | cap(s) | Affichage |
| -------------------------- | ------------------------- | -------- | ------ | ------ | --------- |
| slice `nil`                | `var s []int`             | `true`   | 0      | 0      | []        |
| empty slice                | `s := []int{}`            | `false`  | 0      | 0      | []        |
| empty avec reserve de capa | `s := make([]int, 0, 10)` | `false`  | 0      | 10     | []        |

- `slice nil` : le slice n'existe pas encore, et ne pointe vers aucun emplacement memoire.

```go
func main(){
  // slice avec valeur ==============================
  s := []int{1, 2, 3} // creation du slice
  s[0] = 99 // affectation

  len(s) // 3
  cap(s) // 3
  fmt.Println("s: ", s) // s: [99, 2, 3]

  // slice nil ======================================
  var nums []int // declaration du slice nil

  fmt.Prinln(nums == nil) // true
  fmt.Println(len(nums), cap(nums)) // 0 0
  fmt.Println(nums) // [] afficher mais valeur est nil

  // slice empty litteral
  tasks := []string{} // creation litteral du slice vide

  fmt.Println(tasks == nil) // false
  fmt.Println(len(tasks), cap(tasks)) // 0 0
  fmt.Println(tasks) // []

  // slice empty avec make
  tasks := make([]string, 0)

  fmt.Println(tasks == nil) // false
  fmt.Println(len(tasks), cap(tasks)) // 0 0
}
```

---

### Copie de slice

Lorsque l'on vient copier un slice, on vient faire la copie `slice header` et les elements restent inchange.

```go
func main(){
  t := []int{1, 2, 3}
  s := s
  t[0] = 99

  fmt.Println("t: ", t) // t: [99, 2, 3]
  fmt.Println("s: ", s) // s: [99, 2, 3]
}
```

---

### Utilisation avec des fonctions

Lorsque l'on transmet un slice a une fonction, c'est le `slice header` qui est transmis. Le slice sera modifier par la fonction

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

---

### Make

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
```

---

## Borne de slice

Il est possible de decouper une slice en indiquant l'index de depart, et l'index de fin. Le premier index indiquer est le depart, et le second c'est l'index de fin non inclut.
En cas de depassement de borne, on obtient une panic.
Pour que le decoupage soit valide : `0 <= a <= b <= cap(s)`. La borne superieur est comparer a `cap`. Le decoupage est une modification de la fenetre, et une fenetre peut en principe s'etendre jusqu'a la capaciter memoire allouer.

- `[:n]` : du debut jusqu'a la borne sup non inclut
- `[n:]` : a partir de la borne inferieur jusqu'a la fin
- `[:]` : creation d'une nouvelle fenetre

```go
func main(){
  s := []int{10, 20, 30, 40, 50}
  part := s[1:4] // recupere du deuxieme au quatrieme element
  fmt.Println(part) // [20 30 40]

  fmt.Println(s[:2]) // [10 20]
  fmt.Println(s[3:]) // [40 50]
  fmt.Println(s[:]) // [10 20 30 40 50]
}

/*
indices:   0   1   2   3   4
s:        10  20  30  40  50

s[1:4] => indices 1..3 => 20 30 40
*/
```

### Exemple

L'utilisateur saisit un intervale et la fonction retourne les elements demander.

```go
package main

import "fmt"

func main(){
  tasks := []string{"learn Go", "write code", "drink water", "sleep"}

  var from, to int
  fmt.Scan(&from, &to)

  a := from - 1 // definis la borne inferieur
  b := to

  // si les bornes sont fausse on retourne un slice vide pour eviter les erreurs
  if a < 0 || b < 0 || a > b || b > len(tasks) {
    fmt.Println([]string{}) // []
    return
  }

  fmt.Println(tasks[a:b])
}
```

---

## copy

`copy()` permet de creer une copie independante d'un slice. Tous les elements du slice copier sont copier dans la destination. Elle retourne le nombre d'elements copier.
Elle prends en premier argument la destination, et en second le slice a copier. La fonction ne copie pas la `len`. Pour que la copie se fasse correctement, la `len` de la destination doit contenir autant que celle d'origine.

```go
func main(){
  src := []int{10, 20, 30}
  dst := make([]int, len(src)) // len=3

  n := copy(dst, src)
  fmt.Println(n) // 3
  fmt.Println(dst) // [10 20 30]
}
```

`copy` peut creer une copie, mais egalement deplacer un morceau de slice. Elle fonctionne meme lorsque `dst` et `src` se chevauchent. On prends la queue et on la deplace a gauche ou vers la droite.

### Deplacement vers la gauche - rapprocher la queue

On as une liste de taches, et on souhaite retirer un element du milieu. On viens fermer le trou en deplacant vers la gauche.

```go
copy(s[i:], s[i+1:])
```

On copie les element a partir de `i+1` vers la position `i`

```go
func main(){
  s := []int{1, 2, 3, 4}
  copy(s[1:], s[2:])

  fmt.Println(s) // [1 3 4 4]
}
```

Le trous entre 2 et 3 est fermer, mais un doublon apparait. C'est a cause du `len` qui n'as pas ete modifier, on as simplement reecrit les elements.

### Deplacer vers la droite - ecarter la queue

Le deplacement vers la droite est necessaire pour inserer un element au milieu. On ne peut pas deplacer vers la droite s'il n'y a pas de place en longueur. La premiere etape est d'augmenter `len`. Pour cela, on utilise `append`

```go
func main(){
  s := []int{10, 20, 30}

  i := 1
  s = append(s, 0) // len+1 vers la droite
  copy(s[i+1], s[i:]) // queue vers la droite de 1
  s[i] = 99

  fmt.Println(s) // [10 99 20 30]
}
```

#### Exemple

On a une liste minimale de taches `[]string` et que l'on souhaite savoir faire :

- prendre un screenshot de la liste (pour afficher sans risque de modifier l'original)
- deplacer un bloc de tache

```go
package main

import "fmt"

// creer une copie independante du slice
func cloneStrings(src []string) []string {
  dst := make([]string, len(src))
  copy(dst, src)
  return dst
}

// deplace les element de 1 vers la gauche, en commencant a la position 1
func shiftLeftbyOne(s []string, i int){
  if i < 0 || i >= len(s)-1 {
    return
  }
  copy(s[i:], s[i+1:])
}

func main(){
  tasks := []string{"buy milk", "read book", "write code"}

  view := cloneStrings(tasks)
  view[0] = "buy coffee"

  fmt.Println("tasks:", tasks) // tasks: [buy milk read book write book]
  fmt.Println("view:", view) // view: [buy coffee read book write code]

  shiftLeftByOne(tasks, 1)
  fmt.Println("shift:", tasks) // shift: [buy milk write code write code]
}
```

`cloneString` est une fonction de protection. Elle retourne une copie vers l'exterieur, et le code externe ne peut pas endommager l'etat interne

---

## Clonage de slice

On alloue un nouveau buffer, on copie les elements et on travail avec.

```go
func cloneInts(src [int]) []int {
  dst := make([]int, len(src))
  copy(dst, src)
  return dst
}

func cloneStrings(src []string) []string {
  dst := make([]string, len(src))
  return dst
}

func main(){
  a := []int{1, 2, 3}
  b := cloneInts(a)

  b[0] = 99
  fmt.Prinln(a) // [1 2 3]
  fmt.Prinln(b) // [99 2 3]

  names := []string{"Ann", "Bob"}
  safe := cloneStrings(names)

  safe[1] = "Bobby"
  fmt.Println(names) // [Ann Bob]
  fmt.Prinln(safe) // [Ann bobby]
}
```

## Copie d'une partie de slice

On commence par choisir l'intervale puis on creer son propre buffer

```go
func clonePart(src []int, a, b int) []int {
  part := src[a:b]
  dst := make([]int, len(part))
  copy(dst, part)
  return dst
}

func main(){
  s := []int{10, 20, 30, 40}
  x := cloneParts(s, 1, 3) // [20 30]

  x[0] = 999
  fmt.Println(s) // [10 20 30 40]
  fmt.Println(x) // [999 30]
}
```

## strategie de copie

| Strategie         | syntaxe                            | garantis                                               | non garantis                        |
| ----------------- | ---------------------------------- | ------------------------------------------------------ | ----------------------------------- |
| Alias (view)      | `sub := s[a:b]`                    | rapide, sans allocation                                | modification visible des deux cotes |
| Limitation du cap | `sub := s[a:b:b]`                  | append ne deborde pas dans la fin du tableau d'origine | modification restent partager       |
| copie             | `dst := make(...); copy(dst, srd)` | independance                                           | allocation memoire + copie          |

---

## clear

`clear` permet d'ecrire la zero value dans les elements de la plage. Elle ne modifie pas le `cap` et la `len`.
En passant a la zero value, on supprime les references vers les donnes.

```go
func main(){
  // nettoyage du slice
  s := []int{10, 20, 30}
  clear(s)

  fmt.Prinln(s) // [0 0 0]
  fmt.Println(len(s)) // 3

  // nettoyage partiel
  s := []string{"A", "B", "C", "D"}
  clear(s[1:3])
  fmt.Prinln(s) // [A D]
}
```

### Supression d'un element

```go
func removeAtStable(s []string, i int) []string {
  if i < 0 || i >= len(s) {
    return s
  }

  copy(s[i:], s[i+1]) // decalage vers la gauche
  clear(s[len(s)-1 : len(s)]) // met a zero le dernier slot
  return s[:len(s)-1] // reduit len
}

func main(){
  tasks := []string{"eat", "sleep", "code", "repeat"}
  tasks := removeAtStable(tasks, 1)

  fmt.Println(tasks) // [eat code repeat]
}
```

### Suppression de plage

```go
func deleteRange(s []string, i. j int) []string{
  if i < 0 {
    i = 0
  }
  if j > len(s){
    j = len(s)
  }
  if i >= j {
    return s
  }

  oldLen := len(s)
  moved := copy(s[i:], s[j:])
  newLen := i + moved

  clear(s[newLen:oldLen]) // nettoie la partie liberer
  return s[:newLen]
}

func main(){
  s := []string{"A", "B", "C", "D", "E", "F"}
  s = deleteRange(s, 2, 5)

  fmt.Println(s) // [A B D]
}
```

revoir niveau 12 - 13 et retrvailler cette notes


---
## sort.Slice

`sort.Slice` permet de trier n'importe quel slice on lui fournissant une regle de comparaison sous forme de fonction.

Dans l'exemple, on passe en premier argument le slice, puis la function anonyme. On trie ensuite en utilisant l'index issue des slice

```go 
package main 

import (
  "fmt"
  "sort"
)

func main(){
  words := []string{"go", "gopher", "map", "slices"}

  sort.Slice(words, func(i, j int) bool {
    if len(words[i]) != len(words[j]) {
      return len(words[i]) < len(words[j])
    }
    return words[i] < words[j] // regle pour les cas egaux
  })

  fmt.Prinln(words) // [go map gopher slices]
}
```

---

## Package slices 

Le package contient des fonction pratique sur les slices comme le tri.

### slices.Sort 

Permet de trier des chaine et des nombres directement et ne retourne rien. Le slice est directement modifier.

```go 
import (
  "fmt"
  "slices"
)

func main(){
  nums := []int{5, 2, 10, 2}
  slices.Sort(nums) // triage 

  fmt.Println(nums) // [2 2 5 10]
}
```

---

### slices.SortFunc 

Tri avec comparateur. `slices.SortFunc()` demande une fonction de comparaison d'element `cmp(a, b T) int`.

- si `a` doit venir avant `b` on retourne un nombre negatif 
- si apres retourne un positif
- si les elements sont egaux du point de vue du trie, retourne 0

```go 
package main 

import (
  "fmt"
  "slices"
)

func main(){
  words := []string{"go", "gopher", "map", "slices"}

  slices.SortFunc(words, func(a, b string) int {
    if len(a) != len(b) {
      return len(a) - len(b)
    }
    if a < b {
      return -1
    }
    if a > b {
      return 1
    }
    return 0
  })

  fmt.Println(words) // [go map gopher slices]
}
```