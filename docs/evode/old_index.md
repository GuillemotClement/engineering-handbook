# Evode 

## Helper hover 
Permet d'afficher un  message lors du survol de l'élément. Permet d'indiquer des renseigner sur l'action provoquer par le bouton par exemple
```html
<div class="pointer" onclick="changePassword()" title="Changer mon mot de passe">
    <i class="fa-solid fa-user-cog fa-2x"></i>
</div>
```

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

---

## Système de Confirmations et Messages

Documentation du système de gestion des confirmations, alertes et messages dans EUKLES.

## Vue d'ensemble

EUKLES propose un système unifié de confirmations et messages basé sur **3 fonctions JavaScript principales** définies dans `js/fonctions_generiques.js`.

Le système utilise la modale standard EUKLES (`.modalbox`) et s'intègre parfaitement avec Bootstrap et jQuery.

---

## Fonctions principales

### 1. `confirme()` - Demander une confirmation

**Signature**

```javascript
confirme(
  htmlCode,
  callbackYes,
  callbackNo,
  confirmTitle,
  confirmBtnLabel,
  cancelBtnLabel
);
```

**Paramètres**
| Paramètre | Type | Défaut | Description |
|-----------|------|--------|-------------|
| `htmlCode` | string | - | Contenu HTML/texte du message |
| `callbackYes` | function | - | Callback si l'utilisateur confirme |
| `callbackNo` | function | - | Callback si l'utilisateur annule |
| `confirmTitle` | string | "Confirmation" | Titre de la boîte de dialogue |
| `confirmBtnLabel` | string | "Confirmer" | Label du bouton de confirmation |
| `cancelBtnLabel` | string | "Annuler" | Label du bouton d'annulation |

**Structure HTML générée**

```html
<div class="modal-header">
  <h5 class="modal-title">__TITLE__</h5>
</div>
<div class="modal-body">__CONTENT__</div>
<div class="modal-footer">
  <button class="btn btn-secondary btn-no">__CANCEL_BTN__</button>
  <button class="btn btn-success btn-yes ms-3">__CONFIRM_BTN__</button>
</div>
```

**Exemple d'utilisation**

```javascript
confirme(
    "Êtes-vous sûr de vouloir supprimer cet élément ?",
    function(result) {
        console.log("Utilisateur a confirmé");
        // Effectuer l'action
        $.ajax({...});
    },
    function(result) {
        console.log("Utilisateur a annulé");
        // Rien à faire
    },
    "Suppression",
    "Supprimer",
    "Garder"
);
```

**Cas d'usage**

- Suppressions d'éléments
- Actions irréversibles
- Confirmations critiques
- Changements importants de paramètres

---

### 2. `alerte()` - Afficher une alerte simple

**Signature**

```javascript
alerte(htmlCode, callbackClickBtn, confirmTitle, confirmBtnLabel);
```

**Paramètres**
| Paramètre | Type | Défaut | Description |
|-----------|------|--------|-------------|
| `htmlCode` | string | - | Contenu du message |
| `callbackClickBtn` | function | null | Callback optionnel au clic du bouton |
| `confirmTitle` | string | "Alerte" | Titre de la boîte |
| `confirmBtnLabel` | string | "OK" | Label du bouton |

**Structure HTML générée**

```html
<div class="modal-header">
  <h5 class="modal-title">__TITLE__</h5>
</div>
<div class="modal-body">__CONTENT__</div>
<div class="modal-footer">
  <button class="btn btn-secondary btn-no">__CONFIRM_BTN__</button>
</div>
```

**Exemple d'utilisation**

```javascript
// Alerte simple
alerte("Opération terminée avec succès !", null, "Succès");

// Alerte avec callback
alerte(
  "Clôture mensuelle créée. La page va se recharger.",
  function () {
    location.reload();
  },
  "Clôture créée",
  "OK"
);
```

**Cas d'usage**

- Messages de succès
- Notifications informationnelles
- Confirmations de fin d'opération
- Messages d'erreur non-bloquants

---

### 3. `popup()` - Modal AJAX personnalisée

**Signature**

```javascript
popup(url, postData, bootstrapCssWidth);
```

**Paramètres**
| Paramètre | Type | Défaut | Description |
|-----------|------|--------|-------------|
| `url` | string | - | URL AJAX à charger |
| `postData` | FormData/Array | null | Données POST optionnelles |
| `bootstrapCssWidth` | string | "modal-lg" | Classe Bootstrap (modal-sm, modal-lg, modal-xl) |

**Exemple d'utilisation**

```javascript
// Modal simple
popup("ajax.php?do=php/facturation/ajax&action=general&todo=formDateCloture");

// Modal avec données POST
let fd = generateFormData("myForm");
popup("ajax.php?do=php/test&action=process", fd, "modal-xl");
```

**Cas d'usage**

- Formulaires complexes
- Contenu dynamique depuis le serveur
- Workflows multi-étapes

---

## Système de Messages Inline (Validation)

### `tooltipMsg()` - Message d'erreur sur champ

**Signature**

```javascript
tooltipMsg(id, msg);
```

Affiche un message d'erreur en **popover Bootstrap** sur un champ de formulaire.

**Exemple**

```javascript
// Afficher erreur
tooltipMsg("#email", "Email invalide");

// Effacer erreur
tooltipMsg("#email", "");
```

**Comportement**

- Ajoute une bordure rouge au champ
- Affiche un popover au-dessus
- Disparaît automatiquement après 2 secondes

### `inlineMsg()` - Message flottant

**Signature**

```javascript
inlineMsg(target, string, autohide, indice);
```

Affiche un message flottant qui s'efface automatiquement.

**Paramètres**

- `target` : ID de l'élément cible
- `string` : Contenu du message
- `autohide` : Délai avant effacement (secondes)
- `indice` : Identifiant unique du message

---

## Intégration avec les Modales

### Fermer une modale après confirmation

```javascript
function handleSubmitForm(sender) {
  if (!validateForm($("#myForm"))) {
    return false;
  }

  confirme(
    "Êtes-vous sûr de vouloir valider ?",
    function (result) {
      // OUI - Envoyer les données
      let fd = generateFormData("myForm");
      loadUrl(
        function (response) {
          if (response == 1) {
            // Succès
            alerte(
              "Opération réussie !",
              function () {
                closeModal();
                location.reload();
              },
              "Succès"
            );
          } else {
            // Erreur
            alerte(response, null, "Erreur");
          }
        },
        "ajax.php?do=php/test&action=process",
        fd
      );
    },
    null,
    "Confirmation",
    "Valider",
    "Annuler"
  );
}
```

---

## Exemple complet : Clôture mensuelle

```javascript
function handleSubmitForm(sender) {
  // 1. Validation du formulaire
  if (!validateForm($("#modaleDateCloture"))) {
    return false;
  }

  // 2. Demander confirmation
  confirme(
    "<p>Vous êtes sur le point de <strong>clôturer ce mois</strong>.</p>" +
      "<p>Les factures antérieures à cette date ne pourront plus être validées.</p>" +
      "<p>Êtes-vous sûr ?</p>",

    function (result) {
      // 3. Si OUI : effectuer l'action
      let oldHtml = sender.innerHTML;
      sender.innerHTML = '<i class="fa fa-spinner fa-spin"></i>';
      sender.disabled = true;

      let fd = generateFormData("modaleDateCloture");
      const url =
        "ajax.php?U=<?= setURL('do=php/facturation/ajax&action=general&todo=traitementDateCloture')?>";

      loadUrl(
        function (response) {
          if (response == 1) {
            // 4. Succès : alerte et rechargement
            alerte(
              "Clôture mensuelle créée avec succès !",
              function () {
                redirect(
                  "index.php?U=<?= setURL('do=php/facturation&action=liste&etat=a_valider'); ?>"
                );
              },
              "Clôture créée"
            );
          } else {
            // 5. Erreur : afficher le message
            alerte(response, null, "Erreur lors de la clôture");
            sender.innerHTML = oldHtml;
            sender.disabled = false;
          }
        },
        url,
        fd
      );
    },

    null, // Pas de callback pour "Annuler"
    "Clôture mensuelle",
    "Confirmer la clôture",
    "Annuler"
  );
}
```

---

## Bonnes pratiques

### ✅ À faire

- Utiliser `confirme()` pour les actions irréversibles
- Toujours valider le formulaire **avant** de demander confirmation
- Fournir des messages clairs et détaillés
- Utiliser HTML pour formater les messages complexes
- Fermer la modale automatiquement après succès

### ❌ À éviter

- Utiliser `alert()` natif (préférer `alerte()`)
- Afficher des confirmations inutiles
- Utiliser `confirme()` pour des informations simples
- Oublier les callbacks de traitement
- Laisser les boutons actifs pendant le chargement

---

## Fichiers concernés

| Fichier                      | Description                                       |
| ---------------------------- | ------------------------------------------------- |
| `js/fonctions_generiques.js` | Fonctions `confirme()`, `alerte()`, `popup()`     |
| `js/message.js`              | Fonctions `tooltipMsg()`, `inlineMsg()`           |
| `js/modal/modal.js`          | Gestion des modales `openModal()`, `closeModal()` |
| `index.php`                  | Chargement des ressources (jQuery, Bootstrap)     |

---

## Voir aussi

- [Système de Modales](modale.md) - Intégration complète des modales AJAX
- [Validation de Formulaires](../todo.md#validation) - Validation avant soumission
- [Fermeture Mensuelle](../evode.md) - Exemple d'utilisation dans la clôture


---


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
    const form = document.getElementById('table_article_price');
    
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


---

## Quick 

### Création d'une modale 

Ajout d'une méthode js qui viens ouvrir la modale. Elle pointe sur un endpoint en Ajax qui li gère l'affichage, puis ensuite le traitement de la modale.

```js
<script>
  function openModalFormKit(){
    const url = "ajax.php?do=php/article/ajax/action=general&todo=formKit"; // définis du endpoint
    const fd = new FormData(); // pour passer de la data
    fd.append('maVariable', <?= $maVarialbe ?>); // passer une clé valeur dans le body envoyer de la requête
    loadUrl('popup', url, fd);
  }
</script>
```

On viens aussi ajouter sur un bouton le déclenchement de l'ouverture de la modale :
```html
<button 
  class="btn btn-success" 
  onclick="openModalFormKit()"
>
  Ajouter un kit
</button>
``` 

Dans le fichier `php/article/ajax/general.php`, on viens définir le case pour gérer l'affichage de la modale 







## Vue d'ensemble

EUKLES utilise un système de modales personnalisé basé sur AJAX et jQuery. Le pattern permet d'ouvrir dynamiquement une modale avec du contenu généré par PHP, sans recharger la page.

---

## Architecture générale

### Le pattern EUKLES

```
Clic sur bouton
    ↓
JavaScript déclenche loadUrl()
    ↓
Requête AJAX POST vers PHP
    ↓
PHP génère HTML (formulaire, tableau, etc.)
    ↓
openModal() affiche la modale
```

### Fichiers clés

| Fichier             | Rôle                                         |
| ------------------- | -------------------------------------------- |
| `js/utility.js`     | Fonction `loadUrl()` - effectue requête AJAX |
| `js/modal/modal.js` | Fonctions `openModal()`, `closeModal()`      |
| `php/*/ajax/*.php`  | Génèrent le contenu HTML des modales         |

---

## Fonctionnement détaillé

### 1. La fonction `loadUrl()`

**Localisation:** `js/utility.js` ligne ~50

**Signature:**

```javascript
async function loadUrl(selector, url, param, append=false, showProgressBar=false)
```

**Paramètres:**

| Param             | Type               | Description                                      |
| ----------------- | ------------------ | ------------------------------------------------ |
| `selector`        | function ou string | Fonction callback OU sélecteur jQuery            |
| `url`             | string             | URL de la requête AJAX                           |
| `param`           | object/FormData    | Données POST à envoyer                           |
| `append`          | bool               | Si true, ajoute au lieu de remplacer (optionnel) |
| `showProgressBar` | bool               | Affiche une barre de progression (optionnel)     |

**Comportement:**

- Envoie une requête POST via Axios vers l'URL
- Gère l'encodage charset (ISO-8859-1 ou UTF-8)
- Si `selector` = callback → appelle `callback(response)`
- Si `selector` = string → injecte dans l'élément jQuery

**Exemple simple:**

```javascript
loadUrl(function (response) {
  openModal(response);
}, "ajax.php?U=...&do=php/facturation/ajax&action=general&todo=export");
```

---

### 2. La fonction `openModal()`

**Localisation:** `js/modal/modal.js` ligne ~14

**Signature:**

```javascript
function openModal(response = '')
```

**Comportement:**

1. Prend le contenu HTML en paramètre
2. L'injecte dans `.modalbox-html`
3. Affiche la modale avec `display: block`

**Structure HTML interne:**

```html
<div class="modalbox">
  <div class="modalbox-content">
    <div class="modalbox-close">
      <i class="fas fa-times"></i>
    </div>
    <div class="modalbox-html">
      <!-- Contenu injecté ici -->
    </div>
  </div>
</div>
```

**Exemple:**

```javascript
openModal("<h2>Mon contenu</h2><p>Hello!</p>");
// Affiche la modale avec le contenu
```

---

### 3. Fermer la modale

**Fonction:** `closeModal()`

```javascript
function closeModal() {
  document.querySelector(".modalbox").style.display = "none";
  $(".modalbox-html").html("");
}
```

Peut être appelée:

- Via un bouton dans la modale: `onclick="closeModal()"`
- Après une action réussie: `closeModal(); location.reload();`

---

## Pattern complet d'utilisation

### Étape 1: Le bouton HTML

```php
<button type="button" class="btn btn-primary"
        onclick="ouvrirMonModal('param1', 'param2')">
    <i class="fa-solid fa-folder"></i>
    Ouvrir ma modale
</button>
```

### Étape 2: La fonction JavaScript

```javascript
function ouvrirMonModal(param1, param2) {
  // 1. Construire l'URL
  let url =
    "ajax.php?U=<?= setURL('do=php/facturation/ajax&action=general&todo=monAction'); ?>&param1=" +
    param1 +
    "&param2=" +
    param2;

  // 2. Appeler loadUrl avec callback
  loadUrl(function (response) {
    // 3. Passer la réponse à openModal
    openModal(response);
  }, url);
}
```

### Étape 3: Le PHP qui génère le contenu

**Fichier:** `php/facturation/ajax/general.php` (ou `monAction.php`)

```php
<?php
$action = read_request_var('todo', '');
$param1 = read_request_var('param1', '');
$param2 = read_request_var('param2', '');

switch($action) {
    case 'monAction':
        afficherMonContenu($param1, $param2);
        break;
}

function afficherMonContenu($p1, $p2) {
    ?>
    <div class="modal-header">
        <h4>Titre de ma modale</h4>
    </div>

    <div class="modal-body">
        <p>Contenu dynamique avec param1=<?= $p1 ?></p>
        <form id="ma_form">
            <input type="text" name="mon_champ" />
        </form>
    </div>

    <div class="modal-footer">
        <button class="btn btn-secondary" onclick="closeModal()">Fermer</button>
        <button class="btn btn-primary" onclick="sauvegarder()">Valider</button>
    </div>
    <?php
}
?>
```

---

## Exemples concrets du projet

### Exemple 1: Exporter des factures

**Bouton HTML:**

```php
<button class="btn btn-danger" onclick="downloadFacture(this)">
    <i class="fa-solid fa-file-pdf"></i> Télécharger
</button>
```

**Fonction JS:** (dans `liste.php` ligne ~375)

```javascript
function downloadFacture(sender) {
  let fd = new FormData();
  $('input[name="FACTURE[]"]').each(function () {
    if ($(this).is(":checked")) {
      fd.append("FACTURE[]", $(this).val());
    }
  });

  let url =
    "ajax.php?U=<?= setURL('do=php/facturation/ajax&action=general&todo=exportFactureZIP'); ?>";
  loadUrl(
    function (response) {
      openModal(response); // ← Affiche la modale
    },
    url,
    fd
  );
}
```

### Exemple 2: Valider plusieurs factures

**Fonction JS:** (dans `liste.php` ligne ~387)

```javascript
function validateFacture(sender) {
  let fd = new FormData();
  $('input[name="FACTURE[]"]').each(function () {
    if ($(this).is(":checked")) {
      fd.append("FACTURE[]", $(this).val());
    }
  });

  confirme("Êtes-vous sûr?", function () {
    let url =
      "ajax.php?U=<?= setURL('do=php/facturation/ajax&action=general&todo=validateFactureMultiple'); ?>";
    loadUrl(
      function (response) {
        openModal(response); // ← Modale de confirmation
      },
      url,
      fd
    );
  });
}
```

---

## Variantes et patterns avancés

### Pattern 1: Avec FormData (upload fichier)

```javascript
function sauvegarderFormulaire() {
  let fd = new FormData(document.getElementById("ma_form"));
  let url =
    "ajax.php?U=<?= setURL('do=php/facturation/ajax&action=general&todo=save'); ?>";

  loadUrl(
    function (response) {
      if (response.success) {
        alert("Sauvegardé!");
        closeModal();
        location.reload();
      } else {
        openModal(response.html); // Modale d'erreur
      }
    },
    url,
    fd
  );
}
```

### Pattern 2: Avec barre de progression

```javascript
function importerDonnees() {
  let fd = new FormData();
  fd.append("file", document.getElementById("file_input").files[0]);

  let url =
    "ajax.php?U=<?= setURL('do=php/import/ajax&action=general&todo=import'); ?>";

  // Le 5e paramètre affiche la barre de progression
  loadUrl(
    function (response) {
      openModal(response);
    },
    url,
    fd,
    false,
    true
  ); // ← showProgressBar = true
}
```

### Pattern 3: Injecter dans un élément existant

```javascript
function chargerTableau() {
  let url =
    "ajax.php?U=<?= setURL('do=php/facturation/ajax&action=general&todo=loadTable'); ?>";

  // Injecter directement dans #mon_div au lieu d'ouvrir modale
  loadUrl("#mon_div", url);
}
```

### Pattern 4: Modale imbriquées

```javascript
function ouvrirModalParent() {
  let url = "ajax.php?U=...&action=modale_parent";
  loadUrl(function (response) {
    openModal(response);
  }, url);
}

// Dans la modale parent:
function ouvrirModalEnfant() {
  closeModal(); // Ferme la parent
  let url = "ajax.php?U=...&action=modale_enfant";
  loadUrl(function (response) {
    openModal(response);
  }, url);
}
```

---

## Points importants

### Droits d'accès

Toujours vérifier les droits en début du fichier PHP:

```php
<?php
if (!check('17V')) {  // 17 = Facturation, V = Visualisation
    errorCheckDroit();
    exit();
}
?>
```

### Encoding

- EUKLES supporte ISO-8859-1 et UTF-8
- `loadUrl()` gère la conversion automatiquement
- Assurer que la BDD et PHP utilisent le même charset

### Fermeture sécurisée

```javascript
function fermerEtRecharger() {
  closeModal();
  // Recharger ou rediriger
  location.reload();
  // ou: window.location.href = 'index.php?U=...';
}
```

---

## Structure fichier ajax typique

```
php/
└── facturation/
    └── ajax/
        ├── general.php (contient plusieurs actions)
        ├── cloture.php (actions liées à clôture)
        ├── relance.php (actions liées relances)
        └── ...
```

**Pattern dans `general.php`:**

```php
<?php
$action = read_request_var('todo', '');

switch($action) {
    case 'exportFactureZIP':
        // code...
        break;

    case 'validateFactureMultiple':
        // code...
        break;

    case 'loadTable':
        // code...
        break;
}
?>
```

---

## Conseils de développement

### À faire ✅

- **Vérifier les droits** avant d'afficher du contenu sensible
- **Valider les données** POST reçues en PHP
- **Gérer les erreurs** et retourner un message clair
- **Fermer la modale** après une action réussie
- **Utiliser FormData** pour les uploads

### À éviter ❌

- **Ne pas faire de redirects** depuis une modale (utiliser `location.reload()`)
- **Pas de code JS dans le PHP** retourné (mauvaise pratique)
- **Ne pas oublier `echo`** du contenu HTML
- **Pas de `die()` brut** - préférer exceptions et gestion d'erreur

---

## Troubleshooting

| Problème                        | Cause                              | Solution                                          |
| ------------------------------- | ---------------------------------- | ------------------------------------------------- |
| Modale n'apparaît pas           | `openModal()` pas appelée          | Ajouter `openModal(response)` dans le callback    |
| Contenu ne s'affiche pas        | PHP retourne erreur/blanc          | Vérifier console dev F12, regarder onglet Network |
| Modale reste vide               | FormData mal structurée            | Tester URL AJAX directement dans navigateur       |
| Caractères accentués cassés     | Encoding charset                   | Vérifier utf-8 dans PHP et HTML meta              |
| Bouton Fermer ne fonctionne pas | `onclick="closeModal()"` mal écrit | Vérifier la syntaxe exacte                        |
| AJAX retourne 404               | URL incorrecte                     | Vérifier le `setURL()` et les paramètres          |
| Données POST non reçues         | Pas de `FormData`                  | Utiliser `new FormData()` pour envoi              |

---

## Résumé rapide

```
VOTRE FONCTION JS
    ↓
loadUrl(callback, url, [data])
    ↓
AJAX POST vers PHP (via Axios)
    ↓
PHP retourne HTML
    ↓
Callback reçoit HTML
    ↓
callback(response) { openModal(response) }
    ↓
Modale affichée avec contenu!
```

---

## Références fichiers source

- **Modale JS:** `/js/modal/modal.js` - Structure et affichage
- **Utilitaires AJAX:** `/js/utility.js` - Fonction `loadUrl()`
- **Exemple facturation:** `/php/facturation/liste.php` - Boutons et modales
- **Exemple AJAX PHP:** `/php/facturation/ajax/general.php` - Backend modales


---

## Problème de SESSION 

Si à la connexion, cela ne fonctionne pas, le problème peut provenir de la config du `.ini` de la version du PHP.

Venir modifier ce fichier :
```ini
session.save_path = "C:\PHP8.2.5/tmp"
```

Puis relancer le serveur IIS

## Convertir tableau pour filtre IN 

```php
// check si on veux filtrer sur des tarifs 
if(count($filtre_tarifs) > 0) {
    // préparation du placeholder contenant les index souhaité 
    $placeholders = [];
    // on parcour le tableau de filtre 
    foreach ($filtre_tarifs as $key => $val){
        // on créer un nom pour le PDO 
        $name = ":tarif_" . $key;
        // on ajoute dans le placeholder
        $placeholders[] = $name;
        // on ajoute dans le tableau de vars passer à la requête => on lui ajoute le nom ":tarif_1" => 1
        $vars[$name] = $val;
    }
    // on extrait les valeurs et on les ajoute dans le IN()
    $sql .= " AND TCC.IdTypeClientCat IN (" . implode(", ", $placeholders) . ")";
}
```