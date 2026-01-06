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