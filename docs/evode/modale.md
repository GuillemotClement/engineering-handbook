
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
