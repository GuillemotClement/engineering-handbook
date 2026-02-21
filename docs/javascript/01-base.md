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