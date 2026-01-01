# Langage C 

Un programme simple C est constitué de :

```c 
#include <stdio.h>

int main(){
  printf("Program in C!\n");
  return 0;
}
```

- une fonction `main()` qui sera toujours le point d'entrée du programme.
- `int` est le type de retour de la fonction. Comme c'est la fonction `main`, elle retourne toujours un `exit code`. `0` signifie un success, un autre code signifie un code d'echec
- `{}` permet de définir le code de la fonction.
- `return 0` indique que l'on retourne un entier `0`. C'est le code de sortie de la fonction `main`.

## Afficher 

### printf

Cette fonction permet d'afficher une valeur. Elle provient de la lib `stdio.h` (standard input/output library).

Pour utiliser une lib en C, il faut venir l'importer au top du fichier. On peut ensuite venir l'utiliser dans le code.

```c
#include <stdio.h>

printf("Hello World!\n");
```

Le caractère `\n` permet d'ajouter une nouvelle ligne.

# Commentaire

```c
// commentaire simple ligne 

/*
Commentaire 
multi 
ligne 
*/
```