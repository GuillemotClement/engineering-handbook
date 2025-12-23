# Elysia

## Request

### Récupérer le body 

Dans le controller, on viens déstructurer l'objet `body` dans les arguments de la fonction.

```ts
import { Context } from 'elysia';

export const clubController = {
    // 👇 On déstructure 'body' ici
    async createClub({ body }: Context) {
        
        // Vous pouvez maintenant accéder aux propriétés
        // Note : Sans validation dans le routeur, body est de type 'unknown'
        console.log(body); 
        
        // Exemple d'utilisation avec votre service
        // return await clubService.create(body); 
    }
};
```

Dans le router, il est nécessaire de venir définir la structure. Ce schéma est est passé en 3eme argument du `post()`.

```ts
import { Elysia, t } from 'elysia';
import { clubController } from './club.controller';

export const clubRouter = new Elysia({ prefix: "/club" })
    .post(
        "/", 
        clubController.createClub, // 👈 Votre controller
        {
            // 👇 Le schéma de validation
            body: t.Object({
                name: t.String(),
                city: t.String(),
                sport: t.String(),
                // Vos autres champs...
            })
        }
    );
```



## Router

## Définir une route 

```ts
import { Elysia } from "elysia";

new Elysia()
  .get('/', 'Hello World!')
  // permet de passer un paramètre dynamique
  .get('/id/:id', ({ params: { id } }) => id)
  .listen(3000)
```

### Créer des sous routeur

```ts
// router/authRouter.ts
import Elysia from "elysia";

export const authRouter = new Elysia({ prefix: "/auth" }).get(
  "/",
  "auth module"
);
```

- `prefix`: définis le endpoint pour ce groupe de route

On définis ensuite les différentes routes et on les lie au controlleur.

On viens ensuite ajouter cette instance de routeur dans le routeur principale.

```ts
import { Elysia } from "elysia";
import { authRouter } from "./router/router";

const app = new Elysia()
  .get("/", () => "Hello Elysia")
  // on ajoute le sous retour
  .use(authRouter)
  .listen(3000);

console.log(
  `🦊 Elysia is running at ${app.server?.hostname}:${app.server?.port}`
);
```
