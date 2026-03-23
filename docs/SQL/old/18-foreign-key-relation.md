# Foreign Key

Une clé étrangère est une colonne ou un groupe de colonne qui sert à créer un lien entre les tables. Elle pointe vers une colonne (souvent `PRIMARY KEY`) dans une autre table, ce qui garantit l'intégrité référentielle des données entre elles.

Lorsque l'on créer un FK, PG n'ajoute pas automatiquement un index sur cette colonne, il faudras l'ajouter si on l'utilise souvent.

## SYNTAXE

```sql
CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    customer_id INT REFERENCES customers(customer_id)   -- clé étrangère
);
```

- `customer_id`: c'est la clé étrangère
- Elle fait référence à `customer_id` dans la table `customers`

On peut également utiliser cette syntaxe pour définir de manière explicite la foreign key 

```sql
CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    customer_id INT,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);
```

Lorsque l'on ajoute une FK, PG créer automatiquement une règle qui vérifie que les valeurs de la FK corresponde à des valeurs existante dans la table indiquée. Si on tente d'insérer une valeur incorrecte, la base de donnée va lancer une erreur.

---
## ONE-TO-ONE 

C'est la relation la plu simple, où un enregistrement dans une table correspond à un enregistrement dans une autre table. Par exemple, la table `users` peut contenir des infos sur les utilisateurs, et la table `profiles` peut contenir des infos supplémentaires, comme leurs adresse ou leurs photos.

```sql
CREATE TABLE users (
    user_id SERIAL PRIMARY KEY,
    username TEXT NOT NULL
);

CREATE TABLE profiles (
    profile_id SERIAL PRIMARY KEY,
    user_id INT UNIQUE REFERENCES users(user_id), -- référence à users.user_id
    address TEXT
);
```

Ici, la colonne `user_id` dans la table `profiles` joue le rôle de clé étrangères qui pointe vers `user_id` dans la table `users`.

---
## ONE-TO-MANY

C'est la relation la plus courante. Par exemple, un employée peut avoir une entreprise, mais une entreprise peut avoir plusieurs employées.

La table `MANY` viens recevoir la clé étrangère afin de pouvoir implémenter cette relation.

### Créer une relation ONE-TO-MANY

On viens implémenter la relation entre clients et commande. Un client peut passer plusieurs commandes, mais une commande est lié à un client.

La table `customers`. Elle vient stocker les informations du client 

```sql
CREATE TABLE customers (
    customer_id SERIAL PRIMARY KEY, -- Identifiant unique du client
    name TEXT NOT NULL              -- Nom du client
);
```

La table `orders` viens récupérer la FK. Elle ne pourras être lié qu'a à un seul client.

```sql
CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,           -- Identifiant unique de la commande
    order_date DATE NOT NULL,              -- Date de la commande
    customer_id INT REFERENCES customers(customer_id) -- Clé étrangère
);
```

### Insertion de données

On ajoute quelque clients 

```sql
INSERT INTO customers (name)
VALUES
    ('Ada Lovelace'),
    ('Grace Hopper'),
    ('Linus Torvalds');
```

On ajoute ensuite des commandes

```sql
INSERT INTO orders (order_date, customer_id)
VALUES
    ('2023-10-01', 1),  -- Commande d'Ada
    ('2023-10-02', 2),  -- Commande de Grace
    ('2023-10-03', 1);  -- Encore une commande d'Ada
```

Lorsque l'on ajoute de nouvelle commandes, il faut absolument mettre un `customer_id` existant. Dans le cas contraire, on obtient une erreur.

### Récupération des données

On souhaite savoir quelles commandes un utilisateur à passer 

```sql
SELECT orders.order_id, orders.order_date, customers.name
FROM orders
JOIN customers ON orders.customer_id = customers.customer_id
WHERE customers.name = 'Ada Lovelace';
```

On utilise `JOIN` pour combiner les deux tables via la FK.

### Suppression de donnée 

Si on viens supprimer une donnée de la table `ONE`, les données lié dans la table `MANY` vont devenir orpheline. Pour éviter cela, on utilise le modification `ON DELETE CASCADE`, lorsque l'on supprime une donnée de la table `ONE`, les données de la table `MANY` seront également supprimées.

```sql
CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    order_date DATE NOT NULL,
    customer_id INT REFERENCES customers(customer_id) ON DELETE CASCADE
);
```

---
## MANY-TO-MANY 

Parfois, un enregistrement dans une table peut être lié à plusieurs enregistrements dans une autre table, et inversement.

Par exemple, les étudiants s'inscrivent à des cours, et un cours peut avoir plein d'étudiants. Pour cela, on vient créer une table intermédiaire.

Dans ce cas, on viens ajouter une **table intermédiaire** qui permet de stocker ce lien.

### Table pivot 

La table de pivot, ou table intermédiraire ou table de liaison permet de gérer ce type de realtion. Elle contient les deux FK des deux tables lié par cette relation.

Chaque ligne de la table permet de lier les deux tables.

```sql
CREATE TABLE students (
    student_id SERIAL PRIMARY KEY,
    name TEXT NOT NULL
);

CREATE TABLE courses (
    course_id SERIAL PRIMARY KEY,
    title TEXT NOT NULL
);

CREATE TABLE enrollments (
    enrollment_id SERIAL PRIMARY KEY,
    student_id INT REFERENCES students(student_id), -- foreign key
    course_id INT REFERENCES courses(course_id)		-- foreign key
);
```

### Insertion de données

```sql
-- On ajoute des étudiants
INSERT INTO students (name) VALUES ('Ivan Ivanov'), ('Maria Smirnova');

-- On ajoute des cours
INSERT INTO courses (title) VALUES ('Mathematiques'), ('Physique');

-- On inscrit les étudiants aux cours
INSERT INTO enrollments (student_id, course_id) VALUES (1, 1), (1, 2), (2, 1);
```

### Sélection des données 

On peut ensuite venir faire des requête pour récupérer des données à l'aide des clé étrangère

```sql
-- Les cours auxquels Ivan Ivanov est inscrit
SELECT c.title
FROM enrollments e
JOIN courses c ON e.course_id = c.course_id
WHERE e.student_id = 1;

-- Les étudiants inscrits au cours "Mathematiques"
SELECT s.name
FROM enrollments e
JOIN students s ON e.student_id = s.student_id
WHERE e.course_id = 1;

-- trouver les étudiants et leurs cours
SELECT s.name AS student, c.title AS course
FROM students s
JOIN enrollments e ON s.student_id = e.student_id
JOIN courses c ON e.course_id = c.course_id;
```

### Clé primaire composé 

L'utilisation de clé primaire composé dans la table pivot permet de s'assure que les deux FK ensemble ne sont présente qu'une seule fois dans la table pivot 

```sql
CREATE TABLE enrollments (
    student_id INT REFERENCES students(student_id),
    course_id INT REFERENCES courses(course_id),
    PRIMARY KEY (student_id, course_id)
);
```

---

## ON DELETE 

Permet de définir le comportement de la relation entre les deux tables lorsque l'on tente de supprimer une donnée de la table enfant.

Il existe plusieurs option 

- `ON DELETE CASCADE`: supprimer une ligne de la table parent supprime également toutes les lignes liées dans la table enfant
- `ON DELETE SET NULL`: lors de la suppression de la ligne de la table parent, la valeur de la FK passe à nul.
- `ON DELETE SET DEFAULT`: passe la valeur de la FK à la valeur par défaut 
- `ON DELETE RESTRICT`: comportement par défaut. La suppression est impossible 
- `ON DELETE NO ACTION`: comme `RESTRICT`, mais la vérification est repoussé à la fin de la transaction

### Suppression en cascade 

On souhaite que la suppression d'un étudiant supprime automatiquement tous ses cours dans la table `course`

```sql
CREATE TABLE students (
    student_id SERIAL PRIMARY KEY,
    name TEXT NOT NULL
);

CREATE TABLE courses (
    course_id SERIAL PRIMARY KEY,
    title TEXT NOT NULL,
    student_id INT REFERENCES students(student_id) ON DELETE CASCADE
);
```

Avec ces tables, lorsque l'on supprime un étudiant, toutes les lignes liée à cet étudiant seront supprimée.

```sql
INSERT INTO students (name) VALUES ('Ivan Ivanov');
INSERT INTO courses (title, student_id) VALUES ('Mathematiques', 1), ('Physique', 1);

-- Suppression de l'étudiant Ivanov
DELETE FROM students WHERE student_id = 1;

-- La table courses sera maintenant vide, car tous les cours liés à Ivanov sont supprimés
```

### ON DELETE NULL

```sql
-- Table des commandes avec une nouvelle règle
CREATE TABLE orders_with_null (
    order_id SERIAL PRIMARY KEY,
    customer_id INT REFERENCES customers(customer_id) ON DELETE SET NULL,
    order_date DATE NOT NULL
);

-- Insertion de données
INSERT INTO orders_with_null (customer_id, order_date) VALUES (1, '2023-10-01');

-- Suppression du client
DELETE FROM customers WHERE customer_id = 1;

-- On vérifie la table orders_with_null
SELECT * FROM orders_with_null;
```

Les commandes sont conservé, mais elles ne sont plus liée au client supprimé.

---
## ON UPDATE

Comment pour la suppression, on peut venir définir le comportement lors d'un update de la ligne parent.

- `ON UPDATE CASCADE`: la valeur de la FK est modifié automatiquement pour toutes les lignes liée à la ligne parent
- `ON UPDATE SET NULL`: la FK lié passé la valeur à `NULL`
- `ON UPDATE SET DEFAULT`: valeur par défaut est utilisé
- `ON UPDATE RESTRICT`: l'update est impossible
- `ON UPDATE NO ACTION`: la vérification est faite à la fin de la transaction

### ON UPDATE CASCADE 

```sql
CREATE TABLE customers_with_cascade (
    customer_id SERIAL PRIMARY KEY,
    name TEXT NOT NULL
);

CREATE TABLE orders_with_cascade (
    order_id SERIAL PRIMARY KEY,
    customer_id INT REFERENCES customers_with_cascade(customer_id) ON UPDATE CASCADE,
    order_date DATE NOT NULL
);

-- Insertion de données
INSERT INTO customers_with_cascade (name) VALUES ('Ivan Ivanov');
INSERT INTO orders_with_cascade (customer_id, order_date) VALUES (1, '2023-10-01');

-- Modification du customer_id
UPDATE customers_with_cascade SET customer_id = 100 WHERE customer_id = 1;

-- On vérifie la table orders_with_cascade
SELECT * FROM orders_with_cascade;
```

Lorsque l'on met à jour `customer_id`, PG met à jour automatiquement les valeurs liée





Permet d'empêcher la modification d'une valeur dans la table parent si une ligne de la table enfant pointe dessus. Cela permet de bloquer des modifications pouvant casser l'intégrité des données.

```sql
CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    customer_id INT REFERENCES customers(customer_id) ON UPDATE RESTRICT,
    order_date DATE NOT NULL
);
```

On tente de modifier l'id du client 

```sql
-- Tentative de changer l'ID du client de 2 à 5
UPDATE customers
SET customer_id = 5
WHERE customer_id = 2;
```

On obtient alors une erreur 

```text
ERROR:  update or delete on table "customers" violates foreign key constraint
DETAIL:  Key (customer_id)=(2) is still referenced from table "orders".
```

#### Combiner les modificateurs

Il est possible de combiner les deux modificateurs. Par exemple, on configure la suppression automatique des données quand on supprime un ligne de la table parent, mais on interdit de changer son id depuis la table enfant.

```sql
CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    customer_id INT REFERENCES customers(customer_id)
        ON DELETE CASCADE
        ON UPDATE RESTRICT,
    order_date DATE NOT NULL
);
```
