https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Language_overview
https://github.com/getify/You-Dont-Know-JS
https://javascript.info/
https://developer.mozilla.org/en-US/docs/Web/JavaScript
## Array 

```js
const t = [1, -1, 3]; // déclaration du tableau 

t.push(5); // ajout de 5 au tableau 

console.log(t.length) // 4 est affiché
console.log(t[1])     // -1 est affiché => affiche le deuxième élément

// parcours le tableau, et execute l'instruction pour chaque élément
t.forEach(value => {
  console.log(value)  // les chiffres 1, -1, 3, 5 sont affichés, chacun sur une ligne
})
```

### forEach

Prends une callback, et exécute une instruction sur chaque éléments du tableau.

### concat()

Avec React, on utilise des structures de données immuables. Pour cette raison, on priviligie cette méthode pour "ajouter" des éléments à un tableau.

Cette méthode créer un nouveau tableau dans lequel le contenu de l'ancien tableau et le nouvel élément sont inclus. La méthode retourne ce nouveau tableau 

```js
const t = [1, -1, 3]

const t2 = t.concat(5)

console.log(t)  // [1, -1, 3] est affiché => premier tableau
console.log(t2) // [1, -1, 3, 5] est affiché => second tableau 
```

----

## OBJET 

### Object.keys()

Cette méthode retourne un tableau des clé d'un objet.

### Object.values() 

Cette méthode retourne  un tableau des valeurs d'un objet 

On peut utiliser ces méthodes pour venir faire des recherches dans un objet. L'utilisation des tableau est prévilieger lorsque l'on veux travailler avec des données ordonné 

```js
const allVoteCounts = Object.values(votes); // on obtient un tableau avec les valeurs de l'objet
const maxVotes = allVoteCounts.length === 0 ? 0 : Math.max(...allVoteCounts); // on recupère la valeur max

	// on transforme les clé de l'objet en tableau 
	// .find => prend en argument la clé une par une 
	// on cherche pour chaque clé de l'objet, si la valeur est égale à la valeur max du vote 
	// on retourne directement l'anecdate dans la vriable
const winnerText = Object.keys(votes).find((key) => votes[key] === maxVotes); // on récupère l'anecdote avec la meilleur note
```