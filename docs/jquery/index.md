# Jquery 

## Déclencher des fonction à l'ouverture de la page 

```js
<script>
  // permet de lancer la fonction au chargement de la page
  $(document).ready(function () {
      listArticles();
  });

  /**
   * Permet de générer la liste des articles avec leurs prix. S'affiche dans le bloc div_articles_price dans le HTML
   */
  function listArticles(){
      const url = "ajax.php?U=<?= setURL('do=php/article/ajax&action=general&todo=listeArticlePrice') ?>";
      const fd = new FormData();

      loadUrl(function(response){
          console.log(response);
          $('#div_articles_price').html(response);
      }, url, fd)
  }

</script>
```

## Scroll vers un élément 

```js
// configuration du comportement du scroll 
const configScroll = {  
    behavior: 'smooth',  
    block: 'center',  
}  
// check de la valeur de l'input 
if ($('#IdClient').val() == "") {
	// si la valeur est vide, on affiche le message d'erreur   
    $('#IdClientError').show();  
    // on scroll l'écran vers l'input 
    $('#RaisonSociale')[0].scrollIntoView(configScroll);  
    isInvalidClient = true;  
}
```

## Afficher/Cacher un élément 

```js
// permet de cacher un élément 
$('#IdClientError').hide();

// permet d'afficher un élément 
$('#IdClientError').show(); 

// définition de l'élément cacher par défaut 

```

## Récupération de valeur d'un input 

```js
function searchAnnuaireEntreprise() {  
    $('#div_entreprise').html('<div class="text-center mt-4 mb-4"><i class="fa-solid fa-spinner fa-spin me-2"></i>Chargement en cours</div>');  
  
    let url = "ajax.php?U=<?= setURL('do=ajax&action=general&todo=searchAnnuaireEntreprise'); ?>";  
    let fd = new FormData();  
    fd.append('siret', $('#siret_recherche').val().trim());  
    fd.append('libelle',$('#annuaire_recherche').val().trim());  
  
    loadUrl('#div_entreprise',url,fd);  
}
```

- `.val()` : fonction jquery pour récupérer la valeur set dans un input 
- `.trim()` : fonction pour nettoyer les espace

---

## MODAL 

### Définir la fonction 

