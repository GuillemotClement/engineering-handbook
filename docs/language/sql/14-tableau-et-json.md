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


