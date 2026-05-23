# Binaire 

Un nombre peut etre percu comme un ensemble d'interrupteurs (bits) 1/0. Cette approche est utile lorsque l'on souhaite stocker plusieurs attributs independants dans un seul nombre et verifier rapidement ce qui est actif.

Le senario le plus courant est celui des flags : un ensemble d'options, d'autorisations ou de mode. Par exemple, un utilisateur peut avoir les droits `read`, `write`, `execute`. On peut stocker les trois valeurs booleanne, mais on peut egalement utiliser dans un seul nombre et venir manipuler les bits.

| Operateur | Nom | Sens | Image | 
| --- | --- | --- | ---- |
| `1 << n` | decalage a gauche | permet d'obtenir le masque d'un bit |  place 1 a la position n |
| `a \| b` | OR | activer les drapeaux de `b` dans `a` | fusion |
| `a & b` | AND | verifier la presence d'un drapeau | intersection | 
| `a &^ b` | AND NOT | retirer un ou plusieurs flag selon le masque | eteindre | 
| `a ^ b` | XOR | basculer les bits | toggle | 
| `^a` | NOT | inverser tous les bits | tout retourner | 
| `a >> n` | decalage a droite | extraite un bit | deplacer vers les bits de poids faible | 


## Ecriture binaire 

En Go, l'ecriture binaire est une facon d'ecrire des masques directement dans le code pour pouvoir les lire a l'oeil.
Pour cela, les litteraux numerique ont des prefixe : 

- `0b...` : ecriture binaire
- `0x...` : hexa
- `0o...` : octal 

```go 
const (
  a = 12      // decimal 
  b = 0b1100  // binaire
  c = 0x0c    // hexa 
  d = 0o14    // octal 
)
```

L'utilisation du binaire permet de voir directement quel bit est actif. 

Pour eviter qu'un masque long devienne ilisible, on peut utiliser `_` pour regrouper les bits.

Un masque est un nombre dans lequel certain bits nous interessent. Souvent un masque contient exactement un bit active et represente un flag.

On utilise `%b` pour afficher les bianire dans `printf`

```go 
const mask = 0b1111_0000_0011_0101
```

## Decalage a gauche << 

Lorsque l'on trouve du code du type `1 << n`, il s'agit generalement de drapeau. 

Si on regarde en binaire : `1` correspond au masque `0b0001`.

Le decalage permet de deplacer cette unite vers la gauche et de changer la valeur du masque.

```go 
func main(){
  var mask uint = 1 << 3
  fmt.Printf("mask=%b valeur=%d", mask, mask) // mask=1000 valeur=8
}
``` 

## Activer un drapeau - |

L'operation d'activation de drapeau se fait avec `|` (OR binaire). Cela permet de passer un bit a `1`.

L'operateur permet de combiner les bits actif

```go 
func main(){
  var a uint := 1 << 0 // 0001
  var b uint := 1 << 2 // 0100

  var flags uint = a | b 
  fmt.Printf("%b\n", flags) // 101
}
```

## Verifier si un drapeau est actif 

Le verification de flag se fait generalement de cette maniere `(flags & SomeFlag) != 0`. Cela se lit comme "intersection entre l'ensemble des drapeaux et le masque n'est pas vide".
Autrement dit, si le bit souhaiter est actif alors avec `&` il restera et le resultat ne sera pas nul 

Par exemple, pour verifier les droits d'un utilisateur : 

```go
func main(){
  const (
    read uint = 1 << 0 // 001
    write utin = 1 << 1 // 010
  )

  var perms uint = read // lecture seul (001)

  fmt.Println(perms&write != 0) // false 
  fmt.Println(perms&read != 0) // true 
}
```

## Retirer un flag - &^ 

Pour desactiver un flag, on utilise l'operateur `&^`.

```go 
func main(){
  const (
    read uint = 1 << 0 // 001
    write uint = 1 << 1 // 010
    exec utin = 1 << 2 // 100
  )

  // on viens combiner les bits
  var perms uint = read | write | exec // 111

  // on passe le bite correspond au write a 0 
  perms = perms &^ write // 101
}
```

---

## Bascule et inversion 

Le synbole `^` a deux sens : 
  
  - `a ^ b` : XOR (OU exclusif) binaire. Le resultat vaut 1 si les deux bits sont differents, et resultat vaut 0 si les deux bits sont identiques
  - `^ a` : NON binaire (inversion de tous les bits) => a utiliser avec precaution, et pas adapter au flag

On vient aligner les bits, et faire la comparaison.

```go 
func main(){
  // XOR
  var x uint = 0b1010
  var y uint = 0b1100
  // on compare les bits de x et y pour construire le bit final
  fmt.Println("%b\n", x^y) // 0110

  // toggle de bit
  const debug uint = 1 << 0 // 0001 

  var flags uint = 0
  // on compare les deux bits
  // flags = 0000
  // debug = 0001
  flags = flags ^ debug // debug active // 1 
  flags = flags ^ debug // si les deux bits sont 1 alors on passe a 0

  fmt.Println(flags) // 0
}
```

---

## Decalage a droite - >> 

Le decalage a droite est utiliser pour "lire" un bit ou un petit champ de bits. Par exemple, on veux connaitre la valeurd du bit numero 3. On peut decaler le nombre de 3 vers la droite puis appliquer `&1`.

`>>` place le bit voulu en position `0` et `&1` ne garde que lui 

```go
func main(){
  var flags uint = 0b10100 // le bit 4 et 2 est actif
  
  // on recupere la valeur du bit 
  bit2 := (flags >> 2) & 1 // 1 
  bit3 := (flags >> 3) & 1 // 0
}
```

---

## Debug de masque 

Pour le debug des flag, on ne peut pas utiliser la notation decimal. On souhaite afficher directement bit pour voir directement quel est le flag actif.

L'affichage des zero initial ne s'affiche pas, il faut donc set une longueur fix pour obtenir le masque complet de maniere lisible.

```go 
func main(){
  var x uint = 5
  fmt.Printf("%b", x) // 101 
  fmt.Printf("%08b", x) // 00000101
}
```

---

## Exemple : droits utilisateurs avec flags 

On implemente un modele de droits d'acces : lecture, ecriture, execution.

L'utilisateur saisir le nombre actuel de droit (5 par exemple). On verifie et affiche le droit 

```go 
package main 

import "fmt"

func main(){
  // declaration du type personalise
  type Perm uint

  // declaration des flags 
  const (
    PermRead Perm = 1 << iota // 001
    PermWrite                 // 010
    PermExec                  // 100
  )

  // temp
  // combinaison des bits ======================
  var p Perm = PermRead | PermExec 
  fmt.Printf("perms=%b\n" uint(p)) // perms=101 
  // ============================================

  // recuperation de la saisie
  var raw uint // droit user
  var op int   // operation verifier
  fmt.Scan(&raw, &op)

  p := Perm(raw) // contient le masque des droits

  allowed := false 
  // on check chaque type d'operation
  if op == 1 {
    allowed = (p & PermRead) != 0
  } else if op == 2 {
    allowed = (p & PermWrite) != 0
  } else if op == 3 {
    allowed = (p & PermExec) != 0
  } else if op 99 {
    // avec le nombre 99 on retire le droit d'ecriture
    p = p &^ PermWrite // on toggle ce bit qui correspond au droit d'ecriture
    fmt.Printd("%d %b\n", uint(p), uint(p)) // 5 101
    return
  }

  if allowed {
    fmt.Println("allowed")
  } else {
    fmt.Println("denied")
  }
}
```
