Les sous requête sont des requêtes utiliser à l'intérieur d'une autre requête SQL. Cette sous requête s'exécute en premier, et son résultat est utilisé par la requête principale.

On as deux tables :

`students`

| id  | nom    | âge | groupe_id |
| --- | ------ | --- | --------- |
| 1   | Alisa  | 20  | 1         |
| 2   | Bob    | 22  | 2         |
| 3   | Klark  | 21  | 1         |
| 4   | Dina   | 23  | 3         |
| 5   | Emilia | 22  | 2         |
`groups`

|id|nom_du_groupe|
|---|---|
|1|Classe mathématique|
|2|Classe physique|
|3|Classe littéraire|
Si on souhaite connaitre le nom des groupes où étudient les étudiants, on peut venir utiliser une sous requête.

```sql
SELECT nom_du_groupe
FROM groups
WHERE id IN (
    SELECT groupe_id
    FROM students
    WHERE âge > 21
);
```

```sql
SELECT groupe_id
FROM students
WHERE âge > 21
```

La sous requête sélectionne un `groupe_id` de tous les étudiants de plus de 21ans. On récupère une liste d'id de groupe, par exemple `[2, 3]`.

```sql
SELECT nom_du_groupe
FROM groups
WHERE id IN ([résultats de la sous-requête])
```

La requête principale utilise le résultat de la sous requête et retourne les noms des groupes avec un `id` égal à 2 ou 3.

Le résultat d'une requête `SELECT` est une "table virtuelle". On peut venir l'utilise de la même manière que sur de vraie tables: dans un `JOIN` par exemple.

Les sous requêtes sont utiles dans ces cas :
- Simplifier des tâches compliqués: Parfois, une seule table n'a pas toute les infos nécessaire. Les sous requête permettent de diviser la requête en deux étapes : d'abord trouver un résultat intermédiaire, puis l'utiliser pour obtenir les données finale
- Travail avec des résultats intermédiaire: par exemple, pour faire des calculs en plus avant de traiter les données.
- Améliorer la lisibilité du code: les sous requête permettent de rendre le code plus structuré, surtout lorsque l'on travail avec de grosse tables et une logique compliqué.

### Type de requête imbriquées

Il est possible d'utiliser à plusieurs endroits dans une requête SQL. Selon l'endroit où la sous requête est placé, il y a plusieurs types 

#### Sous requête dans le SELECT 

La sous requête est placer dans la liste de colonne et sert à calculer des valeurs. Cela permet d'ajouter une nouvelle colonne dans le résultat.

Ajouter une colonne avec l'âge max parmi les étudiants.

```sql
SELECT nom, âge,
       (SELECT MAX(âge) FROM students) AS âge_max
FROM students;
```

|nom|âge|âge_max|
|---|---|---|
|Alisa|20|23|
|Bob|22|23|
|Klark|21|23|
|Dina|23|23|
|Emilia|22|23|
#### Sous requête dans le FROM 

La sous requête sera utilisée comme une table temporaire. C'est utile lorsque l'on doit d'abord agréger ou transformer les données.

Par exemple, calculer l'âge moyen des étudiants dans chaque groupe 

```sql
SELECT 
	tmp.groupe_id, 
	tmp.âge_moyen
FROM (
    SELECT 
	    groupe_id, 
	    AVG(âge) AS âge_moyen
    FROM students
    GROUP BY groupe_id
) AS tmp -- On donne le pseudo tmp à la table temporaire
WHERE tmp.âge_moyen > 21;
```

|groupe_id|âge_moyen|
|---|---|
|2|22.0|
|3|23.0|
#### Sous requête HAVING ou WHERE 

La sous requête permet d'ajouter une condition pour filtrer les lignes. C'est généralement utiliser pour vérifier l'existence d'enregistrement ou comparer des valeurs 

Par exemple, les étudiants qui sont plus âgés que la moyenne 

```sql
SELECT nom, âge
FROM students
WHERE âge > (
    SELECT AVG(âge)
    FROM students
);
```

| nom    | âge |
| ------ | --- |
| Bob    | 22  |
| Dina   | 23  |
| Emilia | 22  |

---

## Requête imbriqué dans SELECT 

Une sous requête dans le `SELECT` permet de calculer des valeurs directement pendant l'exécution de la requête principale. Cela permet de combiner des calculs agrégés, des filtres complexes, ou même d'autre collection de données dans une seule requête.

### Ajouter la note maximal du groupe 

On veux afficher le noms des étudiants, leurs notes et la note maximale du groupe.

```sql
SELECT
    s.name AS student_name,
    g.grade AS student_grade,
    (
        SELECT MAX(grade) -- cette requête retourne une seule valeur
        FROM grades
        INNER JOIN students ON grades.student_id = students.student_id
        WHERE students.group_id = s.group_id
    ) AS max_group_grade
FROM
    students s
INNER JOIN
    grades g ON s.student_id = g.student_id;
```

1. Pour chaque étudiants, on récupère son nom et sa note.
2. La sous requête permet de retourner la note maximale dans le groupe d'étudiant
3. La sous requête s'exécute pour chaque ligne de la requête principale, et utilise la condition pour limiter la sélection à un seule groupe.

### Ajouter la moyenne des note du groupe 

On souhaite ajouter la moyenne 
```sql
SELECT
    s.name AS student_name,
    g.grade AS student_grade,
    (
        SELECT AVG(grade)
        FROM grades
        INNER JOIN students ON grades.student_id = students.student_id
        WHERE students.group_id = s.group_id
    ) AS avg_group_grade
FROM
    students s
INNER JOIN
    grades g ON s.student_id = g.student_id;
```

### Nombre de cours pour chaque étudiants 

```sql
SELECT
    s.name AS student_name,
    (
        SELECT COUNT(*)
        FROM enrollments
        WHERE enrollments.student_id = s.student_id
    ) AS course_count
FROM
    students s;
```

### Ajout d'un flag 

```sql
SELECT
    s.name AS student_name,
    (
        SELECT CASE
            WHEN MIN(g.grade) = 5 THEN 'Excellent'
            ELSE 'Pas excellent'
        END
        FROM grades g
        WHERE g.student_id = s.student_id
    ) AS status
FROM
    students s;
```

Ici, le `CASE` dans la sous requête permet de donner un status selon la conditon.
### Limite et recommandations

1. Chaque sous requête s'exécute pour chaque ligne de la requête princiaple. Cela peut ralentir l'exécution de la requête si les tables sont grosses.
2. Index: pour accélérer ce genre de requête, il est important d'indéxer les colonnes utilisées dans la condition de la sous requête
3. Lisibilité: attention à pas trop ajouter d'imbrication. Si les sous requête deviennent trop complete, il faut penser à la déplacer dans le `FROM` ou créer des tables temporaire.

---

## IN 

Permet de récupérer des valeurs dans le cas ou celle ci est passer en argument

```sql
SELECT colonnes
FROM table
WHERE colonne IN (valeur1, valeur2, valeur3, ...);
```

```sql
SELECT name, course
FROM students
WHERE course IN ('Programmation', 'Mathématiques', 'Physique');
```

Avec cette requête, seule les donnée qui valide le `IN` seront retournée.

---

## NOT IN

Permet de retourner les valeurs qui ne valide pas la conditon 

```sql
SELECT name, course
FROM students
WHERE course NOT IN ('Programmation', 'Mathématiques', 'Physique');
```

Cette requête retourne les élève qui ne participe aux cours définit dans la conditon.

### Utilisation avec des sous requêtes

On souhaite trouver les étudiants inscrits à des cours qui existe dans la table `courses`. On peut venir utiliser l'opérateur `IN`.

```sql
SELECT name
FROM students
WHERE course_id IN (
    SELECT id
    FROM courses
);
```

La sous requête permet de récupérer la liste des id des cours. On viens ensuite avec `IN` sélectionnera les cours souhaités.

### Comportement avec des NULL

La valeur `NULL` est ignoré avec le `IN`. 

Avec `NOT IN`, on peut avoir des comportements innatendu. On viendras donc faire une vérification en plus 

```sql
SELECT student_id
FROM grades
WHERE grade NOT IN ('A', 'B', 'C')
   OR grade IS NULL;
```

---

## EXISTS

Vérifie l'existence d'un enregistrement dans le résultat d'une sous requête. Si la requête retourne au moins une liste, return `TRUE`.

Dès qu'nune correspondance est trouvé, la requête est stoper.

```sql
SELECT colonnes
FROM table
WHERE EXISTS (
    SELECT 1
    FROM autre_table
    WHERE condition
);
```

- La sous requête peut être n'importe quel requête 
- C'est le résultat de la sous requête qui décide si on retourne `TRUE`

```sql
SELECT 1
WHERE EXISTS (
    SELECT *
    FROM students
    WHERE grade > 3.5
);
```

Si au moins une donnée vérifie la condition, la requête retourne `1`

---

## NOT EXISTS

Retourne `TRUE` si la sous requête ne retourne aucune ligne.

```sql
SELECT *
FROM students s
WHERE NOT EXISTS (
    SELECT 1
    FROM students
    WHERE grade IS NOT NULL
    AND id = s.id
);
```

La requête va retourner tous les étudiants qui n'ont pas de note.