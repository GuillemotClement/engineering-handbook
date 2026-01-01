# JavaScript 

## Objet

### Fusion d'objets

```js 
const finalObject = {
  ...ancienObject, // recopie le contenu de l'objet
  monNouveauCham: "valeur" // ajout du nouveau champ
}

// exemple
// un objet data + une string userId 
const newClub = {
  ...data, // objet payload
  ownerId: userId, // id de l'user qui vient créer le club
}
```

### Retirer des champs d'un objet 

```js
// response contient les autres data
// le reste est filtré car on retourne le response qui contient les données
const { userId, createdAt, longitude, latitude, image, ...response } = newClub;
return res.status(201).json(response);
```


## Variable

```js
// old syntaxe
var maVar = 7;

// variable 
let maVar = 8;

// constante 
const maVar = 8;
```

- `var`: ancienne syntaxe. Est déprécied.
- `let`: dans le cas ou la variable doit changer
- `const`: la valeur ne peut pas changer.