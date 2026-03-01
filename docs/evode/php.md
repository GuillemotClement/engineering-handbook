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