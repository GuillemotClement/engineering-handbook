## LENGTH() 

Retourne la longueur de la chaîne 
```sql
-- syntaxe
LENGTH(string)

SELECT name, LENGTH(name) AS name_length
FROM students;
```

## SUBSTRING()

Permet d'extraire une partie d'une chaîne 

```sql
-- syntaxe
SUBSTRING(string FROM start FOR length)

SELECT last_name, SUBSTRING(last_name FROM 1 FOR 3) AS prefix
FROM students;
```

- `start`: position du premier caractère (commence à 1)
- `length`: combien de caractères on veux extraire

### Extraire le domaine de l'email

On peut également utilise un caractère pour définir le départ de l'extraction de chaîne

```sql
SELECT email, SUBSTRING(email FROM POSITION('@' IN email) + 1) AS domain
FROM students;
```

## TRIM()

Permet d'enlever les espaces au début et en fin de chaîne 

```sql
TRIM([LEADING | TRAILING | BOTH] chars FROM string)

-- on l'utilise généralement de cette manière
TRIM(string)

SELECT '[' || TRIM('   Art   ') || ']' AS cleaned;
```

Dans la requête, on ajoute des crochets en début et fin.

## POSITION() 

Retourne la position où commence la sous-chaîne 

```sql
POSITION(substring IN string)

SELECT email, POSITION('@' IN email) AS at_position
FROM students;
```

Dans la requête, on obtient la position du @. On peut ensuite la cumuler avec `SUBSTRING()` pour extraire une partie de la chaîne

## REPLACE() 

Remplace toutes les occurrences d'une sous chaîne par une autre

```sql
REPLACE(string, from_substring, to_substring)

-- remplace les espaces par des underscore
SELECT name, REPLACE(name, ' ', '_') AS fixed_name
FROM students;
```

## INITCAP()

Transforme la chaîne pour que la première lettre de chaque mot soit en majuscule, les autres en minuscules

```sql
INITCAP(string)

SELECT INITCAP('anna pal') AS full_name;
```

---

## DISTINCT

Cette instruction permet de récupérer uniquement des valeurs unique sans doublon.

Toutes les lignes en double ne seront pas retournée. De cette manière, cela permet de récupérer :
- les produits unique dans les commandes
- les prénom de client unique 
- les combinaison unique de donnée (ville + pays)

```sql
SELECT DISTINCT colonne1, colonne2, ...
FROM table;
```

En ajoutant l'instruction, cela garantit que chaque ligne du résultat sera unique.

### Utilisation

#### Valeur unique d'une colonne 

On a une table `student` avec des infos sur les étudiants.

|id|first_name|last_name|city|
|---|---|---|---|
|1|Maria|Chi|Seattle|
|2|Alex|Lin|Toronto|
|3|Anna|Song|Seattle|
|4|Nat|Cole|Chicago|
|5|Maria|Chi|Seattle|
On souhaite savoir de quelle villes viennent les étudiants.

```sql
SELECT DISTINCT city
FROM students;
```

Avec le `DISTINCT`, on obtient des valeurs unique de ville 

#### Valeurs unique de plusieurs colonnes 

| id  | first_name | last_name | city    |
| --- | ---------- | --------- | ------- |
| 1   | Maria      | Chi       | Seattle |
| 2   | Alex       | Lin       | Toronto |
| 3   | Anna       | Song      | Seattle |
| 4   | Nat        | Cole      | Chicago |
| 5   | Maria      | Chi       | Seattle |

On souhaite obtenir les combinaison unique nom + prénom

```sql
SELECT DISTINCT first_name, last_name
FROM students;
```

| first_name | last_name |
| ---------- | --------- |
| Maria      | Chi       |
| Alex       | Lin       |
| Anna       | Song      |
| Nat        | Cole      |
`DISTINCT` agit comme un filtre, il regarde les colonnes indiquées et retire les doublons seulement pour les lignes où toutes ces colonnes sont identifique.

#### Combinaison unique et tri 

On peut venir combiner `DISTINCT` et `ORDER BY` pour obtenir les valeurs unique, triée par nom de famille 

```sql
SELECT DISTINCT first_name, last_name
FROM students
ORDER BY last_name ASC;
```

#### Utilisation avec agrégation

```sql
SELECT COUNT(DISTINCT city) AS unique_city_count
FROM students;
```

Cette requête donne le nombre de ville unique

### Particularité

Lorsque l'on utilise `DISTINCT`, il faut bien comprendre que l'on travaille avec toutes les colonnes indiquées.
Si on ajoute de nouvelles colonnes, le résultat peut changer.

---
## ORDER BY

Pour trier sur plusieurs colonne, on utilise l'instruction `ORDER BY`. PG vient trier sur la première, puis la second etc

```sql
SELECT colonnes
FROM table
ORDER BY colonne1 direction1,  colonne2 direction2, ... ;
```

- `colonne1`: ce sont les colonnes sur laquelle on souhaite trier
- `direction`: sens du trie ASC ou DESC

### Utilisation 

#### Triage sur nom et prénom 

```sql
SELECT id, first_name, last_name, age
FROM students
ORDER BY last_name ASC, first_name ASC;
```

Avec cette requête on trie le résultat sur le nom, puis sur le prénom 

### Gestion des NULL

`NULL` indique une absence de valeur. Lorsque l'on trie des colonne avec qui contiennent des NULL, PG doit décider où placer les valeurs vide : début ou fin.

Pour contrôler le placement, il est possible de définir le placement 

```sql
ORDER BY colonne ASC NULLS FIRST
ORDER BY colonne DESC NULLS LAST

SELECT student_id, grade
FROM grades
ORDER BY grade DESC NULLS LAST;
```

Avec cette requête, les NULL seront placé à la fin du trie.

## Erreur classique lors du trie et du formatage

### Type de donnée différents

Lorsqu'une colonne contient plusieurs type de données (string, int) PG viens classer les nombre puis les string.

Pour eviter ce genre d'incohérence, on viendras convertir la donnée

```sql
SELECT * FROM mixed_data ORDER BY value::INT;
```

Dans le cas ou des données ne peuvent être convertis, cette requête provoque une erreur.

### Spécifier plusieurs colonnes pour le tri 

PG vient trier les colonne dans l'ordre dans laquelles elles sont indiqué dans la requête.

### Erreur avec DISTINCT 

Par exemple, on veut obtenir la liste des prénom unique des employées. Si deux employée ont le même nom prénom, cela peux skip certaine valeur.

Dans ce cas, on vient ajouter la clé primaire pour garantir l'unicité

```sql
SELECT DISTINCT ON (id) first_name, last_name
FROM employees;
```