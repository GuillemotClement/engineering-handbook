# Jquery 

## Sélection et stockage d'un élément 

```js
// sélection de l'élément placer dans une variable 
const monElement = $('#monBouton');

// utilisation du bouton 
monElement.css('color', 'red');
```

### Récupération d'élement dans un listing 

Dans un premier, temps, on définis la fonction qui vient ajouter une classe au élément ayant été modifé : 

```js 
function handlePriceChange(element){  
    element.classList.add('touched');  
}
```

La fonction viens ajouter sur l'input qui as été modifié la nouvelle classe : 

```css
.touched {  
    background-color: #FFDF20;  
}
```

Dans le listing, on viens ajouter un event pour surveiller, et appeler la fonction lorsque la valeur est modifée : 

```html
<td rowspan="2" class="align-middle">  
    <input type="number"  
           step="0.01"  
           class="form-control"  
           name="DATA[<?= $article['article_id'] ?>][PrixRevient]"  
           id="PrixRevient_<?= $article['article_id'] ?>"  
           value="<?= mround($article['prix_revient'] ?? 0, 4) ?>"  
           onchange="handlePriceChange(this)"  => ajout fonction 
    />  
</td>
```

Lorsque l'on viens soumettre le formulaire, on viens récupérer l'ensemble des input ayant été marqué par la classe `touched`

```js
 function submitPrice(sender){  
    let oldHtml = sender.innerHTML;  
    sender.innerHTML = '<i class="fa fa-spinner fa-spin"></i>';  
    sender.disabled = true;  
  
	// récupération du formulaire qui contient les valeurs
    const form = $('#table_article_price');
    
    const url = "ajax.php?U=<?= setURL('do=php/article/ajax&action=general&todo=savePrice') ?>";  
    const fd = new FormData();  
  
	// on récupère tous les input qui ont la classe "touched" 
    const dirtyInput = Array.from(form.querySelectorAll('input.touched'));  
	
	// pour chacun, on viens les ajouter dans le formData
    dirtyInput.forEach(el => {  
        fd.append(el.name, el.value);  
    });  
      
    // on envoie les données dans le traitement.
    loadUrl(function(response){  
        console.log(response);  
  
    }, url, fd)  
  
}
```