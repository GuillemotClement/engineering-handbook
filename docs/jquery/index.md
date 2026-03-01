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