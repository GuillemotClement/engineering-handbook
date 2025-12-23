# Express 

## Routeur 

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