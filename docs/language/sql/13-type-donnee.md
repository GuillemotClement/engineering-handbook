# Type SQL 

Permet de définir l'information qui sera stockée dans une colonne de la table (nombre, string, date, etc).

Les types de données sont utilisés pour : 
- Limiter ce que l'on peut placer dans une colonne
- Structurer les données 
- Optimiser les ressources
- Garder de la précision.

---
## Classification des types de données 

Il existe 5 grandes familles :
- Types numérique: `INTEGER`, `NUMERIC`, `REAL`
- Types texte: `CHAR`, `VARCHAR`, `TEXT`
- Types logique : `BOOLEAN`
- Types dates et heures : `DATE`, `TIME`, `TIMESTAMP`
- Types spéciaux: `UUID`, `JSONB`, `ARRAY`

PostgreSQL fournis des types intégrés, et permet également de créer des types de données custom. 

--- 

## Types numérique 

Ce sont les types qui permettent de stocker les nombres entiers (sans la partie décimal), les nombres exacts (nombres avec un nombre fixe après la virgule) ou les flottant (nombres grands)

### INTEGER 

Permet de stocker des nombres entiers, qui n'ont pas besoin de la partie décimale. Il existe trois variantes :
- `SMALLINT`: Intervalle : de -32 768 à 32 767.
- `INTEGER` ou `INT` : standard : -2 147 483 648 à 2 147 483 647
- `BIGINT` : grand nombre : -9 223 372 036 854 775 808 à 9 223 372 036 854 775 807

On l'utilise pour stocker les identifiants, des quantité (nb d'article, nb d'étudiants). 

### NUMERIC 

Permet de stocker les nombres exacts à virgule fixe. Utilise pour stocker des valeur critique, où il n'est pas permis d'avoir des erreurs sur un chiffre avec la virgule (prix par exemple).

On l'utilise pour les calculs financiers, stockage de mesures précises.

```sql
-- syntaxe
NUMERIC(precision, scale)

-- exemple 2345.67
NUMERIC(6, 2)
```

- `precision` : nombre total de chiffre (avant et après la virgule)
- `scale`: nombre de chiffre après la virgule 

### REAL 

Utiliser pour stocker de gros nombres, mais qui n'ont pas besoin d'être super précis.

Par exemple, le résultat de calculs scientifique (masse des atomes, distance à la lune), ou de la modélisation de donné où les erreurs sont acceptables.

### Récapitulatif

|Type de données|Intervalle|Précision|Exemples d'utilisation|
|---|---|---|---|
|`SMALLINT`|-32 768 à 32 767|Nombres entiers|Petits nombres (âge, notes).|
|`INTEGER`|-2 147 483 648 à 2 147 483 647|Nombres entiers|Identifiants, quantités.|
|`BIGINT`|-9 223 372 036 854 775 808 à 9 223 372 036 854 775 807|Nombres entiers|Très grands nombres.|
|`NUMERIC`|Dépend de `precision` et `scale`|Nombres décimaux exacts|Finance, mesures.|
|`REAL`|Environ 6 chiffres décimaux|Virgule flottante|Données approximatives ou scientifiques.|
|`DOUBLE PRECISION`|Environ 15 chiffres décimaux|Virgule flottante haute précision|Science, recherche.|

### Alias 

Dans PostgreSQL, beaucoup de type numérique ont des alias. Ce sont des noms alternatifs pour les types qui se comportent pareil mais rendent le code plus lisible ou compatible avec d'autre SGBD.

#### Alias type entier 

|Alias|Type réel|Taille|Intervalle|
|---|---|---|---|
|`INT`|`INTEGER`|4 octets|−2 147 483 648 à 2 147 483 647|
|`INT4`|`INTEGER`|4 octets|(alias obsolète)|
|`SMALLINT`|`SMALLINT`|2 octets|−32 768 à 32 767|
|`INT2`|`SMALLINT`|2 octets||
|`BIGINT`|`BIGINT`|8 octets|−9 quintillions à +9 quintillions|
|`INT8`|`BIGINT`|8 octets||


#### Alias type numeric 

|Alias|Type réel|Utilisation|
|---|---|---|
|`DEC`|`NUMERIC`|Synonyme selon le standard SQL|
|`DECIMAL`|`NUMERIC`|Pareil|

#### Alias type float 

|Alias|Type réel|Précision|
|---|---|---|
|`FLOAT`|`DOUBLE PRECISION`|~15 chiffres (par défaut)|
|`FLOAT(24)`|`REAL`|~6 chiffres|
|`FLOAT(53)`|`DOUBLE PRECISION`|~15 chiffres|
|`FLOAT8`|`DOUBLE PRECISION`|Alias obsolète|
|`FLOAT4`|`REAL`|Alias obsolète|

