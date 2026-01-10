# CSS 

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


