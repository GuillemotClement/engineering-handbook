# Golang 


## Commande 

```shell
# exécuter un programme => pour tester le programme
go run <name>.go 

# build => une fois terminé, pour distribuer le programme
go build <name>.go 

# build et renommer l'executable
go build -o <new_name> <name>.go

# lancer l'exécutable 
./<name>
```
Le build d'un programme Go viens builder le programme, et créer un exécutable.

### Formatage 

Go fournit `gofmt` pour formater le code. 




### Documentation 

La documentation Go est disponible sur [pkg.go.dev](https://pkg.go.dev/).

La commande `pkgsite` permet d'afficher la documentation du projet en local.

```shell 
pkgsite -http=:6060
```

`godoc` n'est pas installer par défaut. Pour l'installer, on utilise la commande `go install golang.
org/x/tools/cmd/godoc@latest`.

Le binaire `godoc` est installer dans `~/go/bin` et on peut l'exécuter avec la commande `~/go/bin/godoc`.

Cette commande lance un server local et permet d'afficher la documentation Go.

```shell
~/go/bin/godoc -http=:8001

# syntaxe courte 
~/go/bin/godoc -http :8001
```

On peut ensuite accéder à la documentation en accédant à l'url `http://localhost:8001/`.


## Fonction 

Tous les programmes Go possèdent une fonction `main()` qui est le point d'entrée du programme.

```go 
func main(){
    // code exécuter par la fonction
}
```

Les autres fonctions peuvent avoir n'importe quel nom qui permettent de comprendre ce que réalise cette fonction.

Tout ce qui commence par une minuscule est considéré comme `private`, et sera accessible que dans la portée du package. Avec une majuscule, l'élément est `public` et accessible depuis l'exterieur du package.

## Package 

Les programmes Go sont organisés en package. 

Le mot clé `package` permet de définir un nom pour un nouveau package.

Le nom d'un package qui est exécutable doit être `main`.

Le mot clé `import` est utilisé pour importer d'autre package dans un programme et permettre d'utiliser ces méthodes.

### Import de package

Pour installer une nouvelle dépendance, on utilise la commande 

```shell
go get <github/url>
```



## Variables

### Déclaration de variable 

```go
# syntaxe longue
var maVariable int = 1

# syntaxe courte 
maVariable := 1
```

Si aucune valeur n'est passer lors de l'initialisation, alors le compilateur Go initialise automatiquement la variable à la valeur zéro.

Généralement, la syntaxe courte est utilisé pour déclarer les variables, mais dans les cas ou l'on souhaite déclarer une variable globale ou une variable local sans valeur initiale.

L'utilisation de `var` est également dans le cas ou l'on souhaite définir explicitement le type d'une variable.

L'utilisation de la syntaxe courte est impossible en dehors du corps d'une fonction.

### Constante

Dans le cas ou l'on souhaite déclarer une variable qui ne doit pas changer, on utilise une constante.

Il faut obligatoirement définir une valeur à cette constante, et celle ci ne pourras pas changer par la suite.

```go
const maConstante = 10
```

### Variable globale 

Les variables globale sont déclarée en dehors d'une fonction.  Elles sont accessibles en dehors du package sans avoir besoin de les passer explicitement.







## Lint 

https://golangci-lint.run/
## Projet 

### Créer un nouveau projet Go 

Créer un nouveau dossier

### Package

En haut de tous les fichiers Go, on retrouve la déclaration du package. 

Le package principal est le `main`.

```go
package main 
```

### Import de dépendance

En haut du fichier, sous la déclaration de package, on viens importer les dépendances 

```go
import (
	"fmt"
	"os"
)
```

## Struct 

Les structs permettent de déclarer un "type" pour une donnée.

```go
type Page struct {
    Title string
    Body  []byte
}
```

- `[]byte`: indique qu'on attends un slice de byte. C'est le type attendu ensuite dans le programme de l'exemple. C'est à cause de la library `io`.


```go
package main

  

import (

"net/http"

"strconv"

  

"github.com/gin-gonic/gin"

)

  

// représente le type Album

type task struct {

ID int `json:"id"`

Title string `json:"title"`

Description string `json:"description"`

Branche string `json:"branche"`

}

  

// slice qui permet de stocker des albums

var tasks = []task{

{ID: 1, Title: "Ma première task", Description: "Ma super description de task", Branche: "DIT-2026-1-gestion-des-tâches"},

}

  

// fonction principal, c'est elle qui est lancer au démarrage du serveur

func main(){

router := gin.Default() // initialisation du router

router.GET("/tasks", getTasks) // on lie la route au handler

router.POST("/tasks", postTask)

router.GET("/tasks/:id", getTaskByID)

  

router.Run("localhost:8085")

}

  

// handler pour retourner les donénes Album

func getTasks(c *gin.Context) {

// permet de sérializer le JSON

// premier argument est code status pour la reponse

// second est la donnée retourné

// Context.JSON permet de retourner un format plus compact

c.IndentedJSON(http.StatusOK, tasks)

}

  

func postTask(c *gin.Context){

var newTask task

  

// on viens désérialiser le json entrant

if err := c.BindJSON(&newTask); err != nil {

return

}

  

// ajout de la nouvelle task dans le slice

tasks = append(tasks, newTask)

// on retourne la task créer

c.IndentedJSON(http.StatusCreated, newTask)

}

  

func getTaskByID(c *gin.Context){

// permet de récupéer le paramètre passé via l'url

idStr := c.Param("id")

// convertion de l'id string en int

id, err := strconv.Atoi(idStr)

if err != nil{

c.IndentedJSON(http.StatusBadRequest, gin.H{"message": "id is not valid"})

return

}

  

// boucle sur les tasks pour trouver la bonne task

for _, t := range tasks {

if t.ID == id{

c.IndentedJSON(http.StatusOK, t)

return

}

}

c.IndentedJSON(http.StatusNotFound, gin.H{"message": "task not found"})

}
```

---
## TEST 

```go
import "testing"

func TestBonjour(t *testing.T){
	result := Bonjour() // la fonction tester
	want := "Hello";
	
	if result != attendu {
		t.Errorf("reçu %q attendu %q", result, want)
	}
}
```