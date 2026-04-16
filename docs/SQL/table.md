# TABLE

## Création de table 

```sql 
-- id avec SQL server 
CREATE TABLE MaTable (
    Id INT IDENTITY(1,1) PRIMARY KEY,
    -- autres colonnes...
);
```

## Update d'une table 

```sql 
-- pg
ALTER TABLE <name_table>
ADD COLUMN <name_columne> <type>

-- SQLServer 
ALTER TABLE <name_table>
ADD <name_col> <type>

-- ajout colonne boolean avec SQL Server 
ALTER TABLE <name_table>
ADD Actif BIT DEFAULT(1);
```

## Suppression de colonne

```sql 
ALTER TABLE <table>
DROP COLUMN <name_col>;
```

## UPDATE 
Permet de mettre à jour la valeur d'un champ d'une table 

```sql 
-- SQL Server - la requête viens mettre à jour toutes les valeurs NULL à 1 pour rendre actif  
UPDATE TypesClientsCat
SET TypesClientsCat.Actif = 1
WHERE TypesClientsCat.Actif IS NULL;
```
