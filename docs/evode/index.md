# Evode 

## Affichage d'élément 

On souhaite afficher un bouton permettant de vider des input lorsque l'utilisateur à sélectionner un éléménnt de l'autocomplete.

On vient ajouter le bouton dans le formulaire 
```html
<div class="form-group col-12 col-lg-6">
    <label for="RefArticle">Référence :</label>
    <div class="input-group">
        <input  type="numeric"
                class="form-control"
                name="RefArticle"
                id="RefArticle"
                value="<?= $data_article_kit->RefArticle ?>"
                <?= (!empty($data_article_kit->RefArticle)) ? 'readonly' : '' ?>
        />
        <button type="button" 
                class="btn btn-danger btn-sm" 
                id="btn_clear_input" 
                <!-- par défaut, le bouton n'est pas affiché -->
                style="display: none" 
                <!-- la fonction qui déclenche le vidage des inputs-->
                onclick="handleClearInput()" 
        >
            <i class="fas fa-window-close"></i>
        </button>
    </div>
</div>
```

On vient implémenter la fonction js qui permet de vider les champs :
```js 
function handleClearInput(){
    // on récupère les 3 champs à vider
    const $idArticle = $('#IdArticle');
    const $refArticle = $('#RefArticle');
    const $nomArticle = $('#NomArticle');
    
    // on viens vider leurs valeurs, et pour deux champs, les rendre de nouveau modifiable
    $idArticle.val('');
    $refArticle.val('').attr('readonly', false);
    $nomArticle.val('').attr('readonly', false);
    
    // on vient cacher le bouton qui permet de déclencher le vide input
    $('#btn_clear_input').hide();
}
```

C'est la fonction autocomplete qui permet d'afficher le bouton lorsque l'on a sélectionné un élément dans la liste :
```js 
$(document).ready(function(){
  $('#RefArticle').autocomplete({
    minLength: 1,
    source: "ajax.php?U=<?= setURL('do=ajax&action=autocomplete&todo=Articles'); ?>",
    focus: function(event, ui){
        return false;
    },
    select: function(event, ui){
        event.preventDefault(); // permet d'éviter le conflit pour set les valeur (RefArticle)
        $('#IdArticle').val(ui.item.IdArticle);
        $('#RefArticle').val(ui.item.RefArticle).attr('readonly', true);
        $('#NomArticle').val(ui.item.NomArticle).attr('readonly', true);

        // on affiche le bouton qui permet de vier les input
        $('#btn_clear_input').show(); 
    }
}).data('ui-autocomplete')._renderItem = function(ul, item){
    let idArticleParent = <?= $id_article_parent ?>;
    // comparaison avec l'article en cours
    let isParent = (item.IdArticle == idArticleParent);
    let html_select = "";
    if (isParent){
        // affiche la ligne en grisé
        html_select = "<a style='color: #ccc; cursor: not-allowed;'><b>"+ item.RefArticle + "</b> - " + item.NomArticle + " (Article actuel)</a>";
    } else {
        html_select = "<a><b>" + item.RefArticle + "</b> - " + item.NomArticle + "</a>";
    }

    return $('<li></li>')
        .data("ui-autocomplete-item", item)
        .append(html_select)
        .appendTo(ul);
  };
});
```

---


## Mettre en place une confirmation

On commence par ajouter un bouton qui déclenche la fonction. Dans l'exemple, on as une liste et le bouton permet de supprimer l'article correspondant.
```html
<tr onclick="openModalFormKit(<?= $row->ArticleKitId ?>)">
    <td><b><?= $row->RefArticle ?></b></td>
    <td><?= $row->NomArticle ?></td>
    <td><?= $row->Quantite ?></td>
    <td><?= $row->PrixInclus == '1' ? 'Oui' : 'Non' ?></td>
    <td>
        <!-- le bouton déclenche la supression de l'article -->
        <!-- event.stopPropagation() emêcher la remonter de l'event, et permet de déclencher l'action sans déclencher l'ouverture de la modale sur l'élément parent -->
        <button type="button" class="btn btn-danger btn-sm" onclick="event.stopPropagation(); handleDeleteArticle(<?= $row->ArticleKitId ?>)">
            <i class="fas fa-trash"></i>
        </button>
    </td>
</tr>
```

On viens ensuite implémenter la fonction qui : 

- ouvre une modale qui demande la confirmation de l'utilisateur 
- si confirmer, déclenche l'appel Ajax et viens supprimer l'élément en DB 

```js 
function handleDeleteArticle(ArticleKitId){
  // on utilise fonction qui permet d'afficher une modale personnalisé 
  // en premier argument on passe le text qui sera affiché 
  // en second argument, on passe la fonction callback qui est exécuter si l'user confirme 
  confirme("Etes-vous sûr de vouloir supprimer l'article du kit ?", function(){
      // affiche un overload de chargement pendant le traitement serveur 
      showLoader(1);
      
      // on définit le endpoint pour accéder au case côté php via l'ajax
      let url = "ajax.php?U=<?= setUrl('do=php/article/ajax&action=general&todo=deleteArticleKit') ?>";
      // création d'un nouvel objet form data qui permet d'envoyer des données au PHP 
      let fd = new FormData();
      // on ajoute une clé valeur qui sera transmis au php
      fd.append('ArticleKitId', ArticleKitId);

      // envoie de la requête au serveur
      // en premier argument, on passe la fonction de callback déclencher à la réponse du serveur 
      loadUrl(function(response){
          // affichage de la response retourné par le serveur 
          console.log(response);
          // fermeture du loader 
          showLoader(0);
          // on appel la fonction qui permet de reload la liste des articles dans notre exemple 
          showKit();
          // on ferme la modal 
          closeModal();
      }, url, fd);
  });
}
```

---

## Autocomplete 

Dans la page ou l'on souhaite ajouter l'autocomplete, on viens définir la fonction ajax.

```js
 $(document).ready(function(){

  // on pointe sur l'input qui doit déclencher l'autocomplète
  $('#RefArticle').autocomplete({
      // déclenche lorsque user tape au moins un caractères
      minLength: 1,
      // endpoint qui permet d'aller taper dans la db (framework Evode)
      source: "ajax.php?U=<?= setURL('do=ajax&action=autocomplete&todo=Articles'); ?>",
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