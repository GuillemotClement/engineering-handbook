# SQL 

## COMMENTAIRE
```sql
/* Commentaire */ 

-- commentaire

-- commentaire dans une requête
SELECT * FROM employee -- WHERE YEAR(join_date) = 2015
```

---
## REQUETES

### CONVENTION 

Par convention, utiliser ce format pour écrire des requêtes lisible : 

```sql 
SELECT  <col_1>,
		<col_2> 
FROM <table>
WHERE <condition> -- condition pour filtrer les lignes 
ORDER BY <COLONNE> <SENS>
LIMIT <nb_lignes>
OFFSET <nb_lignes>
```

#### Guillemets 

Les noms de colonnes et de tables contenant des espaces ou des caractères spéciaux doivent être entourés de guillemets doubles :

```sql 
SELECT "first name", age 
FROM students;
```

### SELECT 

Permet de sélectionner les colonnes à extraire d'une table. 

L'ordre réel d'exécution est le `FROM` puis le `SELECT` : 
1. Le serveur trouve la table définis par le `FROM`
2. Les conditions sur les lignes sont appliquées, et SQL ne conserve que les lignes qui correspondent aux condition
3. Les colonnes demandées sont sélectionnées

```sql
-- requête basique 
SELECT colonne1, colonne2, … colonneN -- les colonnes à extraires
FROM table; -- depuis quelle table on veux récupérer les données

-- récupérer toutes les colonnes d'une table => à éviter
SELECT * 
FROM students;

-- utiliser une expression dans une requête 
SELECT 
	name,
	21 - age as 'age_before_majority' -- on affiche directement le résultat 
FROM students;
```

#### Concaténation de colonne

Permet de concaténer plusieurs colonnes dans le résultat.

```sql 
SELECT first_name || ' ' || last_name, grade
FROM students;
```

#### Alias
Permet de renommer la colonne dans le résultat 

```sql
SELECT 
  first_name AS "Prénom",
  last_name AS "Nom de famille",
  grade AS "Note"
FROM students;

-- concaténation avec alias 
SELECT  first_name || ' ' || last_name AS "Nom complet", 
		grade AS "Note" 
FROM students;
```

#### DISTINCT 

Retire tous les doublons des résultats

```sql 
-- retire les doublons, chaque ville apparait qu'une seule fois
SELECT DISTINCT 
	city 
FROM students 
ORDER BY 
	city ASC;
```


---

### WHERE 

Permet de filtrer les données retourner par la requête. Les lignes retournées par la requête doivent remplir la condition pour être affichées.

```sql
SELECT colonne1, colonne2
FROM table
WHERE condition;

-- extraire les étudiants de + de 18 ans
SELECT name, age
FROM students
WHERE age > 18;

-- exclure l'étudiant avec un id de 1 
SELECT 
	id,
	name
FROM users 
WHERE id <> 1;
```

#### Opérateur de comparaison

| Opérateur | Signification | Exemple | Résultat |
| --- | --- | --- | --- |
| `=` | Egale | `age = 20` | Retourne les lignes ou age est égale à 20 |
| `<>` | Différents | `age <> 20` | Retourne les ligne ou l'age est différents de 20 |
| `>` | Supérieur | `age > 18` | Retourne les lignes où l'age est supérieur à 18 |
| `<` | Inférieur | `age < 18`| Retourne les ligne ou l'age est inférieur à 18 |
| `>=` | Supérieur ou égale | `age >= 18` | Retourne les ligne ou l'age est supérieur ou égale à 18 |
| `<=` | Inférieur ou égale | `age <= 18` | Retourne les ligne ou l'age est inférieur ou égale à 18 |

#### Opérateur logique 

Ces opérateurs permettent de combiner des conditions : 
- `AND` : les deux conditions sont vraies - 1
- `OR`: une des deux conditions est vraie - 2
- `NOT` : inversion de condition, sélection des lignes pour lesquelles la condition est fausse - 3

Les opérateurs logiques sont exécuté dans l'ordre indiqué par les chiffres. L'utilisation des parenthèses permet de modifier l'ordre d'exécution.

```sql
-- AND - filtrer les étudiants de plus de 18 ans avec la note A
SELECT name, age, grade
FROM students
WHERE age > 18 AND grade = 'A';

-- OR - filtrer les note A ou B
SELECT name, age, grade
FROM students
WHERE grade = 'A' OR grade = 'B';

-- NOT - filtrer les étudiants qui n'ont pas la note A  
SELECT name, age, grade
FROM students
WHERE NOT grade = 'A';

-- combinaison - étudiant de plus de 18 avec une note 1 ou avec la note B
SELECT name, age, grade
FROM students
WHERE (age > 18 AND grade = 'A') OR grade = 'B';
```

---

### ORDER BY 

Permet de trier l'ordre d'affichage des résultats. Sans cette instruction, l'ordre n'est pas garantis. Par défaut, `ASC`.

Il est possible de réaliser un triage sur plusieurs colonne. Dans ce cas, l'ordre se fait sur la première colonne, puis la seconde.

Il est possible de faire le triage sur des résultats de calcul.

```sql
-- trier les etudiant dans l'ordre asc de l'âge 
SELECT name, age
FROM students
ORDER BY age ASC;

-- trier dans l'ordre inverse 
SELECT name, age
FROM students 
ORDER BY age DESC;

-- triage sur colonne multiple
SELECT colonne1, colonne2
FROM table
ORDER BY colonne1 DESC, colonne2 ASC;
-- triage colonne 1 puis colonne 2

-- trie sur l'alias 
SELECT name AS "Nom", age AS "Âge"
FROM students
ORDER BY "Âge" DESC;

-- trie sur colonne calculées
SELECT name, age, age * 2 AS age_doublé
FROM students
ORDER BY age_doublé DESC;

-- avec fonction 
SELECT product_name, price, ROUND(price * 0.9, 1) AS prix_remisé
FROM products
ORDER BY prix_remisé ASC;
```

---
### LIMIT 

Limite le nombre de résultats retourné par la requête. Utilisé avec `ORDER BY` pour obtenir des résultats cohérents.

Avec `SQL Server`, il faut utiliser l'instruction `TOP`.

```sql
-- PG - affichage de 3 lignes de la table students
SELECT *
FROM students 
LIMIT 3;

-- affichage des 5 produits les plus chères
SELECT product_name, price 
FROM products 
ORDER BY price DESC 
LIMIT 5;

-- 5 premiers étudiants de plus de 18 ans 
SELECT *
FROM students 
WHERE age > 18
LIMIT 5;

-- SQL Server - TOP
SELECT  TOP 5 
		Annee, 
		Mois
FROM FacturesCloture
ORDER BY IdFactureCloture DESC
```

#### Optimisation

Dans le cas où l'on travail avec un système d'affichage de données, `LIMIT` permet d'afficher la première page de manière plus optimisé. On ne charge pas toutes les données directement, mais qu'une partie pour réduire la charge à la connexion d'un utilisateur.

```sql
-- récupération des 10 premiers employés, triés par date d'embouche
SELECT *
FROM employees 
ORDER BY hire_date 
LIMIT 10;
```

---
### OFFSET - sauter des lignes

Permet de sauter des lignes et mettre en place une pagination. 

```sql 
-- afficher à partir de la troisième ligne 
SELECT *
FROM students
OFFSET 2; -- on saute les deux première lignes
```

En combinant avec `LIMIT`, on peut mettre en place un système de pagination. Pour chaque page, on saute les lignes qui ont déjà été affichées.

Par exemple, sur une application, on souhaite sur chaque pages afficher deux lignes. 
- Première page: on saute 0 lignes
- Deuxième page: on saute 2 lignes 
- etc ..

```sql
-- requète de la première page 
SELECT *
FROM students 
ORDER BY id 
LIMIT 2;

-- requête pour afficher la deuxième page
SELECT *
FROM students
ORDER BY id
LIMIT 2
OFFSET 2;

-- requête de la troisième page
SELECT *
FROM students
ORDER BY id
LIMIT 2
OFFSET 4;
```

Pour mettre en place une pagination automatiquement, on peut utiliser cette formule :

```sql 
OFFSET = (numero_de_page - 1) * nombre_enregistrements_par_page

-- première page 
(1 - 1) * 2 = 0
-- seconde page 
(2 - 1) * 2 = 2
-- troisème page 
(3 - 1) * 2 = 4
```

#### Optimisation 

L'utilisation de `OFFSET` peut alourdir les requêtes car PG parcourt toutes les lignes qu'il doit sauter avant de retourner le résultat.

Pour optimiser, on peut préférer l'utilisation d'un "curseur". Pour cela, on récupère l'id de la dernière ligne reçue sur la page précédentes, et on démarre la récupération depuis cet id.

```sql 
SELECT *
FROM students 
WHERE id > last_id_afficher
ORDER BY id
LIMIT 2;
```

---

## FORMATAGE DES DONNEES

Le formatage permet de transformer les données dans un format lisible, de simplifier l'extraction de données, de préparer des données pour l'export ou l'intégration avec d'autres systèmes et de rendre les rapports et visualisation lisibles et précis.

### CONCAT()

Cette fonction permet de fusionner les donnée de plusieurs champ, ou des chaînes.

```sql
-- affichage dans une colonne full_name
SELECT 
	CONCAT(first_name, ' ', last_name) AS full_name,
FROM students

-- syntaxe alernative 
SELECT first_name || ' ' || last_name AS full_name 
FROM students;

-- création du nom complet en majuscule 
SELECT 
	CONCAT(UPPER(first_name), ' ', UPPER(last_name)) AS full_name
FROM students;
```

Si la valeur est `NULL`, il sera afficher dans la chaine, par exemple `Otto NULL`. Pour gérer cette valeur, on peut utiliser `COALESCE` qui retourne le seconda paramètre si le premier est `NULL`.

```sql
-- gestion avec une valeur NULL
SELECT 
	CONCAT(first_name, ' ', COALESCE(middle_name, '')) AS full_name
FROM students
```
### UPPER ()

Permet de transformer une chaîne en texte majuscule.

```sql
SELECT 
	email,
	UPPER(email) AS email_upper
FROM students;
```

### LOWER()

Permet de transformer une chaîne en minuscule. Cette fonction permet de standardiser les emails par exemple.

```sql 
SELECT 
	email,
	LOWER(email) AS email_lower
FROM students; 

-- update pour standardiser les emails 
UPDATE students 
SET email = LOWER(email);
```

### NOW() - date complète

Retourne la date et l'heure actuelles du serveur de la base de données. 

```sql
SELECT NOW();
-- retourne ce type de résultat :
2023-11-05 15:23:45.123456+00

-- insertion d'une commande avec la date et l'heure 
INSERT INTO orders (order_id, order_date, total_amount)
VALUES (1, NOW(), 150.00);
```

### CURRENT_DATE - date

Retourne la date du jour sans l'heure. 

```sql
SELECT CURRENT_DATE; -- 2023-11-05

-- récupérer les lignes du jour 
SELECT *
FROM orders 
WHERE order_date = CURRENT_DATE;
```

### DATE_PART() - récupération d'une partie de la date 

Cette fonction permet de récupérer une partie précise d'une date (année, mois, jour, heure, minute).

Argument disponible :
- `year`: année 
- `month`: mois
- `day` : jour
- `hour`: heure
- `minute`: minute 
- `second`: seconde
- `dow` : jour de la semaine => 0 pour dimanche

```sql 
DATE_PART('<partie>', date);

-- retourner l'année
SELECT DATE_PART('year', NOW()) AS current_year; -- 2025

-- extraction du mois de la date du jour 
SELECT DATE_PART('month', CURRENT_DATE) AS current_month;

-- selection ds utilisateurs nés cette année
SELECT 
	id,
	first_name,
	last_name,
	birth_date, -- champ utilisé dans le filtre
	grade
FROM students 
WHERE DATE_PART('year', birth_date) = DATE_PART('year', CURRENT_DATE);

-- calcule âge user 
SELECT 
	user_id,
	first_name, 
	last_name,
	DATE_PART('year', CURRENT_DATE) - DATE_PART('year', birth_date) AS age 
FROM users;
```

### AGE() - intervalle date 

Retourne l'intervalle entre une date passer en argument, et la date du jour sous forme de date

```sql 
SELECT 
	DATE_PART('year', AGE(birth_date)) AS age 
FROM students;
```

### CAST() - conversion de type 

Cette fonction permet de convertir une valeur d'un type de données à un autre. La conversion de type est nécessaire pour comparer des données, faire des opération.

```sql 
CAST(<valeur> AS <type_cible>)

-- conversion string => num 
SELECT CAST('123' AS INTEGER); 
-- conversion num => string 
SELECT CAST(123 AS TEXT); 
-- conversion date => string 
SELECT CAST(NOW() AS TEXT)

-- conversion pour un filtrage
SELECT * 
FROM students 
WHERE CAST(student_id AS INTEGER) = 101;
```

PG permet d'utiliser une syntaxe raccourcie avec `::` 

```sql 
<valeur>::<type_cible>

-- string => number 
SELECT '123'::INTEGER;
-- num => string 
SELECT 123::TEXT;
-- date => string 
SELECT NOW()::TEXT;

-- conversion pour un filtrage 
SELECT * 
FROM students 
WHERE student_id::INTEGER = 101;

-- récupération des commandes de septembre 2023 et > 300
SELECT 
	order_d,
	order_date::DATE as date,
	amount
FROM orders 
WHERE amount > 300
	AND order_date::DATE >= '2023-09-01' AND order_date::DATE <= '2023-09-30';
```
### TO_CHAR() - formatage 

Permet de formater une valeur. 

```sql 
TO_CHAR(<valeur>, <fomat_cible>)

-- formatage de date 
SELECT 
	TO_CHAR(birth_date, 'DD-MM-YYYY') AS formatted_birth_date
FROM students;

-- formatage monétaire 
SELECT 
	order_id,
	TO_CHAR(total_price, 'FM$999,999.00') AS formatted_price
FROM 
	orders;
	
-- FM : enlève les espaces en trop 
-- $ : ajoute le symbole de la monnaie 
-- 999,999.00 : définit le format avec séparateur de milliers et deux décimales
```

### LENGTH() - longueur de chaîne 

Retourne le nombre de caractère d'une chaîne.

```sql 
LENGTH(string)

SELECT 
	name,
	LENGTH(name) AS name_length
FROM students;
```

### TRIM() - retirer les espaces 

Permet de supprimer les espaces au début et fin de string.

```sql 
TRIM([LEADING | TRAILING | BOTH] chars FROM string)
-- généralement utiliser comme cela 
TRIM(string)

SELECT '[' || TRIM('   Art   ') || ']' AS cleaned;
```

### SUBSTRING() - extraction de string

Permet d'extraire une substring d'une string.

- `start` : position du premier caractères, début à 1
- `length`: nombre de caractère à extraire 

```sql
SUBSTRING(string FROM start FOR length)

-- 3 premieres lettres du nom de famille 
SELECT 
	last_name, 
	SUBSTRING(last_name FROM 1 FOR 3) AS prefix
FROM students;

-- domaine de l'email 
SELECT 
	email, 
	SUBSTRING(email FROM POSTITION('@' IN email) + 1) AS domaine
FROM students;
```

### POSITION() - chercher une substring 

Retourne la position où commence la substring.

```sql
POSITION(substring IN string)

SELECT 
	email,
	POSITION('@' IN email) AS at_position --retourne la position du caractère @
FROM students;
```

On peut ensuite l'utiliser avec `SUBSTRING()` pour extraire des partie de string.

### REPLACE() - remplacer une substring 

Remplace les occurrences d'une substring par une autre.

```sql
REPLACE(string, from_substring, to_substring)

-- remplace les espaces par des underscores 
SELECT  
	name,
	REPLACE(name, ' ', '_') AS fixed_name
FROM students;
```

### INITCAP() - première lettre en majuscule 

Transforme la string pour que la première lettre de chaque mot soit en majuscule, les autres en minuscules.

```sql 
INITCAP(string)

SELECT INITCAP('anna pal') AS full_name; --Anna Pal
```




---

---
## TYPE 

### TIME 

#### TO_CHAR() 

Permet de convertie un type temporelle est chaîne. 

```sql
TO_CHAR(value, format)
```

|Modèle de format|Signification|Exemple|
|---|---|---|
|`YYYY`|Année|`2023`|
|`MM`|Mois (nombre de 01 à 12)|`10`|
|`MONTH`|Nom du mois (en majuscules)|`OCTOBER`|
|`DAY`|Jour de la semaine (en majuscules)|`SUNDAY`|
|`DD`|Jour du mois|`01`|
|`HH24`|Heures au format 24h|`15`|
|`MI`|Minutes|`45`|
|`SS`|Secondes|`30`|

#### TO_DATE()

Permet de convertir une chaîne en type temporelle

```sql
TO_DATE(value, format)
```

#### INTERVAL 

Type de donnée spécial utiliser pour représenter des périodes de temps. Ce type ne stocke par une date ou une heure précise, mais décrit une durée type "2 jours", "3 heures"

```sql
INTERVAL 'nombre unité_de_temps'

-- exemple
INTERVAL '2 days' -- Deux jours
INTERVAL '3 hours' -- Trois heures
INTERVAL '15 minutes' -- Quinze minutes
INTERVAL '1 day 2 hours' -- Un jour et deux heures

-- ajouter un interval à une date 
SELECT CURRENT_DATE + INTERVAL '7 days' AS delivery_date;
-- On obtient la date dans 7 jours à partir d’aujourd’hui
```

Postgres gère pleins de type de temps différents que l'on peut combiner dans une même expression :
- `year`
- `month`
- `day`
- `hour`
- `minute`
- `second`





---


---
## TABLE

### Update d'une table 

```sql 
-- pg
ALTER TABLE <name_table>
ADD COLUMN <name_columne> <type>

-- SQLServer 
ALTER TABLE <name_table>
ADD <name_col> <type>

-- ajout colonne boolean avec SQL Server 
ALTER TABLE <name_table>
ADD Actif BIT DEFAULT(1);
```

### UPDATE 
Permet de mettre à jour la valeur d'un champ d'une table 

```sql 
-- SQL Server - la requête viens mettre à jour toutes les valeurs NULL à 1 pour rendre actif  
UPDATE TypesClientsCat
SET TypesClientsCat.Actif = 1
WHERE TypesClientsCat.Actif IS NULL;
```
