
# Core

## Compilateur

Programme qui vient traduire le code Go en code machine. Il prends le code Go, le décompose, le vérifie pour détecter les erreurs et le transforme en code machine

## Lecture d'erreur

`main.go:7:6: undefined: something`

- `main.go` : fichier qui provoque l'erreur
- `7` : ligne
- `6` : colonne
- `undefined: something` : message d'erreur

Il existe plusieurs type d'erreurs :

- `undefined` : nom introuvable
- `cannot use ... as ...` : type ne correspondent pas
- `declared and not used`: une variable n'est pas utilise
- `imported and not used`: importer mais non utiliser
- `syntax error` : erreur de syntaxe

## Go SQK

L'ensemble des outils de l'IDE pour transformer le code source en un fichier exécutable. C'est un répertoire sur l'ordinateur qui content :

- les utilitaire pour la compilation et la construction : `go build`, `go run`
- les outils de formatage et de test : `gofmt`, `go test`
- la bibliothèque standard

Le dossier racine dans lequel Go SDK est installer s'appelle `GOROOT`. L'IDE utilise celui ci comme source de vérité.

