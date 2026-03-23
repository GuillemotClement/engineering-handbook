## Formatage de données

Le formatage de donnée permet de :
- Transformer les données dans une format lisible 
- Préparer les données pour l'export ou l'intégration avec un autre système
- Rendre les rapports et les visualisations lisibles et précises

## Formatage de chaînes 

### CONCAT()

La fonction permet de fusionner plusieurs chaînes en une seule. 

```sql
SELECT CONCAT(first_name, ' ', last_name) AS full_name
FROM students;
```

La fonction ignore `NULL`, si un argument à cette valeur, alors il sera ignoré.

On peut également utiliser `||` 

```sql
SELECT first_name || ' ' || last_name AS full_name FROM students;
```
### UPPER() & LOWER() 

Permet de changer la casse d'une chaîne. Elle permet de standardiser pour l'affichage. Par exempe, on passe les adresse email en maniscule.

De cette manière, on standardise pour :
- mettre tout au même format pour des comparaison
- mettre en valeur des mot important

```sql
SELECT email, UPPER(email) AS email_upper
FROM students;
```

```sql
SELECT email, LOWER(email) AS email_lower
FROM students;
```

```sql
SELECT 
	CONCAT(UPPER(first_name), ' ', UPPER(last_name)) AS full_name_upper
FROM students;
```

Dans cette requête, on concatène les deux colonnes qui sont passer en majuscule.
## Formatage des dates et des heures

Attention de bien travailler sur des date au format `DATE` ou `TIMESTAMP`.

Le type `TIMESTAMPTZ` permet de travailler avec des fuseaux horaire en plus de la date.

### NOW() 

Cette fonction retourne la date et l'heure actuelle du serveur de la base de donnée.

On l'utilise par exemple pour enregistrer l'heure de création d'une nouvelle ligne.

```sql
SELECT NOW();

-- 2023-11-05 15:23:45.123456+00

-- insérer une nouvelle commande avec la date et heure
INSERT INTO orders (order_id, order_date, total_amount)
VALUES (1, NOW(), 150.00);
```

### CURRENT_DATE()

Retourne la date sans l'heure. Contient le jour, le mois et l'année

```sql
SELECT CURRENT_DATE;

-- 2023-11-05

-- récupérer les lignes du jour 
SELECT *
FROM orders
WHERE order_date = CURRENT_DATE;
```

Dans la requête, on compare la date avec la date du jour.

### DATE_PART()

Cette fonction permet d'extraire une partie d'une date.

```sql
DATE_PART('partie', date);

-- récupère l'année 
SELECT DATE_PART('year', date_of_birth) AS birth_year
FROM students;

-- sélection des user née cette année
SELECT *
FROM students
WHERE DATE_PART('year', birth_date) = DATE_PART('year', CURRENT_DATE);
```

Dans cette requête, on extrait l'année de naissance depuis la colonne.

Il est possible d'extraite ces différentes partie d'une date 

- `year`: année
- `month`: mois 
- `day`: jour 
- `hour`: heure
- `minute` : minute 
- `second`: seconde
- `dow`: jour de la semaine (0 pour dimanche)

#### Calcul d'âge 

```sql
SELECT user_id, first_name, last_name,
       DATE_PART('year', CURRENT_DATE) - DATE_PART('year', birth_date) AS age
FROM users;
```

#### Filtrage par heure 

```sql
SELECT *
FROM orders
WHERE order_date >= NOW() - INTERVAL '1 hour';
```

#### Grouper par mois 

```sql
SELECT DATE_PART('month', order_date) AS order_month, COUNT(*) AS order_count
FROM orders
WHERE DATE_PART('year', order_date) = DATE_PART('year', CURRENT_DATE)
GROUP BY DATE_PART('month', order_date)
ORDER BY order_month;
``` 

#### Extraire le jour de la semaine 

```sql
SELECT DATE_PART('dow', order_date) AS day_of_week, COUNT(*) AS order_count
FROM orders
GROUP BY DATE_PART('dow', order_date)
ORDER BY order_count DESC;
```


## Conversion de types

Dans le cas ou l'on as un nombre stocké en format number et un autre au format string on obtient une erreur.

Dans ce cas, on peut venir utiliser un cast pour convertir au bon format la donnée à traiter.

La conversion permet :
- Simplifier le traitement des données pour utiliser des calculs
- Créer des donnée lisible et structurées pour export ou analyse
- Eliminer les bugs liées au compatibilités de type de donnée


### CAST()

Cette fonction permet de convertir une valeur d'un type dans un autre type.

```sql
-- syntaxe
CAST(valeur AS type_de_donnée_cible)

-- conversion d'une chaine en nombre
SELECT CAST('123' AS INTEGER);

-- conversion d'un nombre en chaîne
SELECT CAST(123 AS TEXT);

-- conversion d'une date en chaine
SELECT CAST(NOW() AS TEXT);

-- exemple
SELECT *
FROM students
WHERE CAST(students_id AS INTEGER) = 101;
```

#### :: 

L'opérateur `::` est une alternative à `CAST`. 

```sql
valeur::type_cible

-- string -> nombre
SELECT '123'::INTEGER;

-- nombre -> chaine 
SELECT 123::TEXT;

-- date -> chaine 
SELECT NOW()::TEXT;

-- exemple 
SELECT *
FROM students
WHERE students_id::INTEGER) = 101;

-- extraire l'année sous forme de nombre
SELECT start_date::DATE, start_date::TEXT, start_date::TIMESTAMP
FROM courses;
```

### TO_CHAR

Fonction qui permet de formater dans un format spécifique.

#### Formate date
On veux récupérer une liste d'étudiants avec nom et prénom fusionné, et la date de naissance au format `DD-MM-YYYY`.

```sql
SELECT
    CONCAT(first_name, ' ', last_name) AS full_name,
    TO_CHAR(birth_date, 'DD-MM-YYYY') AS formatted_birth_date
FROM
    students;
```

`TO_CHAR()`: permet de convertir dans un format. Ici DD-MM-YYYY

#### Format monétaire
On veux maintenant préparer une liste pour un export au format `CSV` pour travailler dessus avec Excel.
Les format dans la base de sont pas conforme à ce qui est attendu, il faut donc venir formater les données.

```sql
SELECT
    order_id,
    TO_CHAR(total_price, 'FM$999,999.00') AS formatted_price
FROM
    orders;
```

- `FM`: enlève les espaces en trop
- `$` ajoute le symbole de la monnaie
- `999,999.00` : définit le format avec séparateur de milliers et deux décimales.

Une requête qui :
- fusionne le nom et prénom dans une colonne full_name
- transforme la date au format DD-MM-YYYY
- affiche l'âge de l'étudiant à la date du jour 

```sql
SELECT
    CONCAT(first_name, ' ', last_name) AS full_name,
    TO_CHAR(birth_date, 'DD-MM-YYYY') AS formatted_birth_date,
    DATE_PART('year', AGE(birth_date)) AS age
FROM
    students;
```

- `AGE()`: retourne l'interval entre aujourd'hui et la date de naissance sous forme d'années, mois, jours
- `DATE_PART('year', AGE(birth_date))`: extrait le nombre d'année de cet intervalle.

#### Formatage pour des condition spécifique

Il est parfois nécessaire de formater des données pour créer des conditions ou filtrer. Par exemple, afficher les étudiants qui ont leur anniversaire ce mois ci

```sql
SELECT
    CONCAT(first_name, ' ', last_name) AS full_name,
    TO_CHAR(birth_date, 'DD-MM-YYYY') AS formatted_birth_date
FROM
    students
WHERE
    DATE_PART('month', birth_date) = DATE_PART('month', CURRENT_DATE);
```

Dans la condition, on viens extraire la partie souhaitée.
## Trie des données 

### ORDER BY 

Permet de trier les données par une ou plsuieurs colonnes

```sql
SELECT
    CONCAT(first_name, ' ', last_name) AS full_name,
    TO_CHAR(birth_date, 'DD-MM-YYYY') AS formatted_birth_date
FROM
    students
ORDER BY
    birth_date ASC;
```

On viens trier les résultats avec le `ORDER BY`
### DISTINCT

Permet d'obtenir une valeur unique. Les valeurs doublons seront filtrées.

Par exemple, une université à des campus dans plusieurs ville, et on souhaite retourner la liste des villes unique dans l'order alphabétique

```sql
SELECT DISTINCT
    city
FROM
    students
ORDER BY
    city ASC;
```

