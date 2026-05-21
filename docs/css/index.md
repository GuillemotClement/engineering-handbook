# CSS 

| Nom de la propriété CSS | Explication              | Exemple                       |
| ----------------------- | ------------------------ | ----------------------------- |
| `color`                 | Couleur                  | color: red                    |
| `height`                | Hauteur                  | height: 400px                 |
| `width`                 | Largeur                  | width: 100%                   |
| `background-color`      | Couleur de fond          | background-color: rgb(ffccdd) |
| `border`                | Bordure de l'élément     | border: 1 px solid black      |
| `font-size`             | Taille de la police      | font-size: 2em                |
| `border-radius`         | Rayon des coins arrondis | border-radius: 4px            |
## SELECTEUR 

Permet de cibler des éléments HTML pour leur appliquer du style.

### Sélecteur de balise
Applique le style à tous toutes les éléments ciblé
```css
tagname {
    // styles
}
```

### Sélecteur de classe 
On peut appliquer un style CSS à un élément avec une classe
```html
</h1>
<!DOCTYPE html>
<html lang="en">
<head>
    <style>
        p.important {
            font-weight: bold;
        }
    </style>
</head>
<body>
<p class="important">Ceci est un texte important.</p>
<p>Ceci est un texte ordinaire.</p>
</body>
</html>
```


## LISTE 

### list-style-type 
Cette propriété permet de changer les puces de la liste.
```css
ul.custom-list {
  list-style-type: katakana; /* Syllabaire japonais */
  color: blue; /* Couleur du texte */
}

ol.custom-list {
  list-style-type: hiragana-iroha; /* Système de classement japonais */
  color: green; /* Couleur du texte */
}
```

---

## TABLEAU 
### Style de base pour un tableau 
```css
table {
  width: 100%;
  border-collapse: collapse;
  margin: 20px 0;
  font-size: 18px;
  text-align: left;
}

caption {
  caption-side: top;
  font-weight: bold;
  font-size: 20px;
  margin-bottom: 10px;
}

th, td {
  padding: 12px 15px;
  border: 1px solid #ddd;
}

thead th {
  background-color: #f2f2f2;
}

tbody tr:nth-child(even) {
  background-color: #f9f9f9;
}

tfoot td {
  font-weight: bold;
  background-color: #f2f2f2;
}
```


## Commentaire 

```css
/* commentaire css */
```

## Ajouter du CSS 

### style 

Dans le `head` du HTML, on peut venir ajouter une balise `<style>` permettant de déclarer le style dedans. 

```html
<head>
	<meta charset="utf-8" />
	<title>Cafe Menu</title>
	<style>
	  h1, h2, p {
		text-align: center;
	  }
	</style>
</head>
```

### styles.css

On peut venir déclarer le style dans un fichier à part `style.css` avec l'extension `.css`.

On viens ensuite lier ce fichier au fichier HTML 

```html
<head>
  <meta charset="utf-8" />
  <title>Cafe Menu</title>
  <link rel="stylesheet" href="styles.css"/> 
  <meta name="viewport" content="width=device-width, initial-scale=1.0" /> 
</head>
```

## Text 

### Text-align 

Permet de centrer du texte 

```css
h1 {
	text-align: center;
}
```


---

## Couleur 

### background-color 

Permet d'ajouter une couleur de fond.

---

## Taille

### width

Permet de gérer la largueur d'un élément 

```css
#cat {
  width: 250px;
  width: 80%;
}
```

### margin 

#### Centrer un élément 

Il est possible d'utiliser la propriété pour venir center un élément.

```css
#menu {
  margin-left: auto;
  margin-right: auto;
  
  /* OU */
  margin: 0 auto;
}
```


---

## Sélecteur 

### id 

Il est possible de sélectionner un élément avec son `id`.

```html
<div id="cat">
</div>
```

Dans le css, on pourras venir le sélectionner avec `#name_id`

```css
#cat {
  width: 250px;
}
```



### Groupage 

Lorsque plusieurs sélecteur partage les même règle de style, on peut les regrouper en les séparant par une virgule

```css
h1, h2 {
	text-align: center;
}
```


