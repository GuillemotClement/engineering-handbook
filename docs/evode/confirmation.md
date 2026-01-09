# Système de Confirmations et Messages

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
