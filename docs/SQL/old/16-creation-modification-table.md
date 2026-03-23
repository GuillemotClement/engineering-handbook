# Création et modification des tables 

Les tables c'est ce qui permet de stocker les données, et de mettre en place les relations.

## CREATE TABLE

La commande `CREATE TABLE` permet de créer une nouvelle table. On viens décrire les colonnes de cette table avec le nom et le type.

```sql
CREATE TABLE table (
    colonne1 type_de_donnée contraintes,
    colonne2 type_de_donnée contraintes,
    ...
);
```

- `table` : le nom de la table 
- `colonne1`: nom de la colonne 
- `type_de_donnee`: le type de la donnée 
- `contraintes`: les contraintes sur la donnée

```sql
CREATE TABLE students (
    id SERIAL PRIMARY KEY,  -- ID unique de l’étudiant autoincrémenter
    name VARCHAR(100),      -- nom
    age INTEGER             -- âge
);
```

### Type de données pour les colonnes 

Lorsque l'on créer une table, il est très important de choisir des types adaptés pour chaque colonnes. 

|Type de donnée|Description|Exemples de valeurs|
|---|---|---|
|`INTEGER`|Nombres entiers. Parfait pour l’âge, les quantités, les identifiants.|`1`, `42`, `100`|
|`VARCHAR(n)`|Chaîne de caractères jusqu’à `n` caractères. Idéal pour du texte court.|`'Alice'`, `'Bob'`|
|`TEXT`|Chaînes illimitées. Pour des textes longs (genre description d’un produit).|`'Très long texte...'`|
|`DATE`|Date sans heure. Pour les dates de naissance, d’événements, etc.|`2023-10-12`|
|`BOOLEAN`|Booléen : `TRUE` ou `FALSE`. Pour les flags (genre fait/pas fait).|`TRUE`, `FALSE`|

---

## Contrainte 

Les contraintes permette d'ajouter des règles pour chaque colonnes de la table. Elle permettent d'éviter que la table contiennent des données non adaptées qui pollue la base de donnée.

### NOT NULL 

Cette contrainte permet de contraindre une colonne à toujours avoir une valeur. Une erreur est lancer dans le cas ou l'on tente d'insérer une nouvelle ligne sans valeur dans la colonne.

```sql
CREATE TABLE teachers (
    id SERIAL PRIMARY KEY,          -- Identifiant unique
    name VARCHAR(100) NOT NULL,     -- Nom de l’enseignant (obligatoire)
    subject VARCHAR(50) NOT NULL    -- Matière qu’il enseigne (obligatoire)
);
```

### UNIQUE 

Permet de contrainte à donnée à être unique dans la table. Il sera impossible d'insérer plusieurs fois la même valeur dans la colonne de la table. 

```sql
CREATE TABLE departments (
    id SERIAL PRIMARY KEY,      -- Identifiant unique
    name VARCHAR(100) UNIQUE    -- Le nom du département doit être unique
);
```

#### UNIQUE combinée 

Permet d'ajouter la contrainte sur plusieurs colonnes. Par exemple, un étudiant qui ne peut pas s'inscrire deux fois au même cours.
Chaque paire `student_id` et `course_id` devras être unique dans la table.

```sql
CREATE TABLE enrollments (
    id SERIAL PRIMARY KEY,          -- Identifiant unique pour l’enregistrement
    student_id INTEGER NOT NULL,    -- Identifiant de l’étudiant
    course_id INTEGER NOT NULL,     -- Identifiant du cours
    UNIQUE (student_id, course_id)  -- Un étudiant ne peut pas s’inscrire deux fois au même cours
);
```

### Combiner UNIQUE et NOT NULL

Il est possible d'ajouter les deux contraintes sur une colonne. 

```sql
CREATE TABLE users (
    username VARCHAR(50) NOT NULL UNIQUE, -- Login unique, obligatoire
    email VARCHAR(100) NOT NULL UNIQUE -- Adresse email unique, obligatoire
);
```

---

## DEFAULT 

Permet de définir à l'avance la valeur qui sera insérer dans la colonne. La valeur par défaut s'applique dans le cas ou l'on ne précise pas de valeur lors de l'insertion pour cette colonne.

```sql
CREATE TABLE table (
    colonne type_de_donnée DEFAULT valeur_par_défaut
);
```

```sql
ALTER TABLE table
ALTER COLUMN colonne
SET DEFAULT valeur_par_défaut;
```

### Date par défaut 

```sql
CREATE TABLE students (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    registration_date DATE DEFAULT CURRENT_DATE
);
```

### Numeric par défaut 

```sql
CREATE TABLE grades (
    id SERIAL PRIMARY KEY,
    student_id INTEGER NOT NULL,
    course_id INTEGER NOT NULL,
    grade INTEGER DEFAULT 0
);
```

### TEXT par défaut 

```sql
CREATE TABLE teachers (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    status TEXT DEFAULT 'non vérifié'
);
```

### HEURE par défaut 

```sql
CREATE TABLE meetings (
    id SERIAL PRIMARY KEY,
    topic VARCHAR(100),
    meeting_time TIME DEFAULT '10:00'
);
```

---

## CHECK 

Cette contrainte garantit que les données que l'on ajoute dans la table respectent certaines conditions. Si on tente d'insérer des données qui ne respectent pas ces conditions, la base de données refuse l'insertion.

Cette contrainte permet : 
- **Assurer la qualité des données**: les insertion qui ne respecte pas la contrainte sont refuser
-  **Réduire les risques d'erreurs**: la base de données se charge de vérifier toutes les données insérées
- **Logique autonome**: les règles de validation sont directement intégrées dans la structure de la base, sans avoir besoin de gérer du côté applicatif

On utilise généralement pour :
- **Boutique en ligne** : interdit l'ajout de produits avec un prix négatif 
- **Plateforme éducative** : vérifier l'âge des participants 
- **Système médicaux** : s'assurer que la températures corperelle soit dans les limites 

### Fonctionnement du CHECK 

La contrainte est définit à la création ou il est possible de l'ajouter plus tard avec `ALTER TABLE`

```sql
CREATE TABLE table (
    colonne type_de_donnée CHECK (condition)
);
```

- `condition` : c'est la contrainte logique qui doit être vraie pour chaque valeur de la colonne concernée. 

#### Vérifier une plage de valeur

Par exemple, une table `students` ou l'âge d'un étudiants doit être compris entre 16 et 100.

```sql
CREATE TABLE students (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    age INTEGER CHECK (age >= 16 AND age <= 100)
);
```

#### Vérifier le format des données

On veut s'assurer que les adresses mail soient conforme 

```sql
CREATE TABLE emails (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) CHECK (email LIKE '%@%')
);
```

#### Vérifier des conditions sur plusieurs colonnes 

Il est possible de vérifier plusieurs colonnes avec cette contraintes. Par exemple une table `employees` ou le salaire doit être supérieur au bonus

```sql
CREATE TABLE employees (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    salary NUMERIC CHECK (salary > 0),
    bonus NUMERIC CHECK (bonus >= 0),
    CHECK (salary > bonus)
);
```

Si les deux contrainte ne sont pas respecté, l'insertion échoueras.

---

## ALTER TABLE 

Cette commande permet de modifier la structure des tables existantes. Il est possible de :
- Ajouter une nouvelle colonnes
- Supprimer une colonne
- Changer le type d'une colonne 
- Renommer une table ou ses colonnes 
- Ajouter ou supprimer des contraintes 

```sql
ALTER TABLE table
    action_modification;
```

### Ajouter une nouvelle colonne 

```sql
ALTER TABLE students
ADD COLUMN email VARCHAR(100) NOT NULL;
```

### Supprimer une colonne 

```sql
ALTER TABLE students
DROP COLUMN email;
```

Les données de la colonnes seront également supprimé.

#### Supprimer une colonne avec dépendances

Pour supprimer une colonne avec une dépendances (celle utilisée dans des vues ou des triggers), il faudra utiliser le modificateurs `CASCADE`. Attention, cela peut impacter d'autres table.

```sql
ALTER TABLE students
DROP COLUMN email CASCADE;
```
### Changer le type de donnée d'une colonne 

```sql
ALTER TABLE table_name
ALTER COLUMN column_name TYPE new_data_type;
```

```sql
ALTER TABLE students
ALTER COLUMN age TYPE BIGINT;
```

Si des données sont déjà enregistrées dans la colonne, PG vérifie si elles peuvent être converties dans le nouveau type.

#### Modifier le type avec conversion 

Pour changer de type avec conversion, on utilise une conversion. Cela indique à PG de convertir la donnée avant de changer le type.

Il est nécessaire de spécifier la conversion de type car si la conversion automatique échoue, cela provoque une erreur.

```sql
ALTER TABLE employees
ALTER COLUMN birth_date TYPE DATE USING birth_date::DATE;
```

On peut également utiliser cette syntaxe

```sql
ALTER TABLE employees
ALTER COLUMN birth_date TYPE DATE USING to_date(birth_date, 'YYYY-MM-DD');
```

### USING 

Lorsque l'on change le type de colonne, il faut parfois indiquer comment convertir les données existante. Pour cela, on utilise le mot clé `USING`

```sql
ALTER TABLE table_name
ALTER COLUMN column_name TYPE new_data_type
USING expression;
```

`USING` permet de préciser la formule de conversion des valeurs de l’ancien type vers le nouveau. C'est utile lorsque la conversion automatique est impossible ou ambiguë

L'utilisation d'`USING`  est obligatoire :
- Quand il n'y a pas de conversion direct de type 
- Quand on doit transformer les données 
- Quand les types sont incompatibles 

#### Chaîne -> nombre 

```sql
ALTER TABLE users
ALTER COLUMN age TYPE INTEGER
USING age::INTEGER;
```

Ici, `age` était de type `TEXT` et on souhaite le convertir en `INTEGER`. On déclare une conversion explicite.

#### TEXTE -> DATE

```sql
ALTER TABLE events
ALTER COLUMN event_date TYPE DATE
USING TO_DATE(event_date, 'YYYY-MM-DD');
```

Ici, on indique quel format pour la date.

### Renommer une table 

```sql
ALTER TABLE students
RENAME TO university_students;
```

### Ajouter/retirer une contrainte NOT NULL

```sql
ALTER TABLE students
ALTER COLUMN name DROP NOT NULL;
```

### Ajouter une valeur par défaut 

```sql
ALTER TABLE enrollments
ALTER COLUMN enrollment_date DROP DEFAULT;
```

---

## DROP TABLE 

Cette commande permet de supprimer une table d'une base de données. Les données et la structure seront supprimée. Cela supprime également les index, contraintes et triggers liés.

```sql
DROP TABLE table;
```

```sql
DROP TABLE students;
```

Avec cette commande, la table `students` sera complètement supprimée.

### Supprimer plusieurs tables 

```sql
DROP TABLE table1, table2, table3;
```

### IF EXISTS

La commande `DROP TABLE` retourne une erreur lorsque l'on tente de supprimer une table qui n'existe pas. Pour éviter cela, on utilise l'option 

```sql
DROP TABLE IF EXISTS table_name;
```

### CASCADE

Lorsqu'une table possède des dépendances avec des clé étrangères liée à d'autre table, la suppression n'est pas possible.

Pour forcer la suppression, on peut utiliser l'option qui supprimer la table et tous les objets dépendants.

```sql
DROP TABLE students CASCADE;
```

La requête va supprimer la table `students` et automatiquement tous les objets qui en dépendent.

Attention avec cette option, cela peut venir supprimer plus de données que prévues.

### RESTRICT

Pour éviter de supprimer par erreurs des objets liées, cette option est adapté. Elle interdit la suppression de la table si d'autres table en dépendent.

```sql
DROP TABLE students RESTRICT;
```

### Suppression de tables temporaires

Les table temporaire créer avec `CREATE TEMP TABLE` sont supprimées automatiquement à la fin de session. Il est possible de les supprimer manuellement 

```sql
CREATE TEMP TABLE temp_data (
    id SERIAL PRIMARY KEY,
    value TEXT
);

DROP TABLE temp_data;
```

### Backup avant suppression 

Avant de supprimer une table, il est judicieux de faire un backup avec la commande `pg_dump`. Cela permet de restaurer les données si jamais on supprime quelque chose par erreur.

```bash
pg_dump -U username -d database_name -t table_name > table_backup.sql
```