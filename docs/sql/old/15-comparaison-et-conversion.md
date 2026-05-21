# Comparaison et conversion des types de données

## Comparaison des types de données 

Généralement, PG est capable de convertir automatiquement les types de données. Mais pour certain type (`TEXT` et `BOOLEAN`), il n'est pas capable de faire automatiquement cette conversion.

Par exemple, pour comparer une valeur numérique avec un string 

```sql
SELECT '42'::INTEGER = 42; -- TRUE
```

PG viens convertir '42' en nombre, puis viens faire la comparaison.

### Comparaison des types numérique 

Les trois types numérique sont généralement compatible entre eux. 

```sql
SELECT 42 = 42.0; -- TRUE
SELECT 42::REAL = 42.0; -- TRUE
SELECT 42.0::NUMERIC = 42; -- TRUE
```

Pour les nombres à virgule `REAL`, à cause de la précision limitée, ils peux avoir des comportement inattendus.

```sql
SELECT 0.1 + 0.2 = 0.3; -- FALSE
```

### Comparaison types texte 

Il n'y a pas de soucis à comparer les types textuel.

```sql
SELECT 'Hello' = 'Hello'::TEXT; -- TRUE
SELECT 'World'::CHAR(5) = 'World'::VARCHAR; -- TRUE
```

Attention avec les `CHAR`, il faut penser à gérer l'espace ajouter si la valeur est plus courte que prévus.

## CAST 

L'opérateur `CAST` permet de convertir un type en un autre 

```sql
SELECT CAST('42' AS INTEGER); -- Convertit la chaîne '42' en nombre 42
```

## ::

Syntaxe courte pour convertir les types 

```sql
SELECT '42'::INTEGER; -- Pareil que CAST('42' AS INTEGER)
```

### Eviter les erreurs de conversion 

`TRY_CAST()` permet d'éviter les erreurs de conversion 

```sql
SELECT 'Hello'::INTEGER; -- Erreur : impossible de convertir 'Hello' en nombre
```

### Exemple de conversion 

#### Conversion nombre en texte

```sql
SELECT 42::TEXT; -- Convertit le nombre 42 en chaîne '42'
SELECT 3.14::TEXT; -- Convertit le nombre 3.14 en chaîne '3.14'
```

#### Conversion texte en nombre 

```sql
SELECT '123'::INTEGER; -- Convertit la chaîne '123' en nombre 123
SELECT '3.14'::FLOAT;  -- Convertit la chaîne '3.14' en nombre 3.14
```

#### Boolean en TEXTE

```sql
SELECT TRUE::TEXT; -- 'true'
SELECT FALSE::TEXT; -- 'false'
```

```sql
SELECT 'true'::BOOLEAN; -- TRUE
SELECT 'false'::BOOLEAN; -- FALSE
```

#### Conversion date en TEXTE

```sql
-- Conversion de date en texte
SELECT TO_CHAR(actual_date, 'DD/MM/YYYY') FROM data_types_demo;
```

```sql
-- Conversion de chaîne en date
SELECT TO_DATE(date_as_text, 'YYYY-MM-DD') FROM data_types_demo;

SELECT
    event_date, -- Valeur d'origine de la date au format chaîne
    TO_DATE(event_date, 'DD-MM-YYYY') AS event_date_converted -- Conversion de la chaîne en format DATE
FROM
    events;
```