# Base de données 

Les bases de données assurent :
- Stockage
- Gestion: les données sont organisées, structurées et accessibles.
- Extraction: la récupération des donnée est simple 

## SGBD

Le Système de gestion de base de données permet :
- Ajouter de nouvelle ligne dans les tables
- Modifier les données dans n'importe quelle colonne
- Chercher des informations 
- Supprimer des lignes si elles ne servent plus

## Element de base 

### Table

Contient des lignes et des colonnes. C'est la structure de base pour stocker des données.

Chaque table possède :
- nom: `student`, `courses`
- colonnes ou attributs qui définissent les propriété de la table.
- des lignes qui représente des enregistrements. Elle contient les données pour cet enregistrement (Alex, 101, etc )

Pour les colonnes qui servent de clé primaire, on utilise par convention `nom_id`. Par exemple `customer_id`.

### Lignes

Les lignes sont les enregistrement ou objets que l'on stocke. Une ligne = un enregistrement.

### Colonnes 

Propriété d'un table. 

## SQL 

Le SQL c'est le language qui permet de formuler des requêtes pour effectuer des actions avec la base de donnée. Cette requête est envoyer au SGBD qui réalise les actions.

1. Utilisateur créer la requête 
2. La requête part au SGBD 
3. Le SGBD va voir la base de données
4. La base retourne l'info au SGBD
5. Le SGBD traite les données selon la requête
6. Le résultat est fournis à l'utilisateur.

# Modèle relationnel 

Le modèle relationnel repose sur la représentation des données sous forme de tables, ou chaque table est composée de lignes et de colonnes.

Les lignes sont des enregistrements, les colonnes des propriétés.

Par exemple, un site e-commerce pourrait utiliser ce modèle : 
- `Clients`: stocke les informations du client avec un Numérique unique 
- `Service de livraison`: cette table contient la liste des services qui peuvent être livrer, avec chacun un numero de service unique.
- `Livraison de commande`: c'est la table central, celle qui permet de faire le lien. Chaque ligne est une livraison. Elle possède son propre numéro de livraison, et fait référence au client et au service.

## Clé des tables 

Pour identifier chaque ligne dans une table, on utilise une clé primaire (Primary Key PK). 
C'est une colonne ou plusieurs dont les valeurs sont unique pour chaque ligne et qui ne peuvent pas être vide.

Pour créer des liens entre les tables, on utilise des clé étrangère (Foreign Key FK). C'est une colonne dans une table qui fait référence à la clé primaire d'une autre table.
Généralement, le nom de la FK est le même que celui de la clé primaire à laquelle elle fait référence. Par exemple, dans une table `deliveries`, la colonne `customer_id` sera une clé étrangère stockant la valeur de la colonne `customer_id` de la table `customers`, ce qui permet de lier une livraison à un client.

L'utilisation des clé primaire et étrangère garantit l'intégrité des données. Le SGBD ne permet pas de créer un enregistrmenet si les clé étrangère soumises ne corresponde pas à une valeur stocker dans les tables associées.

---

# Type de SGBD

## SGBD Relationnelle 

Organise les données sous forme de tables, ou chaque ligne est un enregistrement, et chaque colonne un champ.

Les tables sont reliées entre elles grâce à des clés (PK, FK).

Points clés :
- Donnée sont structurées
- Liens entre les tables définis avec les clé 
- Utilise le SQL 

Avantage :
1. structure stricte: les tables garantissent que chaque objet à un set de champs fixe
2. Intégrité des données : avec les clés, cela évite les incohérence
3. Support du standard ACID: assure la fiabilité des transactions
4. Support des requêtes complexes

Les SGBD relationnelles sont adapté la ou les données doivent être structurées (banque, système de gestion d'entreprise, application web avec des listes d'utilisateurs, produits, commande).

## NoSQL

SGBD qui n'utilisent pas le modèle relationnel. Les données peuvent être stocké sous forme de document, format clé-valeur, graphes, colonnes. Elle permet la fléxibilité, il est possible de stocker les données comme ça arrange, sans règle strict.

MongoDB utilise enregistre les donénes sous forme de document, généralement en JSON. Cela simplifie lorsque la structure des données peut changer d'un enregistrement à l'autre.

Redis utilise les clé-valeur. Utiliser pour les caches ou l'accès rapide à des settings simple.

SGBD Graphe comme Neo4j sont adapter pour de l'analyse de donnée. (réseaux sociaux, itinéraire). Elle gardent l'info comme un réseau de noeuds et de liens

SGBD Colonnes comme Apache Cassandra ou HBase stockent les données par colonnes. C'est utilie pour de gros volume de donnée ou pour analyser rapidement des indicateurs (Analytics ou BigData)

### Avantage :
1. Rapide
2. Flexible
3. Scalable: les données peuvent être répartie sur plusieurs serveurs
Elles sont réellement utilise dans les cas ou :
- le volume de données grime trop vite et la structure change (analyse de log ou d'event)
- chercher et agréger de l'info rapidement 
- réseaux complexe de liens (réseau sociaux)

## Choisir le bon SGBD 

Relationnelle : 
- Donnée structurée
- Transactions
- Analyse SQL

NoSQL:
- Fléxibilité 
- Scalabilité 
- Gros volumes de données difficiles à organiser en table