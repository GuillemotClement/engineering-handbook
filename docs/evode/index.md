# Evode 

## Autocomplete 

Dans la page ou l'on souhaite ajouter l'autocomplete, on viens définir la fonction ajax.

```js
 $(document).ready(function(){

  // on pointe sur l'input qui doit déclencher l'autocomplète
  $('#RefArticle').autocomplete({
      // déclenche lorsque user tape au moins un caractères
      minLength: 1,
      // endpoint qui permet d'aller taper dans la db (framework Evode)
      source: "ajax.php?U=<?= setURL('do=ajax&action=autocomplete&todo=Articles); ?>",
      // permet de retirer le comportement par défaut de l'autocomplete
      focus: function(event, ui){
          return false;
      },
      // ui contient les données retourné par le endpoint
      select: function(event, ui){
          event.preventDefault(); // permet d'éviter le conflit pour set les valeur
          // ui.Item contient l'objet JSON des data retourner par le endpoint
          // on ajoute cette donnée dans le champs avec l'id IdArticle
          $('#IdArticle').val(ui.item.IdArticle); // à la sélection dans la liste, on set l'id issue de ui.Item sur l'input
          $('#RefArticle').val(ui.item.Ref).attr('readonly', true); // a la sélection, on passe la valeur et on passe l'input en readonly pour empecher la modif
          $('#NomArticle').val(ui.item.Nom).attr('readonly', true);
      }
  }).data('ui-autocomplete')._renderItem = function(ul, item){
      // définit le select list avec les données affichées
      let html_select = "<a><b>" + item.Ref + "</b> - " + item.Nom + "</a>";
      return $('<li></li>')
          .data("ui-autocomplete-item", item)
          .append(html_select)
          .appendTo(ul);
  };
});
```
On viens dans le HMTL, définir les bon attributs pour connecter l'autocomplete : 

```html
<form method="POST" name="form_kit" id="form_kit" autocomplete="off" novalidate onsubmit="return saveKit(this)">
  <input type="hidden" name="DATA[IdArticleParent]" id="id_article_parent" value="<?= $IdArticleParent ?>">
  <div class="modal-header">
      <h5 class="modal-title"><?= $titre; ?></h5>
  </div>

  <div class="modal-body">
      <div class="row">
          <div class="form-group col-12 col-lg-6">
              <label for="NomArticle">Nom :</label>
              <!-- le champ sera compléter par l'autocomplete -->
              <input type="text" class="form-control" name="NomArticle" id="NomArticle">
              <!-- On définit un champ caché pour passer l'id de l'article qui sera remplit par l'autocomplete -->
              <input type="hidden" name="DATA[IdArticle]" id="IdArticle">
          </div>

          <div class="form-group col-12 col-lg-6">
              <label for="RefArticle">Référence :</label>
              <!-- C'est le champs qui viens lancer l'autocomplete -->
              <input type="text" class="form-control" name="RefArticle" id="RefArticle">
          </div>
      </div>

      <div class="row">
          <div class="form-group col-12 col-lg-6">
              <label for="Quantite">Quantité :</label>
              <input type="number" class="form-control" name="DATA[Quantite]" id="Quantite">
          </div>

          <div class="form-group col-12 col-lg-6">
              <label for="PrixInclus">Prix inclus :</label>
              <select class="form-control" name="DATA[PrixInclus]" id="PrixInclus">
                  <option value="1">Oui</option>
                  <option value="0">Non</option>
              </select>
          </div>
      </div>
  </div>
  <div class="modal-footer justify-content-center">
      <div class="btn-toolbar" role="toolbar">
          <button type="button" class="btn btn-primary" onclick="closeModal()">
              <i class="fas fa-times"></i><span>Fermer</span>
          </button>

          <button class="btn btn-success" type="submit">
              <i class="fa-solid fa-floppy-disk"></i><span>Enregistrer</span>
          </button>
      </div>
  </div>
</form>
```

Dans le dosiser `<project>/ajax/autocomplete.php`, on définit l'implémentation de l'autocomplète qui viens rechercher dans la base de données : 

```php 
<?php

use Evode\Database\DB;
use Evode\Utilities\DateUtilities;

$todo = read_request_var('todo');

$sql = "";
$vars = array();
$result = array();

$term = read_request_var('term', '');

$setUcFirst = true;

// on rentre dans le case et on réalise la requête permettant d'extraire les données que l'on peut sélectionner.
switch ($todo) {
  case 'Articles':
    $IdClient = read_request_var("IdClient", 0);
    $isFormArticle = read_request_var("isFormArticle", false);
    $date = read_request_var("date", '');

    $setUcFirst = false;

    if ($IdClient > 0 || $isFormArticle) {
        $vars[':IdClient'] = $IdClient;
        $vars[':date1'] = date('d/m/Y');
        $vars[':date2'] = date('d/m/Y');
        $vars[':date3'] = date('d/m/Y');
        $vars[':date4'] = date('d/m/Y');

        if (empty($date)) {
            $date = date('d/m/Y');
        }
        $vars[':date5'] = $date;
        $vars[':date6'] = $date;
        $vars[':date7'] = $date;
        $vars[':date8'] = $date;
        $vars[':date9'] = $date;
        $vars[':date10'] = $date;

        $sql = "SELECT DISTINCT Articles.IdArticle AS NumArticle, 
                  Articles.RefArticle AS Ref, 
                  CONCAT(Groupes.NomGroupe,' > ',GroupesDetail.NomGroupeDetail,' > ',ArticlesFamilles.LibelleFamille) AS Famille, 
                  Articles.NomArticle AS Nom, 
                  ArticlesTarifs.PrixArticle AS Prix, 
                  PrixAchat AS Marge, 
                  QtMiniTarif, 
                  ArticlesPromotions.Prix AS PrixPromo, 
                  ArticlesPrixSpeciaux.Prix AS PrixSpec, 
                  GroupesDetail.IdGroupe, 
                  Articles.DureeArticle, TypesDuree.DureeMois,
                  Articles.TVA,
                  /*Clients.IdTVA,*/
                  CASE WHEN Articles.TVA = 1
                      THEN 
                        (
                            SELECT TOP 1 TypesTVA.IdTVA
                            FROM Clients AS C
                                INNER JOIN TypesTVA AS TypesTVAClient ON TypesTVAClient.IdTVA = C.IdTVA
                                INNER JOIN TypesTVA ON TypesTVA.NewTaux = TypesTVAClient.NewTaux
                            WHERE C.IdClient = Clients.IdClient
                              AND ISNULL(TypesTVA.DateFin, :date5) >= :date6
                            ORDER BY ISNULL(TypesTVA.DateFin, :date7) DESC, TypesTVA.Montant DESC
                        ) 
                      ELSE 
                        (
                            SELECT TOP 1 TypesTVA.IdTVA
                            FROM TypesTVA
                            WHERE ISNULL(TypesTVA.DateFin, :date8) >= :date9
                              AND TypesTVA.Montant = 0
                            ORDER BY ISNULL(TypesTVA.DateFin, :date10) DESC, TypesTVA.Montant DESC
                        ) 
                  END AS 'IdTVA',
                  (
                    SELECT AT.PrixArticle
                    FROM ClientsTarifs
                      INNER JOIN ArticlesTarifs AS AT ON AT.IdTypeClientCat = ClientsTarifs.IdTypeClientCat
                    WHERE ClientsTarifs.IdClient = Clients.IdClient
                      AND ClientsTarifs.IdArticleAxe = Articles.IdArticleAxe
                      AND AT.IdArticle = Articles.IdArticle
                      AND AT.HF = '0'
                  ) AS 'PrixClient',
                  TarifDefault.PrixArticle AS 'PrixArticleDefault',
                  Articles.ContratPerpetuel,
                  Articles.Forfait, Articles.VolumeForfait
                FROM Articles
                  LEFT OUTER JOIN Clients ON Clients.IdClient = :IdClient
                  LEFT OUTER JOIN ArticlesTarifs ON ArticlesTarifs.IdArticle = Articles.IdArticle AND ArticlesTarifs.IdTypeClientCat = Clients.IdTypeClientCat AND ArticlesTarifs.HF = '0'
                  LEFT OUTER JOIN ArticlesPromotions ON ArticlesPromotions.IdArticle = Articles.IdArticle AND ArticlesPromotions.Actif = '1' AND ArticlesPromotions.IdTypeClientCat = Clients.IdTypeClientCat AND ArticlesPromotions.DateDebut <= :date1 AND ArticlesPromotions.DateFin >= :date2
                  LEFT OUTER JOIN ArticlesPrixSpeciaux ON ArticlesPrixSpeciaux.IdArticle = Articles.IdArticle AND ArticlesPrixSpeciaux.IdClient = Clients.IdClient AND ((ArticlesPrixSpeciaux.DateDebut IS NULL AND ArticlesPrixSpeciaux.DateFin IS NULL) OR (ArticlesPromotions.DateDebut <= :date3 AND (ArticlesPrixSpeciaux.DateFin IS NULL OR ArticlesPrixSpeciaux.DateFin >= :date4)))
                  LEFT OUTER JOIN ArticlesFamilles ON ArticlesFamilles.IdFamille = Articles.IdFamille
                  LEFT OUTER JOIN GroupesDetail ON GroupesDetail.IdGroupeDetail = ArticlesFamilles.IdGroupeDetail
                  LEFT OUTER JOIN Groupes ON Groupes.IdGroupe = GroupesDetail.IdGroupe
                  LEFT OUTER JOIN TypesDuree ON TypesDuree.IdTypeDuree = Articles.IdTypeDuree 
                  LEFT OUTER JOIN ArticlesTarifs AS TarifDefault ON TarifDefault.IdArticle = Articles.IdArticle AND TarifDefault.IdTypeClientCat = 0
                WHERE ISNULL(Articles.Desactive, 0) = 0 ";
        if (!empty($term)) {
            $sql .= "AND (
                        Articles.NomArticle LIKE :term1
                        OR Articles.RefArticle LIKE :term2
                    )";
            $vars[':term1'] = '%'.$term.'%';
            $vars[':term2'] = '%'.$term.'%';
        }
        $sql .= " ORDER BY Articles.NomArticle ASC, Articles.RefArticle ASC";
    } else {
        $sql = "SELECT Articles.*
              FROM Articles
              WHERE ISNULL(Articles.Desactive, 0) = 0 ";
        if (!empty($term)) {
            $sql .= "AND (
                    Articles.NomArticle LIKE :term1
                    OR Articles.RefArticle LIKE :term2
                  )";
            $vars[':term1'] = '%'.$term.'%';
            $vars[':term2'] = '%'.$term.'%';
        }
        $sql .= " ORDER BY Articles.NomArticle ASC, Articles.RefArticle ASC";
    }

    break;
```