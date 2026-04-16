# PHP 

## CONSTANTE MAGIQUE 

### __DIR__

`__DIR__`est une constante magique qui retourne le chemin absolu du dossier où se trouve le fichier actuellement exécuté.

C'est le path du fichier dans lequel `__DIR__` est utiliser qui compte.

Par exemple, j'ai une fonction qui utilise cette constante magique dans `/inc/fonction_generiques.php`. J'appelle cette fonction depuis le fichier `/php/webhook/index.php`.

C'est le path `/inc/` qui sera retourné.



## Extension 
```shell
# fedora 
sudo dnf install -y \ php-intl \ php-pgsql \ php-xml \ php-amqp \ php-gd \ php-sodium \ php-curl \ php-zip \ php-pecl-redis6

# verif des extension 
php -m | grep -E "intl|pdo_pgsql|xsl|amqp|gd|openssl|sodium|iconv|redis|curl|zip"

# relancer 
sudo systemctl restart php-fpm
```

## COMPOSER

### Installation de dépendances

Pour installer les dépendances avec composer, il faut se placer dans le folder qui contient le `composer.json`.

Dans le terminal, lancer l'installation des plugins : 

```shell
composer install --no-interaction
```

### Erreur Class not found

Parfois, composer contient des fichiers de caches qui peuvent casser le système. Pour résoudre ce type de problème, lancer la commande : 

```shell
composer dump-autoload
```
La commande vient régénéré les fichiers en scannant ce qui est réellement dans le `/vendor` et reconstruit les mappings.

### Forcer l'installation des dépendances 

```shell
# ingore les contriantes de version 
composer install --ignore-platform-reqs
```

## EVODE 



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

---

## ARRAY 

### in_array 

Permet de trouver une valeur dans un array

```php 
in_array(<element_chercher>, <array>)
```