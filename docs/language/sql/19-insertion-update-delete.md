# Insertion, mise à jour et supression des données

## INSERT INTO 

Cette commande permet d'ajouter des lignes dans une table.

```sql
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...);
```

- `table_name`: le nom dans la table où laquelle on ajoute une ligne 
- `column, ..`: la liste des colonnes dans laquelles ont lui ajoute des valeurs 
- `VALUE()`: la liste des valeurs que l'on ajoute et qui correspond au colonne dans le même ordre.

```sql
`INSERT INTO students (id, name, age, course) VALUES (2, 'Anna Lin', 19, 'Informatique');`
```

### Insertion de données dans toutes les colonnes 

Pour insérer des données pour toutes les colonnes, il suffit de skip les colonnes dans la commande. 
Il faut conserver l'ordre des colonne pour l'insertion des données.

```sql
`INSERT INTO students VALUES (3, 'Dan Sim', 20, 'Physique');`
```

---

## UPDATE

Cette commence permet de mettre à une une colonne dans une liste. 

```sql
UPDATE table
SET column1 = value1,
    column2 = value2
WHERE condition;
```

Attention à bien préciser la ligne qui doit être mise à jour. Si non précisé, alors toutes les lignes seront mise à jour.

```sql
UPDATE students
SET name = 'Maria Chi'
WHERE id = 1;
```

### Mettre à jour plusieurs colonnes 

```sql
UPDATE students
SET name = 'Otto Lin',
    email = 'otto.lin@example.com'
WHERE id = 2;
```

### Mettre à jour plusieurs lignes 

```sql
UPDATE students
SET group_number = 202
WHERE group_number = 101;
```

Cette requête vient mettre à jours toutes les lignes ou la colonne `group_number` est égale à 101.

### Mettre à jour à partir des données d'une autre table

```sql
UPDATE students
SET debt = payments.due_amount
FROM payments
WHERE students.id = payments.student_id;
```

- PG utilise le `FROM` comme source de données
- `WHERE` permet de faire le lien entre le lignes des deux tables
- Il ne met à jour que les lignes qui match avec la correspondance.

---

## DELETE 

 ```sql
DELETE FROM table
WHERE condition;
```

```sql
DELETE FROM students
WHERE id = 5;
```

Attention de bien préciser quel ligne doit être supprimé. Dans le cas contraire, cela supprime toutes les données de la table.

### TRUNCATE

C'est une alternative à `DELETE`. Elle est plus rapide, car elle ne log par la suppression de chaque ligne dans le journal des transactions et elle ne supporte pas la condition `WHERE`,

```sql
TRUNCATE TABLE students;
```

### Suppression complexe

```sql
-- supprime les étudiants qui ont plus de 30 ans et qui n'ont pas assisté au cours depuis 3 mois 
DELETE FROM students
WHERE age > 30 AND last_attendance_date < (CURRENT_DATE - INTERVAL '3 months');

-- suppression avec sous requêtes 
-- on selectionne les id et on supprime ensuite ces valeurs
DELETE FROM students
WHERE id IN (SELECT student_id FROM failed_students);
```

---

## RETURNING

Cette commande permet de récupérer directement les infos après une insertion, une update ou une suppression.

```sql
-- Syntaxe INSERT :
INSERT INTO table (column1, column2, ...)
VALUES (value1, value2, ...)
RETURNING colonne;

-- Syntaxe UPDATE :
UPDATE table
SET column1 = value1
WHERE condition
RETURNING colonne;

-- Syntaxe DELETE :
DELETE FROM table
WHERE condition
RETURNING colonne;
```

### RETURNING avec INSERT 

Il est possible de préciser quel colonne on souhaite récupérer :

```sql
INSERT INTO students (first_name, last_name, email)
VALUES ('Otto', 'Lin', 'otto.lin@example.com')
RETURNING student_id;
```

La requête retourneras directement l'id après l'insertion.

```sql
INSERT INTO students (first_name, last_name, email)
VALUES ('Alex', 'Ming', 'alex.ming@example.com')
RETURNING student_id, first_name, last_name;
```

On peut également demander plusieurs colonnes

### RETURNING avec UPDATE 

```sql
UPDATE students
SET email = 'lin.new@example.com'
WHERE student_id = 1
RETURNING student_id, email;
```

### RETURNING avec DELETE 

```sql
DELETE FROM students
WHERE student_id = 2
RETURNING student_id, first_name, last_name;
```