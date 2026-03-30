# PHP 

## Extension 
```shell
# fedora 
sudo dnf install -y \ php-intl \ php-pgsql \ php-xml \ php-amqp \ php-gd \ php-sodium \ php-curl \ php-zip \ php-pecl-redis6

# verif des extension 
php -m | grep -E "intl|pdo_pgsql|xsl|amqp|gd|openssl|sodium|iconv|redis|curl|zip"

# relancer 
sudo systemctl restart php-fpm
```

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

## listing 

### Modifier le style d'une ligne 

Dans la requête du listing, on viens utiliser l'alias `nv_tr_style`, ce qui permet d'appliquer le style dans le `CASE WHEN` de la requête SQL.

```php
CASE  
    WHEN tache.tache_etat = 1 THEN 'background-color:#d3d3d3;'    ELSE ''END AS nv_tr_style,
```

### addClosure() 

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

--

## REQUEST 

### Récupération des headers

La fonction `getallHeaders()` vient lire les headers de la requête entrante et les retournes sous forme de tableau.

```php
$headers = getallheaders();

// $headers ressemble à ça :
[
    'Authorization' => 'Bearer eyJhbGci...',
    'Content-Type'  => 'application/json',
    'Host'          => 'ton-app.com',
]
```

### Extraction de token 

Une fois les headers récupérés, on peut venir extraitre un token, par exemple le token d'Authorization 

```php
$token = str_replace('Bearer ', '', $headers['Authorization'] ?? '');
// $token = "eyJhbGci..."
```