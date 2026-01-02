## GROUP BY

L'opérateur `GROUP BY` prends des données éparpillées et les regroupe.

Permet de savoir combien de commandes chaque client à passé, combien d'édutiants dans chaque course, etc

Le groupement est le processus qui consiste à rassembler les lignes qui ont les même valeurs dans une ou plusieurs colonnes en groupe logique.
Cela permet d'appliquer des fonction d'agrégation séparément à chaque groupe.

Par exemple, on as une table employees, et on souhaite connaitre le salaire moyen par département. 
SQL utilise `GROUP BY` pour diviser la table des employés par département, puis applique `AVG` à chaque groupe.

Les valeurs `NULL` sont considéré comme un groupe séparé. Si la colonne à des null, SQL va créer un groupe pour null.

### Utilisation 

Pour utiliser le groupement, chaque colonne qui n'est pas dans une fonction d'agrégation doit être listé dans le `GROUP BY`

Une fonction d'agrégation est une fonction qui prends plusieurs lignes et les résume en une seule valeur. C'est la capacité à réduire plusieurs résultat en une seule.

```sql
SELECT colonne1,
       fonction_agregat(colonne2)
FROM table
GROUP BY colonne1;
```

1. On indique la colonne sur laquelle on veut grouper les données
2. On utilise des fonctions d'agrégation pour calculer des valeurs dans les groupes
3. Toutes les colonnes dans `SELECT` qui ne sont pas dans des fonctions d'agrégation doivent être listées dans `GROUP BY`.

#### Groupement d'étudiant par faculté 

| id  | name      | faculty     | gpa |
| --- | --------- | ----------- | --- |
| 1   | Alex Lin  | ComputerSci | 3.8 |
| 2   | Maria Chi | Math        | 3.5 |
| 3   | Anna Song | ComputerSci | 4.0 |
| 4   | Otto Art  | Math        | 3.9 |
| 5   | Liam Park | Physics     | 3.7 |
On souhaite connaitre la moyenne des note (GPA) pour chaque faculté.

```sql
SELECT faculty, AVG(gpa) AS avg_gpa
FROM students
GROUP BY faculty;
```

|faculty|avg_gpa|
|---|---|
|ComputerSci|3.9|
|Math|3.7|
|Physics|3.7|
SQL à d'abord séparé les données en groupes selon la colonne `faculty` puis a appliqué la fonction `AVG()` à chaque groupe.

#### Groupement par fac & par nom d'élève

On veux grouper les données sur les faculté et sur le nom des étudiants, on ajoute une seconde colonne dans le `GROUP BY`

| id  | name      | faculty     | gpa |
| --- | --------- | ----------- | --- |
| 1   | Alex Lin  | ComputerSci | 3.8 |
| 2   | Maria Chi | Math        | 3.5 |
| 3   | Anna Song | ComputerSci | 4.0 |
| 4   | Otto Art  | Math        | 3.9 |
| 5   | Liam Park | Physics     | 3.7 |

```sql
SELECT faculty, name, AVG(gpa) AS avg_gpa
FROM students
GROUP BY faculty, name;
```

|faculty|name|avg_gpa|
|---|---|---|
|ComputerSci|Alex Lin|3.8|
|ComputerSci|Anna Song|4.0|
|Math|Maria Chi|3.5|
|Math|Otto Art|3.9|
|Physics|Liam Park|3.7|
#### Groupement avec plusieurs fonction d'agrégation 

Il est possible d'utiliser plusieurs fonction. Par exemple, on veut compter le nombre d'étudiants par fac et calculer la moyenne des notes 

| id  | name      | faculty     | gpa |
| --- | --------- | ----------- | --- |
| 1   | Alex Lin  | ComputerSci | 3.8 |
| 2   | Maria Chi | Math        | 3.5 |
| 3   | Anna Song | ComputerSci | 4.0 |
| 4   | Otto Art  | Math        | 3.9 |
| 5   | Liam Park | Physics     | 3.7 |

```sql
SELECT faculty,
       COUNT(*) AS student_count,
       AVG(gpa) AS avg_gpa
FROM students
GROUP BY faculty;
```

|faculty|student_count|avg_gpa|
|---|---|---|
|ComputerSci|2|3.9|
|Math|2|3.7|
|Physics|1|3.7|
### Particularité 

Si on as un `GROUP BY`dans la requête; alors toutes les colonnes du résultats doivent être considéré comme des expressions calculées.

Les colonnes du `SELECT` peuvent être :
- calculée par des fonctions d'agrégations
- prise depuis GROUP BY - il faut grouper dessus.

```sql
SELECT faculty, name
FROM students
GROUP BY faculty;
```

Cette requête ne fonctionne pas. 

`GROUP BY` va diviser les étudiants en groupe avec la même `faculty` pour chaque groupe. Comme tous les étudiants du groupe ont la même faculté, on peut dire que l'attribut `faculty` appartient au groupe d'étudiant. `name` étant différents, le groupe n'as pas l'attribut name.

---

## HAVING 

Cet opérateur permet de filtrer une fois les agrégat appliqué.

Par exemple, on souhaite voir uniquement les service où le salaire moyen dépasse 50000

`HAVING` prend en compte les NULL, il faut donc faire attention.

### Différence avec WHERE

Le `WHERE` filtre les ligne avant le groupement.

`HAVING` filtre après que les données ont été groupée et que les fonctions d'agrégat ont fait leur magie.

Le `HAVING` permet de filtrer au niveau du groupe.

### Utilisation 

```sql
SELECT colonnes, fonctions_aggrégées
FROM table
GROUP BY colonnes
HAVING condition;
```

1. Les lignes sont filtré avec `WHERE`
2. Les données sont groupées avec `GROUP BY`
3. Les fonctions d'agrégats sont appliquées aux résultat du groupement
4. `HAVING` vient filtrer le résultat des agrégats

#### Filtrer les fac avec beaucoup d'étudiants

| d   | name    | faculty     |
| --- | ------- | ----------- |
| 1   | Alice   | Engineering |
| 2   | Bob     | Engineering |
| 3   | Charlie | Arts        |
| 4   | Daisy   | Business    |
| 5   | ...     | ...         |
```sql
SELECT faculty, COUNT(*) AS student_count
FROM students
GROUP BY faculty
HAVING COUNT(*) > 100;
```

Cette requête permet de voir les fac qui ont plus de 100 étudiants

1. On commence par grouper les étudiants par faculty 
2. La fonction d'agrégat compte le nombre d'étudiants dans chaque fac
3. `HAVING`filtre toutes les fac où il y a 100 étudiants ou moins

Le `HAVING` travail avec les résultat issue du `GROUP BY`

#### Trouver les produits avec un total de vente > 500

```sql
SELECT product_id, SUM(sales_amount) AS total_sales
FROM sales
GROUP BY product_id
HAVING SUM(sales_amount) > 500;
```

#### Utilisation avec WHERE 

```text
1. FROM → 2. WHERE → 3. GROUP BY → 4. HAVING → 5. SELECT
```

```sql
SELECT department, AVG(age) AS avg_age
FROM students
WHERE age > 18
GROUP BY department
HAVING AVG(age) > 20;
```

#### Ordonner le trie

```sql
SELECT  
    reviews.product,  
    AVG(rating) AS avg_rating  
FROM reviews  
GROUP BY product  
HAVING AVG(rating) >= 4  
ORDER BY AVG(rating) DESC;
```

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
