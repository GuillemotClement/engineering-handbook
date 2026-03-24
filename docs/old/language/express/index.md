# Express 

## Convention de nommage

| Action | Router | Controller | Service | Repository |
| ------ | ------ | ---------- | ------- | ---------- | 
| Créer | `POST /clubs` | `create` | `createClub` | `create` |
| Liste un | `GET /clubs/:id` | `getById` | `getClubById` | `findById` |
| Lister all | `GET /clubs` | `getAll` | `getAllClubs` | `findAll` |
| Update | `PATCH /clubs/:id` | `update` | `updateClub` | `update` |
| Delete | `DELETE /clubs/:id` | `delete` | `deleteClub` | `delete` |

## MVC 

### Repository 

C'est dans cette couche que sont réalisé les requête vers la base de données.

### Service

C'est dans cette couche que sont réalisées les traitement métiers.

### Controllers

C'est dans cette couche qu'est réaliser la récupérer des données depuis la requête, et l'envoie de la réponse vers le front end.

```tsx
// gestion d'un get all()
async getAll(_req: Request, res: Response) {
  try {
    const clubs = await clubService.getAll();

    // on itère sur les données pour préparer le bon objet de response -> simplifier dans le cas du listing all
    const response = clubs.map((club) => ({
      id: club.id,
      name: club.name,
      image: club.image,
      city: club.city,
    }));

    return res.status(200).json(response);
  } catch (err: unknown) {
    console.error("Erreur récupération des clubs :", err);
    return res
      .status(500)
      .json({ error: "Erreur lors de la récupération des clubs" });
  }
},
```

### Router

C'est ici que les différentes endpoints de l'api sont déclaré.

#### Routeur multi file 

Dans un nouveau fichier, on viens déclarer notre routeur pour un module.

```ts
// router/userRouter.ts
import express from "express";

// déclaration d'une instance du routeur exporter
export const userRouteur = express.Router();

// déclaration d'une route. La path étant définis dans au niveau supérieur
userRouter.get("/", userController);
```

On viens ensuite le connecter sur le routeur principal de l'application.

```ts
const express from "express";
const app = express(); // création de l'instance express
import { userRouter } from "./router/userRouter.ts";

// on viens connecter le sous routeur. Pour accéder aux route, on viens utiliser la path /user
app.use('/user', userRouter);
```

## Middleware

Les middleware permette d'ajouter un traitement sur une requête. Il permettent d'ajouter de la donnée sur l'objet `req` par exemple (user est authentifié, on ajoute son id dans un middleware).

Express fournis des middleware permettant d'ajouter des fonctionnalitées.

### Morgan 

Ce middleware permet de récupérer des infos sur les requêtes reçu par le serveur.