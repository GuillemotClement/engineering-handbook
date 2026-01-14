# Normalisation des données 

La normalisation est le processus qui consiste à organiser les données dans les tables de façon à minimiser la redondance et à éliminer les soucis liées à la mise à jour, la suppression et l'insertion.

---

## 1NF 

La première forme normale (First Normal Form - 1NF) est le premier niveau de normalisation d'une base de données. Elle impose des règles sur la structure des tables :
- **Toutes les données sont atomatiques** : Chaque valeur dans une cellule doit être indivisible. 
- **Chaque ligne est unique**: la table doit avoir une PK ou un id unique 
- **Pas de groupe de données répétitifs**: les valeurs d'une même entité ne doit pas être dans la même colonne

### Mettre en place une table 1NF

Par exemple, on as cette table qui ne respecte pas les règle : 

|student_id|name|courses|
|---|---|---|
|1|Maria|"Mathématiques, Physique"|
|2|Rob|"Biologie, Chimie"|
Dans la colonne `courses`, on peut voir que plusieurs données sont groupé. Pour que cette table respect la norme, on vas séparer les données en lignes distincts ou chaque valeur sera atomique.

|student_id|name|course|
|---|---|---|
|1|Maria|Mathématiques|
|1|Maria|Physique|
|2|Rob|Biologie|
|2|Rob|Chimie|

---

## 2NF

Une table est considérée comme étant en deuxième forme normal si :
- Elle est déjà 1NF
- Chaque colonne dépedant de toute la clé primaire, et pas seulement d'une partie de la clé

Si la clé primaire est composée de plusieurs champs, alors aucun des attributs non-clés (colonnes), ne doit dépendre uniquement d'une partie de cette clé. 

La 2NF élimine les dépendances partielles 

### Exemple de violation de la 2NF

On as une table `student_courses` qui stocke des infos sur les étudiants, leurs cours et les profs 

|student_id|course_id|course_name|instructor_name|
|---|---|---|---|
|1|101|Mathématiques|Lin|
|1|102|Littérature|Song|
|2|101|Mathématiques|Lin|
- `student_id` et `course_id` ensemble forme la clé primaire composite
- Les colonnes `course_name` et `instructor_name` dépendent seulement d'une partie de la clé composite `course_id`. 

On peut constater une dépendance partielle.

### Passer la table en 2FN

Pour éliminer la dépedance partielle, on vient diviser la table en deux. Cela permet de se débarasser de la redondance et améliore la cohérence des données.

On extrait les infos sur les cours dans une table `courses`

|course_id|course_name|instructor_name|
|---|---|---|
|101|Mathématiques|Lin|
|102|Littérature|Song|
La table principale devient 

|student_id|course_id|
|---|---|
|1|101|
|1|102|
|2|101|
Maintenant chaque colonnes dépend de toute la clé primaire. On a séparé les données pour que tout soit logiquement lié, et on as éliminé la violation de la 2NF.

---

## 3NF

Une table est 3NF si :
- Elle est déjà conforme au 2NF
- Tous les attributs non clé dépendant uniquement de la clé primaire et de rien d'autre.

Les données d'une table 3NF exige que toutes les données d'une table soient directement liées à sa clé primaire et ne dépendent pas d'autre attributs non-clés.

### Exemple de violation 3NF

On as une table `employees` qui stocke les infos des emploés

|employee_id|name|department|department_manager|
|---|---|---|---|
|1|Otto Lin|Marketing|Leo Zhang|
|2|Alex Song|Finance|Maria Chi|
|3|Anna Ming|Finance|Maria Chi|
- `employee_id` : clé primaire
- `department` et `department_manager` sont des attributs non clés 

On peut constater un soucis, `department_manager` dépend pas de `employee_id` mais de `department`. On as une dépendance transitive.

### Mettre la table en 3NF 

Pour virer les dépendances transitives, on va séparer la table en deux : une pour les employés, l'autre pour les départments

|employee_id|name|department|
|---|---|---|
|1|Otto Lin|Marketing|
|2|Alex Song|Finance|
|3|Anna Ming|Finance|

|department|department_manager|
|---|---|
|Marketing|Leo Zhang|
|Finance|Maria Chi|
Maintenant, tout est bien rangé, et chaque table remplit son rôle.

---

## DENORMALISTION 

La normalisation des données peut parfois créer des soucis côté performance. 

La dénormalisation c'est le process inverse de la normalisation. Si la normalisation découpe les tables en entités logiques séparées pour miniser la redondance, la dénormalisation regroupe les données pour améliorer les performance.

On peut être amener à l'utiliser quand les jointures multiples commence à ralentir le système.

- **Les requêtes fréquentes deviennent lentes**
- **Tâches analytiques et stats**
- **Requêtes complexes**
- **Nombre de jointures excessives

### Jointure excessive 

On as une requête permettant de récupérer les informations sur les produits, les clients, les commandes, et les articles de la commande.

```sql
SELECT
    c.customer_name,
    o.order_date,
    p.product_name,
    oi.quantity
FROM
    customers c
JOIN
    orders o ON c.customer_id = o.customer_id
JOIN
    order_items oi ON o.order_id = oi.order_id
JOIN
    products p ON oi.product_id = p.product_id
WHERE
    c.customer_id = 42;
```

Si la boutique gère des centaines de milliers de commandes par jour, la requête va devenir lente à cause des jointures.

On peut venir dénormaliser, en créant une nouvelle table pour les infos utilisées souvent : 

```sql
CREATE TABLE order_summary AS
SELECT
    c.customer_id,
    c.customer_name,
    o.order_id,
    o.order_date,
    p.product_id,
    p.product_name,
    oi.quantity
FROM
    customers c
JOIN
    orders o ON c.customer_id = o.customer_id
JOIN
    order_items oi ON o.order_id = oi.order_id
JOIN
    products p ON oi.product_id = p.product_id;
```

Pour récupérer les données, on as juste à faire une petite requête 

```sql
SELECT * FROM order_summary WHERE customer_id = 42;
```

### Système d'analytics 

on travail sur une base qui gère les billets pour des évènements. Il y a deux tables `events` et `sales`.

Si les analystes veulent un rapport sur le revenu moyen par billet pour tous les events, la structure normalisée oblige à faire une requête d'agrégation à chaque fois 

```sql
SELECT
    e.event_name,
    AVG(s.price) AS avg_ticket_price
FROM
    events e
JOIN
    sales s ON e.event_id = s.event_id
GROUP BY
    e.event_name;
```

Cette requête peut être lente, surtout si chaque vente représente des millions de lignes.

Pour résoudre le problème, on vas créer une table séparée avec les données agrégées 

```sql
CREATE TABLE event_summary AS
SELECT
    e.event_id,
    e.event_name,
    COUNT(s.sale_id) AS ticket_count,
    SUM(s.price) AS total_revenue,
    AVG(s.price) AS avg_ticket_price
FROM
    events e
JOIN
    sales s ON e.event_id = s.event_id
GROUP BY
    e.event_id, e.event_name;
```