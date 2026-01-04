## Fonction d'agrégations

Elles permettent de transformer des lignes de données en résultats compacts et clairs.

Elle travaillent sur des groupes de ligne, et retourne un seul résultat.

Par exemple : 
- Compter le nombre total d'enregistrements dans une table
- Trouver les valeurs minimales ou maximales dans une colonne numérique
- Faire la somme des valeurs dans une des colonnes
- Calculer la moyenne de toutes les valeurs d'une colonne
- Extraire les valeurs uniques

SQL exécute une fonction d'agrégation après avoir sélectionné les données dans `SELECT`. Par exemple, avec `SUM()`, SQL commence par récupérer les données, puis il fait le calcul juste sur les lignes sélectionnées.

---

## COUNT()

Cette fonction permet de compter le nombre total de ligne dans une table ou le nombre de valeur non nulle dans une colonne précise.

```sql
-- syntaxe
COUNT(colonne)
```

### Compter toutes les lignes 

Lorsque l'on souhaite compter chaque ligne d'une table, peu importe si des données sont présente, on utilise `COUNT(*)`

La fonction ne fait pas attention aux valeurs `NULL` dans les colonnes individuelles, elles comptent uniquement le nombre de ligne.

```sql
SELECT COUNT(*)
FROM students;
```

### Compter les lignes avec des valeurs existantes dans une colonne

Lorsque l'on souhaite compter seulement les lignes où il y a une valeur non-NULL, on vient préciser la colonne que l'on souhaite compter.

```sql
SELECT COUNT(name)
FROM students;
```

Le requête vient compter uniquement les ligne ou une valeur non-null est présente dans la colonne `name`

### Compter les valeurs unique 

Pour compter le nombre de ligne ayant une valeur unique, on utilise avec `DISTINCT`

```sql
SELECT COUNT(DISTINCT age) FROM students;
```

Avec `DISTINCT`, en plus d'ignorer les valeurs null, on ignore également les doublons.

---

## SUM()

Cette fonction permet d'additionner les valeurs d'une colonne de type numérique.

Si une valeur est NULL, elle sera ignoré dans la somme.

```sql
SELECT SUM(colonne)
FROM table;
```

### Additionner les valeurs d'une colonne

```sql
SELECT SUM(salary) AS total_salary
FROM salaries;
```

### Additionner avec une condition 

```sql
SELECT SUM(salary) AS high_salary_total
FROM salaries
WHERE salary > 55000;
```

PG applique dans un premier temps la condition, puis effectue l'aggrégation sur les lignes restantes.

### Addition et filtrage

```sql
SELECT SUM(amount) AS total_sales
FROM sales
WHERE product_id = 1;
```

Cette requête permet d'obtenir la somme total du produit avec un id de 1

```sql
SELECT SUM(salary) - 200000 AS surplus
FROM salaries;
```

Cette requête permet de connaitre la valeur en plus

---

## AVG()

Permet de calculer la moyenne des valeurs d'une colonne. Elle additionne toutes les valeurs de la colonne et divise le résultat par le nombre de ces valeurs.

Les valeurs NULL sont ignorés. Si la valeur est 0, celle ci ne sera pas ignoré.
```sql
SELECT AVG(colonne)
FROM table;
```

### Salaire moyen 

```sql
SELECT AVG(salary) AS average_salary
FROM employees;
```

### Arrondir le résultat 

Pour arrondir le résultat, on peut utiliser la fonction `ROUND()`

Elle prends en premier argument la valeur à arrondir, et en second le nombre de chiffre après la virgule.

```sql
SELECT ROUND(AVG(salary), 2) AS rounded_average_salary
FROM employees;
```

### Filtrage avant de calculer

On peut utiliser WHERE pour venir filtrer les valeurs à intégrer dans le calcul de la moyenne.

```sql
SELECT AVG(salary) AS average_salary
FROM employees
WHERE id > 2;
```

### Requête complexe

Il est possible de combiner `AVG()` avec d'autre fonction d'agrégation et opérateur.

Pour calculer la moyenne total des ventes 

```sql
SELECT AVG(quantity * price) AS average_total_sale
FROM sales;
```

---

## MIN() & MAX()

Retourne la plus grande ou la plus petite valeur d'un ensemble de données.

Il est également possible d'utiliser cette fonction sur des chaîne ou bien des dates.

```sql
-- Forme générale des fonctions MIN et MAX
SELECT
    MIN(column_name) AS min_value,
    MAX(column_name) AS max_value
FROM table_name;
```

### Donnée numérique 

```sql
-- Exemple pour les données numériques
SELECT
    MIN(salary) AS min_salary,
    MAX(salary) AS max_salary
FROM employees;
```

### Donnée textuelle

Les chaîne sont comparées par ordre alphabétique

| id  | name  |
| --- | ----- |
| 1   | Anna  |
| 2   | Otto  |
| 3   | Maria |
| 4   | Ben   |
| 5   | Zoe   |

```sql
-- Exemple pour les données textuelles
SELECT
    MIN(name) AS first_name_in_order,
    MAX(name) AS last_name_in_order
FROM students;
```

|first_name_in_order|last_name_in_order|
|---|---|
|Anna|Zoe|

### Date et heure 

On peut également venir chercher la date la plus ancienne ou la plus récente.

| id  | event_name     | event_date |
| --- | -------------- | ---------- |
| 1   | New Year Party | 2023-01-01 |
| 2   | Summer Fest    | 2023-06-15 |
| 3   | Halloween Bash | 2023-10-31 |
| 4   | Year End Gala  | 2023-12-31 |
```sql
-- Exemple pour les dates
SELECT
    MIN(event_date) AS earliest_date,
    MAX(event_date) AS latest_date
FROM events;
```

|earliest_date|latest_date|
|---|---|
|2023-01-01|2023-12-31|
### Utilisation avec des valeurs NULL

Les valeurs NULL sont ignorées.

---

## Fonction numérique

### ROUND()

Cette fonction permet d'arrondir le résultat

```sql
ROUND(number [, digits])
```

- `number`: le nombre à arrondir 
- `digits` : optionnel - combien de chiffre après la virgule à conserver

```sql
SELECT ROUND(4.67);       -- 5
SELECT ROUND(4.6789, 2);  -- 4.68
```
### CEIL()

Permet d'obtenir un arrondis à l'entier supérieur.

```sql
SELECT CEIL(4.1);  -- 5
```
### FLOOR()

Permet d'obtenir un arrondis à l'entier inférieur 

```sql
SELECT FLOOR(4.9);  -- 4
```

### MOD()

Permet d'obtenir le reste d'une divisiopn (modulo).

Permet de :
- vérifier si un nombre est pair
- diviser des lignes en groupe selon un modèle 
- faire une boucle répétivite

```sql
MOD(dividend, divisor)

SELECT MOD(17, 5);  -- 2  (3*5 +2)
SELECT MOD(10, 3);  -- 1  (3*3 +1)
```

```sql
SELECT student_id,
       CASE WHEN MOD(student_id, 2) = 0 THEN 'Pair' ELSE 'Impair' END AS parite
FROM students;
```

Permet de répartir les étudiants en pair et impair.

### POWER()

```sql
POWER(base, exponent)
```

La fonction accepte les nombre entiers, décimaux et négatifs.

```sql
SELECT POWER(2, 3);   -- 8
SELECT POWER(5, 2);   -- 25
SELECT POWER(9, 0.5); -- 3 (racine carrée)
```

### SQRT()

Permet de calculer la racine carré 

```sql
SELECT SQRT(25);    -- 5
SELECT SQRT(2);     -- ~1.4142
```

Pour les nombre négatif, il faut utiliser la fonction `ABS()`

```sql
SELECT SQRT(ABS(-25));  -- 5
```

### Cas pratique 

#### Arrondir le montant total d'une commande

```sql
SELECT order_id, ROUND(total_price, 0) AS total_arrondi
FROM orders;
```

#### Calculer le nombre de page à afficher

```sql
SELECT CEIL(COUNT(*) / 10.0) AS pages_necessaires
FROM products;
```

#### Répartir les étudiants en 3 groupes

```sql
SELECT student_id,
       MOD(student_id, 3) AS numero_groupe
FROM students;
```

#### Racine de la moyenne des carrés

```sql
SELECT SQRT(AVG(POWER(score, 2))) AS racine_moyenne_carre
FROM grades;
```

#### Calcul de l'air des cercle

```sql
SELECT  
    circles.radius,  
    3.14159*POWER(radius, 2) AS area  
FROM circles
```
