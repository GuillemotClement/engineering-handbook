# Transactions

## Introduction

Une transaction est un groupe d'actions exécutées uniquement ensemble. Si une des actions du du groupe échoue, alors le résultat est annuler et le système est remis dans son état initial.

Les actions peuvent être `INSERT`, `UPDATE`, `DELETE`.

Les transactions sont adaptées pour rendre le travail avec les bases de données fiable et cohérente, surtout pour les scénarios critiques comment le traitement des paiements ou la mise à jour de données liées.

Par exemple, on souhaite faire un transfert d'argent entre deux comptes bancaires. On réalise deux opérations qui s'enchaînent : 
1. On retire la somme d'un compte
2. On crédite la somme sur l'autre compte 

Si une erreur survient, il est vitale qu'un utilisateur ne soit perdant, soit les deux étapes sont réalisé, soit les deux sont annulées.

### ACID 

Les transactions reposent sur les princiapes ACID :
- **Atomicity**: tout ou rien. Soit toutes les opérations passent, soit tout est annulé
- **Consistency** : les données restent dans un état correct avant ou après la transaction
- **Isolation**: Chaque transaction fonctionne comme si elle était toute seule sur le système
- **Durability**: une fois validées, les données restent, même si le serveur tombe

### Commandes 

Les transactions fonctionnent avec trois commandes principales : 
1. `BEGIN`: permet de démarrer une nouvelle transaction. Toutes les opérations suivantes seront faite dans ce contexte
2. `COMMIT`: valide les changements. Après cette commande, les changements sont permanent
3. `ROLLBACK`: annule les changements. Si une erreur survient, la transactions et les données sont remis dans leur état initiale.

## Syntaxe de base 

```sql
-- structure simple d'une transaction

BEGIN;
-- ici tu mets tes opérations SQL
COMMIT;

---------------------------------------------

-- utilisation avec ROLLBACK
BEGIN;
-- modification de la table students
UPDATE students
SET grade = grade + 10
WHERE id = 1;

-- Oh ! On s’est rendu compte que c’était une erreur.
ROLLBACK;
```

### Exemple d'utilisation 

On souhaite inscrire un étudiant à un cours, et mettre à jour sa note moyenne en même temps

```sql
BEGIN;

-- Étape 1 : On ajoute une ligne dans la table "inscriptions aux cours"
INSERT INTO course_enrollments (student_id, course_id)
VALUES (1, 2);

-- Étape 2 : On met à jour la note moyenne de l’étudiant
UPDATE students
SET grade = grade + 5
WHERE id = 1;

COMMIT;
```

Si on n'utilisait pas de transaction, si le serveur tombe entre la première et deuxième étape, on obtiendrait des données incohérentes : l'inscription serait ajoutée, mais la note ne serait pas mis à jour. Avec la transaction, soit les deux opérations passent, soit les deux sont annulées.

#### Ajouter un étudiant à un cour

On as des auditeurs libres pour nos cours. S'il reste de la place sur le cours, on inscrit cet auditeur comme étudiant (temporairement) et on l'ajoute au cour.

1. Ajout d'une liste dans la table `students`
2. Création d'une entrée dans la table `enrollments` qui lie l'étudiant au cours

Si une opération échoue (le cours est pleins), il faut annuler l'opération pour que les données restent synchrone entre les tables.

```sql
-- Début de la transaction
BEGIN;

-- Étape 1 : On ajoute l'étudiant
INSERT INTO students (name, age, gender)
VALUES ('Otto Lin', 20, 'Male')
RETURNING id;

-- Disons qu'on récupère id = 10

-- Étape 2 : On l'inscrit au cours
INSERT INTO enrollments (student_id, course_id)
VALUES (10, 5);

-- Tout s'est bien passé ? On valide les changements
COMMIT;
```

Dans le cas ou il y a une erreur (le cours n'existe pas), l'entrée de l'étudiant reste dans la table, mais n'aura pas de relation avec `enrollments`. Cela casse l'intégrité des données. Pour utiliser cela, on peut utiliser la commande `ROLLLBACK`

```sql
-- Début de la transaction
BEGIN;

-- Étape 1 : On ajoute l'étudiant
INSERT INTO students (name, age, gender)
VALUES ('Otto Lin', 20, 'Male')
RETURNING id;

-- Étape 2 : On essaie de l'inscrire au cours
INSERT INTO enrollments (student_id, course_id)
VALUES (10, 999); -- Erreur : pas de cours avec id = 999 !

-- On annule tous les changements
ROLLBACK;
```

Résutalt, aucune des opération n'est réalisé, la base reste comme avant la transaction.
### Gérer les erreurs dans les transactions 

Parfois, quelque chose ne se passe pas comme prévu, et il faut venir gérer proprement l'erreur. Avec PG, la transaction est automatiquement annulée si une erreur arrive.

Avec `ROLLBACK`, on peut venir faire une annulation manuel. Avec cette commande, la table reste inchangée.

### Règles d'utilisation
- Utiliser une transaction si une opération a plus d'une étape, surtout dans le cas ou l'on modifie plusieurs tables.
- Envelopper les opérations complexes dans des transactions pour garder la cohérences des données

--- 
## BEGIN 

Cette commande permet de démarrer une transaction.

## COMMIT

Cette commande valide les changements. Après cette commande, les changements sont permanent

## ROLLBACK

Cette commande annule les changements. Si une erreur survient, la transactions et les données sont remis dans leur état initiale.
## SAVEPOINT

On souhaite faire plusieurs opérations, mais à un moment, on souhaite revenir juste à un point précis, et pas tout annuler.

```sql
-- Début de la transaction
BEGIN;

-- On ajoute l'étudiant
SAVEPOINT add_student; -- On crée un point de sauvegarde
INSERT INTO students (name, age, gender)
VALUES ('Anna Song', 22, 'Female');

-- On l'inscrit au premier cours
SAVEPOINT enroll_course_1; -- Encore un point de sauvegarde
INSERT INTO enrollments (student_id, course_id)
VALUES (11, 5);

-- On l'inscrit au deuxième cours (erreur ici)
INSERT INTO enrollments (student_id, course_id)
VALUES (11, 999); -- Erreur !

-- On revient juste au dernier point de sauvegarde
ROLLBACK TO enroll_course_1;

-- On continue le process
INSERT INTO enrollments (student_id, course_id)
VALUES (11, 6);

-- On valide les changements
COMMIT;
```

Une erreur dans une partie du process n'empêche pas de sauvegarder les autres données.

---
## FOUND

Variable système qui permet de vérifier si des lignes ont été touchées dans la dernière requête SQL.

- `FOUND = TRUE` - la requête à modifié quelque chose
- `FOUND = FALSE` - rien n'as été touché 

### Gestion des paiements 

Les transactions sont super utiles dans les applis financière. Par exemple, un système qui doit transférer de l'argent d'un compte à un autre.

```sql
-- Début de la transaction
BEGIN;

-- Étape 1 : On retire de l'argent du premier compte
UPDATE accounts
SET balance = balance - 100
WHERE id = 1 AND balance >= 100;

-- Étape 2 : On vérifie que l'opération a marché (des lignes ont été modifiées)
IF NOT FOUND THEN
    ROLLBACK; -- Annule si pas assez d'argent
    RAISE EXCEPTION 'Pas assez de fonds !'; -- Erreur ! On lève une exception
END IF;

-- Étape 3 : On ajoute l'argent au deuxième compte
UPDATE accounts
SET balance = balance + 100
WHERE id = 2;

-- On valide la transaction
COMMIT;
```

Si un client essaie de transférer plus d'argent qu'il n'en a, la transaction est annulée et la base ne reste pas dans un état incorrect.

---

## EXCEPTION

Dans PG, il est possible d’anticiper les erreurs et de les gérer directement dans les requête avec les blocs `EXCEPTION`.

Par exemple, on ajoute un étudiant et on l'inscrit à un cours, et si ça plante on log l'erreur.

```sql
DO $$
BEGIN
    -- On tente d'insérer les données
    INSERT INTO students (name, age) VALUES ('Anna Song', 22);
    INSERT INTO enrollments (student_id, course_id) VALUES (2, 999); -- Erreur

    -- Si tout est OK
    RAISE NOTICE 'Enregistrement ajouté avec succès !';
EXCEPTION
    WHEN foreign_key_violation THEN
        -- On gère la violation de clé étrangère
        RAISE WARNING 'Le cours avec ce course_id n\'existe pas.';
END $$;
```

---
## ON CONFLIT 

Il est possible d'éviter à l'avance l'erreur liée à la contrainte `UNIQUE` avec cette option. 

Par exemple, si on tente d'insérer un existant, on saute l'insertion 

```sql
INSERT INTO courses (course_name)
    VALUES ('SQL Basics')
ON CONFLICT (course_name) DO NOTHING; -- Sauter les doublons
```

Ou bien, on met à jour la ligne existante 

```sql
INSERT INTO courses (course_name)
    VALUES ('SQL Basics')
ON CONFLICT (course_name) DO UPDATE
    SET course_name = EXCLUDED.course_name || ' (Mis à jour)';
```

---

## GESTION DES ERREURS

### Transactions comme outil de protection 

Quand on manipule des données, on utilise souvent des transactions pour garantir la cohérence. Si on arrive, on peut annuler les changements.

Exemple, l'ajout de données dans deux tables 

```sql
BEGIN; -- On démarre une transaction

-- On insère des données dans la table students
INSERT INTO students (name, age) VALUES ('Otto Lin', 21);

-- On insère une ligne dans enrollments
-- Ici, ça plante si le cours avec ID=10 n'existe pas
INSERT INTO enrollments (student_id, course_id) VALUES (1, 10);

-- Si tout s'est bien passé
COMMIT;
-- Si y'a eu une erreur, on "rollback"
ROLLBACK;
```

Si le cours avec `course_id = 10` n'existe pas, l'insertion dans la table `students` sera annulée.

### Violation d'unicité UNIQUE 

```sql
-- Une façon de faire :
SELECT * FROM students WHERE email = 'otto.lin@email.com';

-- Ou alors utiliser UPSERT :
INSERT INTO students (name, email)
VALUES ('Peter Pen', 'otto.lin@email.com')
ON CONFLICT (email) DO NOTHING;
```

### Violation contrainte d'unicité 

Lorsque l'on veux supprimer une ligne qui est lié avec une FK, cela provoque une erreur. Pour éviter ce problème, on peut ajouter un `DELETE CASCADE` pour forcer la suppression 

```sql
CREATE TABLE enrollments (
    id SERIAL PRIMARY KEY,
    student_id INT REFERENCES students(id) ON DELETE CASCADE,
    course_id INT
);
```

### Problème d'accès concurrent 

On as deux utilisateurs qui souhaite modifier la même ligne en même temps. Sans isolation, on risque d'avoir des conflits.

Pour éviter cela, on peut utiliser les transactions et les niveaux d'isolation pour empêcher les modifs simmulatanées

```sql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
COMMIT;
```