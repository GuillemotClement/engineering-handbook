# SQL 

Le SQL est le language standards pour travailler avec des base de données.

Le SQL à pour tâche principale : 
- Extraction de données
- Ajout de donnée
- Mise à jour de données
- Suppression de donnée

## Commentaire 

```sql
/* Commentaire */ 

-- commentaire

-- commentaire dans une requête
SELECT * FROM employee -- WHERE YEAR(join_date) = 2015
```

## Formatage

Le SQL ne respect pas la casse. Les retour à la ligne ne sont pas pris en compte. 

Le SGBD peut traiter la requête comme si elle était sur une seule ligne.

Pour faciliter la lecture, on utilise une convention de style

- Mot clé en MAJUSCULE.
- `SELECT`, `FROM`, `WHERE` et les autres parties principale de la requête sur une nouvelle ligne
- `;` à la fin d'une requête.

```sql
SELECT customer_id, full_name, email, registration_date
FROM customers
WHERE delivery_address = 'Anytown';
```

## Ordre d'exécution d'une requête 

C'est la partie `FROM` qui est exécuté en premier. C'est seulement après que l'on vient récupérer le résultat.

1. La table `FROM` est traité: le serveur trouve la table
2. Les conditions sur les lignes sont appliquées: il ne garde que les lignes qui valide la condition
3. Les colonnes demandées sont sélectionnées: il extrait les données souhaité restante.

# Opération de base 


## SELECT

La commande `SELECT` est la commande de base en SQL. Elle permet d'extrare des info.

```sql
SELECT colonne1, colonne2, … colonneN
FROM table
```

### Sélectionner toutes les colonnes 

- `SELECT` : mot clé qui commence une requête
- `colone1`: le nom de colonne que l'on souhaite afficher
- `FROM table`: la table ou l'on souhaite extraite la donnée

Le `*`permet de selectionner toute les colonnes d'une table.

Il est préférable de selectionner uniquement les colonnes que l'on souahite par soucis de performance.

```sql 
SELECT * FROM students;
```

### Filtrer les colonnes afficher 

Filtrer les colonnes demander permet :
- Améliorer les performances
- Lisibilité: en affichant que les donnée souhaité, la lecture des resultats est simplifier
- Moins d'erreurs

```sql
SELECT name, age 
FROM students;
```

Ici, on récupére les données que pour les deux colonnes indiquer dans la requête.

### Utilisation d'expression

```sql
SELECT name, 21-age
FROM students;
```

Pour la deuxième colonne, on affiche directement le résultat du calcul.

### Concaténation 

Il est possible de concaténer directement deux colonnes. Les deux valeurs seront afficher dans la même colonne

```sql 
SELECT first_name || ' ' || last_name, grade
FROM students;
```

### Alias 

Les alias permette de renommer la colonne du résultat. Cela permet également d'améliorer la lisibilité.

```sql
SELECT 
  first_name AS "Prénom",
  last_name AS "Nom de famille",
  grade AS "Note"
FROM students;
```

### Erreurs classique

#### Plusieurs colonne du même nom

Dans le cas ou avec une jointure, deux colonnes ont le même nom, il sera nécessaire de préciser la table que l'on souhaite récupérer la données

```sql
SELECT students.first_name
FROM students;
```

#### * 

L'utilisation du `*` est pas optimal, et peut entrainer de gros ralentissement si il y a beaucoup de donnée sur la table.

---

## WHERE

Permet de filtrer les données retourner par une requête.

```sql
SELECT colonne1, colonne2
FROM table
WHERE condition;

-- exemple 
SELECT name, age
FROM students
WHERE age > 18;
```

La requête permet de récupérer le nom et l'âge des étudiants supérieur à 18.

### Opérateurs de comparaison 

| Opérateur | Signification | Exemple | Résultat |
| --- | --- | --- | --- |
| `=` | Egale | `age = 20` | Retourne les lignes ou age est égale à 20 |
| `<>` | Différents | `age <> 20` | Retourne les ligne ou l'age est différents de 20 |
| `>` | Supérieur | `age > 18` | Retourne les lignes où l'age est supérieur à 18 |
| `<` | Inférieur | `age < 18`| Retourne les ligne ou l'age est inférieur à 18 |
| `>=` | Supérieur ou égale | `age >= 18` | Retourne les ligne ou l'age est supérieur ou égale à 18 |
| `<=` | Inférieur ou égale | `age <= 18` | Retourne les ligne ou l'age est inférieur ou égale à 18 |

### Opérateur logique 

Les opérateurs logique permettent de combiner plusieurs conditions.

#### AND 

Les deux conditions sont vraies
```sql
SELECT colonne1, colonne2
FROM table
WHERE condition AND condition2;

-- exemple 
SELECT name, age, grade
FROM students
WHERE age > 18 AND grade = 'A';
```

#### OR 

Une des conditions doit être vraie 

```sql 
SELECT colonne1, colonne2
FROM table
WHERE condition OR condition2;

-- exemple 
SELECT name, age, grade
FROM students
WHERE grade = 'A' OR grade = 'B';
```

#### NOT 

Inversion. Selectionne les lignes pour lesquelles la condition indiquée est fausse

```sql 
SELECT colonne1, colonne2
FROM table
WHERE NOT condition;

-- exemple
SELECT name, age, grade
FROM students
WHERE NOT grade = 'A';
```

#### Combinaison d'opérateurs logiques 

Il est possible de combiner les opérateurs pour réaliser des conditions plus complexes.

```sql 
SELECT colonne1, colonne2
FROM table
WHERE (condition1 AND condition2) OR condition3;

-- etudiant de > 18 avec la note A ou etudiant avec la note B 
SELECT name, age, grade
FROM students
WHERE (age > 18 AND grade = 'A') OR grade = 'B';
```

#### Priorité des opérateurs 

1. `NOT`
2. `AND`
3. `OR`

Les parenthèses permettent également de modifier la prioritée.

## ORDER BY 

Permet de trier dans l'ordre descendant ou ascendant les résultats d'une requête.

Par défaut, order by trie dans l'ordre `ASC`. Pour inverser l'ordre, on utilise `DESC`

```sql
SELECT colonne1, colonne2
FROM table
ORDER BY colonne1 DESC;

-- tier les etudiant dans l'ordre asc de l'âge 
SELECT name, age
FROM students
ORDER BY age ASC;

-- desc
SELECT name, age
FROM students
ORDER BY age DESC;
```

### Trie sur colonne multiple 

Il est parfois nécessaire d'ajouter une colonne pour trier de manière correct. Par exemple, si plusieurs étudiants ont le même âge, on veux également trier sur le nom.

```sql 
SELECT colonne1, colonne2
FROM table
ORDER BY colonne1 DESC, colonne2 ASC;

-- triage sur age et nom
SELECT name, age
FROM students
ORDER BY age ASC, name ASC;
```

La requête vint trier par âge, puis par nom dans l'ordre alphabétique.

### Trie sur l'alias

```sql 
SELECT name AS "Nom", age AS "Âge"
FROM students
ORDER BY "Âge" DESC;
```

### Trie sur colonne calculées

SQL permet de faire des calculs direct dans la requête, puis de trier le résultat de ces calculs.

```sql 
SELECT name, age, age * 2 AS age_doublé
FROM students
ORDER BY age_doublé DESC;

-- avec fonction 
SELECT product_name, price, ROUND(price * 0.9, 1) AS prix_remisé
FROM products
ORDER BY prix_remisé ASC;
```

## LIMIT

Permet de limiter le nombre de lignes retournées par la requête.

Cela permet de limiter la charge sur le serveur et le réseau.

```sql
SELECT colonne1, colonne2
FROM table
LIMIT nombre_de_lignes;

-- limiter le résultat au 3 première lignes
SELECT *
FROM students
LIMIT 3;

-- les 5 premiers produits les plus chères
SELECT product_name, price
FROM products
ORDER BY price DESC
LIMIT 5;
```

`LIMIT` permet également de faciliter le debug lors de l'écriture des requêtes. Une fois la logique sur, on peut retourner l'ensemble des résultats.

### `LIMIT` sans `ORDER BY`

Si on ne précise pas un ordre de trie, les données seront retourner de manière aléatoire et retourner des résultats imprévisible.

Généralement, on utilise les deux pour obtenir de bon résultats.

### Utilisation combinée avec `WHERE`

Il est possible de combiner `WHERE` pour filtrer les lignes et `LIMIT` pour limiter le résultat.

```sql 
SELECT *
FROM students
WHERE age > 18
LIMIT 5;
```

### Afficher les première page de données

On peut utiliser le `LIMIT` pour créer une pagination sur un affichage de listing. Par exmeple, à l'affichage de la page, on affiche les 10 premier enregistrement 

```sql
SELECT *
FROM employees
ORDER BY hire_date
LIMIT 10;
```

### Combiner LIMIT avec des agrégats

Par exemple, on veut afficher les trois catégories de produits les plus populaire

```sql 
SELECT category, COUNT(*) AS total_products
FROM products
GROUP BY category
ORDER BY total_products DESC
LIMIT 3;
```

La requête groupe d'abords les produits par catégorie, trie par quantité puis limit au 3 première lignes.

---

## Table 

### Alias de table

Il est possible de définir un alias pour table avec `AS`

```sql
SELECT sa05.first_name, sa05.course_id
FROM students_archive_2005 AS sa05;
```


### Ajouter une colonne 

```sql 
ALTER TABLE <tableName>
ADD <colonneName> <typeColonne>;

-- exemple 
ALTER TABLE user 
ADD adresse_stree VARCHAR(255);
```

