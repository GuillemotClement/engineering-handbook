# Evode 

## Modale 

### Déclencher l'affichage de la modale 

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


### Listing dans une modale 

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

