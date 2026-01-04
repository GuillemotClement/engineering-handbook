## Connexion au serveur Postgres

```shell
psql -U <username>
```

Une fois entré, on renseigne le mot de passe.

Une fois connecté, on peut taper des commandes et communiquer avec le serveur.

Par défaut, le serveur utilise le port `5432`. Pour vérifier quel app utilise ce port sur la machine on peut utiliser cette commande 

```shell
# linux 
sudo netstat -tuln | grep 5432

# window
netstat -ano | find "5432"
```

### Vérifier la connexion au serveur 

Pour vérifier si la connexion est correct avec le serveur PG, on peut utiliser une requête simple 

```sql
SELECT version();
```

### Trouver le port utiliser par le serveur 

On peut également utiliser une requête permettant de voir le port utiliser par le serveur.

```sql
SELECT name, setting
FROM pg_settings
WHERE name = 'port';
```

### Obtenir la date et l'heure actuel 

```sql
SELECT now();

-- retourner une string
SELECT NOW()::text();
```
## Composant principaux de PG

### Serveur postgres

C'est le coeur de PG, il s'occupe :
- Traiter les requêtes des clients
- Gérer les données: lecture, écriture, mise à jour
- Assurer la sécurité et l'intégrité des données.

Lorsque le serveur est lancer, il est prête à recevoir des requêtes des clients.

#### Modifier le port du serveur 

Dans le fichier `postgresql.cong`, il faut venir modifier la ligne : 

```
#port = 5432
```

Une fois le port modifier, venir relancer le serveur.

#### Reset le password 

Ouvrir le fichier `pg_hba.conf` et remplacer la ligne 
```md
host    all             all             127.0.0.1/32            md5

# par

host    all             all             127.0.0.1/32            trust
```

Cela permet de désactiver temporairement la vérification du mot de passe.

Redémarrer ensuite le serveur, et se connecter à la db sans mot de passe. On peut ensuite venir changer le mot de passer avec cette commande 
```shell
ALTER ROLE postgres PASSWORD 'new_password';
```

On viens ensuite remettre le fichier de configuration dans son état initial et on redémarre le serveur.

#### logs

Les logs du serveur sont stocker dans le dossier `data`de PG `/var/log/postgresql`


### Clients

C'est le programme qui se connectent au serveur et lui envoient des requête SQL.

- `psql`: CLI permettant de communiquer avec le serveur.
- `pgAdmin`: interface graphique permettant de gérer le serveur. 
