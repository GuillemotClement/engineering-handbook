## CARGO 

```shell
# nouveau projet
cargo new <name_project>

# build 
cargo build 

# lancer le script
./target/debug/<project_name>

# lancer le script
cargo run 

# check script 
cargo check 

# compiler et diffuser -> target/reale est placer le binaire  + optimisation
cargo build --release
```

### Récupérer un projet 
```shell
$ git clone example.org/projet_quelconque
$ cd projet_quelconque
$ cargo build
```

## FONCTION

### main()

C'est la fonction point d'entrée d'un programme rust 

```rust 
fn main() {
    println!("Hello, world!");
}
```

- `fn` : permet de déclarer une fonction 

## LIB 

En haut du fichier, on définis les lib que l'on souhaite importer dans le fichier.

```rust
use std::io; // ici on importe la lib pour traiter in/out
```

## AFFICHAGE 

```rust
println!() // affiche une chaîne à l'écran
```

## VARIABLE 

```rust
let mut supposition = String::new(); // déclare une variable mutable

let pommes = 5; // déclare une variable => immuables 
```

- `String::new()` : fonction qui retourne une instance de String. `String` est un type de chaîne fournis par la lib standard qui est une portion de texte encodé en UTF8 et dont la longueur peut augmenter
- `::` : indique que `new` st une fonction associé au type `String`. Une fonction associée est une fontion qui est
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