# Golang 

## Commande 

### init 
Permet d'initialiser un nouveau projet. Cette commande est lancer au root du répertoire contenant le projet.

```shell
go mod init github.com/GuillemotClement/<project_name>
```

### get 

Cette commande permet d'installer une nouvelle dépendance pour le projet. La version de la dépendance est enregistrer dans le `go.mod`.

### run 
Cette commande permet de lancer le script. Il permet de tester rapidement si le code compile, et fonctionne correctement.

```shell
go run ./cmd/api
```

---
## Structure d'un projet

### Application web 
```tree
.
├── cmd
│ └── web
      |--- main.go
      |--- handler.go
├── internal
├── ui
      |--- html
      |--- static
├── go.mod
└── Makefile
```

- `cmd` : contient le code spécifique de l'application. 
- `internal` : contient du code générique qui peut être réutiliser dans d'autre projets (helper, validateurs, sql).
- `ui/html` : contient les fichier HTML 
- `ui/static` : contient le css et les images

---
### API 

```shell
mkdir -p bin cmd/api internal migrations remote
touch Makefile
touch cmd/api/main.go
```

```tree
.
├── bin
├── cmd
│ └── api
│ └── main.go
├── internal
├── migrations
├── remote
├── go.mod
└── Makefile
```

- `bin`: dossier qui contient les binaire de l'application, prêt pour le deploement sur un serveur de production 
- `cmd/api`: contient le code spécifique spécifique de l'API. Inclut le code pour lancer le serveur, la gestion des requêtes HTTP et la gestion de l'authentification 
- `internal` : contient le code utilisé par l'API. Il contient le code permettant d'intéragir avec la base de donnée, validation des données, l'envoie d'email, etc. Ce code peut potentiellement être réutiliser dans d'autres projet.
- `migrations` : contient les migrations SQL 
- `remote`: contient les fichiers de configuration et les scripts pour le serveur 
- `go.mod`: liste les dépendances, les versons et les modules path 
- `Makefile` : contient la recette pour les tâches administrative comme l'audit du code, le build des binaire et l'exécution des migrations

---
## Serveur Mux 

### Serveur simple 

```go 
func main(){
	mux := http.NewServeMux() // initialise un server mux 
 	mux.HandleFunc("/", home) // enregistre un handler 

	log.Print("Starting server on :4000") // log un message au lancement du server 
	err := http.ListenAndServe(":4000", mux) // lance le serveur, en premier argument le port écouté, en second l'instance du serveur mux 
	// si le lancement retourne une erreur, on utilise log.Fata() pour log le message et stopper le serveur 
	log.Fatal(err)
}
```

Avec ce code, si on accéde depuis le navigateur à `localhost:4000`, on affiche une page avec le message indiquer dans la fonction handler.

#### mux.HandleFunc()
Cette fonction permet de définir une route dans le serveur. 

Elle prends en premier argument le endpoint, et en second argument une fonction handler.

#### http.ListenAndServe()

C'est la fonction qui permet de lancer le serveur. Le premier argument doit être au format `host:port`. Si on omet le `host`, le serveur écoute sur toutes les interfaces de la machine.

On précise l'host que dans les cas où la machine dispose de plusieurs interfaces réseau et que l'on souhaite écouter une spécifiquement.

---
## Handler 

Fonction chargé de traiter une requête et de retourner une réponse.

Cette fonction prends deux paramètre.
- `http.ResponseWritter` : qui permet de construire la réponse 
- `*http.Request` : qui est un pointeur permettant de récupérer des informations sur la requête envoyé

```go
// définit un handler qui vient écrire un byte de slice dans la body de la réponse
func home(w http.ResponseWriter, r *http.Request){
	w.Write([]byte("Hello from Snippetbox"))
}
```

---

## Request 

### w.WriteHandler() 

Cette méthode permet de définir un code HTTP. 

Elle n'est utilisable qu'une seule fois par réponse, et par défaut retourne un `200 OK`.

Elle doit être utilisé avant la méthode `w.Write()` 

```go
func snippetCreate(w http.ResponseWriter, r *http.Request){
	// on viens vérifier la méthode HTTP de la requête 
	if r.Method != "POST" {
		// si la méthode n'est pas celle attendu, on retourne une 405 
		w.WriteHeader(405) // on peut l'utiliser qu'une seule fois par réponse 
		w.Write([]byte("Method Not Allowed"))
		return
	}
	w.Write([]byte("Create a new snippet ..."))
}
```

### http.Error()

Cette fonction prends un message et le status code. Elle appelle ensuite la méthode `w.WritteHeader()` et `w.Write()` pour retourner le message d'erreur dans la response.

```go 
// code version longue 
func snippetCreate(w http.ResponseWriter, r *http.Request){
	if r.Method != "POST" {
		w.Header().Set("Allow", "POST")
		w.WriteHeader(405) 
		w.Write([]byte("Method Not Allowed"))
		return
	}
	w.Write([]byte("Create a new snippet ..."))
}

// version helper 
func snippetCreate(w http.ResponseWriter, r *http.Request){
	if r.Method != "POST" {
		w.Header().Set("Allow", "POST")
		http.Error(w, "Method not allowed", 405)
		return
	}
	w.Write([]byte("Create a new snippet ..."))
}
```

---
## html/template

Package Go qui fournit  des fonctions permettant de parser et de rendre des templates HTML.

### Composition de template 
On retrouve un fichier `/html/base.html` qui définit le template de base. 
```html
{{ define "base" }}
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>{{ template "title" .}} - Snippetbox</title>
  </head>
  <body>
    <header>
      <h1>
        <a href="/">Snippetbox</a>
      </h1>
    </header>
    <main>
      {{ template "main" .}}
    </main>
    <footer>
      Powered by <a href="https://golang.org/">Go</a>
    </footer>
  </body>
</html>
{{ end }}
```

- `{{ define "base"}} ... {{end}}` : définit le nom de template. Ici `base`. Il contient le contenu que l'on souhaite afficher sur chaque page 
- `{{template "titile".}}` : permet d'invoquer un template. Le `.` représente les donnée dynamique que l'on souhaite passer au template

On viens définir un autre fichier HTML :
```html
{{ define "title"}}Home{{end}}

{{ define "main"}}
  <h2>Latest Snippets</h2>
  <p>There's nothing to see here yet!</p>
{{end}}
```

Le handler qui est responsable de l'affichage de la page est ensuite déclaré 

### block action 

Permet de définir un emplacement que l'on peut remplacer, tout en fournissant un contenu par défaut.

```go
{{define "base"}}
	<h1>An example template</h1>
	{{block "sidebar" .}}
		<p>My default sidebar content</p>
	{{end}}
{{end}}
```

Cela permet de fournis un contenu standard, tout en permettant de personnaliser ce bloc dans chaque page.


---

## Fichier statique 

### http.FileServer
Handler permettant de servir des fichiers statiques depuis un répertoire spécifique. 

```go
	// création du file server pour service les fichiers statiques
	fileServer := http.FileServer(http.Dir("./ui/static"))
	// ajoute dans le serveur mux => accessible via url http://localhost:4000/static/
	mux.Handle("/static/", http.StripPrefix("/static", fileServer))
```

On peut ensuite venir modifier le template pour qu'il aille récupérer les fichiers statiques et puisse accéder au CSS, aux images ou au script JS.

```html
{{ define "base" }}
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>{{ template "title" .}} - Snippetbox</title>
    %% Ajout des fichiers statiques %%
    <link rel="stylesheet" href="/static/css/main.css">
    <link rel="shortcut icon" href="/static/img/favicon.ico" image="image/x-icon">
    <link rel="stylesheet" href="https://font.googleapis.com/css?family=Ubuntu+Mono:400,700">
  </head>
  <body>
    <header>
      <h1>
        <a href="/">Snippetbox</a>
      </h1>
    </header>
    {{ template "nav" .}}
    <main>
      {{ template "main" .}}
    </main>
    <footer>
      Powered by <a href="https://golang.org/">Go</a>
    </footer>
    %% Ajout du script js %%
    <script src="/static/js/main.js" type="text/javascript"></script>
  </body>
</html>
{{ end }}
```

### Service un fichier 

Parfois, on souhaite service un simple fichier. On peut venir utiliser la fonction `http.ServeFile()` 

```go
func downloadHandler(w http.ResponseWritter, r *http.request){
	http.ServeFile(w, r, "./ui/static/file.zip")
}
```

---

## Command-line flags 

En go, il est courant de manager la configuration en utilisant la command-line flags au lancement de l'application.

Par exemple : 
```shell
go run ./cmd/web -addr=":80"
```

La méthode la plus simple d'accepter et de parser les paramètres CLI flag dans l'application est d'utiliser ce type de code 

```go 
add := flag.String("addr", ":4000", "HTTP network address")
```

Ce code vient définir une nouvelle commande : 
- `addr` : le nom de la commande 
- `:4000` : la valeur par défaut 
- le dernier argument est un texte de description de la commande

La valeur du flag est stocké dans la variable `addr` au runtime.

L'utilisation de `-help` permet de lister l'ensemble des flags utilisables.

```shell
go run ./cmd/web -help
```

---

## Variables d'environnement 

```go
addr := os.Getenv("SNIPPETBOX_ADDR")
```

---

## loggueur 

```go
	infoLog := log.New(os.Stdout, "INFO\t", log.Ldate|log.Ltime)
	
	errorLog := log.New(os.Stderr, "ERROR\t", log.Ldate|log.Ltime|log.Lshortfile)
```

`log.New`: permet de définir des loggeur préformater.

Le premier argument est l'endroit ou le loguer doit écrire. En second argument, le préfixe afficher lorsque le logueur est appelé, et en dernier argument permet d'ajouter des info supplémentaire.

On viens ensuite les utiliser dans le code 

```go 
	infoLog.Printf("Starting server on %s", *addr)
	err := http.ListenAndServe(*addr, mux)
	errorLog.Fatal(err)
```

### Gestion des logs 

Il est possible de rediriger les logs dans un fichiers.

Par exemple, on peut rediriger les logs `stdout` ou `stderr` directement dans un fichier lorsque l'on lance l'application 
```shell
go run ./cmd/web >> /tmp/info.log 2>>/tmp/error.log
```

---

## Injection de dépendance

```go
// définition d'un struct qui référence les méthodes
type application struct {
	errorLog *log.Logger
	infoLog *log.Logger
}
```

Dans les handler qui ont besoin d'utiliser ces méthodes, on peut injecter la `struct` pour rendre disponible ces méthodes dans le handler.

```go
// on passe la référence de la struct
func (app *application) home(w http.ResponseWriter, r *http.Request){
	if r.URL.Path != "/" {
		http.NotFound(w, r)
		return
	}

	files := []string{
		"./ui/html/base.html",
		"./ui/html/partials/nav.html",
		"./ui/html/pages/home.html",
	}

	ts, err := template.ParseFiles(files...)
	if err != nil {
		// on fait référence à la struct injecter dans le handler
		app.errorLog.Print(err.Error())
		http.Error(w, "Internal Server Error", 500)
		return 
	}

	err = ts.ExecuteTemplate(w, "base", nil)
	if err != nil {
		app.errorLog.Print(err.Error())
		http.Error(w, "Internal Server Error", 500)
	}

	w.Write([]byte("Hello from Snippetbox"))
}
```

On peut ensuite initialiser la `struct` en injectant les méthodes nécessaire 

```go 
func main(){
	// .....
	// initialisation de l'instance de app struct qui contient les dépendances
	app := &application{
		errorLog: errorLog,
		infoLog: infoLog,
	}

	// ....
	
	// routage => on injecte l'instance de la struct
	mux.HandleFunc("/", app.home)
	mux.HandleFunc("/snippet/view", app.snippetView)
	mux.HandleFunc("/snippet/create", app.snippetCreate)

	// ....
}
```

---

## Database 

