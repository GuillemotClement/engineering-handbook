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

#### :focus 
S'applique à un élément du formulaire qui reçoit le focus. Par exemple, lorsque l'utilisateur clic sur un élément, ou en naviguant avec Tab.
```css
input:focus {
  border-color: #4A90E2;
  box-shadow: 0 0 5px rgba(74, 144, 226, 0.5);
  outline: none;
}
```

#### :hover 
S'applique à un élément lorsque l"user passe le curseur dessus.
```css
input:hover {
  border-color: #50E3C2;
  background-color: #F0F8FF;
}
```

#### :disabled 
S'applique aux éléments du formulaire qui sont désativées 
```css
input:disabled {
  background-color: #E0E0E0;
  color: #A0A0A0;
  cursor: not-allowed;
}
```

#### :checked 
S'applique aux élément `checkbox` et `radio` qui sont sélectionné
```css
input[type="checkbox"]:checked,
input[type="radio"]:checked {
  background-color: #4A90E2;
  border-color: #4A90E2;
}
```


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

### Envoie de données

L'envoie des données du formulaire s'effectue à l'aide du bouton `submit`. Lorsque l'utilisateur clique sur ce bouton, le navigateur envoie les données du formulaire au serveur, en utilisant la méthode et l'URL spécifiées

```html
<button type="submit">Submit</button>
```

Le formulaire peut également être envoyer via JavaScript.

```html
<form id="myForm" action="/submit" method="post">
  <label for="username">Nom d'utilisateur:</label>
  <input type="text" id="username" name="username">
  <br>
  <label for="password">Mot de passe:</label>
  <input type="password" id="password" name="password">
  <br>
  <button type="button" onclick="submitForm()">Envoyer</button>
</form>
```

On retrouve ensuite le JavaScript 

```js
function submitForm() {
  document.getElementById('myForm').submit();
}
```

### input

L'élément `<input>` permet de récupérer les saisie utilisateurs.

```html
<input type="type" name="name" id="id" value="value" placeholder="placeholder" required>
```

- `type` : définit le type d'entrée
- `name` : nom de l'élément du formulaire, il permet d'identifier les données sur le serveur 
- `id` : identifiant unique de l'élément 
- `value` : valeur initiale de l'élément 
- `placeholder` : texte d'indication affiché à l'intérieur de l'élément avant l'entrée des données
- `required` : indique que l'élément doit être rempli

#### Type input 

##### text 
Permet de saisir du texte sur une seule ligne.

```html
<label for="name">Nom :</label>
<input type="text" id="name" name="name">
```

- `maxlength`: limite le nombre maximum de caractères
- `placeholder`: texte indicatif à l'intérieur de l'input 
- `required`:  indique que le champ est obligatoire 

##### number 
Permet la saisie de champs numérique. 
```html
<label for="quantity">Quantité :</label>
<input type="number" id="quantity" name="quantity" min="1" max="10" step="1" value="1">
```
- `min` : définit la valeur min
- `max` : définit la valeur max
- `step` : détermine le pas de changement de valeur avec les flèches 
- `value` : valeur initial

##### email 
Permet la saisis d'émail. Il vérifie si la saisie correspond à une email 
```html
<label for="email">Email :</label>
<input type="email" id="emails" name="emails" multiple placeholder="example1@example.com, example2@example.com">
```
- `multiple` : permet de saisir plusieurs adresse email 
- `pattern` : définit une regex pour une vérification supplémentaire 

##### tel 
Permet la saisis d'un numéro de téléphone. Il ne vérifie pas le numéro de téléphone, mai permet de configurer un masque d'entrée via l'attribut pattern 

```html
<label for="phone">Téléphone :</label>
<input type="tel" id="phone" name="phone" pattern="[+][0-9]{1,3} [0-9]{1,4} [0-9]{3,4} [0-9]{4}" placeholder="+1 123 456 7890">
```

- `pattern`: permet de définir une regex 

##### date 
Permet de saisir une date.
```html
<label for="birthday">Date de naissance :</label>
<input type="date" id="birthday" name="birthday">
```
- `min` : date minimal 
- `max` : date maximal 

##### time 
Utilisé pour rentrer une horaire.
```html
<label for="meeting_time">Heure de la réunion :</label>
<input type="time" id="meeting_time" name="meeting_time">
```

- `min` : définit l'heure min 
- `max` : définit l'heure max
- `step` : détermine le pas du changement d'heure 

##### url 
Utilisé pour rentrer des adresse web. Il vérifie si le texte correspond au format d'une URL 
```html
<label for="website">Site web :</label>
<input type="url" id="website" name="website" placeholder="https://example.com">
```

##### password
Permet de saisir un mot de passe. Les données saisie sont masquées, remplacées par des symboles 
```html
<label for="password">Mot de passe :</label>
<input type="password" id="password" name="password">
```
- `maxlength`: limite le nombre max de caractères 
- `placeholder` : texte indicatif 
- `required` : indique que le champ est obligatoire

##### range 
Permet de saisir une plage de valeur. Affiche sous la forme d'un curseur 
```html
<label for="volume">Volume :</label>
<input type="range" id="volume" name="volume">
```
- `min`: définit la valeur min
- `max` : définit la valeur max 
- `step`: détermine le pas de chamgement de la valeur
- `value` : définit la valeur initiale 

##### color 
Utilisé pour sélectionner une couleur. 
```html
<label for="favcolor">Choisissez votre couleur préférée :</label>
<input type="color" id="favcolor" name="favcolor" value="#ff0000">
```

##### search 
Utilisé pour entrer des requêtes de recherches 
```html
<label for="search">Recherche :</label>
<input type="search" id="search" name="search" placeholder="Entrez la requête de recherche">
```

#### datalist 

Cette balise est utilisé pour fournis une liste d'options prédéfinies pour les éléments `<input>`. Permet aux utilisateurs de choisir parmi une liste d'options proposées, facilitant la saisie des données et assurant une saisie plus précise et prévisible.
```html
<form>
  <label for="browser">Choisis un navigateur :</label>
  <input list="browsers" id="browser" name="browser">
  <datalist id="browsers">
    <option value="Chrome">
    <option value="Firefox">
    <option value="Safari">
    <option value="Opera">
    <option value="Edge">
  </datalist>
  <button type="submit">Envoyer</button>
</form>
```

L'input doit être lié avec `list`.

### output 

Utilisé pour afficher le résultat des calculs et actions effectuées via JS. Cette balise permet d'afficher un contenu qui change de manière dynamique comme un résultat de calculs, des données de formulaire ou autre valeurs qui peuvent changer en temps réel.

```html
<output name="result" for="input-id-1 input-id-2">Résultat</output>

```

- `name` : définit le nom de l'élément 
- `for`: contient la liste des identifiants de l'élément avec laquelle la valeur de l'input est lié. 

```html
<form oninput="calculateSum()">
  <label for="num1">Nombre 1:</label>
  <input type="number" id="num1" name="num1">
  <br>
  <label for="num2">Nombre 2:</label>
  <input type="number" id="num2" name="num2">
  <br>
  <output id="result" name="result" for="num1 num2">0</output>
</form>
```

```js
function calculateSum() {
  const num1 = parseFloat(document.getElementById('num1').value) || 0;
  const num2 = parseFloat(document.getElementById('num2').value) || 0;
  const sum = num1 + num2;
  document.getElementById('result').value = sum;
}
```

### label 

Utilisé pour créer une étiquette de texte pour un élément du formulaire. Elle peut être lié à un élément de formulaire de deux manière : via l'attribut `for` ou en imbriquant l'élément du formulaire dans les balises.
```html
<label for="username">Nom d'utilisateur :</label>
<input type="text" id="username" name="username">

<label>Nom d'utilisateur :
  <input type="text" name="username">
</label>
```






### textarea 

Utilisé pour entré du texte multiligne. 

```html
<label for="comments">Commentaires :</label>
<textarea name="comments"
  id="comments"
  rows="5"
  cols="40"
  placeholder="Entrez vos commentaires ici..."
  required>
</textarea>
```

- `name` : nom de l'élément du formulaire, il permet d'identifier la donnée sur le serveur 
- `id` : identifiant unique de l'élément 
- `rows` : nombre de lignes de texte
- `cols` : nombre de caractères dans une ligne 
- `placeholder` : texte d'indication affiché à l'intérieur de l'élément avant l'entrée des données
- `required` : indique que l'élément doit être rempli 

On peut lui appliquer ce CSS pour améliorer son aspect 

```css
textarea {
  width: 75%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  resize: vertical; /* resize sera possible uniquement verticalement */
}
```

### button 

Permet de créer un bouton lié au formulaire. 

```html
<button type="submit">Envoyer</button>

<button type="reset">Réinitialiser</button>

<button type="button" onclick="alert('Bouton cliqué!')">Cliquez-moi</button>

<button type="type" name="name" id="id" value="value">
  <img src="submit_icon.png" alt="Envoyer">
  Envoyer
</button>
```

- `submit` : soumet le formulaire. C'est la valeur par défaut 
- `reset` : renit du formulaire 
- `button` : aucune action par défaut
- `name` : nom de l'élément 
- `id` : identifiant unique 
- `value` : valeur envoyé au serveur à la validation du formulaire

On peut utiliser ce css pour styliser le bouton 

```css
button {
  background-color: #4CAF50; /* Fond vert */
  color: white; /* Texte blanc */
  padding: 15px 20px; /* Espacement interne */
  border: none; /* Pas de bordure */
  border-radius: 4px; /* Coins arrondis */
  cursor: pointer; /* Curseur pointeur */
}

button:hover {
  background-color: #45a049; /* Fond vert foncé au survol */
}
```

### checkbox 

Les checkbox permettent de créer des cases à cocher qui permettent aux utilisateurs de sélectionner une ou plusieurs options parmis celle proposées. La case peut être coché ou non.

```html
<p>Select your interests:</p>
<input type="checkbox" name="interest" value="sports" checked>Sports<br>
<input type="checkbox" name="interest" value="music">Music<br>
<input type="checkbox" name="interest" value="movies">Movies<br>
```

- `name` : définit le nom de l'input pour le serveur 
- `value` : définit la valeur qui sera envoyé au serveur 
- `checked` : détermine si la case doit être coché par défaut 

### radio 

Permet de créer des boutons radio qui permettent aux utilisateurs de sélectionner une seule option parmis un groupe.
Les radio sont groupés par le même attribut `name`

```html
<p>Choose your gender:</p>
<input type="radio" id="male" name="gender" value="male">
<label for="male">Male</label><br>
<input type="radio" id="female" name="gender" value="female">
<label for="female">Female</label><br>
```

### fieldset 

Permet de regrouper des éléments de formulaire. Visuellement, cet élément est affiché comme un cadre autour des éléments groupé.

```html
<form>
  <fieldset>
    <legend>Personal Information</legend>
    <label for="name">Name:</label>
    <input type="text" id="name" name="name">
    <label for="email">Email:</label>
    <input type="email" id="email" name="email">
  </fieldset>
  
  <fieldset>
    <legend>Account Details</legend>
    <label for="username">Username:</label>
    <input type="text" id="username" name="username">
    <label for="password">Password:</label>
    <input type="password" id="password" name="password">
  </fieldset>
  <input type="submit" value="Submit">
</form>
```

- `disabled` : rend tous les éléments à l'intérieur inaccessible (ils en seront pas modifiable, et pas envoyé au serveur)
```html
<fieldset disabled>
  <legend>Disabled Group</legend>
  <label for="disabled-field">This field is disabled:</label>
  <input type="text" id="disabled-field" name="disabled-field">
</fieldset>
```

### legend 

Permet d'ajouter un titre à un groupe d'élément. 
```html
<fieldset>
  <legend>Contact Information</legend>
  <label for="phone">Phone Number:</label>
  <input type="tel" id="phone" name="phone">
  <label for="address">Address:</label>
  <input type="text" id="address" name="address">
</fieldset>
```

### select 

Utilisé pour créer des listes déroulante. Il contient plusieurs élémens avec la balise `<option>` qui permet de définir une options.

```html
<label for="country">Choose a country:</label>
<select id="country" name="country">
  <option value="us">United States</option>
  <option value="ca">Canada</option>
  <option value="mx">Mexico</option>
</select>
```

- `name` : définir le nom de l'élément qui sera envoyé au serveur et selectionné 
- `id` : identifiant unique pour l'élément 
- `multiple` : permet à l'utilisateur de sélectionner plusieurs options.
- `size` : définit le nombre de ligne visibles dans la liste déroulante.

### option 

Permet de définir une option d'une liste déroulante.

```html
<select name="color">
  <option value="red">Red</option>
  <option value="green">Green</option>
  <option value="blue">Blue</option>
</select>
```

- `value` : définit la valeur envoyé au serveur si l'option est sélectionnée
- `selected` : détermine si l'option doit être sélectionnée par défaut 
- `disabled` : rend l'option indisponible 

### optgroup 

Permet de regrouper des options liées dans une liste. 
```html
<select name="car">
  <optgroup label="German Cars">
    <option value="mercedes">Mercedes</option>
    <option value="audi">Audi</option>
    <option value="bmw">BMW</option>
  </optgroup>
  <optgroup label="Japanese Cars">
    <option value="toyota">Toyota</option>
    <option value="honda">Honda</option>
    <option value="nissan">Nissan</option>
  </optgroup>
</select>
```

- `label` : définit le titre du groupe d'options 
- `disabled` : rend toutes les options dans le groupe indisponible 

### Validation des données 

#### required 

Indique qu'un champs doit être saisie. Le navigateur ne permet pas l'envoie du formulaire si l'input est pas complété.
```html
<form action="/" method="post">
  <label for="username">Nom d'utilisateur :</label>
  <input type="text" id="username" name="username" required>
  <button type="submit">Envoyer</button>
</form>
```

#### pattern 
Permet de définir une regex pour vérifier la saisie des données. 
```html
<form action="/" method="post">
  <label for="username">Nom d'utilisateur (lettres seulement, au moins 3 caractères) :</label>
  <input type="text" id="username" name="username" pattern="[A-Za-z]{3,}" required>
  <button type="submit">Envoyer</button>
</form>
```

#### min & max
Définis les valeurs min et max acceptable pour un champs numérique et datés
 ```html
 <input type="number" name="age" min="18" max="99">
 
 <form action="/" method="post">
  <label for="birthday">Date de naissance (de 1900-01-01 à 2024-12-31) :</label>
  <input type="date" id="birthday" name="birthday" min="1900-01-01" max="2024-12-31" required>
  <button type="submit">Envoyer</button>
</form>
 ```

#### placeholder 
Permet d'afficher du texte avant saisie. Celle ci disparait lorsque l'utilisateur saisis de la donnée dans l'input 
```html
<form action="/" method="post">
  <label for="username">Nom d'utilisateur :</label>
  <input type="text" id="username" name="username" placeholder="Entrez le nom d'utilisateur" required>
  <button type="submit">Envoyer</button>
</form>
```

#### value 
Définit la valeur initial de l'input. 
```html
<form action="/" method="post">
  <label for="username">Nom d'utilisateur :</label>
  <input type="text" id="username" name="username" value="default user" required>
  <button type="submit">Envoyer</button>
</form>
```

### Evènement de formulaire
Les formulaires produisent des événements spécifique lié à leur fonctionnement

#### submit 
Se produit lorsque le formulaire est envoyé. Le traitement de cet évènement permet d'effectuer une validation des données avant de les envoyer au serveur

```html
<form id="my-form" action="/submit" method="post">
  <label for="username">Nom d'utilisateur:</label>
  <input type="text" id="username" name="username">
  <button type="submit">Envoyer</button>
</form>
```

```js
const form = document.getElementById('my-form');

form.addEventListener('submit', function(event) {
  const username = document.getElementById('username').value;
  if (username === '') {
    alert('Veuillez entrer un nom d\'utilisateur.');
    event.preventDefault(); // empêchant l'envoi du formulaire
  }
});
```

#### reset 
Se produit lors de la réinitialisation du formulaire. Cela se produit lorsque l'user clique sur le bouton reset.

```html
<form id="my-form">
  <label for="name">Name:</label>
  <br>
  <input type="text" id="name" name="name">
  <br><br>
  <label for="email">Email:</label>
  <br>
  <input type="email" id="email" name="email">
  <br><br>
  <label for="password">Password:</label>
  <br>
  <input type="password" id="password" name="password">
  <div style="margin-top: 10px">
    <button type="reset">Réinitialiser</button>
  </div>
</form>
<p id="log"></p>
```

```js
const form = document.getElementById('my-form');
const log = document.getElementById('log');

form.addEventListener('reset', function () {
  log.textContent = 'Le formulaire a été réinitialisé !';
});
```

#### focus et blur 

Ces event se produisent lorsque les élément du formulaire reçoivent et perde le focus respectivement.
Ces events sont utilisés pour améliorer l'interaction de l'utilisateur avec le formulaire

```html
<form id="my-form">
  <label for="username">Nom d'utilisateur:</label>
  <input type="text" id="username" name="username">
  <div id="username-hint" style="display: none;">
    Vous verrez cette astuce au moment du focus sur le champ.
    Elle sera visible même après le changement de focus.
  </div>
</form>
```

```js
const usernameInput = document.getElementById('username');
const hint = document.getElementById('username-hint');

usernameInput.addEventListener('focus', function () {
  hint.style.display = 'block';
});
```

```html
<form id="my-form">
  <label for="username">Nom d'utilisateur:</label>
  <input type="text" id="username" name="username">
  <div id="username-error" style="display: none; color: red;">
    L'erreur apparaîtra seulement si le champ est vide après le changement de focus
  </div>
</form>
```

```js
const usernameInput = document.getElementById('username');
const error = document.getElementById('username-error');

usernameInput.addEventListener('blur', function () {
  error.style.display = usernameInput.value === '' ? 'block' : 'none';
});
```

#### input 
Cet event se produit lorsque la valeur d'un élément du formulaire change. 
```html
<html lang="fr">
  <head>
    <style>
      input {
        outline: none;
      }

      .valid {
        border-color: green;
      }

      .invalid {
        border-color: red;
      }

      .wrapper-valid::after {
        content: "Email valide";
        margin-left: 5px;
        color: green;
      }

      .wrapper-invalid::after {
        content: "Email non valide";
        margin-left: 5px;
        color: red;
      }
    </style>
  </head>
  <body>
    <form id="my-form">
      <label for="email">Email:</label>
      <div id="wrapper">
        <input type="email" id="email" name="email">
      </div>
    </form>
  </body>
</html>
```

```js
const emailInput = document.getElementById('email');
const inputWrapper = document.getElementById('wrapper');

emailInput.addEventListener('input', function () {
  const emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;

  if (emailPattern.test(emailInput.value)) {
    emailInput.classList.add('valid');
    emailInput.classList.remove('invalid');
  } else {
    emailInput.classList.add('invalid');
    emailInput.classList.remove('valid');
  }
});
```

#### select 

Se produit lorsque l'utilisateur sélectionne du texte dans un élément du formulaire comme un champ de saisie ou une zone de texte. 
```html
<form id="my-form">
  <label for="text-input">Entrez du texte:</label>
  <input type="text" id="text" name="text">
  <div id="select-message"></div>
</form>
```

```js
document.addEventListener('DOMContentLoaded', function () {
  const textInput = document.getElementById('text');
  const selectMessage = document.getElementById('select-message');

  textInput.addEventListener('select', function () {
    selectMessage.textContent = 'Vous avez sélectionné du texte!';
  });
});
```

#### change 
Se produit lorsque la valeur d'un élément du formulaire change et que l'user quitte l'élément
```html
<form id="my-form">
  <label for="color-select">Choisissez une couleur:</label>
  <select id="color-select" name="color">
    <option value="red">Rouge</option>
    <option value="green">Vert</option>
    <option value="blue">Bleu</option>
  </select>
</form>
```

```js
document.addEventListener('DOMContentLoaded', function () {
  const selectElement = document.getElementById('color-select');

  selectElement.addEventListener('change', function () {
    alert('Vous avez choisi la couleur : ' + selectElement.value);
  });
});
```

### Téléchargement de fichiers 

HTML permet aux utilisateurs de télécharger des fichiers sur le serveur avec un input de type `file`.

```html
<form action="/upload" method="post" enctype="multipart/form-data">
  <label for="file">Choisissez un fichier :</label>
  <input type="file" id="file" name="file">
  <button type="submit">Télécharger</button>
</form>
```

- `multiple` : permet aux utilisateurs de sélectionner plusieurs fichiers à télécharger. 
```html
<form action="/upload" method="post" enctype="multipart/form-data">
  <label for="files">Choisissez des fichiers :</label>
  <input type="file" id="files" name="files" multiple>
  <button type="submit">Télécharger</button>
</form>
```

- `accept` : permet de limiter les types de fichiers qui peuvent être sélectionnés par l"utilisateur. 
```html
<form action="/upload" method="post" enctype="multipart/form-data">
  <label for="image">Choisissez une image :</label>
  <input type="file" id="image" name="image" accept="image/*">
  <button type="submit">Télécharger</button>
</form>
```

 - `image/*` : tout type d'image
 - `video/*`: tout type de vidéo 
 - `audio/*`: tout type d'audio
 - `.jpg/ .jpeg/ .png` : permet de limiter le format 

#### Traitement des fichier côté client 
Il est parfois nécessaire d'effectuer un traitement préalable des fichiers côté clients avant de les envoyer au serveur. Par exemple, afficher un aperçus des images ou vérifier la taille du fichier 
```html
<form action="/upload" method="post" enctype="multipart/form-data">
  <label for="image">Choisissez une image :</label>
  <input type="file" id="image" name="image" accept="image/*">
  <div id="preview"></div>
  <button type="submit">Télécharger</button>
</form>
```

```js
document.getElementById('image').addEventListener('change', function(event) {
  const preview = document.getElementById('preview');
  preview.innerHTML = '';

  const files = event.target.files;

  for (let i = 0; i < files.length; i++) {
    const file = files[i];
    const reader = new FileReader();

    reader.onload = function(e) {
      const img = document.createElement('img');
      img.src = e.target.result;
      img.style.maxWidth = '200px';
      preview.appendChild(img);
    }
    reader.readAsDataURL(file);
  }
});
```

- `FileReader` : permet de lire le contenu des fichiers côté client
- `reader.onload`: event déclenché lorsque le fichier est complètement lu
- `reader.readAsDataURL(file)`: méthode qui lit le fichier et encore l'URL de donnée permettant d’afficher dans la balise `<img>`
---

## BALISE SEMANTIQUE 

Les balises sémantiques permette de créer du HTML structurés et lisible, en améliorant l'accessibilité et le SEO.

Ces balises offrent une description claire de la structure de la page et de son contenu.

### header

Permet de représenter l'en tête d'un contenu ou d'une section. Cet élément contient généralement des informations introductives, des liens de navigations, des logs, des titres et d'autres éléments liées à la partie supérieur du contenu 
```html
<header>
  <h1>Website Title</h1>
  <nav>
    <ul>
      <li><a href="#home">Home</a></li>
      <li><a href="#about">About</a></li>
      <li><a href="#contact">Contact</a></li>
    </ul>
  </nav>
</header>
```

### footer 

Utilisé pour représenter le bas du contenu ou d'une section. Contient généralement des informations sur l'auteur, des liens vers des documents, des politiques de confidentialité, des informations de contact et d'autres élément lié à la partie inférieur du contenu 

```html
<footer>
  <p>&copy; 2024 My Website</p>
  <nav>
    <ul>
      <li><a href="#privacy">Privacy Policy</a></li>
      <li><a href="#terms">Terms of Service</a></li>
    </ul>
  </nav>
</footer>
```

### article 

Représente un bloc de contenu indépendant, qui peut être distribué et réutilisé. Par exemple un article de bloc, article de presse, un commentaire ...

```html
<article>
  <header>
    <h2>Blog Post Title</h2>
    <p>Published on July 6, 2024</p>
  </header>
  <p>This is the content of the blog post. It can include text, images, and other media.</p>
  <footer>
    <p>Author: John Doe</p>
  </footer>
</article>
```

### section 

Permet de définir des groupes de contenu thémtiquement liés. Il peut s'agit de chapitres, de sections ou d'autres grandes parties du contenu.

```html
<section>
  <header>
    <h2>About Us</h2>
  </header>
  <p>This section provides information about our company and its history.</p>
</section>
```

### nav 
Permet de définir une section de pages contenant des liens de navigation. 
```html
<header>
  <h1>Mon site</h1>
  <nav>
    <ul>
      <li><a href="#home">Accueil</a></li>
      <li><a href="#about">À propos</a></li>
      <li><a href="#services">Services</a></li>
      <li><a href="#contact">Contacts</a></li>
    </ul>
  </nav>
</header>
```

### main 
Désigne le contenu principal du document. Elle est utilisé pour placer le contenu unique et clé pour cette page. 
Le contenu doit être unique pour chaque page, et ne pas contenir d'éléments qui se répètent sur toutes les pages.

```html
<body>
  <header>
    <h1>Website Header</h1>
    <nav>
      <ul>
        <li><a href="#home">Home</a></li>
        <li><a href="#about">About</a></li>
        <li><a href="#contact">Contact</a></li>
      </ul>
    </nav>
  </header>
  <main>
    <article>
      <h2>Article Title</h2>
      <p>This is the main content of the article.</p>
    </article>
  </main>
  <footer>
    <p>&copy; 2024 Example Website</p>
  </footer>
</body>
```

### aside 

Utilisé pour désigner le contenu supplémentaitre lié au contenu principal mais qui ne fait pas partie du flux principal du document. Utilisé généralement pour les panneaux latéraux contenant de la pub, des liens vers des articles connexes, des bio d'auteurs

```html
<main>
  <article>
    <h2>Article Title</h2>
    <p>This is the main content of the article.</p>
  </article>
  <aside>
    <h3>Related Articles</h3>
    <ul>
      <li><a href="#related1">Related Article 1</a></li>
      <li><a href="#related2">Related Article 2</a></li>
    </ul>
    <h3>About the Author</h3>
    <p>Short bio of the author.</p>
  </aside>
</main>
```

### template

Cette balise permet de définir des fragments de code HTML qui ne seront pas affcihés lors du chargement de la page, mais qui peuvent être utilisé plus tard via le JS.

```html
<button id="addButton">Add Item</button>
<ul id="itemList"></ul>
<template id="itemTemplate">
  <li class="item">This is a new item</li>
</template>
```

```js

// On récupère des références aux éléments du DOM que nous allons utiliser
const addButton = document.getElementById('addButton'); // Le bouton "Ajouter"
const itemList = document.getElementById('itemList'); // La liste où ajouter les éléments
const itemTemplate = document.getElementById('itemTemplate'); // Le template de l'élément de liste

// Ajoutons un gestionnaire d'événement au bouton "Ajouter"
addButton.addEventListener('click', () => {
  // On clone le contenu du template d'un élément de liste
  const newItem = itemTemplate.content.cloneNode(true); // true signifie un clonage profond (inclut tous les éléments enfants)

  // On ajoute le nouvel élément à la liste
  itemList.appendChild(newItem);
});
```