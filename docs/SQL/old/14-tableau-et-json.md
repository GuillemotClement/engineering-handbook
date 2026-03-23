# Tableau et JSON 

## UUID

Le GUID/UUID est un nombre de 128 bits qui ressemble à ça : `550e8400-e29b-41d4-a716-446655440000`

Il est généré à partir de :
- l'heure
- de nombre aléatoire 
- l'adresse MAC de la machine
- des fonction de hash crypto

Il permet de créer des identifiants uniques sans serveur central et sans coordination.

Ce type de données est parfait pour garantir l'unicité des valeurs à l'échelle du système, sans dépendre d'un générateur centralisé d'identifiants, par exemple dans les système distribués ou quand les données sont crées sur plusieurs serveurs.

L'utilisation d'un `UUID` est préférable sur un `INTEGER` pour plusieurs raison : 
- Unicité globale : si la base tourne sur plusieurs serveurs, il permet de garantir une unicité
- Sécurité et masquage: un `UUID` est plus dur à prédire qu'un numéro séquentiel. Cela limite les risque de fuite d'info
- Système distribué: si les données sont générées à différents endroits du système, `INTEGER` auto-incrémenté ne marche pas sans synchro.

L'avantage d'utiliser l'`UUID`: 
- Unicité: l'identifiant sera forcément unique, même si les données sont créer sur différents serveurs ou système 
- Flexibilité : on peut s'en servir pour les clés primaire, étrangère ou d'autres 
- Scalabilité: super pratique sur les bases distribuées

Inconvénients :
- Taille: il prends plus de place en mémoire
- Lisibilité: moins facile à lire et à retenir.

### Générer un UUID 

PG fournis une fonction intégrée permettant de généré des `UUID`. La fonction retourner un identifiant unique que l'on peut utiliser comme valeur pour une colonne de type `UUID`.

`SELECT gen_random_uuid();`

Dans les ancienne version de PG avant la 13, il faut venir installer l'extension `pgcrypto`.

---

## JSONB

Postgres gère deux types de données pour bosser avec du JSON 
- `JSON`: stocke les données comme une chaîne de caractères.
- `JSONB`: représentation binaire du JSON. Ce format est plus efficace pour lire, filtrer, et manipuler car le JSON est parser à l'avance pour optimiser son stockage.

Généralement, le `JSONB` est utiliser car : 
- Plus rapide grâce au stockage binaire 
- Il est possible de l'indexer
- Il garde les clé triées et vire les doublons pour simplifier le traitement.

Le format `JSONB` est utilise pour : 
- Le stockage de données semi-structurées. Généralement les données arrivent sous format d'objets complexes (métadonnées, paramètres, profils utilisateurs). Ce format permet de les stocker sans avoir besoin de créer des centaines de tables et de relations.
- Modélisation de données qui évoluent: lorsque la structure d'un objet change souvent, ce format permet de stocker cela de manière flexible
- Travailler avec des API: les appli web envoient généralement des données au format JSON. Au lieu de transformer les requête, on peut directement les stocker et travailler directement dessus.

### Création de table avec JSONB

On créer une base de données pour stocker des profils utilisateurs. La colonne `profile` va contenir toutes les infos supplémentaires au format JSON.

|id|nom - VARCHAR(100)|profil - JSONB|
|---|---|---|
|1|Alice|{"âge": 25, "compétences": ["SQL", "PostgreSQL", "JSONB"], "lieu": "New York"}|
|2|Bob|{"âge": 30, "intérêts": ["randonnée", "photographie"], "lieu": "Denver"}|
|3|Charlie|{"email": "charlie@example.com", "vérifié": true}|
|4|Diana|{"âge": 22, "compétences": ["Python"], "bio": "Passionné de données", "lieu": "Berlin"}|
|5|Eve|{"âge": 28, "compétences": [], "préférences": {"thème": "sombre", "notifications": false}}|

Il est possible d'insérer des données JSON comme des chaînes de caractères. PG les convertit automatiquement au format `JSONB`.

### Extraire des données depuis JSONB

#### Accéder à la valeur d'un champ 

L'opérateur `->` permet d'extraire une valeur 

```sql
-- Afficher l’âge de l’utilisateur
SELECT profile->'âge' AS âge FROM users;
```

#### Convertir la valeur en texte

L'opérateur `->>`  permet d'extraire la valeur comme une chaîne 

```sql
-- Afficher le lieu de résidence de l’utilisateur
SELECT profile->>'lieu' AS lieu FROM users;
```

### Filtrer des données avec JSONB

Il est possible d'utiliser des opérateurs classique pour bosser avec le format.

#### Filtrage par clé 

```sql
-- Trouver les utilisateurs qui habitent à "New York"
SELECT * FROM users
WHERE profile->>'lieu' = 'New York';
```

#### Recherche dans un tableau 

JSON gère les tableaux, et PG sait chercher dedans 

```sql
-- Trouver les utilisateurs qui maîtrisent SQL
SELECT * FROM users
WHERE 'SQL' = ANY(jsonb_array_elements_text(profile->'compétences'));
```

La fonction `jsonb_array_elements_text` transforme les éléments du tableau en string pour pouvoir les comparer.

L'opérateur `@>` permet d'utiliser une syntaxe courte.

```sql
-- Trouver les utilisateurs qui maîtrisent SQL
SELECT * FROM users
WHERE profile->'compétences' @> '["SQL"]';


-- Requête pour obtenir les noms des utilisateurs ayant la compétence SQL et un âge supérieur à 25
SELECT
    name
FROM
    users
WHERE
    -- Vérifie la présence de l'élément 'SQL' dans le tableau JSONB sous la clé skills
    (profile -> 'skills' @> '["SQL"]'::jsonb)
    AND
    -- Extrait la valeur age comme texte, la convertit en entier et compare > 25
    (profile ->> 'age')::INTEGER > 25;
```


---

## ARRAY 

Les tableaux permettent de stocker des valeurs du même type dans une seule colonne. Il permet de stocker pleins de valeurs dans un seul champ ce qui économise de la place et rends les requêtes plus flexibles.

```sql 
-- tableau de nombre
{1, 2, 3, 4}

-- tableau de string 
{"pomme", "banane", "cerise"}

-- tableau de dates
{2024-10-01, 2024-10-02, 2024-10-03}
```

L'avantage des tableau :

- Stockage compact des données: par exemple une liste de tags, une liste de note pour un étudiant
- Facilité de sélection: il permet de récupérer toute une liste de valeurs depuis un seul champs, et de le manipuler facilement chaque élément du tableau 
- Perf: les tableaux permettent d'éviter de créer des tables supplémentaires pour des données liées, ce qui peut simplifier l'architecture de la base et accélérer le dev.

### Créer un tableau dans SELECT

Pour créer un tableau, on utilise le constructeur `ARRAY[]`  

```sql
-- Exemple de tableau de nombres
SELECT ARRAY[1, 2, 3, 4];

-- Exemple de tableau de chaînes
SELECT ARRAY['pomme', 'banane', 'cerise'];
```

### Créer une table avec des tableaux 

|id|name - VARCHAR(50)|subjects - TEXT[]|
|---|---|---|
|1|Alex|{Mathematique,Physique}|
|2|Maria|{Chimie,Biologie,Anglais}|
|3|Peter|{Informatique}|
On peut ensuite venir récupérer les valeurs : 
```sql
SELECT * FROM students;
```

|id|name|subjects|
|---|---|---|
|1|Alex|{Mathematique,Physique}|
|2|Maria|{Chimie,Biologie,Anglais}|
|3|Peter|{Informatique}|
### Extraction des données depuis les tableaux 

PG propose plusieurs fonctions et opérateurs 

#### Extraire un élément du tableau 

On peut utiliser les `[]` puis l'index de l'élément. La numérotation commence à 1.

```sql
-- On récupère la première matière de chaque étudiant
SELECT name, subjects[1] AS premiere_matiere FROM students;
```

#### Extraite toute la ligne du tableau 

Pour récupérer tous les éléments du tableau, on utilise un `SELECT`

```sql
SELECT name, subjects FROM students;
```

### Rechercher des valeurs dans un tableau 

Parfois, on veut savoir si un tableau contient une valeur précise, pour cela on utilise `ANY`

```sql
-- Sélectionner les étudiants qui étudient "Mathematique"
SELECT name
FROM students
WHERE 'Mathematique' = ANY(subjects);
```

