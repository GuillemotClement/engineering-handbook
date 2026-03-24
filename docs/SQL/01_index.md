# SQL 

## OPERATION DE BASE 

### COMMENTAIRE 
```sql
/* Commentaire */ 

-- commentaire

-- commentaire dans une requête
SELECT * FROM employee -- WHERE YEAR(join_date) = 2015
```

### FORMATAGE 

Par convention, utiliser ce formation pour écrire des requêtes lisible : 

```sql 
SELECT  <col_1>,
		<col_2> 
FROM <table>
WHERE <condition> -- condition pour filtrer les lignes 
GROUP BY <col>
HAVING <col>
ORDER BY <tri>; 
```

- Nom des colonnes et de tables qui contiennent un espace ou des caractères spéciaux entourés de `"`.

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

### OFFSET 
Permet de sauter des lignes et mettre en place une pagination.
```sql 
SELECT colonne1, colonne2
FROM table
OFFSET nombre_de_lignes;

-- afficher à partir de la troisième ligne 
SELECT *
FROM students
OFFSET 2;
```

#### Pagination 
```sql
-- on affiche deux enregistrement à la fois à partir de la 3 lignes
SELECT *
FROM students
LIMIT 2
OFFSET 2;

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

-- pagination automatique 
OFFSET = (numero_page - 1) * nb_row_per_page
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
