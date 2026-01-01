## Formatage de données

Le formatage de donnée permet de :
- Transformer les données dans une format lisible 
- Préparer les données pour l'export ou l'intégration avec un autre système
- Rendre les rapports et les visualisations lisibles et précises

## Formatage de chaînes 

### CONCAT()

La fonction permet de fusionner plusieurs chaînes en une seule. 

```sql
SELECT CONCAT(first_name, ' ', last_name) AS full_name
FROM students;
```

La fonction ignore `NULL`, si un argument à cette valeur, alors il sera ignoré.

On peut également utiliser `||` 

```sql
SELECT first_name || ' ' || last_name AS full_name FROM students;
```
### UPPER() & LOWER() 

Permet de changer la casse d'une chaîne 

## Formatage des dates et des heures

### NOW() 

Date et heure actuelle

### CURRENT_DATE()

La date du jour sans les heures

### DATE_PART()

Extraction d'une partie d'une date (année ou mois)

```sql
SELECT DATE_PART('year', date_of_birth) AS birth_year
FROM students;
```

Dans cette requête, on extrait l'année de naissance depuis la colonne.

## Conversion de types

### CAST()

Permet de convertir le type d'une donnée
`<valeur>::<type>`

```sql
SELECT birth_year::text || ' an' AS formatted_year
FROM (
  SELECT DATE_PART('year', date_of_birth) AS birth_year
  FROM students
) subquery;
```

Dans cette requête, on extrait l'année de naissance, puis on la convertir en chaîne et on ajoute une chaine "an".

## Trie des données 

### ORDER BY 

Permet de trier les données par une ou plsuieurs colonnes

### DISTINCT

Extrait les valeurs uniques

