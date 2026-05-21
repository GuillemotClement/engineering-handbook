# Table temporaire 

Les tables temporaires sont des tables qui n'existent que pendant la session ou la transaction actuel.

Une fois que la session est terminée ou la transaction terminé, la table temporaire et ses données disparaissent. 

Elles sont adaptés pour stocker des données temporaire, faire des calcul intermédiaire ou préparer des données pour des opération complexes.

- **Stockage temporaire de données**: Par exemple, pour faire des calculs en plusieurs étapes. Au lieu de surcharger la table principale, on peut utiliser des tables temporaires
- **Analyse de données**: On peut venir rassembler des données de plusieurs sources, les traites, puis supprimer la table temporaire après analyse.
- **Optimisation de requêtes complexe**: Il est parfois plus simple de découper une requête en plusieurs étapes avec des tables temporaires
- **Minimiser les risques**: Pas de risque de corrompre les vraies tables, les tables temporaires sont isolées.

Les données des tables temporaire ne sont visible que par sois. Les autres utilisateurs de la base ne peuvent pas y accéder, c'est parfait pour faire des test et des expériences.

## Créer une table temporaire 

On ajoute `TEMP` ou `TEMPORARY` sur la requête de création d'une table.

```sql
CREATE TEMP TABLE temp_students (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    age INTEGER CHECK (age > 0)
);
```

On viens créer une nouvelle table temporaire `temp_students` dans laquelle on peut insérer des données et bosser dessus comme une table normal.

### Créer une table temporaire

```sql
CREATE TEMP TABLE temp_unregistered_students AS
    SELECT s.id, s.name
    FROM students s
    LEFT JOIN enrollments e ON s.id = e.student_id
    WHERE e.student_id IS NULL;
```

Cette requête vient créer une nouvelle table temporaire, et remplis directement la table avec le résultat de la requête `SELECT`.

On peut ensuite venir travailler sur cette table temporaire avec les données insérées

PG est capable de définir automatiquement les types des colonnes.

### Table temporaire dans les transactions 

Il est possible de créer des tables temporaires à l'intérieur d'une transaction. Dans ce cas, elles seront supprimées automatiquement quand la transaction sera terminée.

```sql
BEGIN;

CREATE TEMP TABLE temp_transactions (
    transaction_id SERIAL PRIMARY KEY,
    amount NUMERIC(10, 2) NOT NULL,
    status VARCHAR(50)
);

INSERT INTO temp_transactions (amount, status)
VALUES (100.50, 'En attente');

-- On regarde les données
SELECT * FROM temp_transactions;

COMMIT;
-- Maintenant la table temp_transactions a disparu !
```

#### Fusion temporaire de données

On as deux table `students` et `courses`. On souhaite savoir quels étudiants ne sont pas encore inscrits à des cours.

On peut commencer par rassembler les données dans une tables temporaire, puis faire l'analyse.

```sql
CREATE TEMP TABLE temp_unregistered_students AS
	SELECT s.id, s.name
	FROM students s
	LEFT JOIN enrollments e ON s.id = e.student_id
	WHERE e.student_id IS NULL;

-- Maintenant on peut bosser avec la table temporaire temp_unregistered_students.
SELECT * FROM temp_unregistered_students;
```

#### Préparer des données pour un rapport 

Parfois, on as besoin de rassembler des données de plusieurs tables, les transformer et préparer un rapport. Les tables temporaires sont adapté 

```sql
CREATE TEMP TABLE temp_sales AS
	SELECT p.product_id, p.name, SUM(s.quantity) AS total_quantity
	FROM products p
	JOIN sales s ON p.product_id = s.product_id
	GROUP BY p.product_id, p.name;

-- On a créé une table temporaire avec le résultat. Maintenant, on peut faire un rapport dessus :
SELECT name, total_quantity FROM temp_sales WHERE total_quantity > 50;
```