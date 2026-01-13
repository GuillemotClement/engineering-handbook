# Variable

```rust
let team_name = "Rustaceans";
team_name = "Test"; // error

// mut permet d'indiquer qu'une variable peut changer
let mut mutable_team_name = "Rustaceans";
mutable_team_name = "Crustaceans"; // This doesn't error
```

Par défaut, les variables sont immutables. Une fois définis, on ne peut plus les changer.

## Console

```rust
// This prints "Hello world" to the console et ajoute une nouvelle ligne
println!("Hello world")

// pas d'ajout de nouvelle ligne
print!("This is a single ")
print!("line")

// retourne dans la stderr. Utiliser pour les messaged d'erreur
println!("Printing to stdout")
eprintln!("Printing to stderr")
```

## Type

```rust
// Here, we're specifying that `flag` is of type `bool` 
let flag: bool = true;

let flag = true; // type inféré

// scalar type 
let x = 42; // Integer
let x = 4.2; // Float
let x = true; // Boolean
let x = 'a'; // Char

// compund types => 
// This is a tuple. It can group multiple values of different types.
let chess_square = (5, 'A');

// This is an array. It can group multiple values of the same type.
let array_of_ints = [1, 2, 3]; 
```

Le compilateur Rust est capable de définir automatiquement le type de la variable.