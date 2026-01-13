# Normalisation des données 

La normalisation est le processus qui consiste à organiser les données dans les tables de façon à minimiser la redondance et à éliminer les soucis liées à la mise à jour, la suppression et l'insertion.

---

## 1NF 

La première forme normale (First Normal Form - 1NF) est le premier niveau de normalisation d'une base de données. Elle impose des règles sur la structure des tables :
- **Toutes les données sont atomatiques** : Chaque valeur dans une cellule doit être indivisible. 
- **Chaque ligne est unique**: la table doit avoir une PK ou un id unique 
- **Pas de groupe de données répétitifs**: les valeurs d'une même entité ne doit pas être dans la même colonne

### Mettre en place une table 1NF

Par exemple, on as cette table qui ne respecte pas les règle : 

|student_id|name|courses|
|---|---|---|
|1|Maria|"Mathématiques, Physique"|
|2|Rob|"Biologie, Chimie"|
Dans la colonne `courses`, on peut voir que plusieurs données sont groupé. Pour que cette table respect la norme, on vas séparer les données en lignes distincts ou chaque valeur sera atomique.

|student_id|name|course|
|---|---|---|
|1|Maria|Mathématiques|
|1|Maria|Physique|
|2|Rob|Biologie|
|2|Rob|Chimie|

---


