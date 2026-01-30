# Base HTML 

Le HTML est utilisé pour définir la structure et le contenu d'une page web.

```html
<!DOCTYPE html>
<html>
    <head>
        <-- Balises de service -->
        <title>Titre de la page</title>
    </head>
    <body>
        <-- Document principal -->
        <h1>Salut, le monde !</h1>
        <p>C'est ma première page web.</p>
    </body>
</html>
```

-  `html`: indique que le document est du HMTL ⇒ permet au navigateur d’afficher correctement la page. Marque le début et la fin du document.
- `head`: balise qui contient les informations de service sur la page. Ces infos ne sont pas directement affiché sur l page, mais sont utilisé par le navigateur et les moteurs de recherche.
- `title`: définit le titre de la page qui s’affiche dans l’onglet du navigateur
- `body`: contient le contenu visible de la page (texte, images, liens, etc). Tout ce que l’utilisateur voir à l’écran.
- `h1`: titre de premier niveau
- `p`: permet d’affiche un paragraphe
- `br`:
- `hr`: permet de créer une ligne
### Attributs
```html
<tag name1="value1" name2="value2">

<a href="<http://codegym.cc/about>" target="_blank">
    Lien vers quelque chose d'intéressant
</a>
```

La balise ouvrante peut contenir des info spéciales de services. Ce sont les attributs. Chaque attribut est présent sous forme de `nom` et `valeur`.

Les caractères spéciaux doivent utiliser ces écritures HTML pour être utilisé.

| Nom du caractère   | Caractère | Écriture HTML |
| ------------------ | --------- | ------------- |
| Guillemets doubles | "         | `&quot;`      |
| Esperluette        | &         | `&amp;`       |
| Symbole inférieur  | <         | `&lt;`        |
| Symbole supérieur  | >         | `&gt;`        |
| Espace             |           | `&nbsp;`      |
| Guillemet simple   | '         | `&#39;`       |
### Images

```html
<img src="image.jpg" alt="Description de l'image">
```

Utilisée pour intégrer des images sur un site web.

- `src`: indique le chemin de l’image
- `alt`: décrit l’image ⇒ utilisé si le navigateur ne parvient pas à trouver le fichier correspondant

### Lien

```html
<a href="<https://www.example.com>" target="_blank">Aller sur Example.com</a>
```

Permet de créer des hyperlien.

- `href`: indique l’URL de la page ou la ressource sur lequel point le lien
- `target`: spécifie si le lien s’ouvre sur l’onglet, ou dans un nouvel onglet

---

## MISE EN FORME DU DOCUMENT 

### Paragraphe 

La balise `<p>` est utilisée pour créer des paragraphes de texte. Ils permettent de diviser des blocs logique de texte, améliore la lisibilité et la compréhension des informations.

```html
<p>Ceci est le premier paragraphe de texte. Il contient une certaine quantité d'informations.</p>
<p>Ceci est le deuxième paragraphe de texte. Il est séparé du premier paragraphe.</p>
```

### Texte gras 

La balise `<b>` permet de mettre du texte en gras. Cela permet de souligner l'importance de certains mots ou phrase.
```html
<p>Ceci est un texte <b>important</b>.</p>
```

### Italique 

La balise `<i>` permet de mettre du texte en italique. Cela permet de marquer des termes, des titres, etc 
```html
<p>Ceci est un <i>terme</i>, qui est mis en italique.</p>
```

### Titre 

Les balise `<h1>` jusqu’à `<h6>` permettent de définir des titre du plus important au moins important.
```html
<h1>Titre principal de la page</h1>
<h2>Sous-titre de niveau 2</h2>
<h3>Sous-titre de niveau 3</h3>
```

### title 

Balise `<title>` que l'on retrouve dans le `<head>`. Elle permet de définir le titre qui est afficher dans l'onglet du navigateur.

### meta 

La balise `<meta charset="UTF-8">` définis dans le `<head>` définit l'encodage des caractères du document. 

La balise `<meta name="viewport" content="width=device-width, initial-scale=1.0">` définit le `<head>` permet de garantir que l'afficahge sera correct sur les appareils mobiles. Elle définit la largeur de la zone de visualisation égale à la largueur de l'écran de l'appareil et le niveau de zoom initial

### link 

La balise **`<link rel="stylesheet" href="styles.css">`** permet de lier le fichier CSS externe pour le style de la page. 

La balise **`<script src="script.js" defer></script>`** permet de lier le fichier HTML au fichier contenant les scripts JS. `defer` demande au navigateur de l'exécuter après le chargement du HTML.

### style 

Permet d'ajouter du CSS sur un élément HTML
```html
<p style="color: blue; font-size: 16px;">Ceci est un exemple de style intégré.</p>
```

On peut également placer cette balise dans le `head`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Exemple de CSS</title>
    <style>
        body {
            background-color: lightgray;
        }
        p {
            color: blue;
            font-size: 16px;
        }
    </style>
</head>
<body>
    <p>Ceci est un exemple d'utilisation des styles dans la balise <style>.</p>
</body>
</html>
```

### script 
Permet d'intégrer du code JS dans une page HTML.
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Introduction à JavaScript</title>
</head>
<body>
    <h1>Bienvenue sur mon site!</h1>
    <script>
        // Ton code JavaScript ici alert('Salut, le monde!');
    </script>
</body>
</html>
```

#### Lier un fichier JS 
Généralement, on viendras déclarer le code Javascript dans un fichier, qui sera connecter au HTML.

On utilise la balise `<script>` et via l'attribut `src`, on indique le path vers le fichier JavaScript.

L'attribut `defer` indique au navigateur d'exécuter le script une fois que tout le HTML est chargé et affiché.
```html
</h1>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Introduction à JavaScript</title>
    <script src="script.js" defer></script> <-- connexion au JS -->
</head>
<body>
    <h1>Bienvenue sur mon site!</h1>
</body>
</html>
```

On retrouve ce JS
```js
//script.js:
// Ton code JavaScript ici
alert('Salut, le monde!');
```
#### JavaScript inline 
On peut également déclarer le JS directement sur une balise HTML 
```html
<p style="color:red" onclick="this.style.color = 'blue';">Rouge</p>
```

- `onclick`: attribut HTML qui spécifie une fonction exécutée lors d'un clic sur l'élément
- `this` : fais référence à l'élément cliqué 
- `style.colo`: propriété css qui modifie la couleur de l'élément.

---

## LISTE 

### ul 

Permet de déclarer une liste non numérotée. 
```html
<ul>
  <li>Premier élément de la liste</li>
  <li>Deuxième élément de la liste</li>
  <li>Troisième élément de la liste</li>
</ul>
```

### ol 
Permet de déclarer une liste numérotée 
```html
<ol>
  <li>Premier élément de la liste</li>
  <li>Deuxième élément de la liste</li>
  <li>Troisième élément de la liste</li>
</ol>
```

### li 
Permet de déclarer les éléments d'une liste. 

Ils doit toujours être imbriqué dans une liste.

### Liste imbriquée 
```html
<ul>
  <li>Premier élément de la liste</li>
    <ul type="circle">
      <li>Premier élément imbriqué</li>
      <li>Deuxième élément imbriqué</li>
    </ul>
  <li>Deuxième élément de la liste</li>
  <li>Troisième élément de la liste</li>
</ul>
```

---

## TEXTE 

- `<b>` : texte en gras sans signification particulière 
- `<strong>` : texte en gras avec importance
- `<i>` : texte en italique 
- `<em>` : text italique avec importance 





### pre 
Utilisé pour afficher du texte en HTML tel qu'il est écrit en conservant tous les espaces, saut de ligne et tabulation.

Utilise pour représenter des extraits de code ou du texte préformaté 
```html
<pre>
  function sayHello() {
    console.log("Hello, world!");
  }
</pre>
```

### code 
Cet balise est utilisé pour indiquer des extrait de code ou d'autre données lisibles par machines dans le texte.

Elle est généralement utilisé pour mettre en évidence des lignes de code, des variables, des fonctions et d'autres éléments de programmation 
```html
<p>Pour afficher un message dans la console, utilisez la fonction <code>console.log()</code>.</p>
```

#### Utilisation 
On utilise généralement ces deux balises pour la mise en forme des blocs de code.
```html
<pre>
  <code>
    function helloWorld() {
      console.log('Hello, world!');
    }
  </code>
</pre>
```

#### Mettre en évidence la syntaxe
On peut utiliser des lib comme **Prism.js** ou **Highlight.js** pour mettre en valeur le code :
```html
<html>
  <head>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/10.7.2/styles/default.min.css">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/10.7.2/highlight.min.js"></script>
    <script>hljs.initHighlightingOnLoad();</script>
  </head>
  <body>
    <pre>
      <code>
        function helloWorld() {
          console.log('Hello, world!');
        }
      </code>
    </pre>
  <body>
</html>
```

---

## BLOC 

Les éléments de type bloc sont utilisées pour créer la structure et organiser le contenu d'une page web. Ils aident à diviser le contenu en bloc, segments logique.

Ces éléments commencent une nouvelle ligne et occupent toutes la largueur disponible.

- **Elément bloc**: se place sur une nouvelle ligne et occupe toute la largeur 
- **Marge automatique**: les navigateur ajoutent des marges avant et après 
### p

La balise `<p>` permet de créer des paragraphes de texte. C'est un élément bloc, ce qui signifie que chaque paragraphe commence  sur une nouvelle ligne, et possède des marges en haut et en bas.
```html
<p>
  Ceci est le premier paragraphe de texte. Les paragraphes sont utilisés pour diviser le texte en blocs logiques,
  améliorant ainsi sa lisibilité.
</p>
<p>Ceci est le second paragraphe de texte. Chaque paragraphe commence sur une nouvelle ligne.</p>
```

### div 
Conteneur bloc universel utilisé pour regroupé des éléments et appliquer des styles à ceux ci. 

Elle ne possède pas de style intégrés et est utilisée uniquement pour structurer le contenu.
```html
<div class="container">
  <h2>Titre du bloc</h2>
  <p>Un peu de texte à l'intérieur du bloc div.</p>
</div>
```

### span 
Eléments inline utilisés pour mettre mettre en évidence une partie de texte ou un autre contenu à l'intérieur d'éléments bloc. 

Elle n'as pas de styles intégrés et sert à appliquer des style CSS et des script Js à des parties spécifiques de texte.
```html
<p>Ce texte contient un <span class="highlight"> mot</span> surligné, qui est différent du reste du texte.</p>
```

- **Elément inline**: ne créer pas de nouvelle ligne, et occupe seulement la largeur nécessaire 
- **Fléxiblité** utilisé pour appliquer des styles à des parties spécifiques du texte ou d'autres éléments
- **Souvent utilisé avec CSS ou Js**

---

## BLOC DE CITATION 

### blockquote 
Cette balise est utilisée pour mettre en évidence de gros blocs de texte cité. Les navigateurs généralement ces citations avec un retrait à gauche pour les distinguer du texte principal.
```html
<blockquote>
  "The only limit to our realization of tomorrow is our doubts of today."
</blockquote>
```

- Elément bloc 
- S'affiche souvent avec un retrait à gauche 
- Peut contenir d'autres éléments bloc et inline 
- Peut utiliser l'attribut cite pour spécifier l'URL de la source de la citation 

#### cite 

L'attribut `cite` est utilisé pour spécifier la source de la citation. L'attribut ne s'affiche pas visuellement, il aide à conserver l'information sur l'origine de la citation, ce qui est utile pour les moteurs de recherche et d'autres systèmes automatisés de traitement de texte 
```html
<blockquote cite="https://example.com/quote-source">
  "The only limit to our realization of tomorrow is our doubts of today."
</blockquote>
```

### cite 
La balise `<cite>` est utilisée pour indiquer la source d'une citation ou le titre d'une œuvre, comme un livre, un article, un file. 

Le texte est généralement affiché en italique.
```html
<p>Source de la citation : <cite>Franklin D. Roosevelt</cite></p>
```

- Elément inline 
- S'affiche en italique 
- Utilisé pour indiquer les sources de citations ou les titres d'oeuvres
```html
<blockquote  cite="https://example.com/quote-source">
  "The only limit to our realization of tomorrow is our doubts of today."
  <cite>— Franklin D. Roosevelt</cite>
</blockquote>
```

### q 
La balise `<q>` est utilisée pour encadrer de courte citation intégrées dans le texte. Les navigateurs ajoutent automatiquement des guillemets autour du texte inclus dans la balise `<q>`.
```html
<p>Comme Albert Einstein l'a dit, <q>L'imagination est plus importante que la connaissance</q>.</p>
```

- Elément inline 
- Ajoute des guillemets autour de la citation 

On peut également ajouter l'attribut `cite` pour spécifier l'url de la source de la citation 
```html
<p>Comme Albert Einstein l'a dit, <q cite="https://example.com/einstein-quote">L'imagination est plus importante que la connaissance</q>.</p>
```

---

### SYMBOLES SPECIAUX 

### Echappement des caractères 
En HTML, les caractères spéciaux sont également connus sous le nom de "character entities", sont utilisés pour représenter des caractères qui sinon seraient interprétés comme faisant partie de la syntaxe HTML.

```html
<p>Utilisez &lt; et &gt; pour indiquer des chevrons, et &amp; pour l'esperluette.</p>
```

| Nom du caractère   | Caractère | Écriture HTML |
| ------------------ | --------- | ------------- |
| Guillemets doubles | "         | `&quot;`      |
| Esperluette        | &         | `&amp;`       |
| Symbole inférieur  | <         | `&lt;`        |
| Symbole supérieur  | >         | `&gt;`        |
| Espace             |           | `&nbsp;`      |
| Guillemet simple   | '         | `&#39;`       |

- © : &copy;
- ® : &reg;
- ™ : &trade;
- € : &euro;
- £ : &pound;
- ± : &plusmn;
- × : &times;
- ÷: &divide;
- ≤ : &le;
- ≥: &ge;
- ∞ : &infin;
- √ : &radic;
-  " : &quot;
- ' : &apos;
- ← : &larr;
- ↑ : &uarr;
- → : &rarr;
- ↓ : &darr;
- ↔ : &harr;
- ♠ : &spades;
- ♣ : &clubs;
- ♥ : &hearts;
- ♦ : &diams;

On peut également insérer des emoji : 
```html
<p>Visage souriant : &#x1F600;</p> 
<p>Cœur : &#x2764;&#xFE0F;</p> 
<p>Pouce levé : &#x1F44D;</p>
```

---

## TABLEAU  

Les tableaux sont utilisées pour organiser les données. 

- `<table>` : conteneur exterieur du tableau 
- `<caption>`: permet d'ajouter un titre au tableau 
- `<tr>` : définit une ligne du tableau 
- `<th>` : définit une cellule de header 
- `<td>`: définit une cellule 
- `<thead>` : header du tableau 
- `<tbody>`: body du tableau 
- `<tfoot>` : footer du tableau 

```html
<table>
  <caption>Résultats de l'examen</caption>
  <thead>
    <tr>
      <th>Nom</th>
      <th>Note</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Ivan</td>
      <td>91</td>
    </tr>
    <tr>
      <td>Maria</td>
      <td>94</td>
    </tr>
    <tr>
      <td>Nina</td>
      <td>89</td>
    </tr>
  </tbody>
  <tfoot>
    <tr>
      <td>Moyenne</td>
      <td>91.33</td>
    </tr>
  </tfoot>
</table>
```

### Fusions des cellules 

- `colspan`: permet de fusionner les cellules horizontalement; indique combien de colonne la cellule doit couvrir.

```html
<table border="1">
  <tr>
    <th>Nom</th>
    <th>Âge</th>
    <th>Ville</th>
  </tr>
  <tr>
    <td>Alice</td>
    <td>25</td>
    <td>Londres</td>
  </tr>
  <tr>
    <td colspan="2">Total</td> # fusion sur 2 cellules
    <td>2 enregistrements</td>
  </tr>
</table>
```

- `rowspan`: fusion verticale.
```html
<table border="1">
  <tr>
    <th>Nom</th>
    <th>Projet</th>
    <th>Ville</th>
  </tr>
  <tr>
    <td rowspan="2">Alice</td>
    <td>Projet A</td>
    <td>Londres</td>
  </tr>
  <tr>

    <td>Projet B</td>
    <td>Batoumi</td>
  </tr>
</table>
```

### Styliser un tableau 

#### Attribut de table

- `border`: l'attribut permet de définir l'épaisseur de la bordure du tableau.
- `cellpadding`: définit le padding des cellules 
- `cellspacing`: définit la distance entre les cellules
- `height`: définit la hauteur.
- `width`: définit la largeur
- `align` : définit l'alignement du tableau sur la page
- `bgcolor`: définir la couleur de fond du tableau ou des cellules

```html
```html
<table border="1" cellpadding="10" cellspacing="5" width="80%" align="center" bgcolor="#e0e0e0">
  <caption>Liste des étudiants et leurs notes</caption>
  <thead bgcolor="#c0c0c0">
    <tr>
      <th align="left">Nom</th>
      <th align="center">Âge</th>
      <th align="right">Ville</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Ivan</td>
      <td align="center">30</td>
      <td align="right">Londres</td>
    </tr>
    <tr>
      <td colspan="2" align="center">Marie et Pierre</td>
      <td align="right">Liverpool</td>
    </tr>
    <tr>
      <td rowspan="2" valign="middle">Anna</td>
      <td>25</td>
      <td>Manchester</td>
    </tr>
    <tr>
      <td>27</td>
      <td>Leeds</td>
    </tr>
  </tbody>
  <tfoot>
    <td colspan="3" align="center">Fin du tableau</td>
  </tfoot>
</table>
```

---

## FORMATAGE DE TEXTE 

- `<b>` : texte en gras sans signification
- `<string>` : texte en gras avec accent sur l'importance 
- `<i>` : texte en italique 
- `<em>` : texte en italique avec emphase logique o sémantique 
- `<ul>` : texte souligné 
- `<ins>` : souligne et indique un texte inséré
- `<small>` : texte en petit pour des information secondaire 
- `<s>` : barre le texte 
- `<del>` : barre et indique une suppression de texte
- `<mark>` : met en surbrillance le texte 
- `<sub>` et `<sup>` : créer un texte en indice ou exposant 

```html
<p>Ce texte est <b>gras</b>, et ce texte est <strong>très important et gras</strong>.</p>

<p>Ce texte est <i>italique</i>, et ce texte est <em>très important et italique</em>.</p>

<p>Ce texte est <u>souligné</u>, et ce texte est <ins>inséré et souligné</ins>.</p>

<p>Ce texte est <s>barré</s>, et ce texte est <del>supprimé et barré</del>.</p>

<p>Ce texte est <mark>en surbrillance</mark> pour attirer l'attention.</p>

<p>Ceci est le texte principal, et ceci est du <small>texte petit</small>.</p>

<p>Ceci est un texte avec un indice H<sub>2</sub>O et un exposant x<sup>2</sup>.</p>
```

- `<br>`: saut de ligne 
- `<hr>` : insère une ligne horizontale 
- 
```html
<p>Ceci est un texte avec un saut de ligne<br>à l'intérieur d'un seul paragraphe.</p>

<p>Texte au-dessus de la ligne.</p>
<hr>
<p>Texte en dessous de la ligne.</p>
```

--- 

## IMAGE 

La balise `<img>` permet d'insérer une image dans un document HTML.

```html
<img src="images/photo.jpg" alt="Description de l'image" width="500" height="300">
```

- `src` : spécifie le chemin du fichier d'image qui doit être affiché sur la page 
- `alt` : fournit une description textuelle de l'image
- `width` : définit la largeur de l'image en pixel. Si l'attribut `heigt` n'est pas spécifié, l'image conserve ses proportions lors du changement de largeur
- `heigth` : définit la hauteur fixe 

### Optimisation des images 

L'optimisation des images est un processus qui vise à réduire la taille des images sans perte de qualité. Cela permet d'accélérer le chargement des pages web, d'améliorer le confort d'utilisation et bootse le classement du site dans les moteurs de recherche.

#### Choisir le bon format 

Chaque format a ses propres avantages et domaines d'applications : 

**JPEG**
- Application: photo et image avec beaucoup de couleur 
- Avantage: haute compression avec perte, ce qui permet de réduire considérablement la taille de fichier 
- Inconvénient : perte de qualité lors d'une sauvegarde répétée 

**PNG**
- Application: images avec transparence et graphiques avec lignes nettes, texte
-  Avantage: compression sans perte, conserve une haute qualité d'image 
- Inconvénient : taille de fichier plus grand 

**GIF**
- Application: animations et images avec palette de couleur limitées
- Avantage: support de l'animation et de la transparence 
- Inconvénient: palette de couleur limitée

**WebP**
- Application: format moderne pour différents types d'images 
- Avantage: support de la compression avec et sans pertes, animations et transparence, haute compression 
- Inconvénients: support pas encore universel dans tous les navigateurs 

**SVG**
- Application: images vectorielles, logos et icônes 
- Avantage: mise à l'échelle sans perte de qualité, petite taille de fichier 
- Inconvénients: convient uniquement pour les graphiques vectoriels 

#### Compression des images 

La compression permet de réduire la taille du fichier tout en maintenant une qualité d'image acceptable 

**Compression avec perte**
Cette méthode supprime une partie des données de l'image, ce qui réduit la taille du fichier avec une perte de qualité minime. Utilisé par le JPEG et Webp

- **TinyPNG/TinyJPG**: outils en ligne pour compresser les images. 
- **ImageOptim** : application Mac 
- **Squoosh**: outil de google 

**Compression sans perte** : Cette méthode réduit la taille du fichier sans supprimer les données, conservant la qualité d'origine de l'image. Utilisé par PNG et WebP.

- **PNGGauntlet** window
- **OptiPNG**: utilitaire CLI 

#### Redimensionner les images 

Redimensionner les images à la taille nécessaire avant de les télécharger sur le site aide à réduire leur taille et accélère le chargement de la page.
- **Adobe Photoshop**
- **GIMP**
- **Online Image Resizer** 

Spécifier les attributs `width` et `height` dans les balise `img` permet au navigateur de réserver de l'espace pour les images avant leur chargement, évitant ainsi le décalage lors du chargement de la page.

#### Recommandation 

**Lazy Loading**
Le chargement différé permet de charger les images uniquement lorsqu'elle apparaissent dans la zone visible de l'écran.
```html
<img src="example.jpg" alt="Description de l'image" loading="lazy">
```

**Mise en cache**
Configurer la mise en cache serveurs pour les images permet de charger à nouveau les images réutilisées depuis le cache du navigateur.

--- 

## AUDIO 

```html
<audio src="audiofile.mp3" controls></audio>
```

- `<audio>` : permet d'insérer un fichier audio. 
- `src` : indique le chemin vers le fichier 
- `controls`: permet d'afficher les contrôles standards (lecture, pause, etc)
- `loop`: répète la lecture de l'audio en boucle
- `autoplay`: lit automatiquement l'audio au chargement de la page
- `muted` : coupe le son lors du chargement 
- `preload`: indique comment le navigateur doit charger l'auto 

### Source multiple 
Il est possible de renseigner plusieurs sources avec la balise `<source>`pour permettre au navigateur de choisir le format audio supporté 

```html
<audio controls> 
	<source src="audiofile.mp3" type="audio/mpeg"> 
	<source src="audiofile.ogg" type="audio/ogg"> 
	<source src="audiofile.wav" type="audio/wav">
	Votre navigateur ne supporte pas l'élément audio. 
</audio>
```

### Format supportés 

- **MP3**: format audio le plus populaire. Il est supporté par tous les navigateurs. Il utilise une compression avec perte, ce qui permet de réduire la taille du fichier tout en maintenant une qualité sonore élevé.
```html
<audio src="audiofile.mp3" type="audio/mpeg"></audio>
```

- **OGG**: format de compression audio ouvert avec perte, supporté par la plupart des navigateurs. Il offre une bonne qualité sonore et est souvent utilisé comme alternative au MP3
```html
<audio src="audiofile.ogg" type="audio/ogg"></audio>
```

- **WAV**: format audio non compressé qui offre une haute qualité sonore. Le fichier est plus lourd que les format précédents.
```html
<audio src="audiofile.wav" type="audio/wav"></audio>
```

---

## VIDEO 
```html
<video controls>
  <source src="videofile.mp4" type="video/mp4">
  <source src="videofile.webm" type="video/webm">
  <source src="videofile.ogv" type="video/ogg">
  Votre navigateur ne supporte pas l'élément vidéo.
</video>
```

- `src`: permet de définir la source du fichier vidéo 
- `controls`: permet d'ajouter les contrôles de lecture
- `autoplay`: lit automatiquement la vidéo au chargement de la page
- `loop`: répète la lecture de la vidéo à la fin 
- `muted`: désactive le son de la vidéo 
- `preload`: indique comment le navigateur doit charger la vidéo
- `width` & `heigt`: permet de définir la taille de l'emplacement 
- `poster`: indique une image de prévisualisation qui s'affiche avant le début de la lecture de la vidéo 

L'attribut `preload` peut prendre trois valeurs : 
- `none` : le navigateur ne précharge pas
- `metadata`: le navigateur doit charger uniquement les métadonnées 
- `auto`: charge complètement la vidéo 
```html
<video src="videofile.mp4" preload="auto"></video>
```

### Format supportés 

- **MP4**: format vidéo le plus populaire. Il utilise une compression avec perte et offre une bonne qualité vidéo avec une taille de fichier relativement petite.
```html
<video src="videofile.mp4" type="video/mp4"></video>
```

- **WebM**: format de compression moderne
```html
<video src="videofile.webm" type="video/webm"></video>
```

- **OGG** : offre une bonne qualité et est souvent utilisé comme alternative 
```html
<video src="videofile.ogv" type="video/ogg"></video>
```

---
## ATTRIBUT MULTIMEDIA 

Les éléments `<audio>` et `<video>` fournissent de nombreux attributs pour gérer la lecture de contenu. 

### crossorigin 

Cet attribut indique comment le navigateur doit gérer les requêtes pour les fichiers multimédia situé sur d'autre domaines.
- `anoymous`: la requête est effectuée sans inclusion des informations d'identifications (cookie, en-tête d'autorisation)
- `use-credentials`: la requête est effectuée avec l'inclusion des informations d'identification 
```html
<audio src="https://example.com/audiofile.mp3" controls crossorigin="anonymous"></audio> 
<video src="https://example.com/videofile.mp4" controls crossorigin="use-credentials" width="800" height="450"></video>
```

### track 
Permet d'ajouter des sous-titres, piste textuelle et description. 
```html
<video controls width="600">
  <source src="videofile.mp4" type="video/mp4">
  <track src="subtitles_en.vtt" kind="subtitles" srclang="en" label="English">
  <track src="subtitles_es.vtt" kind="subtitles" srclang="es" label="Spanish">
  Your browser does not support the video element.
</video>
```

---

## FIGURE & FIGCAPTION

Ces balises offrent un moyen sémantique d'ajouter des images et éléments multimédia avec des légendes.
Ces balises permettent d'améliorer la structure du contenu, le rendant plus accessible et compréhensible pour les utilisateurs et moteurs de recherche.

### figure 

Permet de désigner un contenu autonome qui illuste ou explique le reste du document. 
Il peut contenir une image, un diagramme, un audio, un tableau ou un bloc de code.

Il est généralement utiliser avec l'élément `<figcaption>` qui permet d'ajouter une légende.

Image avec une légende : 

```html
<figure>
  <img src="example.jpg" alt="Exemple d'image">
  <figcaption>Légende de l'image expliquant son contenu</figcaption>
</figure>
```

Tableau avec une légende : 
```html
<figure>
  <table>
    <tr>
      <th>Titre 1</th>
      <th>Titre 2</th>
    </tr>
    <tr>
      <td>Donnée 1</td>
      <td>Donnée 2</td>
    </tr>
  </table>
  <figcaption>Légende du tableau</figcaption>
</figure>
```

### figcaption

Cet élément permet d'ajouter une légende au contenu `<figure>`. Il peut être placé avant ou après le contenu.

```html
<figure>
  <img src="landscape.jpg" alt="Paysage">
  <figcaption>Beau paysage avec des montagnes et un lac</figcaption>
</figure>

<figure>
  <figcaption>Beau paysage avec des montagnes et un lac</figcaption>
  <img src="landscape.jpg" alt="Paysage">
</figure>
```

### Accessibilité et SEO 

L'utilise des balises permet d'améliorer l'accessibilité et le SEO du site. 

Les logiciels de lecture d'écran comprennent mieux le contexte de l'image avec la balise `<figcaption>`. Les moteur de recherche prennent également en compte la structure du document HTML ce qui permet d'améliorer l’indexation et le classement de la page.

---

## MODELE DE BOITE 

Concept qui décrit la structure et l'affichage des éléments HTML. 
Le modèle en boite est constitué de deux principaux types d'élément : **bloc** et **inline** . 

### Bloc 

`<div>`	Le conteneur principal pour regrouper d'autres éléments.
`<p>`	Un paragraphe de texte.
`<h1> – <h6>`	Titres de différents niveaux.
`<ul>, <ol>`	Listes non numérotées et numérotées.
`<li>`	Élément de liste.
`<section>`	Conteneur sémantique pour une section du document.
`<article>`	Bloc de contenu indépendant.
`<aside>`	Contenu lié au contenu principal (barre latérale).
`<header>`	Partie introductive ou de navigation de la page ou de la section.
`<footer>`	La partie inférieure de la page ou de la section.
`<nav>`	Liens de navigation.
`<main>`	Contenu principal du document.


### Inline

Ils occupent uniquement la largeur nécessaire pour leur contenu et ne commencent par sur une nouvelle ligne. 

|Tag HTML|Description|
|---|---|
|`<span>`|Conteneur principal pour regrouper du contenu en ligne.|
|`<a>`|Lien hypertexte.|
|`<strong>`, `<b>`|Texte en gras.|
|`<em>`, `<i>`|Texte en italique.|
|`<img>`|Insertion d'images.|
|`<code>`|Texte de code.|
|`<label>`|Association entre une étiquette et un élément de formulaire.|
|`<input>`, `<select>`, `<textarea>`|Éléments de formulaires.|

### Composant du modèle de boite 
![[Pasted image 20260128205843.png]]

- **Content**: le contenu interne de l'élément
- **Padding** : espace entre le contenu et la bordure 
- **Border**: ligne de la boite 
- **Margin**: espace entre la boite et les autre boite 

---

## PSEUDO-CLASSE ET PSEUDO-ELEMENTS

Elément permettant de styliser les éléments en fonction de leur état ou de leur contenu sans avoir besoin d'ajouter des classes ou de modifier la structure du document HTML.

### Pseudo-classes

S'appliquent aux éléments en fonction de leur état ou de leur position dans la structure du document.
Elles aident à styliser les éléments, au survole, au focus, ou à la sélection des éléments par exemple.
Les pseudo-classe comment par `:`.

- `:focus`: applique le style lorsque l'élément obtient le focus, par exemple, au clique sur un champ de saisie
- `:hover`: applique le style lorsque l'élément est survoler par le curseur 
- `:active`: applique un style lorsque celui ci est actif (clic sur un client, ou bouton)
- `:visited`: applique un style 

### Pseudo-éléments 
Permettent de styliser des parties d'éléments qui ne sont pas des éléments HTML distinct. 
Ils commencent par `::`.

Ils sont utilisés pour créer et styliser du contenu avant ou après un élément, mettre en evidence la première ligne ou première lettre d'un élément, etc 

#### ::before
insère du contenu avant le contenu de l'élément 
```html
<p>Je m'appelle Stepane.</p>
```

```css
p::before {
  content: "Salut! ";
  color: blue;
}
```
Avec ce CSS, on avant "Salut!" en bleu avant le paragraphe HTML.

#### ::after
insère du contenu après le contenu de l'élément 
```html
<p>Attention!</p>
```

```css
p::after {
  content: " Merci pour votre attention!";
  color: red;
}
```

![[Pasted image 20260129100215.png]]

#### ::first-line
S'applique à la première ligne d'un élément. Il permet de styliser uniquement la première ligne du texte 
```html
<p>
 Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.
  Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute
  irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat
  cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
</p>
```

```css
p::first-line {
  font-weight: bold;
  color: green;
}
```

![[Pasted image 20260129100410.png]]

#### ::selection 
S'applique au texte sélectionné par l'utilisateur
```html
<p>
  Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.
  Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.
</p>
```
```css
::selection {
  background-color: blue;
  color: yellow;
}
```

Avec ce code, le style s'applique lorsque l'on sélectionne du texte.

#### Insertion d'îcone avant un lien 
```html
<html>
  <head>
    <style>
      a::before {
        content: "🔗";
        margin-right: 5px;
      }
    </style>
  </head>
  <body>
     <a href="#">Ceci est un lien avec une icône</a>
  </body>
</html>
```

---

## FORMULAIRE
```html
<form action="/submit" method="post">
  <label for="username">Nom d'utilisateur:</label>
  <input type="text" id="username" name="username" required>
  <br>
  <label for="email">Email:</label>
  <input type="email" id="email" name="email" required>
  <br>
  <button type="submit">Envoyer</button>
</form>
```

### form 
Cette balise permet de créer un formulaire sur une page web. Elle sert de conteneur pour les éléments du formulaire.
Toutes les données peuvent être envoyées au serveur pour le traitement.

- `action`: permet de spécifier l'URL à laquelle les données du formulaire seront envoyées après avoir cliqué sur le bouton d'envoi.
- `method`: permet de spécifier la méthode d'envoie des données `GET` ou `POST`.

### Méthode d'envoie

#### GET 
Envoie les données du formulaire sous forme de paramètre d'URL. Utilisé par défaut 
- Les données sont visible dans l'URL
- Limitation de la taille de l'url 
- Donnée peuvent être mise en cache

#### POST 
Envoie les données du formulaire dans la requête HTTP.  Permet de transmettre les données de manière sécurisée.
- Les données ne sont pas visible dans l'URL
- Pas de limitation des données 


### enctype 

Cet attribut permet de spécifier la manière dont les données du formulaire sont encodées lors de la l'envoie avec `POST`.
Généralement, `multipart/form-data` est utilisé, et est nécessaire pour le téléchargement de fichier 

```html
<form action="/upload" method="post" enctype="multipart/form-data">
  <label for="file">Télécharger le fichier:</label>
  <input type="file" id="file" name="file">
  <button type="submit">Envoyer</button>
</form>
```

### target 
Spécifie où la réponse du serveur sera affichée après l'envoi du formulaire :
- `_self`: par défaut, dans la même fenêtre
- `_blank`: dans une nouvelle fenêtre
- `_parent`: dans le cadre parent 
- `_top`: dans le cadre supérieur 
```html
<form action="/submit" method="post" target="_blank">
  <!-- éléments du formulaire -->
</form>
```

### novalidate 
Désactive la validation intégrée du formulaire par le navigateur 
```html
<form action="/submit" method="post" novalidate>
  <label for="email">Email:</label>
  <input type="email" id="email" name="email" required>
  <button type="submit">Envoyer</button>
</form>
```