# Dictionnaire

`map` est une structure de donnee qui stocke les valeurs sous forme de cle/valeur. On accede au valeur par la cle.

Un map s'ecrit sous la forme `map[K]V`, ou `K` est le type de cle et `V` le type de la valeur.

```go
map[int]string // la cle est une string, et la valeur un int
```

Il existe une contrainte important: les cle doivent etre comparable avec `==` et `!=`. On peut utiliser des `string`, `int` et `bool` pour le type des cle d'un map.
Il est egalement possible d'utiliser un `struct` si tous les champs sont comparable. On l'utilise quand on a deja les donnes initiale.

---

## Creation d'un map

Il existe deux maniere :

- `make(map[K]V)` : permet de creer une carte vivante prete a recevoir des ecriture.
- `var m map[K]V` : creer une variable de type `map` mais sa valeur par defaut est `nil`. On l'utilise generalement dans les cas ou l'on dispose des donnees (donnees de test, config de depart)

```go
func main(){
  // make => le plus courante ==============================
  phones := make(map[string]string) // initialiser via le make

  phones["alice"] = "+1-202-555-0101" // ajout d'une valeur
  fmt.Println(phones["alice"]) // affichage

  // creation litteral =====================================
  phones := map[string]string{
    "alice": "+1-202-555-0101",
    "bob": "+1-202-555-0110",
  }

  fmt.Println(phones["bob"]) // +1-202-555-0110
}
```

---

## Lecture et ecriture de valeur

Lorsqu'une cle n'existe pas dans un map, on obtient la zero value du type. Elle ne provoque pas d'erreur.

Le fait de cherhcer une valeur dans un map retourne la valeur et un bool. 

```go
func main(){
  phones := make(map[string]string)

  phones["alice"] = "111"
  phones["alice"] = "222" // reecriture

  if _, ok := phones["alice"]; !ok {
    return fmt.errors("not found")
  }
}
```



---

## Passage au fonction

Si on passe un `map` a une fonction, et que l'on modifie les valeurs, les changements sont visible en dehors de la fonction.

```go
// permet de modifier la valeur d'une cle
func setPhone(m map[string]string, name string, phone string){
  m[name] = phone
}

func main(){
  phones := make(map[string]string)

  setPhone(phones, "bob", "333")
  fmt.Println(phones["bob"]) // 333
}
```

Pour securiser l'usage des map dans des fonction contre les map nil, on ajoute une etape de protection

```go
func safeSet(m map[string]string, k, v string) map[string]string {
  if m == nil {
    m = make(map[string]string)
  }
  m[k] = v
  return m
}

func main(){
  var phones map[string]string

  phones = sageSet(phones, "alice", "111")
}
```

---

## len(m)

Retourne le nombre de paire cle/valeur presente dans le map.

```go
func main(){
  phones := map[string]string{"alice" : "111", "bob": "222"}

  fmt.Println(len(phones)) // 2
  phones["carol"] = "333"
  fmt.Println(len(phones)) // 3
}
```

---

## delete(m, k)

La fonction `delete()` permet de supprimer une paire cle/valeur d'un `map`.
Si la cle n'existe pas, la suppression ne fait rien, et ne provoque pas d'erreur.

```go
func main(){
  phones := map[string]string{"alice" : "111", "bob": "222"}

  delete(phones, "bob")

  fmt.Println(len(phones))   // 1
	fmt.Println(phones["bob"]) // "" (la cle n'existe pas, zero value renvoyee)
}
```

---

## nil-map

Il est possible de lire dans un `nil-map` mais pas ecrire. La lecture donne la zero value, `len(nilMap)` retourne `0`.
Le parcours avec `range` effectuera `0` iterations.

En revanche, l'ecriture provoque une panic.

```go
func main(){
  var phones map[string]string // nil-map

  fmt.Prinln(phones == nil) // true
  fmt.Println(len(phones)) // 0
  fmt.Println(phones["x"]) // "" -> zero value

  phones["x"] = "123" // panic => map non initialiser
}
```

---

## Gestion zero value

Lorsqu'un cherche une clé qui n'existe pas dans le `map`, retourne retourne une zero value.

Pour confirmer qu'une valeur n'existe pas dans le `map`, Go fournis une manière étendue de lire un `map`

```go
v, ok := m[k]
```

- `v` : valeur
- `ok` : boolean; si `true` alors la valeur existe dans le map

```go
func main(){
  m := map[string]int{"a":0}

  v1, ok1 := ["a"]
  v2, ok2 := ["b"]

  fmt.Prinln(v1, ok1) // 0 true
  fmt.Println(v2, ok2) // 0 false
}
```

On utilise generalement une syntaxe courte pour gerer ce type de pattern

```go
func main(){
  price := map[string]int{"apple": 100}

  if v, ok := price["banane"]; ok {
    fmt.Println("banana price:", v)
  } else {
    fmt.Prinln("no price for banana")
  }
}
```

On peut mettre en pratique dans une partie d'une application. L'utilise vient saisir la commande et l'id d'une task.

```go
func main(){
  done := main[string]bool{"t1": true, "t2": false}

  var cmd string
  var id string
  fmt.Scan(&cmd, &id)

  if cmd == "status" {
    if v, ok := done[id]; ok {
      fmt.Println("done:", v)
    } else {
      fmt.Println("unknown task")
    }
  }
}
```

---

## Iteration sur map

L'ordre des valeurs n'est pas garantis avec lors d'une iteration sur un map. A chaque iteration, on obtient la cle en premiere valeur, et la valeur associer retourner par le `range`.

```go
func main(){
  ages := map[string]int{"alice:": 30, "bob": 25}

  // name correspond a la cle du map
  // age correspond a la valeur en cours d'iteration
  for name, age := range ages {
    fmt.Println(name, age)
  }
}
```

Dans le cas ou l'on tente d'iteration un map `nil`, l'iteration ne provoque pas d'erreur. Elle ne se lance pas.

Pour modifier les valeurs dans une iteration, on utilise ce pattern

```go
func main(){
  counts := map[string]int{"go": 1}

  for k, v := range counts {
    counts[k] = v + 1
  }
}
```

---

## Sortie stable

Pour obtenir une iteration avec un ordre controler pour un `map`, il est necessaire d'utiliser un slice.

### sort.Strings()

Permet de trier le slice de `string`

```go
// triage par chaine
package main

import (
  "fmt"
  "sort"
)

func main(){
  counts := map[string]int{"go" : 3, "map": 1, "sort": 2} // declaration du map

  keys := make([]string, 0, len(counts)) // creation d'un slice. On fait une prealoc sur la longeur du map
  for k := range counts {
    keys = append(keys, k) // ajout dans le slice de la cle du map
  }
  sort.Strings(keys) // triage des cle par ordre alphabetique

  // on parcours le slice pour obtenir les cle du map trier
  for _, k := range keys {
    fmt.Println(k, counts[k]) // go 3 / map 1 / sort 2 => sortie toujours identique
  }
}
```

### sort.Ints()

Permet de trier le slice sur les `int`

```go
// triage sur int
package main

import (
  "fmt"
  "sort"
)

func main(){
  m := map[int]string{10 : "ten", 2: "two", 7: "seven"} // declaration du map

  keys := make([]int, 0, len(counts)) // creation d'un slice. On fait une prealoc sur la longeur du map
  for k := range m {
    keys = append(keys, k) // ajout dans le slice des cle
  }
  sort.Ints(keys) // triage asc

  // on parcours le slice pour obtenir les cle du map trier
  for _, k := range keys {
    fmt.Println(k, m[k]) // 2 two / 7 seven / 10 ten
  }
}
```

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

On peut utiliser cette fonction pour venir trier un map selon les valeurs. Dans ce exemple, on souhaite afficher en premier le mot le plus frequent.

```go
package main

import (
  "fmt"
  "sort"
)

func main(){
  counts := map[string]int{"go":3, "map":1, "sort": 3} // creation du map

  keys := make([]string, 0, len(counts)) // slice pour stocker les index
  for k := range counts {
    keys = append(keys, k)
  }

  sort.Slice(keys, func(i, j int) bool {
    wi, wj := keys[i], keys[j] // on affecte les index i et i+1
    if counts[wi] != counts[wj] {
      return counts[wi] > counts[wj] // plus grand = plus tot
    }
    return wi < wj // frequence ordonne par ordre alphabetique
  })

  for _, k := range keys {
    fmt.Println(k, counts[k])
  }
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

---

### Lecture de mots et comptage

```go
package main

import "fmt"

func main(){
  counts := make(map[string]int)

  for {
    var w string
    _, err := fmt.Scan(&w)
    if err != nil {
      break
    }
    counts[w]++
  }

  fmt.Println("unique:", len(counts)) // unique: 3
}
```

---

### Sortie stable par ordre alphabetique (sort.Strings)

```go
package main

import (
  "fmt"
  "sort"
)

func printAlpha(counts map[string]int){
  keys := make([]string, 0, len(counts)) // creer le slice pour sotcker les cle

  // parcours le map pour passer les cle dans le slice
  for k := range counts {
    keys = append(keys, k)
  }

  // triage du slice
  sort.Strings(keys)

  // affichage des valeur du map en itereant le slice et afficher les valeurs par index
  for _, k := range keys {
    fmt.Println(k, counts[k])
  }
}
```

---

### Sortie stable par frequence (sort.Slice)

```go
package main

import (
  "fmt"
  "sort"
)

func printByCount(counts map[string]int) {
  // stocke les cle du map
  keys := make([]string, 0, len(counts))
  for k := range counts {
    keys = append(keys, k)
  }

  sort.Slice(keys, func(i, j int) bool {
    a, b := keys[i], keys[j]
    if counts[a] != counts[b] {
      return counts[a] > counts[b]
    }
    return a < b
  })

  for _, k := range keys {
    fmt.Println(k, counts[k])
  }
}
```

---
## Pattern de map 

| Tache | Ce qui est stocker | Type | Verification |
| ----- | ------------------ | ---- | ------------ |
| Stocker la presence et l'absence d'une valeur | presence ou absence | `map[string]struct{}` | `_, ok := set[x]` |
| Frequence et compteur | nombre de repetition | `map[string]int` | `counts[x]` ou `v, ok := counts[x]` | 

### Operation sur ensemble => map[T]struct{}

Un ensemble permet de stocker "un fait" => l'element est present ou non. On peut alors utiliser un type qui ne stocke rien : `struct{}`.On l'utilise souvent comme marqueur.

Ici `struct{}` est une valeur vide. On l'ajoute seulement parce que `map` exige une valeur.

Lorsque l'on rencontre `map[string]struct{}` dans le code, il en faut pas essayer de chercher ce qui est stocker. On y stocke rien.
Il faut penser cela comme une table present/absent. Les operations sur un ensemble se lisent comme en anglais : ajouter, verifier, supprimer, compter.

```go
func main(){
  // creation de l'ensemble 
  tags := make(map[string]struct{})

  tags["urgent"] = struct{}{} // add 
  _, ok := tags["urgent"] // check si present 
  delete(tags, "urgent") // suppression 
  fmt.Prinln(ok, len(tags)) // compter => true 0
}
```

---

### Conpteur et frequence => map[string]int 

Le deuxieme pattern de map est le compteur. On le retrouve dans les probleme d'algo, le traitement des logs, l'analytique, l'import de donnees, les programmes CLI.
Lorsque la cle n'existe pas, la valeur retourner est zero.

```go 
func main(){
  words := []string{"go", "map", "go"}

  counts := make(map[string]int) // init pattern compteur 
  
  // parcours le slice et valeur dans le map lorsque la cle est trouver
  for _, w := range words {
    counts[w]++
  }

  fmt.Println(counts["go"]) // 2 => present 2 fois dans le slice 
  fmt.Println(counts["java"]) // 0 => non present dans le slice 
}
```

Il existe des situations ou `0` est une vrai valeur. Par exemple, on stocke un solde actuel de bonus et peut devenir nul apres deduction, ou alors on stocke un nombre d'essaie disponible et `0` signifie la cle est presente mais il n'y a plus d'essaies. 
Dans ce cas, on souhaite distinguer l'absence de cle.

```go 
func main(){
  counts := map[string]int{"go": 0} // init pattern compteur 

  v1, ok1 := counts["go"] // on recupere si la valeur est presente et sa valeur
  v2, ok2 := counts["java"]

  fmt.Println(v1, ok1) // 0 true 
  fmt.Println(v2, ok2) // 0 false
}
```

