# Chargement de données en masse

On récupère un fichier CSV avec des infos depuis une autre base de données ou une application externe. On peut venir insérer ces nouvelles données via la chargement de données.

On l'utilise : 
- **Migration de données**: on doit transférer des données d'un système à un autre. 
- **Initialisation de la base de données**: on remplit la base de données de départ
- **Intégration avec des système externes** : ajout régulier de nouvelles données, mise à jour de donnée de tiers via un fichier 
- **Mise à jour de gros volumes de données** : on doit remplacer des données obsolètes par des données à jour

---
## Chargement depuis des fichiers CSV

On utilise un fichier au format `CSV`. Dans ce type de format, chaque ligne est un enregistrement et les valeurs sont séparés par des virgules ou des points virgules.

Ce format est facile à lire. On peut ouvrir le `CSV` dans Excel, le traiter en python ou autre langage, et l'importer dans la base de données.

```sql
id,name,age,course
1,Otto Lin,20,Programmation
2,Maria Chi,21,Design
3,Alex Ming,19,Mathématiques
```

## Préparation au chargement 

Pour gérer correctement le chargement massif, il faut préparer correctement la base pour recevoir les données, vérifier que la structure est correct et que les données sont clean.

### Vérification de la structure de la table 

On comment par s'assurer que la base contient une table qui correspond à la structure du fichier. Par exemple, il faut une table avec les bonne colonnes.

### Vérification des données 

Avant le chargement, il faut venir checker les données.

1. Pas de ligne vide ou de valeur incorrect
2. Correspondance des séparateurs
3. Encodage: PG attends des fichier en UTF-8.

---

## COPY 

Cette commande permet de déplacer des données entre une table et le système de fichiers. Elle et adapté pour charger des données en masse dans la base ou pour les exporter.

Elle s'exécute directement sur le serveur.

```sql
COPY table_name FROM 'path/to/your/file.csv' DELIMITER ',' CSV HEADER;
```

- `FROM 'path/to/your/file.csv`: indique le chemin vers le fichier CSV
- `DELIMITER ','`: définis le caractère séparateur 
- `CSV HEADER`: indique que le fichier a un en tête (la première ligne avec les noms des colonnes)

### COPY et \COPY

Il existe deux variantes de la commande :
- `COPY`: s'exécute côté serveur. Elle permet de charger des fichiers qui sont sur le serveur
- `\COPY`: commande côté client dispo sur PG. Le chargement se fait depuis l'ordi local.

### Utilisation de la commande

#### Préparation de la table 

On viens créer une table pour stocker les infos à charger 

```sql
CREATE TABLE students (
    student_id SERIAL PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    date_of_birth DATE,
    grade FLOAT
);
```

#### Préparer le fichier CSV 

Le format du fichier doit matcher avec la table. L'ordre des colonnes du CSV doit correspondre à celui de la table, et les types de données doivent matcher.

```text
first_name,last_name,date_of_birth,grade
John,Doe,2001-05-15,85.5
Jane,Smith,2000-12-22,90.0
Alice,Johnson,2002-03-10,78.0
Bob,Brown,2001-08-30,NULL
```

#### Chargement des données 

On utilise ensuite la commande pour charger le fichier dans la table 

```sql
COPY students (first_name, last_name, date_of_birth, grade)
FROM '/path/to/your/students.csv'
DELIMITER ','
CSV HEADER;
```

- On indique le nombre de la table et les colonne ou l'on charge les données.
- On indique le path vers le fichier 
- On définit le séparateur
- Et on indique que la première ligne contient les noms des colonnes.

### Vérifier le chargement 

On peut venir compter le nombre total de ligne :
```sql
SELECT COUNT(*) FROM students;
```

On peut venir chercher les valeurs nul
```sql
SELECT * FROM students WHERE grade IS NULL;
```

### Astuce

#### Log des erreurs 
On peut utiliser le paramètre `LOG ERRORS`

#### Désactiver les index et triggers 
Lorsque l'on charges beaucoup de données, on peut temporairement désactiver les triggers 
```sql
ALTER TABLE students DISABLE TRIGGER ALL;
```

#### Utiliser les transactions 
Cela permet de charger de manière atomique les données.
```sql
BEGIN;
COPY students FROM '/path/to/your/file.csv' CSV HEADER;
COMMIT;
```

#### Cas pratique 
On reçoit les données d'une API externe. On peut venir sauvegarder ces données en format CSV, puis on les charge dans PG. 

---

## PREPARATION DES TABLES 

On commence par définir comment les données seront stockées dans la base. Cela signifie qu'il faut d'abord créer une table avec la bonne structure.

On souhaite importer un fichier CSV `students.csv`.

```csv
id,name,age,email,major
1,Alex,20,alex@example.com,Computer Science
2,Maria,21,maria@example.com,Mathematics
3,Otto,19,otto@example.com,Physics
```

On partir de ce fichier, on peut définir la table que l'on créer dans la base de données.

```sql
CREATE TABLE students (
    id SERIAL PRIMARY KEY,       -- Identifiant unique de l'étudiant
    name VARCHAR(100) NOT NULL,  -- Prénom de l'étudiant (chaîne jusqu'à 100 caractères)
    age INT CHECK (age > 0),     -- Âge de l'étudiant (doit être supérieur à 0)
    email VARCHAR(100) UNIQUE,   -- Email unique
    major VARCHAR(100)           -- Spécialité principale
);
```

### Vérification des données avant l'import 

On vérifie la correspondance des colonnes et le type de données.

On peut faire cette vérification depuis Excel, ou via la programmation avec Python et la lib `pandas` par exemple 

```python
import pandas as pd

# On lit le CSV
df = pd.read_csv('students.csv')

# On vérifie les données
print(df.dtypes)  # Affiche les types de données pour chaque colonne
print(df.isnull().sum())  # Vérifie les valeurs manquantes
```

### Nettoyage des données avant import 

#### Lignes ou colonnes vides 
Si une valeur manque dans une colonne obligatoire, cela va planter. On viens donc remplacer ces valeurs absente par des valeurs `NULL`

#### Espace en trop 
Les espaces au début ou à la fin des chaînes peuvent poser problèmes. On peut utiliser Python pour enlever les espaces en trop 

```python
df = df.apply(lambda x: x.str.strip() if x.dtype == "object" else x)
```

#### Caractères incorrects ou encodage 
Si le fichiers contient des caractères spéciaux incompatible avec la base, l'import peut échouer
L'outtil `iconv` permet de convertir l'encodage.
```bash
iconv -f WINDOWS-1251 -t UTF-8 students.csv > students_utf8.csv
```

#### Nettoyage avec Python 
```python
import pandas as pd

# On lit le fichier
df = pd.read_csv('students.csv')

# On nettoie les données
df['name'] = df['name'].str.strip()  # On enlève les espaces
df['email'] = df['email'].str.lower()  # On met les emails en minuscules
df['age'] = df['age'].fillna(0)  # On remplit les âges manquants avec 0
df['age'] = df['age'].astype(int)  # On convertit l'âge en entier

# On sauvegarde dans un nouveau fichier
df.to_csv('cleaned_students.csv', index=False)
```

---

## GESTION DES ERREURS DE CHARGEMENT

Pendant les imports massifs, on peut tomber sur plusieurs problème classiques : 
- **Doublons de données** : si la table à une contrainte `UNIQUE` et que le fichier de données possède des répétitions
- **Conflits avec les contraintes**: On essaie d'importer une valeur vide dans une colonne avec la contrainte `NOT NULL`
- **Info primaires dupliquée**: la table peut déjà contenir des données avec les même identifiants que le fichier CSV

### ON CONFLICT 

La commande `ON CONFLICT` permet de définir le comportement en cas de conflit avec une contrainte `UNIQUE` ou `PRIMARY KEY`.

```sql
INSERT INTO table_name (column1, column2, ...)
VALUES (value1, value2, ...)
ON CONFLICT (conflict_target)
DO UPDATE SET column1 = new_value1, column2 = new_value2;
```

On remplace `DO UPDATE` par `DO NOTHING` si on veux ignorer le conflit

### Mise à jour en cas de conflit 

On veux importer de nouvelles données, mais certaines existent déjà dans la base 

```sql
INSERT INTO students (id, name, age)
VALUES
    (1, 'Peter', 22),  -- Cet étudiant existe déjà
    (2, 'Anna', 20),  -- Nouvel étudiant
    (3, 'Mal', 25) -- Nouvel étudiant
ON CONFLICT (id) DO UPDATE SET
    name = EXCLUDED.name,
    age = EXCLUDED.age;
```

Dans cette table, si un id d'étudiant existe déjà, ses données seront mis à jour.

`EXCLUDED` indique "les valeurs que tu as essayé d'insérer mais qui ont été exclues à cause du conflit"

- L'étudiant avec `id = 1` verra ses info mise à jour 
- Les étudiants avec `id = 2` et `id = 3` seront ajoutées à la table

### Ignorer les conflits 

Si on ne souhaite pas mettre à jours les données, mais juste ignorer les lignes qui posent problèmes, on utilise `DO NOTHING`
```sql
INSERT INTO students (id, name, age)
VALUES
    (1, 'Peter', 22),  -- Cet étudiant existe déjà
    (2, 'Anna', 20),  -- Nouvel étudiant
    (3, 'Mal', 25) -- Nouvel étudiant
ON CONFLICT (id) DO NOTHING;
```

Les lignes en conflits ne seront pas insérées, et les autres s'installeront dans la base.

### Logge des erreurs 

Parfois, ignorer ou update les données ne suffit pas. Par exemple, on veux enregistrer les conflits pour les analyser plus tard. On peut créer une table spéciale pour logguer les erreurs 
```sql
CREATE TABLE conflict_log (
    conflict_time TIMESTAMP DEFAULT NOW(),
    id INT,
    name TEXT,
    age INT,
    conflict_reason TEXT
);
```

On ajoute ensuite une gestion d'erreurs avec le logging 

```sql
INSERT INTO students (id, name, age)
VALUES
    (1, 'Peter', 22),
    (2, 'Anna', 20),
    (3, 'Mal', 25)
ON CONFLICT (id) DO UPDATE SET
    name = EXCLUDED.name,
    age = EXCLUDED.age
RETURNING EXCLUDED.id, EXCLUDED.name, EXCLUDED.age
INTO conflict_log;
```

Cet exemple ne marque que dans les procédure stockées avec PL-SQL.

---

## CHARGEMENT DES DONNEES DEPUIS LE SERVEUR

Lorsque les données sont déjà présente sur le serveur, on peut utiliser la commande `COPY` pour les charger dans des tables PG. 

La commande est rapide car les fichiers sont déjà sur le serveur, donc pas de transferr via le réseau à réaliser. C'est également plus sécurisé car pas besoin de les copier depuis le pc local, et c'est simple d'intégrer le chargement dans des scripts ou des services en arrière plan.

```sql
COPY table_name
FROM '/path/to/file.csv'
WITH (FORMAT CSV, HEADER TRUE);
```


### Configuration des droits d'accès 

PG doit avoir accès au fichier pour utiliser la commande. Pour vérifier :
1. Le fichier doit être lisible par l'utilisateur PG
2. Vérifier les droits sur le fichier ou le dossier se trouve 
```bash
ls -l /var/lib/postgresql/data/students.csv
```

Pour changer les droits 
```bash
chmod 644 /var/lib/postgresql/data/students.csv
chown postgres:postgres /var/lib/postgresql/data/students.csv
```

### Limitation de COPY 

Le chemin du fichier doit être en absolu.

Si l'encodage n'est pas le même, il faut le préciser dans la commande 

```sql
COPY students
FROM '/var/lib/postgresql/data/students.csv'
WITH (FORMAT CSV, ENCODING 'WIN1251', HEADER TRUE);
```

---


## \COPY 

Cette commande permet de charger des donnée dans PG directement depuis l'ordi. La commande fonctionne avec les fichiers directement accessible côté client.

Cette commande est un peu plus limité :
- elle n'est pas adapté pour des fichiers énormes, les données doit passer via le réseau.

### Syntaxe 

```sql
\COPY table FROM 'chemin_du_fichier' [WITH] (options)
```

- `table`: le nom de la table où l'on souhaite charger les données
- `chemin_du_fichier`: le path vers le fichier présent sur le pc 
- `options`: paramètres optionnels pour customiser le comportement d'une commande 

```sql
\COPY students FROM 'C:/data/students.csv' DELIMITER ',' CSV HEADER;
```

### FORMAT

Définit le format des données. Les plus utilisé sont `text` et `CSV`

```sql
\COPY users FROM 'users.csv' WITH (FORMAT csv)
```

Par défaut, PG utilise le format texte avec tabulation.

### DELIMITER 

Définit le caractère séparateur des champs. Par défaut `,` pour le CSV et tab pour TEXT

```sql
\COPY products FROM 'products.csv' WITH (FORMAT csv, DELIMITER ';')
```

### HEADER 

Indique que la première ligne contient les noms des colonnes.

```sql
\COPY users FROM 'users.csv' WITH (FORMAT csv, HEADER)
```

### ENCODING 

Indique l'encadage du fichier 

```sql
\COPY clients FROM 'clients.csv' WITH (FORMAT csv, HEADER, ENCODING 'WIN1251')
```

### NULL

Définit quelle valeurs text dans le fichier est considéré comme NULL

```sql
\COPY orders FROM 'orders.csv' WITH (FORMAT csv, NULL 'NULL')
```

---

## CONFIGURATION SEPARATEUR ET FORMAT DES DONNEES

Lorsque les données proviennt de systèmes différents, leurs format peut varier. Un fichier peut utiliser des virgules comme séparateurs, un autre des points-virgule, et un autre des tabulation. 

Une mauvaise configuration des séparateur peut provoquer des erreurs ou des mauvaises interprétation des données.

### Définir les séparateurs

Avec la commande `COPY`, on s'atttends à quel les colonnes dans un fichier CSV soient séparées par des virgules. On peut utiliser une option pour définir le séparateur 

```sql
COPY students FROM '/path/to/students.csv'
DELIMITER ','
CSV HEADER;

-- séparateur ;
COPY students FROM '/path/to/students.csv'
DELIMITER ';'
CSV HEADER;

-- séparateur tab
COPY students FROM '/path/to/students.tsv'
DELIMITER E'\t'
CSV HEADER;
```

### Ignorer les lignes vides et définir NULL

Pour que les valeurs NULL soient interprété comme NULL, on peut utiliser le paramètre `NULL AS`.

```sql
COPY students(id, first_name, last_name, email)
FROM '/path/to/students.csv'
DELIMITER ','
CSV HEADER
NULL AS '';
```

### Ignorer les lignes vide

Si le fichier contient des lignes vides que l'on ne souhaite pas importer, PG peut les ignorer automatiquement

```sql
COPY students(id, first_name, last_name, email)
FROM '/path/to/students.csv'
DELIMITER ','
CSV HEADER
NULL AS ''
IGNORE_BLANK_LINES;
```

### Gérer des données non standard

Il est parfois nécessaire d'importer des données en format texte.

```sql
COPY students(id, first_name, last_name, email)
FROM '/path/to/students.txt'
DELIMITER '|'
NULL AS ''
CSV;
```

---

## VERIFICATION DE LA VALIDITE DES DONNEES IMPORTEES

On peut venir ajouter une couche de validation avant de les charger, ou juste après.

### Vérification de la structure des données 

La première étape est de vérifier que les données sont bien importée selon la structure de la table. 

On peut ensuite utiliser les fonction PG pour checker le contenu des colonnes :

#### Vérification valeurs vide 

```sql
SELECT * FROM students WHERE first_name IS NULL OR last_name IS NULL;
```

#### Vérification des formats de données 

```sql
SELECT * FROM students WHERE birth_date::DATE IS NULL;
```

### Recherche d'erreurs 

#### Recherche des doublons 

```sql
SELECT email, COUNT(*)
FROM students
GROUP BY email
HAVING COUNT(*) > 1;
```

#### Vérification des données incorrecte 

```sql
SELECT * FROM students
WHERE birth_date < '1900-01-01' OR birth_date > CURRENT_DATE;
```

### Gestion des données incorrectes 

Une fois les lignes à problèmes trouvée, il faut venir les corrgier 

#### Suppression des données incorrecte 

```sql
DELETE FROM students
WHERE first_name IS NULL OR last_name IS NULL;
```

#### Mettre à jour des données 

```sql
UPDATE students
SET email = 'unknown@example.com'
WHERE email IS NULL;
```

### Visualisation des données pour l'analyse 

#### Fonction d'agrégation 

```sql
SELECT EXTRACT(YEAR FROM birth_date) AS year, COUNT(*)
FROM students
GROUP BY year
ORDER BY year;
```

#### Vérification unicité 

```sql
SELECT DISTINCT email
FROM students;
```

#### Vérification des plages de valeurs 

```sql
SELECT * FROM students
WHERE LENGTH(first_name) > 50 OR LENGTH(last_name) > 50;
```

---

## OPTIMISATION DE CHARGEMENT MASSIF DE DONNEES

L'optimisation des chargements permet d'éviter de surcharger le serveur, de réduire le temps d'attente et de minimiser les risques d'erreurs pendant le chargement.

### Désactivation des index et triggers 

Ils permettent de rendre les base de données intelligente et réactive. Pendant un chargement massifs, ils peuvent ralentir le process, parce que le serveur va essayer de mettre à jours et d'exécuter les triggers pour chaque ligne charger.

Pour soulager le système, on peut venir les désactiver.

```sql
-- Désactiver les triggers pour la table
ALTER TABLE students DISABLE TRIGGER ALL;

-- Charger les données
COPY students FROM '/path/to/students.csv' DELIMITER ',' CSV HEADER;

-- Réactiver les triggers
ALTER TABLE students ENABLE TRIGGER ALL;
```

### Utilisation des transactions 

Avec les transactions, on peut éviter les problème en empêchant d'appliquer les changement en cas de problème.

```sql
-- Démarrer une transaction
BEGIN;

-- Charger les données
COPY courses FROM '/path/to/courses.csv' DELIMITER ',' CSV HEADER;

-- Valider les changements
COMMIT;
```

Lorsque l'on charges des données sans transaction, le serveur valide les changement après chaque ligne. Avec une transaction, il le fait une seule fois à la fin du chargement.

### Désactivation de la vérification d'intégrité

Si la vérification des FK et de contrainte d'unicité n'est pas nécessaire pendant le chargement, on peut venir les désactiver. Sinon, la base va chercker chaque ligne, et cela ralentit le processus.

```sql
SET session_replication_role = 'replica';

-- Charger les données
COPY enrollments FROM '/path/to/enrollments.csv' DELIMITER ',' CSV HEADER;

SET session_replication_role = 'origin';
```

### Augmenter la mémoire d’exécution

On peut venir régler la mémoire de PG pour améliorer les perfs du chargement.

```sql
-- Augmenter la mémoire
SET work_mem = '64MB';
SET maintenance_work_mem = '256MB';

-- Charger les données
COPY teachers FROM '/path/to/teachers.csv' DELIMITER ',' CSV HEADER;
```

- `work_mem`: sert pour les opérations intermédiaire comme les tris et le hash 
- `maintenance_work_mem`: impacte les opérations liées aux index, comme leur reconstructions

### Préparer les données avant le chargement 

Faire un premier traitement avant l'import permet d'optimiser. Une première étape de triage est faite permettant de réduire potentiellement le nombre de donnée à importer.

### Partitionnement des données 

Si le fichier est énorme, on peut venir le pétitionner en plusieurs petits fichier permettant à PG de traiter les données plus efficacement.

```bash
split -l 1000 large_data.csv chunk_
```

Cette commande permet de partionner le fichier. On peut ensuite venir les charger 

```sql
COPY students FROM 'chunk_aa' DELIMITER ',' CSV HEADER;
COPY students FROM 'chunk_ab' DELIMITER ',' CSV HEADER;
-- Et ainsi de suite
```

### Charger en arrière plan 

Il est possible d'utiliser des process en arrière plan pour charger les données, pour ne pas saturer la base principale.

Les outils comme `pg cron` permettent de lancer des jobs planifiés.

```sql
CREATE EXTENSION pg_cron;

SELECT cron.schedule('*/5 * * * *', $$COPY students FROM '/path/to/data.csv' DELIMITER ',' CSV HEADER$$);
```

Toutes les 5 minutes, les données du fichiers seront chargées dans la table.

### Mettre en place un système de log 

Lorsque l'on charge des gros fichiers, il est essentiels de garder une trace des erreurs. Malheureusement la commande `COPY` ne propose pas de log par défaut.

On peut utiliser cette commande pour log 

```sql
COPY students FROM '/path/to/file.csv'
DELIMITER ','
CSV HEADER
LOG ERRORS INTO error_log
REJECT LIMIT 100;
```

