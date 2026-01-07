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

--- 

## Type Text

### CHAR

`CHAR` ou character est une chaîne de longeur fixe. Si la chaîne fait moins de caractère que prévu, elle est automatiquement complétée avec des espaces.

Ce type est utilisé pour les chaînes devant avoir la même taille (code, code-bare, identifiant à taille fixe).

|id|code - CHAR(5)|
|---|---|
|1|''`ABC`''|

#### Comparaison de CHAR 

Si on compare des chaîne dans un champ `CHAR` sans tenir compte des espaces ajoutés, cela peut provoquer des comportement innatendu.

L'utilisation de `TRIM()` permet de s'assurer du résultat 

```sql
SELECT * FROM cities WHERE TRIM(iso_code) = 'NYC';
```

### VARCHAR

Permet de stocker des chaîne de longeur variable mais avec une limite max de caractère.

Ce type conserve que le texte réel. Si a longeur est dépasser, une erreur est retourée.

On l'utilise généralement pour les nom, email ...

|id|username - VARCHAR(10)|
|---|---|
|1|''`Alice`''|

### TEXT 

Chaîne sans limite de longueur. 

|id|content - TEXT|
|---|---|
|1|''`Ceci est un long texte. Pas de limites !`''|

### Recapitulatif 

|Type de données|Longueur|Performance|Quand l’utiliser ?|
|---|---|---|---|
|`CHAR(n)`|Fixe|Plus rapide pour des chaînes de longueur fixe|Pour des codes à longueur fixe (genre ISO)|
|`VARCHAR(n)`|Longueur max `n`|Plus rapide que `TEXT` si tu mets une limite|Pour des chaînes de longueur variable avec un max connu|
|`TEXT`|Illimitée|Le plus polyvalent|Pour des textes longs dont la taille est imprévisible|

---

## Type boolean 

Il permet de contenu une valeur `TRUE` ou `FALSE` ou `NULL`

```sql
SELECT username, is_active
FROM users
WHERE is_active = TRUE;
```

```sql
SELECT task_name
FROM tasks
WHERE is_complete = FALSE;
```

---

## Type temps 

Ils permettent de stocker des valeurs lié au temps (date, opérations temporel, gestion des fuseaux horaire)

### DATE 

Permet de stocker une date, sans l'heure. Il enregistre l'année, le mois et le jour au format `YYYY-MM-DD`

- conserver la date d'anniversaire d'un user 
- date d'un event ou il n'y a pas d'heure 

|id|name - VARCHAR(100)|event_date - DATE|
|---|---|---|
|1|SQL Workshop|2025-06-15|
|2|Python|2025-06-17|
|3|Java Courses|2025-06-25|

### TIME 

Permet de stocker l'heure au format `HH:MI:SS`

|id|task_name - VARCHAR(100)|start_time - TIME|
|---|---|---|
|1|Team Meeting|09:00:00|
|2|Code Review|11:30:00|
|3|Client Call|15:00:00|

### TIMESTAMP 

Permet de conserver la date et l'heure au format `YYYY-MM-DD HH:MI:SS`

|id|action - VARCHAR(100)|login_time - TIMESTAMP|
|---|---|---|
|1|User Login|2023-10-15 14:30:00|
|2|File Uploaded|2023-10-15 15:10:00|
|3|User Logout|2023-10-15 16:45:00|

### TIMESTAMPTZ

Permet de conserver la date et l'heure et le fuseau horaire.

|id|subject_name|class_date - DATE|start_time - TIME|end_time - TIME|created_at - TIMESTAMP|
|---|---|---|---|---|---|
|1|Mathematics|2023-10-16|09:00:00|10:30:00|2023-10-12 14:00:00|
|2|Physics|2023-10-16|11:00:00|12:30:00|2023-10-12 14:00:00|
|3|Chemistry|2023-10-17|09:00:00|10:30:00|2023-10-12 14:01:00|
|4|Literature|2023-10-17|11:00:00|12:30:00|2023-10-12 14:01:00|
|5|Computer Science|2023-10-18|10:00:00|11:30:00|2023-10-12 14:02:00|

### Opération avec les dates et heures 

Postgres fournit des fonctions pour manipuler les date et les heures.

#### Extraire des parties de date et d'heure 

Le fonction `EXTRACT` est utiliser 

```sql
SELECT EXTRACT(YEAR FROM TIMESTAMP '2025-06-15 14:30:00') AS year;
-- Résultat : 2025

SELECT EXTRACT(MONTH FROM TIMESTAMP '2025-06-15 14:30:00') AS month;
-- Résultat : 06

SELECT EXTRACT(DAY FROM TIMESTAMP '2025-06-15 14:30:00') AS day;
-- Résultat : 15

SELECT 
EXTRACT(HOUR FROM TIMESTAMP '2023-11-05 15:45:30') AS hour,
EXTRACT(MINUTE FROM TIMESTAMP '2023-11-05 15:45:30') AS minute,
EXTRACT(SECOND FROM TIMESTAMP '2023-11-05 15:45:30') AS second;
```

#### Ajouter et soustraire des intervalles de temps 

```sql
-- Ajouter 7 jours à la date actuelle
SELECT CURRENT_DATE + INTERVAL '7 days' AS next_week;

-- Soustraire 1 mois
SELECT CURRENT_DATE - INTERVAL '1 month' AS last_month;
```

#### Comparer des dates 

```sql
SELECT event_date
FROM events
WHERE event_date < CURRENT_DATE;
-- On sélectionne tous les événements passés

-- retourne les réunions après la date actuel
SELECT
    title,
    meeting_date,
    meeting_time
FROM
    meetings
WHERE
    (meeting_date + meeting_time) > NOW() -- DATE + TIME dans PostgreSQL donne un TIMESTAMP
ORDER BY
    meeting_date, meeting_time; -- Optionnel : on trie par date et heure de la réunion
```