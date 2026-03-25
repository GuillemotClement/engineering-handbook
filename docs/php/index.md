# PHP 

## Composer 

Pour installer les dépendances avec composer, il faut se placer dans le folder qui contient le `composer.json`.

Dans le terminal, lancer l'installation des plugins : 

```shell
composer install --no-interaction
```

## EVODE 

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