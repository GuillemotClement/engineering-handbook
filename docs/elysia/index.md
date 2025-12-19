# Elysia

## Router

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
