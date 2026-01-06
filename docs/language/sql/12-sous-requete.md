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

Elle permet d'ajouter des colonnes supplémntaire avec des valeurs calculées ou des données qui dépendent d'autre enregistrements ou table.

```sql
SELECT 
	column1, 
	column2, 
	(
		SELECT agrégation_ou_condition 
		FROM autre_table 
		WHERE condition) AS nouveau_nom_colonne 
FROM table_principale;
```

Attention, la sous-requête ne retourne qu'une seule valeur, qui apparaît dans le résultat comme une nouvelle colonne.

La condition peut faire référence aux colonnes de la `table_principale`.

Si la sous requête ne trouve pas de donnée, le résultat sera `NULL`.
### Ajouter la moyenne d'un étudiant

```sql
SELECT 
	s.id, 
	s.name, 
	(
		SELECT 
			AVG(g.grade) 
		FROM grades g 
		WHERE g.student_id = s.id
	) AS average_grade 
FROM students s;
```

La sous requête viens calculer la moyenne pour chaque étudiant. Elle retourne une valeur pour chaque ligne de la table `student`.

Cela permet d'éviter de faire un `JOIN` ou préparer des `VIEW`.

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

Ici, le `CASE` dans la sous requête permet de donner un statuts selon la condition.
### Limite et recommandations

1. Chaque sous requête s'exécute pour chaque ligne de la requête principale. Cela peut ralentir l'exécution de la requête si les tables sont grosses. L'utilise d'un `JOIN` est préférable pour l'optimisation
2. Index: pour accélérer ce genre de requête, il est important d'indexer les colonnes utilisées dans la condition de la sous requête
3. Lisibilité: attention à pas trop ajouter d'imbrication. Si les sous requête deviennent trop complète, il faut penser à la déplacer dans le `FROM` ou créer des tables temporaire.

---

## Sous-requête dans FROM 

L'utilisation de sous requête dans le `FROM` permet de créer des tables temporaire, directement utilisation dans la requête.
Elles ne sont pas stocker sur le serveur.

Elles permettent :
- fusionner ou agréger des données temporairement avant la requête principale
- créer des ensemble de données structurées à la volée
- réduire le nombre d'opérations, en demandant à la base de stocker un minimum de données intermédiaires

1. On écrit la sous requête dans le `FROM` entre parenthèses
2. On lui donne un alias 
3. On utilise cette alias comme si c'était une table 

```sql
SELECT 
	colonnes 
FROM ( 
		SELECT colonnes 
		FROM table 
		WHERE condition 
) AS alias 
WHERE condition_externe;
```

On utilise ce type de sous requête :
- Pour les données agrégée
- Pour filtrer les données
- Pour simplifier les requêtes complexes
### Etudiants et moyenne des notes 

On commence par écrire une sous requête qui calcule la moyenne de chaque étudiant, puis l'utiliser dans la requête principale

```sql
SELECT 
	s.student_name, 
	g.avg_grade -- on tape dans la table temporaire
FROM ( 
	SELECT 
		student_id, 
		AVG(grade) AS avg_grade 
	FROM grades 
	GROUP BY student_id 
) AS g 
JOIN students AS s ON s.student_id = g.student_id;
```

### Deux niveau de traitements 

On veut trouver les étudiants qui ont une moyenne supérieur à 80. On commence par une sous-requête qui calcule les moyenne, puis on utilise dans le filtre 

```sql
SELECT 
	s.student_name, 
	g.avg_grade 
FROM students AS s 
	JOIN ( 
		SELECT student_id, 
		AVG(grade) AS avg_grade 
		FROM grades 
		GROUP BY student_id 
	) AS g ON s.student_id = g.student_id 
WHERE g.avg_grade > 80;
```

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

### Etudiants inscrits à des cours 

On recherche ceux qui sont déjà inscrit quelque part.

```sql
SELECT name
FROM students s
WHERE EXISTS (
    SELECT 1
    FROM enrollments e
    WHERE s.id = e.student_id
);
```

Si un étudiant apparait dans `enrollments`, il est dans le résultat.

### Cours avec au moins 5 étudiants inscrits

`courses`

|course_id|name|
|---|---|
|1|Mathématiques|
|2|Histoire|
|3|Biologie|
|4|Philosophie|
`enrollments`

|student_id|course_id|
|---|---|
|1|1|
|2|1|
|3|1|
|4|1|
|5|1|
|6|1|
|7|2|
|8|2|
|9|2|
|10|NULL|
On souhaite trouver les cours où plus de 5 étudiants sont inscrits. `EXISTS` demande s'il y a au moins un groupe d'inscriptions pour ce cours où il y a plus de cinq étudiants ?

```sql
SELECT name 
FROM courses c 
WHERE EXISTS ( 
	SELECT 1 
	FROM enrollments e 
	WHERE c.course_id = e.course_id 
	GROUP BY e.course_id 
	HAVING COUNT(*) > 5 
);
```

On obtient "Mathématiques" qui a six étudiants inscrits.

---

## NOT EXISTS

Retourne `TRUE` si la sous requête ne retourne aucune ligne.

|id|name|grade|
|---|---|---|
|1|Otto|NULL|
|2|Anna|4.7|
|3|Dan|5.0|
|4|Lina|NULL|


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

|id|name|grade|
|---|---|---|
|1|Otto|NULL|
|4|Lina|NULL|
### Etudiants sans cours

On cherche les étudiants qui existent dans le système, mais qui ne sont inscrit nulle part 

```sql
SELECT name 
FROM students s 
WHERE NOT EXISTS ( 
	SELECT 1 
	FROM enrollments e 
	WHERE s.id = e.student_id 
);
```

## Comparaison EXISTS et IN

`EXISTS` et `IN` font plus ou moins la même chose. Mais il existe quelque subtilités, surtout avec les `NULL`.
Le comportement de `IN` peut être innatendu, alors qu'avec le `EXISTS` non.

`courses`

|course_id|name|
|---|---|
|1|Mathématiques|
|2|Histoire|

`students`

|student_id|name|
|---|---|
|1|Alex Lin|
|2|Anna Song|
|3|Maria Chi|
|4|Dan Seth|
|5|Shadow Moon|

`enrollments`

| student_id | course_id |
| ---------- | --------- |
| 1          | 1         |
| 2          | 2         |
| 3          | NULL      |

On veut sélectionner les noms des cours ou quelqu'un est inscrit.

```sql
-- avec IN 
SELECT name 
FROM courses 
WHERE course_id IN ( 
	SELECT course_id 
	FROM enrollments 
);
```

Dans le cas où il y a un `NULL` dans `course_id`, `IN` peut retourner rien du tout. Car `NULL` rend la sous requête indéfinie, et SQL bug.

```sql
-- avec EXISTS
SELECT name 
FROM courses c 
WHERE EXISTS ( 
	SELECT 1 
	FROM enrollments e 
	WHERE c.course_id = e.course_id 
);
```

Avec `EXISTS`, on vérifie simplement qu'une ligne au moins corresponde à l'id. Donc pas de bug.

Conclusion: si la sous requête peut contenir des `NULL`, alors il vaut mieux utiliser `EXISTS` pour éviter les surprises.

---

## VIEW

Les `VIEW` sont des "requêtes sauvegardée" à laquelle on donne un nom et que l'on peut utiliser comme une table. Elle ne stocke pas les donnée, juste la structure de la requête.

Elles sont utilisé lorsqu'une requête devient trop longue, trop complexe, et que l'on se retrouve à répéter la même chose partout. Par exemple, pour un rapport sur les étudiants avec leurs moyennes et le nombre de cours, ou l'on copie la même sous requête cinq fois. Pour éviter cette duplication, on peut utiliser les vues.

Créer une vue, c'est créer une pseudo-table basées sur une requête `SELECT`.

La view devras être créer sur le serveur PG !

```sql
CREATE VIEW student_avg_grades AS 
SELECT 
	s.student_id, 
	s.name, 
	AVG(g.grade) AS avg_grade 
FROM students s 
	JOIN grades g ON s.student_id = g.student_id 
GROUP BY s.student_id, s.name;
```

On fois créer, on peut venir l'utiliser comme une table normal :

```sql 
SELECT * 
FROM student_avg_grades 
WHERE avg_grade > 4.5;
```

Un VIEW est réellement utilise :
- Faire des rapports : une `VIEW` correspond à une logique `avg_count`
- Gestion d'accès : on créer une `VIEW` qui retourne des données limitées selon les rôle 
- Réutilisation : au lieu de copier des sous-requête, on lui donne une forme de `VIEW`
### Utilisation des VIEW

On travail sur un rapport sur les étudiants, leurs cours et leurs notes. On souhaite faire un rapport récapitulatif pour chaque étudiant :
- nom,
- nombre d'inscriptions aux cours
- note moyenne

Si on fais tout d'un coup, on obtiens une requête SQL longue et dur à lire. On peut venir découper la requête, en commençant par créer deux vues, puis en les combinant.

`students`

|id|name|
|---|---|
|1|Alex Lin|
|2|Anna Song|
|3|Maria Chi|
|4|Dan Seth|

`enrollments`

|student_id|course_id|grade|
|---|---|---|
|1|1|90|
|1|2|85|
|2|2|88|
|2|3|91|
|3|1|75|
|3|3|NULL|

On créer la première vue qui permet de compte combien de cours chaque étudiant à :

```sql
CREATE VIEW student_course_count AS 
SELECT 
	student_id, 
	COUNT(*) AS course_count 
FROM enrollments 
GROUP BY student_id;
```

On créer une seconde vue avec la note moyenne

```sql
CREATE VIEW student_avg_grade AS 
SELECT 
	student_id, AVG(grade) AS avg_grade 
FROM enrollments 
WHERE grade IS NOT NULL 
GROUP BY student_id;
```

On peut ensuite venir travailler avec ces view pour simplifier la requête final 

```sql
SELECT 
	s.name, 
	c.course_count, -- on récupère la sum des cours depuis la view
	a.avg_grade -- on récupère la moyenne depuis la view
FROM students s 
-- on viens joindre les deux views
	LEFT JOIN student_course_count c ON s.student_id = c.student_id 
	LEFT JOIN student_avg_grade a ON s.student_id = a.student_id;
```

### Update des VIEWS

Les vues en général ne contiennent pas de données, c'est simplement un wrapper autour d'une requête. Il est quand même possible de les utiliser pour mettre à jour des données, si la `VIEW` respect certaine condition (pas de `JOIN`, `GROUP BY`, ou d'agrégat)

```sql
CREATE VIEW active_students AS 
SELECT * 
FROM students 
WHERE active = true;
```

On peut maintenant faire : 

```sql
UPDATE active_students 
SET name = 'Ivan Petrov' 
WHERE student_id = 2;
```

### Supprimer une VIEW

```sql
DROP VIEW student_avg_grade;
```

### Modifier une VIEW

```sql
CREATE OR REPLACE VIEW student_avg_grade AS 
SELECT 
	student_id, 
	ROUND(AVG(grade), 2) AS avg_grade 
FROM grades 
GROUP BY student_id;
```