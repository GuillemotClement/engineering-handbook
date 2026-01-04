## CASE 

Cet opérateur permet de créer des conditions.

```sql
CASE
    WHEN condition1 THEN résultat1
    WHEN condition2 THEN résultat2
    ...
    ELSE résultat_par_défaut
END
```

- `WHEN`: correspond au `if`
- `THEN`: c'est ce qu'il faut faire dans le cas ou la condition est `true`
- `ELSE`: si la condition est pas évaluer à `true`, alors on effectue 

### Classification de produit 

|id|name|price|
|---|---|---|
|1|Magic Wand|120|
|2|Potion Set|45|
|3|Crystal Ball|75|
|4|Enchanted Map|NULL|
|5|Broomstick|99|
|6|Spell Book|180|

```sql
SELECT
    name AS product_name,
    price,
    CASE
        WHEN price IS NULL THEN 'Inconnu'
        WHEN price < 50 THEN 'Économique'
        WHEN price BETWEEN 50 AND 100 THEN 'Standard'
        ELSE 'Premium'
    END AS price_category
FROM products;
```

1. Si le prix est `NULL` alors on affiche la chaîne 
2. etc

|product_name|price|price_category|
|---|---|---|
|Magic Wand|120|Premium|
|Potion Set|45|Économique|
|Crystal Ball|75|Standard|
|Enchanted Map|NULL|Inconnu|
|Broomstick|99|Standard|
|Spell Book|180|Premium|
### Gérer le NULL dans le CASE WHEN

Il peut y avoir des données `NULL`, et il est nécessaire de les remplacer par autre chose.

Par exemple, des emails manquant, que l'on viens remplacer par une chaîne.

|user_id|name|email|
|---|---|---|
|1|Alex Lin|alex@example.com|
|2|Maria Chi|NULL|
|3|Anna Song|anna@magic.com|
|4|Otto Art|NULL|
|5|John Smith|john@wizard.org|

```sql
SELECT
    user_id,
    name,
    CASE
        WHEN email IS NULL THEN 'Non renseigné'
        ELSE email
    END AS email_address
FROM users;
```

| user_id | name       | email_address    |
| ------- | ---------- | ---------------- |
| 1       | Alex Lin   | alex@example.com |
| 2       | Maria Chi  | Non renseigné    |
| 3       | Anna Song  | anna@magic.com   |
| 4       | Otto Art   | Non renseigné    |
| 5       | John Smith | john@wizard.org  |

### Expression conditionnelle avec des nombres

Parfois, il ne s'agit pas juste de remplacer des valeus, mais construire une nouvelle logique.

Par exemple, selon la valeur, on la remplace par une chaine

|name|score|
|---|---|
|Alex Lin|95|
|Maria Chi|82|
|Anna Song|48|
|Otto Art|NULL|
|John Smith|67|
|Zoe Black|30|

```sql
SELECT
    name AS student_name,
    score,
    CASE
        WHEN score IS NULL THEN 'Pas de note'
        WHEN score >= 90 THEN 'Excellent'
        WHEN score >= 50 THEN 'Passable'
        ELSE 'Échec'
    END AS performance_category
FROM students;
```

| student_name | score | performance_category |
| ------------ | ----- | -------------------- |
| Alex Lin     | 95    | Excellent            |
| Maria Chi    | 82    | Passable             |
| Anna Song    | 48    | Échec                |
| Otto Art     | NULL  | Pas de note          |
| John Smith   | 67    | Passable             |
| Zoe Black    | 30    | Échec                |

### Groupement et gestion des NULL

On veux compter le total de commande par statut, y compris celle avec un statut `NULL`

|order_id|status|
|---|---|
|1|Completed|
|2|Pending|
|3|NULL|
|4|Shipped|
|5|Completed|
|6|NULL|
|7|Pending|
|8|Completed|
|9|Shipped|
|10|NULL|
```sql
SELECT
    CASE
        WHEN status IS NULL THEN 'Aucun statut'
        ELSE status
    END AS order_status,
    COUNT(*)
FROM orders
GROUP BY
    CASE
        WHEN status IS NULL THEN 'Aucun statut'
        ELSE status
    END;
```

Cette requête permet de gérer proprement les status `NULL` et retourne une chaîne à la place, puis compte le nombre total de commande dans chaque groupe.

|order_status|count|
|---|---|
|Completed|3|
|Pending|2|
|Shipped|2|
|Aucun statut|3|
### Cas pratique 

#### Tri en tenant compte des NULL

Parfois, il faut afficher les `NULL` soit en premier, soit en dernier dans une liste triée.

| task_id | task_name            | priority |
| ------- | -------------------- | -------- |
| 1       | Fix bugs             | 1        |
| 2       | Update documentation | 3        |
| 3       | Plan sprint          | NULL     |
| 4       | Code review          | 2        |
| 5       | Organize meeting     | NULL     |
| 6       | Deploy release       | 1        |

```sql
SELECT
    task_name,
    priority,
    CASE
        WHEN priority IS NULL THEN 1
        ELSE 0
    END AS priority_sort
FROM tasks
ORDER BY priority_sort ASC, priority ASC;
```

On ajoute une colonne virtuelle `priority_sort` qui met les `NULL` tout en bas, le reste est trié par ordre croissant.

|task_name|priority|priority_sort|
|---|---|---|
|Deploy release|1|0|
|Fix bugs|1|0|
|Code review|2|0|
|Update documentation|3|0|
|Plan sprint|NULL|1|
|Organize meeting|NULL|1|
#### Calcul avec NULL

On veux calculer le montant final d'une commande, ou la colonne discount peut être `NULL` s'il n'y a pas de remise.

|order_id|total_price|discount|
|---|---|---|
|101|100|10|
|102|200|NULL|
|103|150|15|
|104|120|NULL|
|105|80|5|
On doit remplacer `NULL` par 0 pour que le calcul soit correct 

```sql
SELECT
    order_id,
    total_price,
    discount,
    total_price -
    CASE
        WHEN discount IS NULL THEN 0
        ELSE discount
    END AS final_price
FROM orders;
```

Le requête permet de garantir que `NULL` ne viendras pas casser les calculs.

| order_id | total_price | discount | final_price |
| -------- | ----------- | -------- | ----------- |
| 101      | 100         | 10       | 90          |
| 102      | 200         | NULL     | 200         |
| 103      | 150         | 15       | 135         |
| 104      | 120         | NULL     | 120         |
| 105      | 80          | 5        | 75          |
#### Afficher des status utilisateurs

|user_id|name|last_login|
|---|---|---|
|1|Alex Lin|2024-12-10|
|2|Maria Chi|2025-04-20|
|3|Anna Song|NULL|
|4|Otto Art|2025-05-01|
|5|Liam Park|2025-05-25|
```sql
SELECT
    user_id,
    name,
    CASE
        WHEN last_login IS NULL THEN 'Jamais connecté'
        WHEN last_login < CURRENT_DATE - INTERVAL '30 days' THEN 'Inactif'
        ELSE 'Actif'
    END AS user_status
FROM users;
```

|user_id|name|user_status|
|---|---|---|
|1|Alex Lin|Inactif|
|2|Maria Chi|Inactif|
|3|Anna Song|Jamais connecté|
|4|Otto Art|Inactif|
|5|Liam Park|Actif|
