# Defer 

`defer` permet de définir une action obligatoire à la sortie de la fonction courante. L'instruction concerne la sortie de la fonction. L'appel differe sera exécuté à la fin lors d'une fin normale et lors d'un `return`anticipé. Le `defer` reste en attente, et dès la fin de la fonction arrive, s'exécute.

```go
package main

import (
	"errors"
	"fmt"
)

func checkAge(age int) error {
	fmt.Println("start")          // start
	defer fmt.Println("finish")   // s'exécute à la fin de la fonction 

	if age < 0 {
		return errors.New("age must be >= 0")
	}
	return nil
}

func main() {
	fmt.Println(checkAge(-10)) // affiche age; affiche l'erreur: age must be >= 0; puis "finish"
}
```

`defer`travail avec un appel de fonction. La forme correct est `defer something()`. Defer doit savoir quoi appeller et avec quel argument.

## Arguments et ordre d'exécution de defer 

Les arguments de `defer`sont calculés immédiatement. Ils sont calculé au moment ou l'instruction `defer`est exécuté, et pas au moment ou la fonction se termine.

```go
func main() {
	i := 0
	defer fmt.Println("defer i =", i) // defer i = 0 => afficher à la fin de la fonction
	i = 10
	fmt.Println("now i =", i)         // now i = 10 => valeur de i à la fin normal de la fonction 
}
```

Dans ce code, on aura donc `i = 0` qui est sa valeur au moment où `defer` est déclarer dans la fonction. 

## Defer multiple 

Si il y a plusieurs `defer` dans une fonction, ils s'exécute dans l'ordre inverse. La dernier se retrouve en haut de la pile, et elle est exécuté en premier.

```go 
func main() {
	defer fmt.Print("A") // sera execute en troisieme
	defer fmt.Print("B") // sera execute en deuxieme
	defer fmt.Print("C") // sera execute en premier
}
```

## Defer dans une boucle 

Le defer dans une boucle s'exécute à la fin de la fonction. 

```go 
func main() {
	for i := 0; i < 3; i++ {
		defer fmt.Print(i) // 2 1 0
	}
}
```

A la sortie, on obtient `2 1 0` car l'ordre des defer est inversé à cause de la boucle. On aura en première valeur `2`, puis `1`, puis `0`.

## Defer et fonction anonyme 

```go
func main() {
	i := 0

	defer fmt.Println("arg:", i) // arg: 0 => fonctionnement normal, la valeur est figé au moment de la déclaration 

	defer func() {
		fmt.Println("closure:", i) // closure: 10 => la fonction anonyme capture la variable i et la lira plus tard; lorsque i vaut 10 à la fin de la fonction 
	}()

	i = 10
}
```

- `defer`enregistre l'appel de la fonction anonyme immédiatemment.
- la closure capture la variable `i`, et pas sa valeur 
- lorsque la fonction defer est exécuté à la fin de `main()`, elle vient lire la valeur actuelle de la variable, donc `10`

Pour que la fonction anonyme mémorise la valeur "maintenant", il faut transmettre la valeur dans un paramètre.

```go 
func main() {
	i := 0

	defer func(v int) {
		fmt.Println("v =", v) // v = 0
	}(i)

	i = 10
}
```

## Utilisation 

On as une mini calculatrice. On utilise le `defer` pour afficher un message lorsque le calcul est terminé 

```go 
package main

import (
	"errors"
	"fmt"
)

func compute(op string, a, b int) (int, error) {
	fmt.Println("compute: start")     // compute: start
	defer fmt.Println("compute: end") // compute: end => s'exécute à la fin de la fonction compute() 

	if op == "/" && b == 0 {
		return 0, errors.New("division by zero")
	}
	if op == "+" {
		return a + b, nil
	}
	if op == "/" {
		return a / b, nil
	}
	return 0, errors.New("unknown operation")
}

func main() {
	res, err := compute("/", 10, 0)
	fmt.Println("res =", res, "err =", err) // res = 0 err = division by zero
}
``` 

## Modèle 

```go 
package main

import "fmt"

func demo() {
	defer fmt.Print("1") // sera execute en troisieme
	fmt.Print("A")       // sera affiche immediatement

	defer fmt.Print("2") // sera execute en deuxieme
	fmt.Print("B")       // sera affiche immediatement

	defer fmt.Print("3") // sera execute en premier
	fmt.Print("C")       // sera affiche immediatement
}

func main() {
	demo() // ABC321
}
```

---

## Pattern defer libération ressource 

Lorsque l'on viens travailler avec des ressources (fichier, réseau, etc), il faut absolument libérer la ressource une fois qu'il n'est plus nécessaire de l'utiliser.
Les fichiers, connexions, etc qui sont "Close-able" dispose généralement d'une méthode `Close()` qui retourne une erreur. 

Dès qu'une ressource à été obtenu avec succes, on place immédiatement un `defer` pour la libérer. On utilise ce pattern :

- Obtenir la ressource et l'erreur 
- Si erreur - sortir 
- Si succes - placer `defer` pour la libération 
- continuer le script 

```go 
func readFirstByte(path string) (byte, error) {
    f, err := os.Open(path) // on vient récupérer la ressource fichier 
    if err != nil {
        return 0, err
    }
    defer f.Close() // on libère la ressource à la fin de la fonction 

    var buf [1]byte
    _, err = f.Read(buf[:])
    if err != nil {
        return 0, err
    }
    return buf[0], nil
}

func main() {
    b, err := readFirstByte("data.txt")
    fmt.Println(b, err)
}
```

Le deferred-cleanup doit être court. Il doit être utiliser comme petite garantie : fermer, dévérouiller et libérer. Plus le cleanup est complexe, et plus le risque d'erreur augmente.
Le code deferred ne doit pas modifier l'état externe sans necessite absolue. Si beaucoup de logique est nécesaire, il vaut mieux la sortie dans une fonction séparée et l'appeller depuis le `defer` 

---

## Pattern Lock() Unlock() 

Lorsque que l'on rencontre `Lock()`, la ressource est vérouiller pour les autres parties du programme. Pour libérer la ressource, on utilise `Unlock()`

```go 
package main

import (
    "fmt"
    "sync"
)

var mu sync.Mutex
var counter int

func incSafely() {
    mu.Lock() // lock de la ressource 
    defer mu.Unlock() // defer pour unlock la ressource 

    counter++
    fmt.Println("counter =", counter) // counter = 1 (puis 2, 3...)
}

func main() {
    incSafely()
    incSafely()
}
```

--- 

## Echec de libération de ressource 

Parfois la libération de ressource peut échouer. `mu.Unlock()` ne retourne pas d'erreur. `Close()` retourne très souvent un `error`.
Il existe plusieurs stratégie pour gérer les cas d'erreur lors d'une libération de ressource.

| Situation | Stratégie | Raison | 
| --------- | --------- | ------ | 
| Les données sont lu, le resultat est déjà obtenu, `Close()` a peu d'impact | Ignorer | L'erreur de fermeture n'est pas important pour la logique | 
| On écrit des données, la réussite est important | L'erreur `Close()` ne doit pas être perdu | L'erreur peut signifier que les données n'ont pas été sauvegardées | 
| Il y a une erreur de travail principal et une erreur de `Close()` | `errors.Join` | Pour ne pas choisir qui perdre | 

**Stratégie simple: Ignorer l'erreur de Close** 
Par exemple, pour une lecture d'un fichier, on peut ignorer l'erreur `Close()`.

```go 
defer func() { _ = f.Close()}()
```

**Stratégie pratique: Retourner l'erreur Close**
`Close()` devient l'erreur par défaut, mais elle n'écrase pas l'erreur principale. On utilise un `err` nommé, car `defer` peut modifer le résultat nommé juste avant le retour.

```go
package main

import (
    "fmt"
    "os"
)

func readSomething(path string) (err error) {
    f, err := os.Open(path)
    if err != nil {
        return err
    }
    // retourne l'erreur Close
    defer func() {
        if cerr := f.Close(); cerr != nil && err == nil {
            err = cerr
        }
    }()

    // imaginons qu'ici il y ait un travail qui peut renvoyer une erreur
    return nil
}

func main() {
    fmt.Println(readSomething("data.txt"))
}
```

**Stratégie honnete: retourner les deux erreurs avec errors.Join**

`errors.Join()` permet de retourner l'erreur provenant du travail principal, ainsi que l'erreur retourner par `Close()`.

```go 
package main

import (
    "errors"
    "fmt"
    "os"
)

func doWork(path string) (err error) {
    f, err := os.Open(path)
    if err != nil {
        return err
    }
    defer func() {
        if cerr := f.Close(); cerr != nil {
            err = errors.Join(err, cerr) // on join les deux erreurs 
        }
    }()

    // Supposons que le travail principal "echoue"
    return errors.New("work failed")
}

func main() {
    fmt.Println(doWork("data.txt"))
}
```

---

## Libération dans une boucle

Dans le cas ou un `defer`est placer dans une boucle, la fermeture se produire à la fin de la fonction, et pas à la fin de l'itération.
Pour fermer à la fin d'une itération, on extrait le corps de l'itération dans une fonction, et le `defer` est placer dans la fonction. 

```go 
package main

import "fmt"

func oneIteration(i int) {
    defer fmt.Println("cleanup", i)
    fmt.Println("work", i)
}

func main() {
    for i := 0; i < 3; i++ {
        oneIteration(i)
    }
}
// work 0
// cleanup 0
// work 1
// cleanup 1
// work 2
// cleanup 2
```

--- 

## Application 

L'application maintient la liste des tâches en mémoire. On applique le pattern pour libérer la ressource.

```go 
package main

import (
    "errors"
    "fmt"
    "os"
)

func exportTasks(path string, tasks []string) (err error) {
    f, err := os.Create(path)
    if err != nil {
        return fmt.Errorf("create report %q: %w", path, err)
    }
    // ajout du defer pour libérer la ressource
    defer func() {
        // on récupère l'erreur potentiel de Close
        // si on as une erreur, on join les erreurs pour les remonter (erreur Close et du flux normal de la fonction)
        if cerr := f.Close(); cerr != nil {
            err = errors.Join(err, fmt.Errorf("close report %q: %w", path, cerr))
        }
    }()

    for i, t := range tasks {
        _, werr := fmt.Fprintf(f, "%d) %s\n", i+1, t)
        if werr != nil {
            return fmt.Errorf("write report %q: %w", path, werr)
        }
    }
    return nil
}


func main() {
    tasks := []string{"acheter du lait", "lire sur defer", "ne pas paniquer"}

    err := exportTasks("tasks.txt", tasks)
    fmt.Println("export err:", err) // export err: <nil>  (si tout va bien)
}
```