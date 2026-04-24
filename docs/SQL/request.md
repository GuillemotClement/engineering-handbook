# Requête 

## COMMENTAIRE
```sql
/* Commentaire */ 

-- commentaire

-- commentaire dans une requête
SELECT * FROM employee -- WHERE YEAR(join_date) = 2015
```

## CONVENTION 

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

### Guillemets 

Les noms de colonnes et de tables contenant des espaces ou des caractères spéciaux doivent être entourés de guillemets doubles :

```sql 
SELECT "first name", age 
FROM students;
```

---
## SELECT 

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

Cette commande permet de retourner uniquement les résultat unique. Elle filtre les doublons.

Lorsque l'on utilise cette commande, elle agit sur les colonnes indiqué dans le `SELECT`.
```sql 
-- retire les doublons, chaque ville apparait qu'une seule fois
SELECT DISTINCT city 
FROM students;

-- valeur unique sur plusieurs colonnes 
-- on obtient les combinaison unique de prénom et de nom
SELECT DISTINCT first_name, last_name
FROM students;

-- combinaison unique et tri
-- on obtient les nom + prénom unique trié par nom de famille
SELECT DISTINCT first_name, last_name
FROM students
ORDER BY last_name ASC;

-- utilisation avec agrégation 
-- compte le nombre de ville unique
SELECT 
	COUNT(DISTINCT city) AS unique_city_count
FROM students;
```


---

## WHERE 

Permet de filtrer les données retourner par la requête. Les lignes retournées par la requête doivent remplir la condition pour être affichées.

Il filtre les données avant un groupement.

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
## ORDER BY 

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
## GROUP BY 

Cette instruction permet de rassembler des lignes qui ont la même valeur dans une ou plusieurs colonnes en groupes logiques.
Cela permet d'appliquer des fonctions d'agrégation séparément à chaque groupe.

Par exemple, on souhaite connaitre le salaire moyen par département. On utilise `GROUP BY
 pour créer des groupes par département, puis on applique `AVG()` sur chaque groupe.

**Chaque colonne qui n'est pas dans une fonction d'agrégation doit être listée dans le `GROUP BY`** 

On indique dans le `GROUP BY`les colonnes que l'on souhaite grouper. Toutes les colonnes dans le `SELECT`qui ne sont pas agréger doivent être listé dans le `GROUP BY`.

SQL commence par créer les groupes, puis vient appliquer la fonction d'agrégation sur chaque groupe.

```sql
SELECT colonne1, fonctionAgr(colonne2)
FROM table
GROUP BY colonne1;

-- moyenne des notes par facs
SELECT
	faculty,
	AVG(gpa) AS avg_gpa
FROM students
GROUP BY faculty -- on groupe sur la fac

-- groupement sur fac et nom 
SELECT
	faculty,
	name,
	AVG(gpa) AS avg_gpa
FROM students 
GROUP BY faculty, name;

-- compter le nombre d'étudiants et la moyenne des notes par fac
SELECT 
	faculty,
	COUNT(*) AS student_count,
	AVG(gpa) AS avg_gpa
FROM students
GROUP BY faculty;
```

## HAVING 

Permet de filtrer les données après que les données aient été groupées et que les fonctions d'agrégat est fait leur calcul.

Cet instruction permet de filtrer les données au niveau des groupes.

Elle peut être utilisé sans `GROUP BY`. 
```sql
SELECT 
	colonnes,
	fonction_aggr
FROM table 
GROUP BY colonnes -- grouper
HAVING condition; -- filtre sur les groupes

-- faculté avec + 100 étudiants
SELECT
	faculty,
	COUNT(*) AS student_count
FROM students
GROUP BY faculty 
HAVING COUNT(*) > 100

-- departement avec le salaire moyen > 50 0000
SELECT 
	department,
	AVG(salary) AS avg_salary 
FROM employees
GROUP BY department 
HAVING AVG(salary) > 50000;

-- filtre sans groupement 
SELECT 
	AVG(salary) AS avg_salary 
FROM employees
HAVING AVG(salary) > 50000;

-- produits avec un total de vente > 500
SELECT 
	product_id
	SUM(sales_amount) AS total_sales
FROM sales 
GROUP BY product_id
HAVING SUM(sales_amount) > 500;
```


---
## LIMIT 

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
## OFFSET - sauter des lignes

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

### CONCAT_WS() - gestion des NULL

Si une des valeurs passer dans le concat et `NULL`, cela provoque des bugs. Toutes les valeurs seront `NULL`, et il n'y aura pas de données affichée.
Pour corriger, utiliser la fonction `CONCAT_WS()`

```sql
CONCAT_WS(<separator>, <value1>, <value2>, ...)

CONCAT_WS(' - ', produit.produit_libelle, produit.produit_reference) AS 'Libellé produit',
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

-- utilisation avec SUBSTRING
-- on vient récupérer la position de @ 
-- on extrait ensuite le domaine sans le @ avec le +1
SELECT 
    email,
    SUBSTRING(email, FROM POSITION('@' IN email) + 1) AS domain
FROM users;
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
## FONCTION AGREGATION

Fonction spéciale SQL qui travaillent sur des groupes de lignes et retourne un seul résultat.

SQL exécute une fonction d'agrégation après avoir sélectionné les données dans `SELECT`.

### COUNT() - compter le nombre de lignes

Retourne le nombre total de ligne dans le résultat, ou le nombre de valeurs non nul dans une colonne précise.

`COUNT(*)` viens compter le nombre de lignes, peut importe si une des valeurs est `NULL`

`COUNT(colonne)` on compte le nombre de ligne où la valeur de la colonne n'est pas `NULL`.

```sql 
COUNT(colonne)

-- compte le nombre d'étudiants
SELECT 
	COUNT(*) AS total_students
FROM students;

-- compte le nombre d'étudiant avec une note 
SELECT 
	COUNT(grade) AS students_with_grades
FROM students;

-- compte les valeurs unique distinct 
SELECT 
	COUNT(DISTINCT age)
FROM students;
```

### SUM()

Additionne toutes les valeurs d'une colonne numérique. Les `NULL` sont ignoré dans la somme.

Si on ajoute une condition dans la requête, seul les lignes qui vérifie la condition seront pris en compte dans la somme.

```sql
-- total de points cumulé 
SELECT 
	SUM(grade) AS total_grades
FROM students;

-- addition avec conditon
SELECT 
	SUM(salary) AS high_salary_total
FROM salaries
WHERE salary > 55000;

-- savoir combien la masse salariale dépasse 200 000
SELECT 
	SUM(salary) - 2000000 AS surplus 
FROM salaries;
```

### AVG()

Calcule la moyenne des valeurs numérique d'une colonne. Les `NULL` sont ignorés

```sql
SELECT avg(colonne)
FROM table;

-- calcul de la note moyenne des étudiants
SELECT 
	AVG(grade) AS average_grade
FROM students;

-- résultat arrondi
SELECT ROUND(AVG(salary), 2) AS rounded_average_salary
FROM employees;

-- moyenne avec filtre 
SELECT 
	AVG(salary) AS average_salary
FROM emplyees 
WHERE id > 2;

-- calculer la moyenne total des ventes 
SELECT AVG(quantity * price) AS average_total_sale 
FROM sales;
```

### MIN() & MAX()

Retourne la plus grande et la plus petite valeur d'une colonne. Ces fonctions sont utilisable sur des colonnes numérique ou bien textuelle. Dans ce cas, elles sont comparé par ordre alphabétique.

Elle fonctionne aussi sur les dates.

Les valeurs `NULL` sont exclus du résultat.

```sql
 SELECT 
	 MIN(age) AS youngest_student,
	 MAX(age) AS oldest_student
FROM students;
```

---

## FONCTION NUMERIQUE 

### ROUND() - arrondi

Permet de faire un arrondi classique. 
- `number` : nombre arrondir
- `digits`: optionnel, indique combien de chiffres après la virgule 

```sql
ROUND(number, [, digits])

SELECT ROUND(4.67); -- 5
SELECT ROUND(4.6789 , 2); -- 4.68
```

### CEIL() - arrondi supérieur

Permet de faire un arrondis à l'entier supérieur.

Retourne toujours un entier. 

```sql 
SELECT CEIL(4.1); -- 5

-- calculer le nombre de pages à ajouter 
SELECT 
	CEIL(COUNT(*) / 10.0) AS pages_necessaires
FROM products;
```

### FLOOR() - arrondi inférieur 

Permet de faire un arrondis à l'entier inférieur. 

```sql
SELECT FLOOR(4.9); -- 4
```

### MOD() - modulo

Retourne le reste d'une division.

```sql
MOD(dividend, divisor)

SELECT MOD(17,5); -- 2 
SELECT MOD(10,3); --1

-- nombre impair 
SELECT 
	student_id
	CASE 
		WHEN MOD(student_id, 2) = 0 THEN 'Pair' ELSE 'Impair'
	END AS parite
FROM students;
```

### POWER() - puissance

```sql 
POWER(base, exposant)

SELECT POWER(2, 3); --8
SELECT POWER(9, 9.5)
```
### SQRT() - racine carrée

```sql 
SELECT SQRT(25); -- 5 
SELECT SQRT(2); -- 1.4142
```

Les valeurs négative font planter la requête. Utiliser `ABS()` si possible.

```sql 
SELECT SQRT(ABS(-25)); -- 5
```

---

## NULL 

Un valeur `NULL` est une valeur ayant un comportement spécifique avec SQL. Elle indique une "valeur inconnue ou non définie".

Cette valeur est égale à rien et n'est pas inégale à rien, même pas à lui même. Cette valeur dans une addition, ou n'importe quel calcul retourne toujours `NULL`.

### IS NULL 

Permet de vérifier si une valeur est `NULL`. 

```sql 
SELECT *
FROM orders
WHERE comment IS NULL;

-- sélectionner les étudiants sans date renseigné
SELECT name 
FROM students 
WHERE birth_date IS NULL;
```

### IS NOT NULL

Permet de vérifier si une valeur est non `NULL.

```sql 
SELECT *
FROM orders
WHERE comment IS NOT NULL;

-- sélectionner les commandes avec un commentaire 
SELECT customer_name, comment 
FROM orders 
WHERE comment IS NOT NULL;
```

### COALESCE()

Cette fonction permet de gérer les valeurs `NULL`. Elle retourne la première valeur non `NULL` de la liste d'arguments que l'on passe. Si tout est `NULL` dans la liste, la fonction retourne `NULL`.

```sql 
COALESCE(value1, value2, ...)

-- remplacer NULL par zéro 
SELECT 
	SUM(COALESCE(salary, 0)) AS total_salary 
FROM salaries;
```

Dans l'exemple, cela permet de faire la somme des salaires. Dans le cas où la valeur serait `NULL`, l'opération ne serait pas possible.
Avec `COALESCE`, on remplace la valeur `NULL` par la valeur du champ `salary` ou bien `0` ce qui permet de faire la `SUM`.

### NULLIF()

Cette fonction permet de comparer deux valeurs, et de retourner `NULL` si elles sont identiques, ou de renvoyer la première valeur si elles sont différentes.

```sql 
NULLIF(value1, value2)

SELECT NULLIF(10, 10); -- Résultat : NULL
SELECT NULLIF(10, 20); -- Résultat : 10

-- éviter la division par zéro
SELECT 
	revenue / NULLIF(expenses, 0) AS profitability
FROM sales;

-- filtrer les données: retourner les commandes avec une ristourne
SELECT 
	order_id, 
	original_price,
	discounted_price 
FROM orders 
WHERE NULLIF(original_price, discounted_price) IS NOT NULL;
```

### NULLS FIRST & NULLS LAST 

Lorsque l'on souhaite trier des colonnes contenant des valeur `NULL`, on peut indiquer comment ranger ces valeurs.

Par défaut, avec `ASC` les nul vont à la fin; et avec `DESC` les nul sont placé au début.

```sql
ORDER BY colonne ASC NULLS FIRST 
ORDER BY colonne DESC NULLS LAST 

-- meilleurs notes en premier et les nul à la fin
SELECT student_id, grade
FROM grades 
ORDER BY grade DESC NULLS LAST;
```

---
## CONDITION 

### CASE 

Equivalent au `switch`. Il compare l'expression à plusieurs valeurs, et exécute le bloc qui match.

```sql
CASE expression
    WHEN value1 THEN result1
    WHEN value2 THEN result2
    ...
    ELSE default_result
END

-- gestion des notes 
SELECT
    name,
    grade,
    CASE grade
        WHEN 5 THEN 'Excellent'
        WHEN 4 THEN 'Bien'
        WHEN 3 THEN 'Satisfaisant'
        WHEN 2 THEN 'Insuffisant'
        ELSE 'Inconnu'
    END AS note_en_mots
FROM students;

-- gestion du null 
SELECT
    user_id,
    status,
    CASE status
        WHEN 'active' THEN 'Actif'
        WHEN 'blocked' THEN 'Bloqué'
        WHEN status IS NULL THEN 'Pas de statut'
        ELSE 'Inconnu'
    END AS etat_utilisateur
FROM users;
```
### CASE WHEN 

```sql
 CASE
    WHEN condition1 THEN résultat1
    WHEN condition2 THEN résultat2
    ...
    ELSE résultat_par_défaut
END

-- classification de produit 
SELECT
    name AS product_name,
    price,
    CASE
        WHEN price IS NULL THEN 'Inconnu'
        WHEN price < 50 THEN 'Économique'
        WHEN price BETWEEN 50 AND 100 THEN 'Standard'
        ELSE 'Premium'
    END AS price_category
FROM products;

-- groupement avec des null 
SELECT
    CASE
        WHEN status IS NULL THEN 'Aucun statut'
        ELSE status
    END AS order_status,
    COUNT(*)
FROM orders
GROUP BY
    CASE
        WHEN status IS NULL THEN 'Aucun statut'
        ELSE status
    END;
```

- `CASE` : début de la condition 
- `WHEN`: condition que l'on teste
- `THEN`: ce qui est retourner 
- `ELSE`: si aucune condition ne match
- `END` : indique la fin de la condition 

### GREATEST() & LEAST()

Retourne la plus grande ou petite valeur passer.

Les deux fonctions ignorent les valeurs `NULL` quand elle cherchent la valeur max ou min. Si toutes les valeurs sont `NULL`, retourne `NULL`

```sql 
SELECT
    student_id,
    GREATEST(exam_1, exam_2, exam_3) AS highest_score,
    LEAST(exam_1, exam_2, exam_3) AS lowest_score
FROM students_scores;
```

---

## JOINTURE 

| Type              | Description                                                                                      |
| ----------------- | ------------------------------------------------------------------------------------------------ |
| `INNER JOIN`      | Retourne les lignes ayant des correspondance dans les deux tables                                |
| `LEFT JOIN`       | Retourne les lignes de la table de gauche, et seulement les correspondance de la table de droite |
| `RIGHT JOIN`      | Retourne les lignes de droite, et seulement les correspondance de la table de gauche             |
| `FULL OUTER JOIN` | Retourne les lignes des deux tables, en mettant `NULL` pour les cas sans correspondance.         |
 La table de gauche correspond à la table parent. Par exemple :
```sql
SELECT students.name, courses.title
FROM enrollments
	JOIN students ON enrollments.student_id = students.id
	JOIN courses ON enrollments.course_id = courses.id;
```

Ou ici `enrollments` est la table de gauche pour les deux autres tables.

### INNER JOIN 

Retourne les lignes où les deux tables ont des correspondance. Utiliser dans les cas ou on on souhaite retourner des valeurs qui ont une correspondance, par exemple les commandes qui ont un client.

```sql 
SELECT colonnes
FROM table1 INNER 
	JOIN table2 ON table1.champ = table2.champ;
	
-- récupération des inscriptions des étudiants et leurs cours 
SELECT 
	students.name, 
	enrollments.course_id
FROM students 
	INNER JOIN enrollments ON students.student_id = enrollments.student_id;
```

La requête retourne la correspondance entre un étudiant et le cours. Uniquement les étudiants qui sont inscrits à des cours sont retourné. Un étudiant n'étant pas inscrit ne sera pas retourné.

### LEFT JOIN 

Retourne toutes les lignes de la table de gauche, et les correspondances de la table de droite.
Dans les cas ou il n'y a pas de correspondance, les colonnes de la table de droite seront `NULL`.

```sql 
SELECT
    table1.colonne1,
    table1.colonne2,
    table2.colonne1,
    table2.colonne2
FROM
    table1 LEFT JOIN table2
ON
    table1.colonne_commune = table2.colonne_commune;
```

- `table1` : table de gauche 
- `table2` : table de droite 
- `colonne_commune`: colonne permettant de faire la jointure (commune au deux tables)

Par exemple, on souhaite obtenir la liste des étudiants, même ceux n'étant pas inscrits à un cours.

```sql
SELECT
    students.name,
    enrollments.course
FROM
    students LEFT JOIN enrollments
ON
    students.student_id = enrollments.student_id;
```

Pour gérer les valeurs `NULL` on pourras venir utiliser `COALESCE()`

```sql 
SELECT
    students.name,
    COALESCE(enrollments.course, 'Cours non choisi') AS course
FROM
    students LEFT JOIN enrollments
ON
    students.student_id = enrollments.student_id;
```

### RIGHT JOIN 

Retourne les lignes de la table de droit et seulement les correspondances de la table de gauche. Si il n'y a pas de valeur, alors `NULL`.

Par exemple, on veux obtenir la liste de toutes les inscriptions, et les infos sur les étudiants s'il existent. Si l'étudiants n'est pas trouvé, on aura `NULL`

```sql 
SELECT
    enrollments.enrollment_id,
    enrollments.course_name,
    students.name AS student_name
FROM
    students RIGHT JOIN enrollments
    ON
    students.student_id = enrollments.student_id;
```

### FULL OUTER JOIN 

Les lignes des deux tables sont retournées. Si une ligne n'as pas de correspondances, les valeurs manquantes seront `NULL`.

La requête permet de faire une liste complète des étudiants et des cours, y compris ceux qui ne sont inscrits à aucun cours, et les cours sans étudiants.

```sql 
SELECT
    s.student_id,
    s.nom,
    e.cours
FROM
    students s
FULL OUTER JOIN
    enrollments e
ON
    s.student_id = e.student_id;
```

### ON ... AND ... 

Permet d'ajouter des conditions supplémentaire dans la jointure. Permet d'optimiser la jointure, en filtrant avec une condition supplémentaire.

De cette manière, PG optimise le plan de jointure, et traite moins de ligne.

On souhaite obtenir les étudiants qui ont des cours actifs :

```sql 
SELECT
    students.name AS student_name,
    courses.name AS course_name
FROM students
INNER JOIN enrollments
    ON students.id = enrollments.student_id
    AND enrollments.status = 'active' -- ajout d'une conditon
INNER JOIN courses
    ON enrollments.course_id = courses.id;
```

### SELF JOIN 

Permet de joindre une table avec elle même. Par exemple une table `employee`, et chaque employé à un manager. Les manager est aussi un employé, dans ses infos sont dans la même table.

Un `SELF JOIN` c'est un `JOIN` mais qui utilise le même table deux fois en lui donnant des alias.

```sql
 SELECT
    e.name AS employee_name,
    m.name AS manager_name
FROM
    employees e
LEFT JOIN
    employees m -- on donne un alias 
ON
    e.manager_id = m.employee_id;
```

 ---

## REQUETE IMBRIQUE

Requête SQL utiliser à l'intérieur d'une autre requête SQL. Cette sous requête s'exécute en premier, et son résultat est utilisé par la requête principale.

### Requête imbriquée dans SELECT 

Une sous requête dans le `SELECT` permet de calculer des valeurs directement pendant l'exécution de la requête principale. Permet de combiner des calculs agrégés, des filtres complexes, ou d'autres collections de données.

Elles sont puissant mais sont à utiliser avec précautions car chaque SR s'exécute pour chaque ligne de la requête principale, ce qui peut ralentir l'exécution de la requête si les tables sont grosses.

Par exemple, cette requête affiche les noms des étudiants, leurs notes et la note maximale du groupe.

```sql 
SELECT
    s.name AS student_name,
    g.grade AS student_grade,
    (
	    -- sous requête
        SELECT MAX(grade) -- cette requête retourne une seule valeur
        FROM grades
        INNER JOIN students ON grades.student_id = students.student_id
        WHERE students.group_id = s.group_id -- fait référence à la requête principale
    ) AS max_group_grade
FROM
    students s
INNER JOIN
    grades g ON s.student_id = g.student_id
```

1. Pour chaque étudiant, on récupère son nom et sa note (`s.name`, `g.grade`)
2. `SELECT MAX(grade)` - c'est la sous requête qui permet de retourner la note maximale
3. La SR s'exécute pour chaque ligne de la requête principale, et utilise la condition pour limiter la sélection à un seul groupe.

#### Ajout de colonne supplémentaire 

Il est possible de venir ajouter des colonnes supplémentaires avec des valeurs calculées ou des données qui dépendant d'autres enregistrements ou tables.

```sql 
-- ajouter la moyenne d'un étudiant 
SELECT
    s.id,
    s.name,
    (SELECT AVG(g.grade)
     FROM grades g
     WHERE g.student_id = s.id) AS average_grade -- nouvelle colone avec valeur calculée
FROM students s;
```


### IN & NOT IN

Permet de simplifier les conditions de filtrage. Les valeurs passée, seront celle vérifier par la condition du `IN`.

```sql
SELECT colonnes
FROM table
WHERE colonne IN (valeur1, valeur2, valeur3, ...);

-- trouver les étudiants qui suivent certains cours 
SELECT name, course
FROM students
WHERE course IN ('Programmation', 'Mathématiques', 'Physique');

-- NOT IN
SELECT name, course
FROM students
WHERE course NOT IN ('Programmation', 'Mathématiques', 'Physique');

-- gestion des NULL
SELECT student_id
FROM grades
WHERE grade NOT IN ('A', 'B', 'C')
   OR grade IS NULL;
```

#### Utilisation avec SOUS-REQUETE 

La sous requête retourne la liste dans résultat dans le `IN`.  Dans la requête principal, on obtient alors que les valeurs qui correspondent aux résultat retourné par la sous requête.

```sql 
SELECT name
FROM students
WHERE course_id IN (
    SELECT id
    FROM courses
);
```

### EXISTS & NOT EXISTS - vérifier l'existence de données

Permet de vérifier l'existence d'enregistrements dans le résultats d'une sous requête. Si la SR renvoie au moins une ligne, la condition retourne `TRUE`.

Cet opération est plus rapide que `IN` car il arrête la sous requête dès qu'une correspondance est trouvé.

`NOT EXISTS` retourne `TRUE` si la SR ne retourne aucune ligne.

```sql
SELECT colonnes
FROM table
WHERE EXISTS (
    SELECT 1
    FROM autre_table
    WHERE condition
);

-- vérifier si des étudiants ont une note > à 4
SELECT 'Il y a des étudiants avec une bonne note !'
WHERE EXISTS (
    SELECT 1
    FROM students
    WHERE grade > 4
);

-- retourner les étudiants sans note 
SELECT *
FROM students s
WHERE NOT EXISTS (
    SELECT 1
    FROM students
    WHERE grade IS NOT NULL
    AND id = s.id
);
```

### Requête imbriquée dans FROM 

Permet de créer des tables temporaire qui devient une partie de la requête principale.

```sql 
SELECT colonnes
FROM (
    SELECT colonnes
    FROM table
    WHERE condition
) AS alias
WHERE condition_externe;

-- obtenir la liste des étudiants et leur moyenne de note 
SELECT g.student_id, g.avg_grade, g.max_grade
FROM (
    SELECT student_id,
           AVG(grade) AS avg_grade,
           MAX(grade) AS max_grade
    FROM grades
    GROUP BY student_id
) AS g;

-- etudiant avec une moyenne supérieur à 80
SELECT s.student_name, g.avg_grade
FROM students AS s
JOIN (
    SELECT student_id, AVG(grade) AS avg_grade
    FROM grades
    GROUP BY student_id
) AS g ON s.student_id = g.student_id
WHERE g.avg_grade > 80;
```

Dans la requête principale pour obtenir la liste des étudiants et leurs moyenne, on fait référence à la table temporaire créer avec la sous requête, et référence avec le `g`.

### Requête imbriquée dans HAVING 

`HAVING` permet de filtrer les données issue des requêtes agrégées.

Une SR dans le `HAVING` permet d'ajouter de la flexibilité lorsque l'on filtre des données agrégées. Elle permet de comparer des agrégats comme une moyenne ou le max avec des valeurs calculées ailleurs dans la base.

```sql 
-- filtrer les facs par note moyenne
SELECT department, AVG(grade) AS avg_grade
FROM students
GROUP BY department
HAVING AVG(grade) > (SELECT AVG(grade) FROM students);

-- comparer des facs par moyenne et groupe d'étudiants 
SELECT department, AVG(grade) AS avg_grade, COUNT(*) AS student_count
FROM students
GROUP BY department
HAVING AVG(grade) > ( SELECT AVG(grade) FROM students )
   AND COUNT(*) > (
       SELECT COUNT(*)
       FROM students
       GROUP BY department
       ORDER BY AVG(grade)
       LIMIT 1
   );
```


-- 14-3





---
## VIEW

Les `VIEW` permette de "sauvegarder" une requête à laquelle on donne un nom et que l'on peut utiliser comme une table. Elle ne stocke pas les données mais la structure de la requête.

On déclarer un fichier SQL avec les différentes views, et on exécute ensuite une migration pour enregistrer dans la DB.

Par exemple, une view qui permet d'analyser les étudiants et qui affiche le nom, le nombre d'inscriptions aux cours, et leur note moyenne. On peut donc créer deux vues, puis on viens les combiner

```sql 
-- view qui permet de compter le nombre de cours suivis par un étudiant 
CREATE VIEW student_course_count AS
SELECT
    student_id,
    COUNT(*) AS course_count
FROM
    enrollments
GROUP BY student_id;

-- view qui calcule la note moyenne
CREATE VIEW student_avg_grade AS
SELECT
	student_id,
	AVG(grade) AS avg_grade
FROM
enrollments
WHERE grade IS NOT NULL
GROUP BY student_id;

-- utilisation des view
SELECT
    s.name,
    c.course_count,
    a.avg_grade
FROM
    students s
-- on fait référence au view
LEFT JOIN student_course_count c ON s.student_id = c.student_id
LEFT JOIN student_avg_grade a ON s.student_id = a.student_id;
```

### Update avec des view 

Il est possible d'utiliser des view pour mettre à jour des données, si la view ne contient pas de `JOIN`, `GROUP BY`, d'agrégats ou de sous requête.

```sql 
-- création de la view modifiable 
CREATE VIEW active_students AS
SELECT * FROM students WHERE active = true;

-- update avec cette view 
UPDATE active_students SET name = 'Ivan Petrov' WHERE student_id = 2;
```

### Supprimer une view

```sql 
-- supression d'une view 
DROP VIEW student_avg_grade;
```

### Modifier une view 

```sql 
CREATE OR REPLACE VIEW student_avg_grade AS
SELECT 
	student_id, 
	ROUND(AVG(grade), 2) AS avg_grade
FROM grades
GROUP BY student_id;
```




















---
## OLD


### TYPE 

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
