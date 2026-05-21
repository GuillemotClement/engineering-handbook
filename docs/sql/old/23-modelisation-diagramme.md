# Modalisation et diagrammes ER 

La modélisation des relations entres les tables permet de lier les tables entre elles. Ces relations montrent comment les données interagissent entre elles.

Ces relations aident le système à comprendre quelles infos sont liées à quoi, sans dupliquer les données pour rien.

## UN-A-UN

C'est relation où un enregistrement dans la table A correspond à un seul enregistrement dans la table B. 

Par exemple, un passeport appartient à une personne. La relation est définis par la FK `employe_id` qui pointe vers l'id dans la table `employees`

|id|employé_id|numéro de passeport|
|---|---|---|
|1|1|123456789|

|id|nom|poste|
|---|---|---|
|1|Otto Lin|manager|

---

## UN-A-PLUSIEURS

Un enregistrement de la table A est lié à plusieurs enregistrements de la table B. 

Par exemple, un prof peut donner plusieurs cours.

|id|nom|
|---|---|
|1|Anna Song|
|2|Alex Min|

|id|nom du cours|professeur_id|
|---|---|---|
|1|Bases de SQL|1|
|2|Administration BD|1|
|3|Programmation en Python|2|
La relation est créer via la FK `professeur_id` dans la table `cours` qui permet de lier un prof à plusieurs cours.

---

## PLUSIEURS-A-PLUSIEURS

Plusieurs enregistrement dans une table A corresponde à plusieurs enregistrement dans la table B. 

Par exemple, les étudiants peuvent s'inscrire à plusieurs cours, et chaque cours peut être liés à plusieurs étudiants.

`etudiants`

|id|nom|
|---|---|
|1|Otto Lin|
|2|Maria Chi|

`cours`

|id|nom du cours|
|---|---|
|1|Bases de SQL|
|2|Administration BD|

Pour faire le lien entre les deux tables, on utilise une table pivot qui permet de définir la relation entre les deux tables.

|id|étudiant_id|cours_id|
|---|---|---|
|1|1|1|
|2|1|2|
|3|2|1|

---

## MODELISATION DES RELATIONS

Pour modéliser les relations entre les tables, on utilisent des `foreign key`.

```sql
CREATE TABLE Cours (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(255)
);

CREATE TABLE Inscriptions (
    id SERIAL PRIMARY KEY,
    étudiant_id INT,
    cours_id INT,
    FOREIGN KEY (cours_id) REFERENCES Cours(id)
);
```

Dans cet exemple, la table `cours` envoie sa FK dans la table `inscriptions`. Un cours peut être lié à plusieurs inscriptions, et une inscription peut être lié à un cours (Relation un à plusieurs - 1-N).

Pour éviter les erreurs, il faut :
- Vérifier que le type de données entre les colonnes de la PK et FK soient cohérente (SERIAL pour la PK, et INT pour la FK)
- Penser au cas de suppression ou update (`ON DELETE CASCADE` si une suppression d'une ligne parent déclenche la suppression des enfants associé).

### Modélisation MANY-TO-MANY 

```sql
CREATE TABLE Étudiants (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(255)
);

CREATE TABLE Cours (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(255)
);

CREATE TABLE Inscriptions (
    id SERIAL PRIMARY KEY,
    étudiant_id INT,
    cours_id INT,
    FOREIGN KEY (étudiant_id) REFERENCES Étudiants(id),
    FOREIGN KEY (cours_id) REFERENCES Cours(id)
);
```

La table `inscriptions` est la table pivot. elle reçoit les FK des tables `etudiants` et `cours`.

---

## ERD 

Un ERD ou **Entity Relationship Diagram (diagramme entité relation)** est une représentation visuelles de la structure de la base de donnée, qui montrent comment les entités sont reliées entre elles.

Elle se composent :
- **Entités** : ce sont les tables
- **Attributs**: ce sont les colonnes 
- **Relations**: ce sont les lignes entre les tables 

### Entités

L'entité correspond à un objet réel ou un concept que l'on souhaite gérer dans le système.

```plaintext
+---------------+
| Étudiants     |
+---------------+
| ID            |
| Prénom        |
| Nom           |
| Date de naissance |
+---------------+
```

### Attributs

Les attributs sont les propriétés ou caractéristiques d'une entité. Ils apparaissent comme des colonnes dans la table de la base de données.

### Relation 

La relation montre comment les deux entités interagissent entre elles.

- **1:1**: un étudiant est lié à une carte
- **1:N** : un enseignant peut donner plusieurs cours
- **M:N**: plusieurs étudiants sont inscrits à plusieurs cours

```plaintext
Étudiants (1) <----- inscrit -----> (N) Cours
```

### Construire un ERD 

Pour construire un ERD, on utilise ce processus 

#### 1 - Définir les entités

On commence par découper le système en objets principaux. Par exemple, pour une bibliothèques on retrouve `livres`, `lecteurs`, `emprunts`.

#### 2. Ajouter les attributs

Chaque table est constitué d'attributs clé, par exemple :
- La table `livre` doit inclure un `Id`, `Titre`, `Auteur`, `ISBN`
- La table `lecteurs` doit inclure `Id`, `Prénom`, `Nom`

#### 3. Mise en place des relations 

On viens ensuite ajouter les relations entre les entité 
- `emprunts` est lié à `livres` (un livre peut être emprunté plusieurs fois)
- `emprunts` est lié à `lecteurs` (un lecteur peut emprunter plusieurs livres).

#### 4. Visualiser l'erd

On peut utiliser des outils pour créer les diagrammes :
- draw.io
- dbdiagram.io
- Lucidchart
- Microsoft Visio 



