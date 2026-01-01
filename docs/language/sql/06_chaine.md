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