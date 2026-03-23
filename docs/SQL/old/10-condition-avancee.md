## CASE WHEN

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

---

## CASE 

Il est possible d'utilise un `switch` en utilisant juste le `CASE`. Il permet de comparer une expression à plusieurs valeurs.

```sql
CASE expression
    WHEN value1 THEN result1
    WHEN value2 THEN result2
    ...
    ELSE default_result
END
```

- `expression`: c'est l'expression ou le champ qui est évalué 
- `THEN` : indique ce qui doit être retourné si la valeur match 
- `ELSE` : ce qui est retourné par défaut. Il est possible de ne pas l'utiliser.

### Note des étudiants

```sql
SELECT
    name,
    grade,
    CASE grade
        WHEN 5 THEN 'Excellent'
        WHEN 4 THEN 'Bien'
        WHEN 3 THEN 'Satisfaisant'
        WHEN 2 THEN 'Insuffisant'
        ELSE 'Inconnu'
    END AS note_en_mots
FROM students;
```

### Catégorie de produits par code 

```sql
SELECT
    product_name,
    category_code,
    CASE category_code
        WHEN 'el' THEN 'Électronique'
        WHEN 'frn' THEN 'Meubles'
        WHEN 'bks' THEN 'Livres'
        ELSE 'Autre'
    END AS category_name
FROM products;
```

### Nom des mois 

```sql
SELECT
    EXTRACT(MONTH FROM order_date) AS month_number,
    CASE EXTRACT(MONTH FROM order_date)
        WHEN 1 THEN 'Janvier'
        WHEN 2 THEN 'Février'
        WHEN 3 THEN 'Mars'
        WHEN 4 THEN 'Avril'
        WHEN 5 THEN 'Mai'
        WHEN 6 THEN 'Juin'
        WHEN 7 THEN 'Juillet'
        WHEN 8 THEN 'Août'
        WHEN 9 THEN 'Septembre'
        WHEN 10 THEN 'Octobre'
        WHEN 11 THEN 'Novembre'
        WHEN 12 THEN 'Décembre'
        ELSE 'Inconnu'
    END AS month_name
FROM orders;
```

### Traitement des NULL

```sql
SELECT
    user_id,
    status,
    CASE status
        WHEN 'active' THEN 'Actif'
        WHEN 'blocked' THEN 'Bloqué'
        WHEN status IS NULL THEN 'Pas de statut'  -- ça marche pas !
        ELSE 'Inconnu'
    END AS etat_utilisateur
FROM users;
```

### Regroupement par catégorie pour un rapport 

|employee_id|name|department|
|---|---|---|
|1|Alex Lin|HR|
|2|Maria Chi|IT|
|3|Anna Song|IT|
|4|Otto Art|FIN|
|5|Jane Doe|HR|
|6|Max Gray|SALES|
|7|Zoe Black|IT|
|8|Tom Brown|FIN|
|9|Liam Park|NULL|
|10|Eva Gold|HR|
```sql
SELECT
    CASE department
        WHEN 'HR' THEN 'Ressources humaines'
        WHEN 'IT' THEN 'Technologies'
        WHEN 'FIN' THEN 'Finances'
        ELSE 'Autre'
    END AS dept_name,
    COUNT(*) AS staff_count
FROM employees
GROUP BY
    CASE department
        WHEN 'HR' THEN 'Ressources humaines'
        WHEN 'IT' THEN 'Technologies'
        WHEN 'FIN' THEN 'Finances'
        ELSE 'Autre'
    END;
```

|dept_name|staff_count|
|---|---|
|Ressources humaines|3|
|Technologies|3|
|Finances|2|
|Autre|2|

---

## GREATEST() & LEAST()

Ces fonctions permettent de récupérer la valeur maximal et minimal d'une liste de données

```sql
GREATEST(value1, value2, ..., valueN)
LEAST(value1, value2, ..., valueN)
```

|student_id|exam_1|exam_2|exam_3|
|---|---|---|---|
|1|85|90|82|
|2|NULL|76|89|
|3|94|NULL|88|

```sql
SELECT
    student_id,
    GREATEST(exam_1, exam_2, exam_3) AS highest_score,
    LEAST(exam_1, exam_2, exam_3) AS lowest_score
FROM students_scores;
```

La requête récupère la valeur max et min dans les colonnes passer en argument.

|student_id|highest_score|lowest_score|
|---|---|---|
|1|90|82|
|2|89|NULL|
|3|94|NULL|

### Traitement des NULL

Avec ces deux fonctions, les valeurs `NULL` sont ignoré lorsqu'elles cherchent la valeur max ou min parmi leurs arguments.

Si toutes les valeurs sont `NULL`, alors elles retournent `NULL`.

#### Remplacer NULL par 0
|student_id|exam_1|exam_2|exam_3|
|---|---|---|---|
|1|90|85|82|
|2|NULL|89|NULL|
|3|NULL|NULL|94|

```sql
SELECT
    student_id,
    GREATEST(
        COALESCE(exam_1, 0),
        COALESCE(exam_2, 0),
        COALESCE(exam_3, 0)
    ) AS highest_score,
    LEAST(
        COALESCE(exam_1, 0),
        COALESCE(exam_2, 0),
        COALESCE(exam_3, 0)
    ) AS lowest_score
FROM students_scores;
```

#### Remplacer NULL par la valeur d'une colonne 

|student_id|exam_1|exam_2|exam_3|
|---|---|---|---|
|1|90|85|82|
|2|NULL|89|NULL|
|3|70|NULL|NULL|

```sql
SELECT
    student_id,
    GREATEST(
        exam_1,
        exam_2,
        COALESCE(exam_3, exam_1)
    ) AS highest_score
FROM students_scores;
```

---

## Calcul et filtrage avec des NULL

### NULLS LAST

|product_id|name|price|
|---|---|---|
|1|Téléphone|45000|
|2|Ordinateur portable|NULL|
|3|Caméra|25000|
|4|Montre connectée|NULL|
On viens trier les produits par prix, et ceux sans prix sont placés à la fin 

```sql
SELECT product_id, name, price
FROM products
ORDER BY price ASC NULLS LAST;
```

### Calcul du total avec les valeurs NULL

```sql
SELECT SUM(COALESCE(order_amount, 0)) AS total_amount
FROM orders;
```

### Afficher du texte à la place de NULL

```sql
SELECT
    customer_name,
    COALESCE(order_amount::TEXT, 'Non renseigné') AS order_status
FROM orders;
```

### NULLS FIRST

Triage des `NULL` en début

```sql
SELECT customer_name, order_amount
FROM orders
ORDER BY order_amount DESC NULLS FIRST;
```

### Trier en remplaçant les NULL

```sql
SELECT
    student_id,
    name,
    COALESCE(birth_date::TEXT, 'Inconnu') AS birth_date_info
FROM students;
```