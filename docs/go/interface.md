# Interface 

Une interface permet de définir un ensemble de comportement lié à un `struct`. Pour qu'un type soit valide, il doit implémenter les méthodes définis dans l'interface.
L'interface énumère des signatures de méthodes attendues par le consommateur.

```go 
type Greeter interface {
	Greet() string // signature de méthode 
}
```

`Greeter` est un type d'interface qui dit: "tout ce qui sait faire `Greet() string` convient".

On ne peut pas accéder aux champs via une interface. 

```go 
type User struct {
	Name string
}
```

Si `var g Greeter = User { Name: "Anna" }`, on ne pourras pas faire `g.Name` car `Greeter` est une interface, et qui sert uniquement à promettre des méthodes. L'interface doit protéger contre les détails d'implémentation.
