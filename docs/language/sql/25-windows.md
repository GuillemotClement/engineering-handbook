# Windows Fonctions 

Les fonctions window sont des outils puissant qui permet de bosser sur les données ligne par ligne, tout en gardant le contexte complet.

Elles permettent de faire des calculs (somme, moyenne, range, etc) sur une "fenêtre" de lignes, sans faire de collapse sur les données. Cela veut dire que contrairement au fonctions d'agrégations, on obtiens à la fois le résutat et les détails dans chaque ligne.

```sql
SELECT
    student_id,
    grade,
    RANK() OVER (ORDER BY grade DESC) AS rank
FROM
    students;
```

Avec cette requête, on obtient une table où chaque étudiant a son rang unique selon sa note.

---

## ROW_NUMBER()

Retourne un numéro unique pour chaque ligne dans la window. Cela permet de créer une numérotation de ligne selon l'ordre définir dans le `ORDER BY`

```sql
ROW_NUMBER() OVER ([PARTITION BY colonne] ORDER BY colonne)
```

- `PARTITION BY colonne`: optionnel; divise les données en groupes. Si omit, la numérotation sera globale sur tout le set
- `ORDER BY colonne`: définit l'ordre des lignes pour la numérotation 

### Utilisation

Par exemple, on souhaite ajouter une numérotation sur une table qui contient des informations sur les étudiants et leurs notes 

|id|name|score|
|---|---|---|
|1|Eva Lang|95|
|2|Maria Chi|87|
|3|Alex Lin|78|
|4|Anna Song|95|
|5|Otto Mart|87|

On viens numéroter les lignes par ordre décroissant de leur notes 

```sql
SELECT
    name,
    score,
    ROW_NUMBER() OVER (ORDER BY score DESC) AS row_num
FROM students;
```

On obtient ce résultat 

|name|score|row_num|
|---|---|---|
|Eva Lang|95|1|
|Anna Song|95|2|
|Maria Chi|87|3|
|Otto Mart|87|4|
|Alex Lin|78|5|

Chaque ligne à reçu un numéro d'ordre unique - en tenant compte du tri décroissant des notes.

---

## RANK() 

Ressemble à `ROW_NUMBER()` mais prends en compte les valeurs identiques. Si des lignes ont la même valeurs dans le tri, elles auront le même rang, et le suivant sera sauté.

```sql
RANK() OVER ([PARTITION BY colonne] ORDER BY colonne)
```

### Utilisation 

On souhaite faire un classement des étudiants par leurs notes :

```sql
SELECT
    name,
    score,
    RANK() OVER (ORDER BY score DESC) AS rank
FROM students;
```

|name|score|rank|
|---|---|---|
|Eva Lang|95|1|
|Anna Song|95|1|
|Maria Chi|87|3|
|Otto Mart|87|3|
|Alex Lin|78|5|
Ici, les lignes avec les même valeurs `95` et `87` ont le même range, et les rangs suivant sont sautés.

---

## DENSE_RANK()

Ressemble à `RANK()` mais ne saute pas les valeurs de range. Cela signifie que s'il y a des lignes identiques, le range suivant sera +1 par rapport au rang précédent

```sql
DENSE_RANK() OVER ([PARTITION BY colonne] ORDER BY colonne)
```

###  Utilisation 

```sql
SELECT
    name,
    score,
    DENSE_RANK() OVER (ORDER BY score DESC) AS dense_rank
FROM students;
```

|name|score|dense_rank|
|---|---|---|
|Eva Lang|95|1|
|Anna Song|95|1|
|Maria Chi|87|2|
|Otto Mart|87|2|
|Alex Lin|78|3|
Les valeurs de rang augmentent sans trous.

---

## NTILE()

Cette fonction divise les lignes en groupes égaux et donne à chaque ligne un numéro de groupe 

```sql
NTILE(n) OVER ([PARTITION BY colonne] ORDER BY colonne)
```

- `n` : le nombre de groupes dans lesquels on souhaite diviser les données.

### Utilisation 

On souhaite diviser les étudiants en 3 groupes par ordre décroissant de note 

```sql
SELECT
    name,
    score,
    NTILE(3) OVER (ORDER BY score DESC) AS group_num
FROM students;
```

|name|score|group_num|
|---|---|---|
|Eva Lang|95|1|
|Anna Song|95|1|
|Maria Chi|87|2|
|Otto Mart|87|2|
|Alex Lin|78|3|

Si on ne peut pas diviser les lignes en groupes parfaitement égaux, les lignes en trop vont dans les premiers groupes. Ici, les deux premiers groupes ont deux lignes, le dernier en a une.

---

## OVER() 

Instruction qui indique à SQL sur quel ensemble de lignes il faut appliquer une window function. On peut dire que c'est une façon de définir la fenêtre de données pour appliquer la window function.

Permet de définir sur quel ensemble de lignes la fonction va travailler.

L'opérateur s'utilise uniquement avec les window fonction pour faire des opérations sur les lignes d'une ou plusieurs tables, sans faire de `GROUP BY`

```sql
window_function() OVER (
    [PARTITION BY ...]
    [ORDER BY ...]
    [ROWS/RANGE ...]
)
```

- `PARTITION BY` : divise l'ensemble de données en groupes logiques
- `ORDER BY`: définit l'ordre des lignes à l'intérieur de chaque groupe 
- `ROW/RANGE` : précise la taille de la fenêtre (exemple la ligne courante + 1)

### OVER sans paramètre 

Lorsque cette instruction est utilisé sans paramètre supplémentaire, cela signifie que la fonction juste avant va bosser sur tout l'ensemble de données.

```sql
SELECT
    employee_id,
    salary,
    ROW_NUMBER() OVER () AS row_num -- ROW_NUMBER() sera appliqué à toutes les lignes du résultat
FROM employees;
```

- `ROW_NUMBER()`: attribue un numéro à chaque ligne 
- Comme il n'y a pas de paramètre dans `OVER()`, toute la lignes de la table sont traitées comme un seul bloc 

|employee_id|salary|row_num|
|---|---|---|
|1|50000|1|
|2|60000|2|
|3|55000|3|

### Utilisation avec PARTITION BY 

On souhaite numéroté les employés à l'intérieur de chaque département. `PARTIION BY` dans `OVER()` divise les données en groupes. Pour chaque groupe, la fonction calcule la valeur séparément.

```sql
SELECT
    department_id,
    employee_id,
    salary,
    ROW_NUMBER() OVER (PARTITION BY department_id) AS row_num
FROM employees;
```

| department_id | employee_id | salary | row_num |
| ------------- | ----------- | ------ | ------- |
| 1             | 1           | 50000  | 1       |
| 1             | 3           | 55000  | 2       |
| 2             | 2           | 60000  | 1       |

### Utilisation avec ORDER BY 
On souhaite numéroter les lignes dans un ordre précis. `ORDER BY` définit l'ordre dans lequel les lignes vont être traitées par la window function 

```sql
SELECT
    department_id,
    employee_id,
    salary,
    RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rank
FROM employees;
```


### Combinaison de window function 

```sql
SELECT
    department_id,
    employee_id,
    salary,
    ROW_NUMBER() OVER (PARTITION BY department_id ORDER BY salary DESC) AS row_num,
    AVG(salary) OVER (PARTITION BY department_id) AS avg_salary
FROM employees;
```

---

## PARTITION BY 
https://codegym.cc/fr/quests/lectures/fr.codegym.sql.core.lecture.level15.lecture04

---

https://codegym.cc/fr/quests/lectures/fr.codegym.sql.core.lecture.level15.lecture05

https://codegym.cc/fr/quests/lectures/fr.codegym.sql.core.lecture.level15.lecture06

https://codegym.cc/fr/quests/lectures/fr.codegym.sql.core.lecture.level15.lecture07

https://codegym.cc/fr/quests/lectures/fr.codegym.sql.core.lecture.level15.lecture08

https://codegym.cc/fr/quests/lectures/fr.codegym.sql.core.lecture.level15.lecture09

https://codegym.cc/fr/quests/lectures/fr.codegym.sql.core.lecture.level15.lecture10