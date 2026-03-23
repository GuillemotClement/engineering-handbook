Les jointures permettent de combiner plusieurs tables dans une requête.

## Type de jointure 

|Type de `JOIN`|Description|
|---|---|
|`INNER JOIN`|Renvoie les lignes qui ont une correspondance dans les deux tables.|
|`LEFT JOIN`|Renvoie toutes les lignes de la table de gauche, et seulement les correspondances de la table de droite.|
|`RIGHT JOIN`|Renvoie toutes les lignes de la table de droite, et seulement les correspondances de la table de gauche.|
|`FULL OUTER JOIN`|Renvoie toutes les lignes des deux tables, en mettant `NULL` là où il n'y a pas de correspondance.|
- Si on veux seulement les données qui matchent dans les deux tables : `INNER JOIN`
- Si on veux garder toutes les données d'une table, et juste faire correspondances de l'autre: `LEFT JOIN` ou `INNER JOIN`
- Si on veux tout, même ce qui ne match pas : `FULL OUTER JOIN`

### Utilisation 

On as trois tables :

`students`

|id|name|
|---|---|
|1|Otto|
|2|Anna|
|3|Peter|
`courses`

|id|title|
|---|---|
|101|Mathématiques|
|102|Anglais|

`enrollments`

|student_id|course_id|
|---|---|
|1|101|
|2|102|
Les trois tables sont lié de cette manière : 
- `id` de `students`
- `id` de `courses`
- Dans la table `enrollments`, les deux id permettent de lier un étudiant avec un cours.

On souhaite savoir quel étudiants est inscrit à quel cours. Pour cela, on utilise une jointure.

```sql
SELECT students.name, courses.title
FROM enrollments
JOIN students ON enrollments.student_id = students.id
JOIN courses ON enrollments.course_id = courses.id;
```

- `FROM enrollments` : on se base sur la table qui contient les relations
- `JOIN students ON enrollments.student_id = students.id` - on ajoute la table `students`
- `JOIN courses ON enrollments.course_id = courses.id;` - on ajoute la table `courses`

|name|title|
|---|---|
|Otto|Mathématiques|
|Anna|Anglais|
	Si un étudiant n'as pas de cours, il n’apparaît pas dans le résultat. `JOIN` est strict par défaut (`INNER JOIN` )

1. L'ordre dans le `JOIN` compte. Par exemple `LEFT JOIN` retourne les lignes de la table de gauche.

--- 

## INNER JOIN 

Permet de récupérer des lignes de deux tables qui match selon la condition définis. Elle retourne que les parties qui se croisent entre deux tables. Le reste est ignoré.

Par exemple, une table students, et une autre cours. Si un étudiant n'est pas lié à un cours, celui ci n'est pas retourné.

```sql
SELECT colonnes
FROM table1 INNER JOIN table2
	ON table1.champ = table2.champ;
```

- `ON` permet de déclarer la condition.
- `table1` et `table2` sont les tables que l'on souhaite joindre
- `champ` : les colonnes sur lesquels ont fait la correspondance

### Récupération des inscriptions des étudiants et leurs cours 

`students`

|student_id|name|age|
|---|---|---|
|1|Otto|20|
|2|Anna|22|
|3|Peter|19|
|4|Dia|21|
`enrollments`

|enrollment_id|student_id|course_id|
|---|---|---|
|101|1|501|
|102|2|502|
|103|2|503|
|104|3|504|

```sql
SELECT students.name, enrollments.course_id
FROM students INNER JOIN enrollments
	ON students.student_id = enrollments.student_id;
```

| name  | course_id |
| ----- | --------- |
| Otto  | 501       |
| Anna  | 502       |
| Anna  | 503       |
| Peter | 504       |
L'étudiant qui n'as pas de cour lié n'est pas retourné par la requête.

---

## LEFT JOIN 

`LEFT JOIN` retourne toutes les lignes de la table de gauche (celle indiquée en premier dans la requête) et les lignes correspondantes de la table de droite.

Si il n'y a pas de correspondance avec les colonnes de la table de droite, elles auront la valeur `NULL`

Par exemple, une liste d'étudiants et de cours. Tous les étudiants ne sont pas inscrits à des cours, et on souhaite voir la liste complète des étudiants.

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

- `table1` : c'est la table de gauche => celle où l'on souhaite voir toutes les données. 
- `table2` : c'est la table de droite 

```sql
SELECT
    students.name,
    enrollments.course
FROM
    students LEFT JOIN enrollments
ON
    students.student_id = enrollments.student_id;
```

Avec celle requête, on obtiens la liste de tous les étudiants, même ceux n'ayant pas de cours associé.

| name  | course        |
| ----- | ------------- |
| Otto  | Mathématiques |
| Otto  | Physique      |
| Anna  | Biologie      |
| Peter | NULL          |
### Afficher les produits et leurs ventes 

On souhaite voir la listes de tous les produits, la quantité vendu et même ceux n'ayant aucune vente.

```sql
SELECT
    products.product_name,
    SUM(sales.quantity) AS total_sold
FROM
    products LEFT JOIN sales
    ON
    products.product_id = sales.product_id
GROUP BY
    products.product_name;
```

|product_name|total_sold|
|---|---|
|Smartphone|8|
|Tablette|2|
|Ordinateur portable|NULL|
### Remplacer le NULL générer par LEFT JOIN 

```sql
SELECT
    students.name,
    COALESCE(enrollments.course, 'Cours non choisi') AS course
FROM
    students LEFT JOIN enrollments
ON
    students.student_id = enrollments.student_id;
```

|name|course|
|---|---|
|Otto|Mathématiques|
|Otto|Physique|
|Anna|Biologie|
|Peter|Cours non choisi|
### Filtrer les valeurs NULL

```sql
SELECT
    students.id,
    students.name,
    enrollments.course_name
FROM
    students LEFT JOIN enrollments
    ON students.id = enrollments.student_id
WHERE
    enrollments.course_name IS NOT NULL;
```

### Utiliser CASE

```sql
SELECT
    students.id,
    students.name,
    CASE
        WHEN enrollments.course_name IS NULL THEN 'Pas de cours'
        ELSE enrollments.course_name
    END AS course_name
FROM
    students LEFT JOIN enrollments
    ON students.id = enrollments.student_id;
```

#### Doublons 

Si les données de la table de droite contiennent des doublons, le résultat de la requête aura plus de lignes qu'attendus. On viens donc utiliser `DISTINCT`  pour filtrer les doublons.

---

## RIGHT JOIN 

Retourne toutes les lignes de la table de droite, et seulement les match de la table de gauche.

```sql
SELECT
    colonnes
FROM
    table_gauche RIGHT JOIN table_droite
ON
    condition_de_jointure;
```

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

|enrollment_id|course_name|student_name|
|---|---|---|
|101|Mathématiques|Otto|
|102|Informatique|Maria|
|103|Biologie|NULL|

---

## FULL OUTER JOIN 

Permet de retourner toutes les lignes, même celle n'ayant pas de match. Les valeurs manquante sont remplacé par NULL.

```sql
SELECT
    colonnes
FROM
    table1
FULL OUTER JOIN
    table2
ON table1.colonne_commune = table2.colonne_commune;
```

On souhaite obtenir la liste complète des étudiants et des cours, y compris ceux inscrit à aucun cours, et les cours sans étudiants

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

### Gérer les NULL

```sql
SELECT
    COALESCE(s.nom, 'Aucun étudiant') AS nom_étudiant,
    COALESCE(e.cours, 'Aucun cours') AS nom_cours
FROM
    students s
FULL OUTER JOIN
    enrollments e
ON
    s.student_id = e.student_id;
```

---

## JOIN multiple 

Lorsque l'on utilise plusieurs `JOIN`, il sont traité de gauche à droite. Cela signifie qu'il commence à joindre les deux première table, puis le résultat est joint à la troisième et ainsi de suite 

```sql
SELECT *
FROM students
    INNER JOIN enrollments ON students.id = enrollments.student_id
    INNER JOIN courses ON enrollments.course_id = courses.id;
```

1. les tables `students` et `enrollments` sont jointe sur la colonne définis.
2. le résultat est utilisé pour joindre la table `courses` sur la condition

L'odre des jointure est importante pour améliorer les performances.

### Filtrage de requêtes avec plusieurs JOIN 

On peut ajouter une condition pour optimiser la requête. Seulement les étudiants dans le bon cours seront traité.

```sql
SELECT
    students.name AS student_name,
    courses.name AS course_name
FROM students
    INNER JOIN enrollments ON students.id = enrollments.student_id
    INNER JOIN courses ON enrollments.course_id = courses.id
WHERE courses.name = 'Mathematics';
```

### Optimisation des requêtes

#### Utiliser des index.

Les index permettent d'aller plus vite, surtout lors des jointures sur des champs clés. 

#### Filtrer au plus tôt 

Utiliser des conditions `WHERE` pour réduire le nombre de lignes avant de faire un `JOIN`

```sql
SELECT
    students.name AS student_name,
    courses.name AS course_name
FROM students
    INNER JOIN enrollments ON students.id = enrollments.student_id
    INNER JOIN courses ON enrollments.course_id = courses.id
WHERE
    courses.teacher = 'Ivan Petrov';
```

#### Réduire le nombre de lignes à joindre

Venir utiliser des sous requête pour réduire le nombre de ligne 

```sql
SELECT
    students.name AS student_name,
    courses.name AS course_name
FROM
    (SELECT * FROM students WHERE id IN (1, 2)) sub_students
INNER JOIN enrollments ON sub_students.id = enrollments.student_id
INNER JOIN courses ON enrollments.course_id = courses.id;
```

---

## ON ... AND ...

Il est possible d'ajouter des conditions directement dans la jointure. Cela permet :
- Plus rapide => moins de lignes passent par le `JOIN`
- Plus précise => le filtrage se fait au moment de la jointure 
- Plus prévisible => avec un `LEFT JOIN`

On souhaite récupérer la liste des étudiants qui ont des cours actifs :

```sql
SELECT
    students.name AS student_name,
    courses.name AS course_name
FROM students
INNER JOIN enrollments
    ON students.id = enrollments.student_id
    AND enrollments.status = 'active'
INNER JOIN courses
    ON enrollments.course_id = courses.id;
```

---

## SELF JOIN 

Permet de joindre une table avec elle-même.

Par exemple, une table employée, et chaque employée a un manager. Le manager est également un employée.
`SELF JOIN` permet de relier les employés à leur manager.

`SELF JOIN` est un `JOIN` mais on utilise deux fois la même table en lui donnant des alias différents pour distinguer ses versions.

On utilise `SELF JOIN` : 
- Les relations hiérarchique : relation parent-> enfant
- Analyse de données dans la même table
- Requête complexe sur des structures avec de la logique dupliquée

```sql
SELECT
    A.column_name,
    B.column_name
FROM
    table_name A
JOIN
    table_name B
ON
    A.common_column = B.common_column;
```

- `table_name A` et `table_name B` : même table mais avec un alias
- `A.common_column` et `B.common_column` : les colonnes utilisé pour joindre les lignes.

|employee_id|name|manager_id|
|---|---|---|
|1|Alex Lin|NULL|
|2|Maria Chi|1|
|3|Otto Song|1|
|4|Nina Zhao|2|
- `employee_id`: identifiant de l'employé
- `name` : nom de l'employé
- `manager_id` : identifiant du manager, qui est également `employee_id` d'un autre employé

```sql
SELECT
    e.name AS employee_name,
    m.name AS manager_name
FROM
    employees e
LEFT JOIN
    employees m
ON
    e.manager_id = m.employee_id;
```

### Trouver des produits similaire 
|product_id|product_name|category|
|---|---|---|
|1|Réfrigérateur|Technika|
|2|Machine à laver|Technika|
|3|Smartphone|Gadzhety|
|4|Tablette|Gadzhety|
On veux trouver les paires de produits qui sont dans la même catégorie 

```sql
SELECT
    p1.product_name AS product_1,
    p2.product_name AS product_2
FROM
    products p1
JOIN
    products p2
ON
    p1.category = p2.category
AND
    p1.product_id < p2.product_id;
```

La dernière condition permet d'éviter de dupliquer les paires.
