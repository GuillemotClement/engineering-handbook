# C++ 

## AFFICHAGE 

`std::cout` : permet d'afficher à l'écran 

```c++
#include <iostram> // import de lib 

int main(){
	// permet d'afficher 
	// << envoie dans le flux 
	std::cout << "Hello Worl" << '\n';
	
	// retourn le code pour terminer la fonction
	return 0
}

int main() {
    // Important : afficher exactement 5 lignes, chacune terminée par le caractère '\n'
    std::cout
        << "+----------------------+\n"
        << "| Name: Ada Lovelace   |\n"
        << "| Role: Programmer     |\n"
        << "| City: London         |\n"
        << "+----------------------+\n";
    return 0;
}
```

---
## VARIABLE 

```c++
// déclaration d'un int 
int a;

a = 7; // affectation

// déclaration d'une string 
std::string s;

// déclaration d'un double 
double c;

// syntaxes de déclaration de variable  
int a = 5;
int b(5);
int c{5};
```

---

## TYPE 

### int 

Peut contenir des entiers positif et négatif. 

### double 

Stocke des nombres à virgules. Attention à la précision.

### string 

```c++
std::string s;

// concaténation 
std::string s1 = std::string("Amigo") + " the best";

int x = 333;
std::string s3 = std::string("Amigo") + std::to_string(x);
```
