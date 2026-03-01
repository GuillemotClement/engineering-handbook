# Glossaire 

## COMMENTAIRE 
```sql
/* Commentaire */ 

-- commentaire

-- commentaire dans une requête
SELECT * FROM employee -- WHERE YEAR(join_date) = 2015
```

## FORMATAGE 

- Nom des colonnes et de tables qui contiennent un espace ou des caractères spéciaux entourés de `"`.

---
## REQUETE
### SELECT 
Permet de sélectionner les colonnes à extraire 
```sql
SELECT colonne1, colonne2, … colonneN
FROM table
```

On peut utiliser des expressions pour retourner directement le résultat 
```sql
SELECT 
	name, 
	21-age -- affiche directement le résultat du calcul
FROM students;
```

#### Concaténation 
```sql 
SELECT first_name || ' ' || last_name, grade
FROM students;
```

#### AS 
Permet de renommer la colonne du résultat.
```sql
SELECT 
  first_name AS "Prénom",
  last_name AS "Nom de famille",
  grade AS "Note"
FROM students;
```

---

### WHERE 
Permet de filtrer les données retourner par la requête 
```sql
SELECT colonne1, colonne2
FROM table
WHERE condition;

-- exemple 
SELECT name, age
FROM students
WHERE age > 18;
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

```sql
-- AND 
SELECT colonne1, colonne2
FROM table
WHERE condition AND condition2;

-- OR 
SELECT colonne1, colonne2
FROM table
WHERE condition OR condition2;

-- NOT => inversion 
SELECT colonne1, colonne2
FROM table
WHERE NOT condition;

-- combinaison 
SELECT colonne1, colonne2
FROM table
WHERE (condition1 AND condition2) OR condition3;
```

---

### ORDER BY 
Permet de trier le résultats issue de la requête 
```sql
SELECT colonne1, colonne2
FROM table
ORDER BY colonne1 DESC;

-- tier les etudiant dans l'ordre asc de l'âge 
SELECT name, age
FROM students
ORDER BY age ASC;

-- desc
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
Permet de limiter le nombre de résultats retournées par la requête. Généralement utiliser avec `ORDER BY` pour garder des résultats cohérents.
```sql
SELECT colonne1, colonne2
FROM table
ORDER BY colonne ASC
LIMIT nombre_de_lignes;
```

Pour limiter le nombre de résultat avec une requête dans `SQL Server`, on utilise `TOP`

```sql
$sql = "SELECT TOP 5
          Annee,
          Mois
	      FROM FacturesCloture
	      ORDER BY IdFactureCloture DESC";
```

Dans cette requête, on aura les 5 dernière lignes ajoutée.





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
