# JavaScript 
Technologie permettant d'ajouter des élément dynamique sur une page web. Il permet de réagir aux actions utilisateurs, de modifier le contenu d'une page web, d'intéragir avec des serveurs, etc 

## MULTIMEDIA

Les éléments `<audio>` et `<video>` fournissent une API pour gérer la lecture via JS. 

```html
<video id="myVideo" src="videofile.mp4" controls></video>
<button onclick="playVideo()">Play</button>
<button onclick="pauseVideo()">Pause</button>
<button onclick="stopVideo()">Stop</button>
```

```js
const video = document.getElementById("myVideo");

function playVideo() {
  video.play();
}

function pauseVideo() {
  video.pause();
}

function stopVideo() {
  video.pause();
  video.currentTime = 0;
}
```

L'API fournis plusieurs type d'event :
- `play`: se produit lorsque la lecture commence
- `pause` : se produit lorsque la lecture se met en pause 
- `ended`: se produit lorsque la lecture se termine
- `timeupdate`: se produit périodiquement au fur et à me sure que le temps de lecture change

```js
const video = document.getElementById("myVideo");

video.addEventListener('play', function() {
  console.log('Vidéo en cours de lecture');
});

video.addEventListener('pause', function() {
  console.log('Vidéo en pause');
});

video.addEventListener('ended', function() {
  console.log('Vidéo terminée');
});

video.addEventListener('timeupdate', function() {
  console.log('Temps actuel : ' + video.currentTime);
});
```




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