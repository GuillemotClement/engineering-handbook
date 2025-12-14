# Tanstack Router 

Permet de définir le router d'une application React. 

Il existe deux manière de définir un routeur, soit par le code, soit sur le file system. 

Par choix personnel, j'utilise le router basé sur le code, qui est plus explicite à mon sens.

--- 


## Déclaration d'un router 

Dans un premier temps, on créer un nouveau dossier `router`, celui ci sera dédié au différents fichier de routing.

```tree
src/
  layouts/
    RootLayout.tsx // layout principal de l'app 
  pages/ 
    Homepage/
      Homepage.tsx
  components/
    Header/
      Header.tsx
    Footer/
      Footer.tsx
  @types/
    router.ts // pour le typage du router 
  router/
    router.tsx // définition du router 
  main.tsx
```

Dans le fichier `router.tsx`, on viens déclarer notre routeur principale.

```tsx
import {
  createRootRoute,
  createRoute,
  createRouter,
} from "@tanstack/react-router";
import RootLayout from "../layouts/RootLayout";
import Homepage from "../pages/Homepage";

// création de la route parent du projet 
// ici on définit un composant qui gère le layout principal de l'application 
const rootRoute = createRootRoute({
  component: () => <RootLayout />,
});

// création d'une route => celle ci correspond à la homepage 
const indexRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: "/",
  component: () => <Homepage />,
});

// on viens ajouer la route sur le router 
const routeTree = rootRoute.addChildren([indexRoute]);

// on exporte l'instance du router 
export const router = createRouter({
  routeTree, // on passe l'arbre des routes 
  defaultPreload: "intent", // puis différents paramètre pour la configuration 
  scrollRestoration: true,
});
```

Il faut ensuite venir déclarer le typage dans `@types/router.ts` 

```ts
import type { router } from "../router/router";

declare module "@tanstack/react-router" {
  interface Register {
    router: typeof router;
  }
}
````

On viens ensuite ajouter notre routeur sur notre `main.tsx` 

```tsx
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import "./index.css";
import { RouterProvider } from "@tanstack/react-router";
import { router } from "./router/router";

createRoot(document.getElementById("root") as HTMLElement).render(
  <StrictMode>
    <RouterProvider router={router} /> // on passe notre router
  </StrictMode>,
);
````

On utiliseras ce composant pour définir le layout principal `layouts/RootLayout.tsx` 

```tsx
import { Outlet } from "@tanstack/react-router";
import { TanStackRouterDevtools } from "@tanstack/react-router-devtools"; // pour le debug

export default function RootLayout() {
  return (
    <div className="min-h-screen">
      <header>header</header>
      <div>
        <Outlet /> // permet d'afficher le composant enfant
      </div>
      <TanStackRouterDevtools />
      <footer>footer</footer>
    </div>
  );
}
```

Puis il reste plus qu'a définir le composant `Homepage` 

```tsx
export default function Homepage() {
  return <div>Homepage</div>;
}
```

De cette manière, on obtient un router basique, qui permet de gérer la navigation.

---
