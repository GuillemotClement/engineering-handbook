# CTE récursive et table commune

Les CTE (Common Table Expresson, ou expressions de table commune)  permet de créer des "fonction". 

On peut venir sortir une sous-requête SELECT, lui donner un nom et s'en servie dans la requête principale.

Les CTE sont utiles :
- Découper une requête complexe en plusieurs étapes logiques
- Améliorer la lisibilité de la requête
- Préparer des données temporaires, utilisée juste dans la requête en cours

La CTE existe que pendant le temps d'exécution de la requête.
## WITH 

La CTE commence par le mot clé `WITH` et s'assemble de cette manière 

```sql
WITH cte_name AS (
    SELECT ... -- ta requête ici
)

SELECT ...
FROM cte_name;
```

- `cte_name`: c'est le nom de la CTE, on peut choisir n'importe quel nom
- `()` : dans les paramètres, on met la requête permettant de préparer les données pour la suite
- une fois la CTE définis, on peut s'en servie comme d'une table table dans la requête principale

### CTE simple 

On as une table `students` avec leurs notes 

|student_id|name|grade|
|---|---|---|
|1|Otto Lin|89|
|2|Anna Song|94|
|3|Alex Ming|78|
|4|Maria Chi|91|
On souhaite sortir tous les étudiants avec une note au dessus de 85 et d'afficher leurs infos 

On peut faire cela avec une sous requête 

```sql
SELECT *
FROM (
    SELECT *
    FROM students
    WHERE grade > 85
) AS filtered_students;
```

Et la même chose avec une CTE 

```sql
WITH filtered_students AS (
    SELECT *
    FROM students
    WHERE grade > 85
)
SELECT *
FROM filtered_students;
```

### CTE multiples 

On peut définir plusieurs CTE dans une seule requête. 

On as une table `grades`, où sont stockées les notes des étudiants par cours

|student_id|course_id|grade|
|---|---|---|
|1|101|89|
|2|102|94|
|3|101|78|
|4|103|91|

Pour chaque étudiants, on veux trouver la note moyenne de ses notes, puis sortir ceux qui ont une moyenne > 85

```sql
-- définition des CTE
WITH student_averages AS (
    SELECT student_id, AVG(grade) AS avg_grade
    FROM grades
    GROUP BY student_id
),
high_achievers AS (
    SELECT student_id, avg_grade
    FROM student_averages 	-- on utilise le premier CTE - student_averages
    WHERE avg_grade > 85
)

SELECT *
FROM high_achievers; -- on utilise le deuxième CTE - high_achievers
```

- `student_average` : prépare les données - les moyennes de étudiants
- `high_achievers`: utilise ces données pour ne garder que ceux qui ont > 85

### CTE et table temporaire 

La CTE est parfaite pour préparer des données intermédiaire et les utiliser directements.

Si on souhaite réutiliser ce résultat à plusieurs endroits, la table temporaire est plus adaptée. 

---

## CTE DE PREPARATION DES DONNEES 

Les CTE simplifie la préparation des données, en permettant d'ajoute des étapes intermédiarie (filtrer, compter, agréger, calculer des moyennes, etc). 

### Filtrage avec CTE 

On commence par filtrer, on donne un nom à l'étape, et on travail ensuite avec le résultat comme avec une table normal

On souhaite utiliser une CTE pour sélectionner tous les etudiants ayant une note au desus de 85 

```sql
WITH excellent_students AS (
    SELECT student_id, first_name, last_name, grade
    FROM students
    WHERE grade > 85
)
SELECT * FROM excellent_students;
```

On filtre les données, qui seront ensuite utiliser dans la requête principale.

### Agrégation avec CTE 

On souhaite compter le nombre d'étudiants inscrits à chaque cours.

```sql
WITH course_enrollments AS (
    SELECT course_id, COUNT(student_id) AS student_count
    FROM enrollments
    GROUP BY course_id
)
SELECT * FROM course_enrollments;
```

Notre CT permet de grouper par cours, et de compte le nombre d'étudiants pour chacun.

### Calcul de métrique 

On souhaite calculer pour chaque cours le nombre d'étudiants, et la moyenne des notes pour le cours 

```sql
WITH course_counts AS (
    SELECT course_id, COUNT(student_id) AS student_count
    FROM enrollments
    GROUP BY course_id
),
course_avg_grades AS (
    SELECT e.course_id, AVG(g.grade) AS avg_grade
    FROM enrollments e
    JOIN grades g ON e.student_id = g.student_id
    GROUP BY e.course_id
)
SELECT cc.course_id, cc.student_count, cag.avg_grade
FROM course_counts cc
JOIN course_avg_grades cag ON cc.course_id = cag.course_id;
```

---

## CTE RECURSIFS 

Les CTE récursive permettent de gérer les structures de donnée hiérarchique ou en arbre, comme les organigramme entreprise, les arbres généalogique ou les arborescence de fichier.

Ce sont des expressions qui peuvent s'appeler elle même pour parcourir, et traiter tous les niveaux de données petit à petit.

Une CTE recursive et composé du mot clé `WITH RECURSIVE` ainsi que de deux parties : 
- **La requête de base**: elle définit le point de départ de la récursivité 
- **La requête récursive**: elle traite le reste des données en utilisant le résultat de l'étape précédente.

```sql
WITH RECURSIVE cte_name AS (
    -- Requête de base
    SELECT column1, column2
    FROM table_name
    WHERE condition_pour_le_cas_de_base

    UNION ALL

    -- Requête récursive
    SELECT column1, column2
    FROM table_name
    JOIN cte_name ON some_condition
    WHERE condition_arret
)
SELECT * FROM cte_name;
```

### UNION || UNION ALL

Chaque CTE récursive doit utiliser les opérateurs `UNION` ou `UNION ALL` entre la partie de base et la partie récursive

- `UNION`: fusionne les résultats des deux requêtes et supprime les doublons de lignes
- `UNION ALL`: fusionne et garde toutes les lignes, même les répétées

Par défaut, on prends `UNION ALL` car c'est le plus rapide. Il fusionne juste les résultats sans vérification des doublons.

On utilise `UNION` seulement dans les cas ou on sait que les doublons sont gênants et qu'il faut les retirer.

```sql
-- UNION : les doublons sont exclus
SELECT 'A'
UNION
SELECT 'A';     -- Résultat : une seule ligne 'A'

-- UNION ALL : les doublons sont gardés
SELECT 'A'
UNION ALL
SELECT 'A';     -- Résultat : deux lignes 'A'
```

On as une table employees avec les colonnes `employee_id` et `manager_id` et `name`. On veut construire la hiérachie en partant du directeur (la personne sans chef `manager_id = NULL).

|employee_id|name|manager_id|
|---|---|---|
|1|Eva Lang|NULL|
|2|Alex Lin|1|
|3|Maria Chi|1|
|4|Otto Mart|2|
|5|Anna Song|2|
|6|Eva Lang|3|

On souhaite savoir qui dépend de qui, et connaître le niveau de chaque employé dans la structure. 

```sql
WITH RECURSIVE employee_hierarchy AS (
    -- On commence par ceux qui n'ont pas de chef
    SELECT
        employee_id,
        name,
        manager_id,
        1 AS level
    FROM employees
    WHERE manager_id IS NULL

    UNION ALL

    -- On ajoute les subordonnés et on augmente le niveau
    SELECT
        e.employee_id,
        e.name,
        e.manager_id,
        eh.level + 1
    FROM employees e
    INNER JOIN employee_hierarchy eh
    ON e.manager_id = eh.employee_id
)
SELECT * FROM employee_hierarchy;
```

On obtiens ce genre de résultat : 

|employee_id|name|manager_id|level|
|---|---|---|---|
|1|Eva Lang|NULL|1|
|2|Alex Lin|1|2|
|3|Maria Chi|1|2|
|4|Otto Mart|2|3|
|5|Anna Song|2|3|
|6|Eva Lang|3|3|
Cette requête permet de parcourir la hiérarchie des employés. Le champ level est utile pour formater ou visualiser l'arbre.

---

