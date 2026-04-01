# Evode 

## JQUERY
### Modale 

Dans le fichier qui nécessite de d'afficher avec l'ajax une modale 

```js
function displayHistoriqueStatusFacture() {
	let url = "ajax.php?U=<?= setURL('do=php/facturation/ajax&action=general&todo=historiqueStatusFacture'); ?>";
	let fd = new FormData();
	fd.append('IdFacture', <?= $IdFacture ?>);

	loadUrl('popup', url, fd);
}
```

On viens appeler la fonction lorsque l'on clique sur un bouton par exemple 

```php
 <button type="button" onclick="displayHistoriqueStatusFacture()">
```

Dans le fichier ajax, on ajoute ensuite le case, et le code pour implémenter les besoins. Ici on vient réaliser un lisitng basique dans la modale.

```php
case 'historiqueStatusFacture':
	$IdFacture = read_request_var('IdFacture', 0);

// déclaration de la requête SQL
	$sql = "SELECT 
				fhs.IdFactureHistoriqueSeres AS nv_idFactureHistoriqueSeres,
				FORMAT(fhs.DateAction, 'dd/MM/yyyy HH:mm:ss') AS Date,
				tfss.Libelle 
			FROM FacturesHistoriqueSeres fhs
				LEFT JOIN TypesFactureStatutSeres tfss ON fhs.IdTypeFactureStatutSeres = tfss.IdTypeFactureStatutSeres
			WHERE fhs.IdFacture = :IdFacture";

	$listing = new listing($sql);
	$listing->table_id = 'historique_facture';
	$listing->vars = [':IdFacture' => $IdFacture];
	$listing->title = "Historique";
	$listing->titleID = "title_historique";
	$listing->orderBy(["Date" => "DESC"]);
	$listing->isLien = false;

	?>

	<div class="modal-header">
		<h5 class="modal-title" id="title_historique"></h5>
	</div>
	<div class="modal-body">
		<?= $listing->show(); ?>
	</div>
	<div class="modal-footer">
		<div class="btn-toolbar" role="toolbar">
			<button type="button" class="btn btn-primary" onclick="closeModal()">
				<i class="fas fa-times"></i><span>Fermer</span>
			</button>
		</div>
	</div>
<?php
break;
```

---

## PHP 

### selectGenerique()

Permet de générer des selectlist générique. Elle viens directement rechercher dans la table pour afficher les option disponible.

```php
echo selectGenerique( $id_selected, // valeur sélectionnée 
'entreprises', // table 
"AND pays = 'FR'", // condition supplémentaire 
'id_entreprise', // colonne ID 
'nom_entreprise', // colonne libellé 
'actif', // colonne actif (optionnel) 
'', // colonne désactivé (optionnel) 
'', // valeur défaut option vide 
'Choisir une entreprise' // libellé défaut );
```

### listing 

#### Modifier le style d'une ligne 

Dans la requête du listing, on viens utiliser l'alias `nv_tr_style`, ce qui permet d'appliquer le style dans le `CASE WHEN` de la requête SQL.

```php
CASE  
    WHEN tache.tache_etat = 1 THEN 'background-color:#d3d3d3;'    ELSE ''END AS nv_tr_style,
```

#### addClosure() 

Permet de faire des actions particulière, en venant passer de la donnée issue de la requête.

Dans un premier temps, on prépare une colonne pour transmettre de la données vers la closure 

```php 
CONCAT(tache.tache_id,'||',tache.tache_salarie_id,'||',tache.tache_initiateur_id,'||',tache.tache_etat) AS 'Actions'
```

Dans la définition du listing, on vient ajouter la méthode `addClosure()`, et on applique des instructions 
Dans l'exemple, on vient ajouter 2 boutons permettant d'exécuter des actions spécifique 

```php 
$listing->addClosure('Actions', function ($var) {  
    $tab = explode("||", $var); // on récupère les données info dans un tableau
    $tache_id = $tab[0];  
    $tache_salarie_id = $tab[1];  
    $tache_initiateur_id = $tab[2];  
    $tache_etat = $tab[3];  
  
    $html = '<div class="">';  
    if ($tache_etat != 1 && $tache_salarie_id == $tache_initiateur_id && $tache_salarie_id = $_SESSION['user']['IdSalarie']) {  
        $html .= '<button type="button" class="btn btn-sm btn-info me-3" title="Réaliser la tâche" onclick="realiserTache(\''.$tache_id.'\')">  
                    <i class="fa-solid fa-check"></i>                  </button>';  
    }  
    if ($tache_initiateur_id == $_SESSION['user']['IdSalarie']) {  
        $html .= '<button type="button" class="btn btn-sm btn-success" onclick="formTache(\''.$tache_id.'\')">  
                    <i class="fa-solid fa-edit"></i>                  </button>';  
    }  
    $html .= '</div>';  
  
    return $html;  
});
```

---