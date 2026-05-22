# Arithmetique

## Division

### Division entiere

Si les deux operande sont entier, la partie decimal est ignorer.

```go
package main

import "fmt"

func main(){
  fmt.Println(7 / 2) // 3
}
```

#### Division decimal 

Pour obtenir un resultat d'une division decimalm il est necessaire d'utiliser des `float` avant de realiser la division.

```go
func main(){
  a := 7 
  b := 2 

  fmt.Println(a / b) // 3
  fmt.Prinln(float64(a) / float64(b)) // 3.5
}
```

### Division des nombres negatifs

La division entiere se fait vers zero.

```go
package main

import "fmt"

func main(){
  fmt.Println(-7 / 2) // -3
}
```

### Division de float

````go
func main(){
  fmt.Println(7 / 2) // 3
  fmt.Println(7.0 / 2.0) // 3.5
}

---

## Modulo

L'operateur `%` permet d'obtenir le reste d'une division. L'operation est possible uniquement sur les entier.

```go
func main(){
  a := 17
  b := 5

  fmt.Println(a / b, a % b) // 3 2
  fmt.Println((a / b) * b + (a % b)) // 17
}
````

---

## Calcul monetaire

Pour les calcul lie au somme monetaire, on passe en centime pour eviter de traiter des `float`.

```go
func main(){
  totalCents := 12345

  euros := totalCents / 100 // recupere les euros
  cents := totalCents % 100 // recupere les centimes

  fmt.Printf("%d euros %02d cents\n", euros, cents) // 123 euros 45 cents
}
```

- `%02d` permet de formater l'affichage. Ici on force l'affichage des centimes en `05` par exemple.

#### Calcul du cout d'une commande 

On souhaite calculer des sommes monetaire, il faut donc passer les valeurs en entier pour eviter les erreurs de precision des `float`.

L'utilisateur saisit un prix en euro et une quantite. On viens stocker le total en centime en `int64` pour conserver de la marge.

```go
func main(){
  var priceEuro int 
  var qty int 

  fmt.Scan(&priceEuro, &qty)

  totalCent := int64(priceEuro) * 100 * int64(qty) // *100 pour passer en centimes 
  fmt.Println(totalCent) // par exemple 19900
}
```

---

## Calcul de pourcentage

```go
func main(){
  var priceCents int64
  var qty int64
  var discountPercent int64

  fmt.Scan(&pricecentsm, &qty, &discountPercent)

  subtotal := priceCents * qty // on calcul le total en centime
  discount := subtotal * discount / 100 // on calcul la valeur de la ristourne
  total := subtotal - discount // on obtient le prix final

  fmt.Println(total)
}
```

## Calcul de reste

```go
func main(){
  totalCommande := 1000 //10euro 00 cents
  people := 3

  perPerson := totalCommande / people
  remainder := totalCommande % people

  fmt.Println(perPerson, remainder) // 333cents 1reste
}
```
