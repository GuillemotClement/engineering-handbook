# CONSTANTE

Une constante désigne une valeur qui est définie une fois et qui ne change jamais. Elle doit etre connu au moment de la compilation.

Une constante est utiliser pour definir des regles et des valeurs fixes, par exemple pour des limites fixes, et des configuration.

## Declaration des constantes

```go
const minAge = 18 // déclaration de la constante

if age >= minAge {
  fmt.Println("welcome")
}
```

---

## Declaration groupee

En Go, on declare souvent les constantes par groupe. Chaque ligne du bloc est une constante ordinaire.

```go
const (
	minAge = 18
	maxUsers = 100
	appName = "DoorGuard"
)
```

---

## Constante untyped

Une constante `untyped` est une constante sans type fixe. Elle peut s'adapter au contexte.

Une fois declarer, le type de la constante peut s'adapter selon le contexte dans laquelle elle est utiliser.

```go
func main(){
  const n = 5 // constante untyped 

  var a int = n
  var b int64 = n

  fmt.Println("a=%v type=%T", a, a) // a=5 type=int
  fmt.Println("b=%v type=%T", b, b) // b=5 type=int64
}
```

Ici, c'est la variable qui recoit le type (`a`, `b`). La constante se "glisse" dans le type requis, si la valeur est representable par le type definis sur la variable qui recoit la valeur de la constante.

### Default type 

Pour les constante `untyped`, il existe un `default type` qui est utiliser lorsqu'aucune information de type est disponible.
Dans ce cas, c'est la constante `untyped` qui suggere le type de la variable.

| type constante | exemple | type par defaut |
| --- | --- | --- | 
| entier | `const x = 10` | `int` |
| float | `const x = 1.5` | `float64` |
| string | `const s = "go" | `string` |
| boolean | `const b = true` | `bool` |
| rune | `const r = 'A' | `rune` |

---

## Constante typed

Les constante `typed` on un type explicite. Elle obeit au meme regles que les variable ordinaire. Si on souhaite l'utiliser dans un type, il faudras la convertir vers celui ci.

```go 
func main(){
  const m int = 5 // constante typed

  var b int64 = int64(m) // conver sion necessaire
}
```

---

## Constante nommee pour un type personnalise 

Lorsque des valeurs magique apparaissent dans le code, il faut mettre en place un systeme pour que le code reste lisible.

On peut utiliser les `const` pour definir des noms a ces valeurs. 

```go 
package main 

import (
  "fmt"
)

func main(){
  type Status int // creation du type personnalise

  // declaration des constantes pour representer les valeurs
  // les constante sont declarer avec un type Status
  const (
    StatusNew         Status = 0
    StatusInProgress  Status = 1
    StatusDone        Status = 2
  )

  // declaration des variable pour recuperer les saisies 
  var taskID int 
  var statusCode int 

  // recuperation des saisies 
  fmt.Scan(&taskID, &statusCode)

  status := Status(statusCode) // transformation explicite int -> Status 

  // on compare en utilisant la constante qui fait reference a une valeur pour ameliorer la lisibilite
  if status == StatusNew {
    fmt.Println("task#%d: new\n", taskID)
  } else if status == StatusInProgress {
    fmt.Println("task #%d: in progress\n", taskID)
  } else if status == StatusDone {
    fmt.Println("task #%d: done\n", taskID)
  } else {
    // on garde un else pour les statusCode inconnu
    fmt.Println("task #%d: unknow status (%d)\n", taskID, statusCode)
  }
}
```

---

## Iota 

Les `iota` ne sont disponible que sur les groupe de `const`. Il permette d'automatiser les valeurs pour chaque constante du groupe en ajoutant a la premiere ligne du groupe de constante `0` et ensuite, pour chaque ligne du groupe augmente la valeur de `1`.

Chaque groupe de constante avec `iota` dispose de sa numerotqtion independante.

```go 
func main(){
  const (
    StatusNew = ioat  // 0
    StatusInProgress  // 1
    StatusDone        //2
  )

  fmt.Println(StatusDone) // 2
}
```

### Sauts 

Il est parfois sauhaiter de sauter la premiere ligne. `0` etant utiliser pour signifier une valeur nul ou non definie et les vrai valeurs debut a `1`
Lorsque les donnees provienne de l'exterieur, `0` est souvent utiliser comme valeur vide par defaut.

Il est possible de sauter la premiere ligne avec `_`. 

```go 
const (
  _ = iota // saut de ligne 
  One      // 1
  Two      // 2
) 
```

---

## Enum 

En Go, on cree une enumeration de cette maniere : 

1. creation d'un type numerique avec un sens (par exemple `type Status int`) 
2. creation d'un ensemble de constante de ce type 

```go 
package main 

import "fmt"

// declaration du type 
type TaskStatus int 

// creation des constante avec le type et le iota
const (
  StatusNew TaskStatus = iota // 0 
  StatusInProgress            // 1 
  StatusDone                  // 2
)

func main(){
  // on passe la constante qui set la valeur sur la variable
  var s TaskStatus = StatusInProgress // 1

  switch s {
    // on check la constante qui fait reference a la valeur 
    case StatusNew :
      fmt.Println("status: new")
    case StatusInProgress:
      fmt.Println("status: in progress")
    case StatusDone:
      fmt.Println("status: done")
    default:
      // on prevoit un case par defaut si valeur de status inconnu
      fmt.Println("status: unknow")
  }
}
```

### Pattern enum 

Dans ce pattern, on retrouve une fonction qui permnet de centraliser les regles.
La fonction est appeler, et est responsable de ce realiser les actions selon la valeur de l'enum.

```go 
package main 

import "fmt"

// declaration du type perso 
type Status int 

// declaration des valeurs de l'enum
const ( 
  StatusNew Status = iota 
  StatusInProgress
  StatusDone 
)

// fonction traductrice 
func statusText(s Status) string {
  switch s {
    case StatusNew:
      return "new"
    case StatusInProgress:
      return "in_progress"
    case StatusDone:
      return "done"
    default:
      return "unknown"
  }
}

func main(){
  fmt.Println(statusText(StatusDone))
}
```

## Flag

Il est parfois necessaire de stocker un ensemble de caracteristique de maniere independante. Par exemple, une tache peut avoir plusieurs options : urgent, archiver, lecture seule.
Ces options peuvent ensuite etre combiner.

Pour cela, on utilise des flags binaire : un seul nombre, dans lequel chaque bit represente une case a cocher. 

On utilise `1 << iota` qui signifie : prend le nombre 1 et decale le bit a 1 de `iota` position. On obtient alors des puissance de deux.

On utilise alors les `uint` pour travailler avec des masque de bits.

```go
package main 

import "fmt"

type TaskFlag uint 

// ensemble de caracterisque que l'on peut combiner 
// les valeurs sont en bit donc en puissance de 2
const (
  FlagUrgent TaskFlag = 1 << iota // 1
  FlagNotify // 2
  FlagArchived // 4
)
```

### Utilisation des flag et ioat 

```go 
package main 

import "fmt"

type TaskStatus int 
type TaskFlag uint 

// definis les status avec ioat 
const (
  StatusNew TaskStatus = ioat // 0 
  StatusInProgress            // 1
  StatusDone                  // 2
)

// definis les masque de bits pour les flag 
const (
  FlagUrgent TaskFlag = 1 << iota // 1 = 001
  FlagNotify                      // 2 = 011
  FlagArchived                    // 4 = 111
)

func main(){
  var statusCode int 
  var flagsCode uint 

  fmt.Scan(&statusCode, &flagsCode)

  status := TaskStatus(statusCode) // conversion dans le type perso des constantes de status 
  flags := TaskFlag(flagsCode) // conversion pour le masque de flag 

  switch status (
    case StatusNew:
      fmt.Println("status: new")
    case StatusInProgress:
      fmt.Println("status: in progress")
    case StatusDone:
      fmt.Println("status: done")
    default:
      fmt.Println("status: unknown")
  )

  // verification des flags => si le bit est actif le flag est true 
  if flags&FlagUrgent != 0 {
    fmt.Println("flag: urgent")
  }
  if flags&FlagNotify != 0 {
    fmt.Println("flag: notify")
  }
  if flags&FlagArchived != 0 {
    fmt.Println("flag: archived")
  }
}
```

### Pattern Flag 

Pour les flags, l'idee est d'avoir un flag pour un bit destinct. 

L'activation des flags se fait avec `|` => ajout de caracteristique 
La verification se fait avec `&` et une compairaison a zero 

```go 
package main 

import "fmt"

type TaskFlags uint 

const ( 
  FlagUrgent TaskFlags = 1 << iota
  FlagHasDeadline 
  FlagBlocked
)

// fonction helper pour valider le flag
func hasFlag(all, f TaskFlags) bool {
  // verification du flag 
  return all&f != 0
}

// fonction helper pour traduire la valeur de la constante en texte
func flagsText(f TaskFlags) string {
  out := ""

  if hasFlag(f, FlagUrgent) {
    out += "urgent|"
  }
  if hasFlag(f, FlagHasDeadline) {
    out += "deadline|"
  }
  if hasFlag(f, Flagblocked){
    out += "blocked|"
  }
  if out == "" {
    return "none"
  }

  return out[:len(out)-1] // supprime le dernier |
}

func main(){
  flags := FlagUrgent | FlagBlocked
  // premier argument contiens les bits 
  // second argument le flag a verifier dans les bits passer a la fonction
  fmt.Println(hasFlag(flags, FlagBlocked)) // true
  
  fmt.Println(flagsText(flags)) // urgent|blocked
}
```


---










