# Variables 

Les variables sont des éléments utilisées pour stocker de la données. Chaque variable est composé de :
- **nom**: permet de distinguer une variable d'une autre.
- **type**: le type de la variable détermine quel donnée, valeurs on peut stocker. 
- **valeur**: la donnée stocké dans la variable.

Il existe quelques règles concernant le nom des variables 
- Il ne peut pas commencer par un chiffre et ne doit pas être utiliser par un mot réservé
- Il ne peut pas contenir de symboles spéciale sauf `$` et `_`
- La casse est importante
- Par convention, on utilise le `camelCase`
## DECLARATION DE VARIABLE

```java
int a; 
String s;
double c;
```

On commence par définir le type, puis le nom de la variable.

### Déclaration multiple 

Il est possible de créer plusieurs variable d'un même type sur une seule ligne

```java
int var1, var2, var3;
```

Il est possible de déclarer et d'affecter directement des variables  sur une seule ligne

```java
int imya1 = var1, imya2 = var2, imya3 = var3;
```
## AFFECTATION 

Pour ajouter une valeur à une valeur, on utilise l'opérateur d'affectation. Elle copie la valeur de la variable dans une autre. 

```java
int i;			// La variable i est créée
int a, b;		// Les variables a et b sont créées
int x;			// La variable x est créée

i = 3;			// La valeur 3 est placée dans la variable i.

a = 1;			// La valeur 1 est placée dans la variable a.
b = a + 1;		// La valeur 2 est placée dans la variable b.

x = 3;			// La valeur 3 est placée dans la variable x.
x = x + 1;		// À cette ligne, la valeur de x augmente de 1, x vaut maintenant 4.
```


