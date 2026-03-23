# Gestion des dates et des heures 

## DATE 

Permet de stocker une date sans l'heure. 

```sql
-- Exemple de création de table avec le type `DATE`
CREATE TABLE events (
    event_name TEXT,
    event_date DATE
);

-- Insertion de données
INSERT INTO events (event_name, event_date)
VALUES ('Conférence PostgreSQL', '2023-12-01'),
       ('Anniversaire', '2023-10-12');

-- Requête de sélection
SELECT * FROM events;
```

---
## TIME

Permet de stocker uniquement les heures

```sql
-- Exemple de création de table avec `TIME`
CREATE TABLE schedules (
    schedule_name TEXT,
    start_time TIME,
    end_time TIME
);

-- Insertion de données
INSERT INTO schedules (schedule_name, start_time, end_time)
VALUES ('Heures de travail', '09:00:00', '18:00:00'),
       ('Pause déjeuner', '13:00:00', '14:00:00');

-- Requête de sélection
SELECT schedule_name, start_time, end_time FROM schedules;
```

---

## TIMESTAMP 

Ce type combine les date et les heure dans une seule valeur. Il ne gère pas les fuseaux horaire.

```sql
-- Exemple de création de table avec `TIMESTAMP`
CREATE TABLE documents (
    document_id SERIAL PRIMARY KEY,
    created_at TIMESTAMP
);

-- Insertion de données
INSERT INTO documents (created_at)
VALUES ('2023-10-12 15:30:00'),
       ('2023-12-01 08:45:15');

-- Requête de sélection
SELECT document_id, created_at FROM documents;
```

---

## TIMESTAMPTZ

Permet de stocker en plus le fuseau horaire.

```sql
-- Exemple de création de table avec `TIMESTAMPTZ`
CREATE TABLE meetings (
    meeting_id SERIAL PRIMARY KEY,
    meeting_time TIMESTAMPTZ
);

-- Insertion de données (PostgreSQL garde le fuseau horaire courant)
INSERT INTO meetings (meeting_time)
VALUES ('2023-10-12 15:30:00+03'),
       ('2023-12-01 08:45:15-05');

-- Requête de sélection
SELECT meeting_id, meeting_time FROM meetings;
```

---

## NOW() 

Cette fonction retourne la date et l'heure avec le fuseau horaire du serveur au format `TIMESTAMPS WITH TIME ZONE`.

C'est une fonction spécifique à Postgres.


```sql
SELECT NOW();
-- Résultat : 2025-05-25 14:30:45.761523+03
```

Si le fuseau horaire n'est pas nécessaire, on peut venir convertir explicitement en type `TIMESTAMP`

```sql
SELECT NOW()::TIMESTAMP;
-- Résultat : 2025-05-25 14:30:45.761523
```

### CURRENT_TIMESTAMP

Fais la même chose, mais c'est un standard SQL.


---

## CURRENT_DATE

Cette fonction retourne uniquement la date du jour. Le type de retour est `DATE`.

```sql
SELECT CURRENT_DATE;
-- Résultat : 2025-05-25
```

---

## CURRENT_TIME()

Cette fonction retourne l'heure actuelle au format `TIME WITH TIME ZONE`. 

```sql
-- avec le fuseau horaire 
SELECT CURRENT_TIME;
-- Résultat : 14:30:45.761523+03

-- conversion explicite si pas besoin du fuseau
SELECT CURRENT_TIME::TIME;
-- Résultat : 14:30:45.761523
```

### Utilisation 

#### Remplissage automatique de la date de création d'une ligne 

Pour que la date soit automatiquement insérer lors d'une insertion dans une table, on peut definir cette table 

```sql
CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    customer_name TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT NOW() -- moment de création de la ligne
);
```

#### Filtrage des données par date 

On souhaite sélectionner toutes les commandes du cours 

```sql
SELECT *
FROM orders
WHERE created_at::DATE = CURRENT_DATE;
```

#### Utiliser des fonctions temporelles dans les conditions de filtrage 

On veux créer une requête qui sélectionne les lignes créees dans les 7 derniers jours. 

```sql
SELECT *
FROM orders
WHERE created_at >= NOW() - INTERVAL '7 days';
```

Même principe pour sélectionner les commandes du mois en cours 

```sql
SELECT *
FROM orders
WHERE created_at >= DATE_TRUNC('month', NOW());
```

---

## EXTRACT()

Cette fonction permet d'extraite une partie précise d'une date ou d'un timestamp. Par exemple, l'année d'une date de naissance, le numéro du mois ou même le jour de la semaine.

```sql
EXTRACT(part FROM source)
```

- `part` : c'est la partie de date que l'on souhaite extraite (`YEAR`, `MONTH`, `DAY`, `MINUTE`, `SECOND`).
- `source`: c'est le type de donnée temporelles que l'on souhaite extraite l'information (colonne, constante, ou résultat de fonction).

```sql
SELECT
	-- extraction de l'année 
    EXTRACT(YEAR FROM '2024-11-15'::DATE) AS year_part,
    -- extraction du mois 
    EXTRACT(MONTH FROM '2024-11-15'::DATE) AS month_part,
    -- extraction du jour 
    EXTRACT(DAY FROM '2024-11-15'::DATE) AS day_part;
    
    
SELECT
	-- extraction du jour de la semaine 
    EXTRACT(DOW FROM '2024-11-15'::DATE) AS day_of_week,
    -- extraction de l'heure 
    EXTRACT(HOUR FROM '15:30:00'::TIME) AS hour_part;
```

- `DOW`: (Day of Week) qui retourne le numéro du jour (0 pour dimanche)

On peut venir également l'appliquer sur une colonne 

```sql
SELECT
    order_id,
    -- extraction du mois
    EXTRACT(MONTH FROM order_date) AS month,
    -- extraction dujour
    EXTRACT(DAY FROM order_date) AS day
FROM orders;
```

---

## AGE() 

Cette fonction retourne la différence entre deux timestamp. Par exemple, pour calculer l'âge d'un client à partir d'une date de naissance, ou savoir combien de temps s'est écoulé depuis une commande 

```sql
AGE(timestamp1, timestamp2)
```

- `timestamp1`: le plus récent 
- `timestamp2`: le plus ancien 

Si on ne passe qu'une paramètre, PG compare automatiquement avec la date actuelle `NOW()`.

```sql
-- calculer l'âge 
SELECT AGE('2025-11-15'::DATE, '1990-05-12'::DATE) AS age;

-- intervalle entre deux events
SELECT AGE('2023-06-01 15:00'::TIMESTAMP, '2023-05-20 10:30'::TIMESTAMP) AS duration;

-- extraite l'âge d'un client 
SELECT
    customer_id,
    AGE(NOW(), birth_date) AS age
FROM customers;
```

### Utilisation combiné EXTRACT() et AGE() 

#### Grouper les données par mois 

On as une table de commandes avec des dates. Pour compter les commandes par mois, on peut utiliser cette requêtes :
```sql
SELECT
    EXTRACT(MONTH FROM order_date) AS order_month,
    COUNT(*) AS total_orders
FROM orders
GROUP BY order_month
ORDER BY order_month;
```

#### Jours avant expiration 

```sql
SELECT
    subscription_id,
    AGE(expiry_date, NOW()) AS time_remaining
FROM subscriptions;
```

---

## CONVERSION DE FORMATS 

### TO_CHAR() 

Permet de transformer des données de types temporelles en format texte lisible. Elle prends une date en timestamp et la formate selon le modèle fournit.

```sql
TO_CHAR(value, format)
```

- `value`: la date ou le timestamp à convertir
- `format`: string ou modèle de format dans lequel on souhaite afficher la date.

|Modèle de format|Signification|Exemple|
|---|---|---|
|`YYYY`|Année|`2023`|
|`MM`|Mois (nombre de 01 à 12)|`10`|
|`MONTH`|Nom du mois (en majuscules)|`OCTOBER`|
|`DAY`|Jour de la semaine (en majuscules)|`SUNDAY`|
|`DD`|Jour du mois|`01`|
|`HH24`|Heures au format 24h|`15`|
|`MI`|Minutes|`45`|
|`SS`|Secondes|`30`|

https://www.postgresql.org/docs/current/functions-formatting.html

```sql
-- formatage en date 
SELECT TO_CHAR(NOW(), 'DD.MM.YYYY') AS formatted_date;
-- Résultat : '09.10.2023'

-- affichage au format 12h
SELECT TO_CHAR(NOW(), 'HH12:MI AM') AS formatted_time;
-- Résultat : '03:45 PM'

-- affichage du mois en lettre 
SELECT TO_CHAR(NOW(), 'Month') AS month_name;
-- Résultat : 'October '

-- pg ajoute un espace automatiquement, on utilise trim() 
SELECT TRIM(TO_CHAR(NOW(), 'Month')) AS trimmed_month_name;

-- format custom
SELECT TO_CHAR(NOW(), 'YYYY/MM/DD HH24:MI:SS') AS custom_format;
-- Résultat : '2023/10/09 15:45:30'

-- formatage pour interface utilisateur 
SELECT TO_CHAR(NOW(), 'DD "octobre" YYYY année') AS user_friendly_date;
-- Résultat : '09 octobre 2023 année'

```

### TO_DATE() 

Cette fonction fait l'inverse. Elle prends une chaîne et la convertir en `DATE`. 

```sql
TO_DATE(value, format)
```

- `value` : la chaîne
- `format`: la chaîne qui décrit le format de ma chaîne

```sql
-- conversion d'une date string en date table 
SELECT TO_DATE('01-10-2023', 'DD-MM-YYYY') AS date_value;
-- Résultat : '2023-10-01' (type : DATE)
```

```sql
-- comparer une date string en date table 
SELECT *
FROM appointments
WHERE appointment_date = TO_DATE('2023-10-09', 'YYYY-MM-DD');
```

```sql
-- insertion d'une saisie user 
CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    order_date DATE
);

-- Insertion de données avec conversion de chaîne en date
INSERT INTO orders (order_date)
VALUES (TO_DATE('10-09-2023', 'MM-DD-YYYY'));
```

---

## INTERVAL 

Type de donnée spécial utiliser pour représenter des périodes de temps. Ce type ne stocke par une date ou une heure précise, mais décrit une durée type "2 jours", "3 heures"

```sql
INTERVAL 'nombre unité_de_temps'

-- exemple
INTERVAL '2 days' -- Deux jours
INTERVAL '3 hours' -- Trois heures
INTERVAL '15 minutes' -- Quinze minutes
INTERVAL '1 day 2 hours' -- Un jour et deux heures
```

Postgres gère pleins de type de temps différents que l'on peut combiner dans une même expression :
- `year`
- `month`
- `day`
- `hour`
- `minute`
- `second`

### Ajouter un intervalle à une date 

```sql
SELECT CURRENT_DATE + INTERVAL '7 days' AS delivery_date;
-- On obtient la date dans 7 jours à partir d’aujourd’hui
```

### Soustraire des intervalles d'une date/heure 

```sql
SELECT NOW() - INTERVAL '2 hours' AS two_hours_ago;
-- On obtient l’heure qu’il y a deux heures
```

### Calculer la différences entre deux tables 

```sql
SELECT '2023-10-15'::DATE - '2023-10-01'::DATE AS days_difference;
-- Combien de jours entre deux dates
```

### Calculer la durée d'une tâche 

```sql
CREATE TABLE tasks (
    task_id SERIAL PRIMARY KEY,
    task_name TEXT,
    start_time TIMESTAMP,
    duration INTERVAL
);

INSERT INTO tasks (task_name, start_time, duration)
VALUES
('Préparer le rapport', '2023-10-01 09:00:00', INTERVAL '4 hours'),
('Tester l’application', '2023-10-01 10:00:00', INTERVAL '2 hours 30 minutes');

SELECT task_name,
       start_time,
       start_time + duration AS end_time
FROM tasks;
```

### Comparer les durées d'exécution

```sql
SELECT task_name
FROM tasks
WHERE duration < INTERVAL '3 hours';
```

---

## DATE_TRUNC() 

Permet de tronquer des valeurs temporelles en unité de temps précise. Par exemple, pour arrondir un timestamp au début du jour, du mois, de l'année, de l'heure, etc 

```sql
DATE_TRUNC(field, source)
```

- `field`: unité de temps à laquelle on souhaite couper la date 
- `source`: valeur temporelle que l'on veux tronquer.

```sql
SELECT DATE_TRUNC('day', TIMESTAMP '2023-10-01 15:30:45');
-- Résultat : 2023-10-01 00:00:00
```