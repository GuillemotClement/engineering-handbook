## Créer une table 

```sql
CREATE TABLE employees (
    id SERIAL PRIMARY KEY,
    full_name TEXT NOT NULL
    -- ... autres colonnes
);
```


## Supprimer une table

```sql
DROP TABLE IF EXISTS <table_name>; 

-- suppression si dependances
DROP TABLE IF EXISTS <table_name> CASCADE; 
```

Parfois, la supression de table est impossible car il existe des référence à d'autre table via des `FK`. Pour cela, on peut utiliser l'option `CASCADE` pour forcer la supression.

Attention, cette option ne doit pas être utiliser en production car cela peut supprimer des données non prévue.


