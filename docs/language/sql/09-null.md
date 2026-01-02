## NULL

NULL représente une absence de valeur. Il signifie "valeur inconnue ou non définie".

|Concept|Valeur|
|---|---|
|`NULL`|Absence totale de valeur. Ce n'est pas juste une boîte vide, c'est l'absence de boîte|
|Chaîne vide|C'est une chaîne qui n'a tout simplement aucun caractère. Par exemple : `''`|
|Nombre `0`|Une valeur qui représente un nombre précis, égal à zéro|
NULL est égal à rien et n'est pas inégal à rien, même pas à lui même.

```sql
SELECT NULL = NULL;  -- Résultat : FAUX
```

```sql
SELECT NULL + 1;      -- Résultat : NULL
SELECT NULL * 100;    -- Résultat : NULL
SELECT NULL = 0;      -- Résultat : FAUX
SELECT NULL <> 0;     -- Résultat : FAUX
```

NULL dans une addition, une multiplication ou n'importe quel calcul retourne toujours NULL.

### IS NULL

Cette opérateur permet de retourner les valeurs `NULL`. Elle permet de vérifier si une colonne ou expression contient la valeur `NULL`

```sql
SELECT * 
FROM students 
WHERE birth_date IS NULL;
```

Les valeur `''` ne sont pas incluse.
### IS NOT NULL

Permet de vérifier si une valeur n'est pas `NULL`

```sql
SELECT *
FROM orders
WHERE comment IS NOT NULL;
```

Cette requête retourne seulement les ligne ou la colone contient une valeur autre que `NULL`

---

## Influence de NULL sur les fonctions d'agrégation 

- `SUM()`& `AVG()` : ignore les `NULL`
- `MIN()` & `MAX()`: ignore les `NULL`
- `COUNT(*)` : compte toute les lignes même celle contenant des valeurs `NULL`. En revanche, elle ne compte que les colonne n'ayant pas de valeur `NULL`

--- 

## COALESCE()

Cette fonction permet de gérer de manière élégante les valeurs `NULL`dans les données.

Par exemple, elle permet de remplacer les valeurs `NULL` par 0 pour effectuer des opérations.

La fonction retourne la première valeur non-NULL de la liste d'arguments fournis. Si tout est `NULL`, alors la fonction retourne `NULL`. "Donne moi la première valeur potable que tu trouves".

```sql
COALESCE(value1, value2, ..., value_n)
```

### Remplacer NULL par 0

|id|name|salary|
|---|---|---|
|1|Otto|50000|
|2|Maria|`NULL`|
|3|Alex|60000|
|4|Anna|`NULL`|
On souhaite calculer la somme total des salaire si on donne à chaque employé une prime de 1000.

```sql
SELECT SUM(COALESCE(salary, 0)) AS total_salary
FROM salaries;
```

Avec `COALESCE` on se débarasse des valeurs `NULL` et on les remplace par 0.

Ici, on fait la somme sur le salaire. Si la valeur est NULL, on la remplace par 0.

### Remplacer NULL par une valeur par défaut 

| id  | name  | address |
| --- | ----- | ------- |
| 1   | Anna  | Kanne   |
| 2   | Peter | `NULL`  |
| 3   | Lisa  | Painful |
| 4   | Alex  | `NULL`  |
On remplace `NULL` par une valeur par défaut 

```sql
SELECT name, COALESCE(address, 'Non renseigné') AS resolved_address
FROM students;
```

### Utiliser plusieurs valeurs 

Parfois, il est nécessaire de remplacer `NULL` par une série de valeur. Par exemple, on veut choisir le prénom, le surnom ou utiliser "sans nom" si la valeur est NULL.

| user_id | first_name | short_name | full_name     |
| ------- | ---------- | ---------- | ------------- |
| 1       | John       | Jonny      | Johnny Walker |
| 2       | `NULL`     | Pete       | Peter Kamen   |
| 3       | `NULL`     | `NULL`     |               |
```sql
SELECT user_id,
       COALESCE(first_name, short_name, 'Sans nom') AS display_name
FROM users;
```

### Piège

Tous les arguments passé à la fonction `COALESCE()` doivent être compatible niveau type de donnée. Il n'est pas possible de mélanger des chaînes de caractères et des nombres.

---

## NULLIF()

Cette fonction permet de comparer deux valeurs et de retourner `NULL` si elles sont identique ou de renvoyer la première valeur si elles sont différentes.

C'est comme se dire "Si ces deux valeurs sont pareilles, on les oublie".

```sql
NULLIF(value1, value2)
```

Si les deux arguments sont égaux, le résultat sera `NULL`. Si différents, le résultat sera `value1`

```sql
SELECT NULLIF(10, 10); -- Résultat : NULL
SELECT NULLIF(10, 20); -- Résultat : 10
SELECT NULLIF('hello', 'hello'); -- Résultat : NULL
SELECT NULLIF('hello', 'world'); -- Résultat : 'hello'
```

### Eviter la division par zéro 

| revenue | expenses |
| ------- | -------- |
| 1000    | 200      |
| 1500    | 0        |
| 2000    | 250      |
```sql
SELECT revenue / NULLIF(expenses, 0) AS profitability
FROM sales;
```

| profitability |
| ------------- |
| 5.00          |
| NULL          |
| 8.00          |
Maintenant, si le résultat est 0, alors la valeur retourner est `NULL` et la requête ne plante pas car elle retourne `NULL`.

### Remplacer les valeurs identiques par NULL

| first_name | preferred_name |
| ---------- | -------------- |
| John       | Johnny         |
| Anna       | Anna           |
| Alex       | Lex            |
Parfois les étudiants préfère leur vrai prénom, parfois il choississent un autre. Si les deux prénoms sont identiques, cela ne sert à rien d'afficher le surnom

```sql
SELECT first_name,
       NULLIF(preferred_name, first_name) AS display_name
FROM students;
```

Lorsque les deux prénom sont identiques, le résultat est nul.

### Filtrage de données

| order_id | original_price | discounted_price |
| -------- | -------------- | ---------------- |
| 1        | 100            | 100              |
| 2        | 200            | 180              |
| 3        | 150            | 150              |
On souhaite afficher uniquement les valeurs lorsque les colonnes sont différentes.

```sql
SELECT order_id, original_price, discounted_price
FROM orders
WHERE NULLIF(original_price, discounted_price) IS NOT NULL;
```

|order_id|original_price|discounted_price|
|---|---|---|
|2|200|180|
Seule les commandes avec une remise apparaissent dans le résultat.

### Choisir une valeur optionnelle

| employee_id | base_salary | bonus |
| ----------- | ----------- | ----- |
| 1           | 50000       | 10000 |
| 2           | 40000       | 0     |
| 3           | 60000       | 5000  |
Si le bonus vaut 0, on le remplace par 999

```sql
SELECT employee_id,
       base_salary + COALESCE(NULLIF(bonus, 0), 999) AS total_salary
FROM employees;
```

|employee_id|total_salary|
|---|---|
|1|60000|
|2|40999|
|3|65000|
### Compter les lignes

On peut utiliser `NULLIF()` pour simplifier des conditions de filtrage.

Par exemple, une table user et on souhaite savoir combien d'user au total et combien sont enregisté.

```sql
SELECT
  COUNT(*) AS total_users,
  COUNT(NULLIF(status, 'guest')) AS registered_users
FROM users;
```