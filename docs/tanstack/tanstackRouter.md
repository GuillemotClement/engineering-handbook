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

```tsx linenums="1"
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
```

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
```

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


## Utiliser des paramètre dynamique

On commence par déclarer une nouvelle route qui prend un paramètre dynamique.

```tsx 
export const detailClubPage = createRoute({
	getParentRoute: () => rootRoute,
	path: "/clubs/$clubId",
	component: () => <ClubDetail />,
});
```

- `$clubId`: c'est le paramètre dynamique. 

Dans le composant, on pourras récupérer cette donnée en venant récupérer la route, et en extrayant la donnée de l'objet.
```tsx 
import { detailClubPage } from "../../router/router";

type ClubDetailProps = {};

export default function ClubDetail({}: ClubDetailProps) {
  // ici on récupère le paramètre. 
	const { clubId } = detailClubPage.useParams();

	console.log(clubId);

	return <div>ClubDetail</div>;
}
```

## Routeur multi-file 

On déclare le `rootRoute` dans un fichier séparer 

```tsx 
//router/root.tsx
import { createRootRoute } from "@tanstack/react-router";
import RootLayout from "../layouts/RootLayout";

export const rootRoute = createRootRoute({
	component: () => <RootLayout />,
});
```

On déclare ensuite un nouveau fichier qui contient les différentes routes d'un sous routeur 
```tsx 
//router/toolsRoute.tsx
import { createRoute } from "@tanstack/react-router";
import ConvertorPage from "../pages/Tools/Convertor/ConvertorPage";
import FcmPage from "../pages/Tools/FCM/FcmPage";
import PurcentPage from "../pages/Tools/Purcent/PurcentPage";
import ToolsPage from "../pages/Tools/ToolsPage";
import VmaPage from "../pages/Tools/VMA/VmaPage";
import { rootRoute } from "./root";

// définition du parent de la section. On l'exporte car c'est lui qu'on rattache au routeur principal
export const toolsRoute = createRoute({
	getParentRoute: () => rootRoute,
	path: "/tools",
	component: () => <ToolsPage />,
});

// définition des enfants. Leur layout se base sur la parent.
const fcmPage = createRoute({
	getParentRoute: () => toolsRoute,
	path: "/fcm",
	component: () => <FcmPage />,
});

const vmaPage = createRoute({
	getParentRoute: () => toolsRoute,
	path: "/vma",
	component: () => <VmaPage />,
});

const purcentPage = createRoute({
	getParentRoute: () => toolsRoute,
	path: "/purcent",
	component: () => <PurcentPage />,
});

const convertorPage = createRoute({
	getParentRoute: () => toolsRoute,
	path: "/convertor",
	component: () => <ConvertorPage />,
});

// on attache les enfants au parent
toolsRoute.addChildren([fcmPage, vmaPage, purcentPage, convertorPage]);
```

On viens ensuite assembler le tout dans le fichier `router.tsx`
```tsx
import { createRoute, createRouter } from "@tanstack/react-router";

import LoginPage from "../pages/Auth/LoginPage";
import RegisterPage from "../pages/Auth/RegisterPage";
import ClubDetail from "../pages/Club/ClubDetail";
import ClubForm from "../pages/Club/ClubForm";
import ClubPage from "../pages/Club/ClubPage";
import Homepage from "../pages/Homepage/Homepage";
import { rootRoute } from "./root";
import { toolsRoute } from "./toolsRoutes";

const indexRoute = createRoute({
	getParentRoute: () => rootRoute,
	path: "/",
	component: () => <Homepage />,
});

// Auth ======================================
const loginPage = createRoute({
	getParentRoute: () => rootRoute,
	path: "/login",
	component: () => <LoginPage />,
});

const registerPage = createRoute({
	getParentRoute: () => rootRoute,
	path: "/register",
	component: () => <RegisterPage />,
});

// Club =========================================
const clubPage = createRoute({
	getParentRoute: () => rootRoute,
	path: "/clubs",
	component: () => <ClubPage />,
});

const createClubPage = createRoute({
	getParentRoute: () => rootRoute,
	path: "/create-club",
	component: () => <ClubForm />,
});

export const detailClubPage = createRoute({
	getParentRoute: () => rootRoute,
	path: "/clubs/$clubId",
	component: () => <ClubDetail />,
});

const routeTree = rootRoute.addChildren([
	indexRoute,
	loginPage,
	registerPage,
	clubPage,
	createClubPage,
	detailClubPage,
	toolsRoute, // on ajoute le sous routeur
]);

export const router = createRouter({
	routeTree,
	defaultPreload: "intent",
	scrollRestoration: true,
});
```

De cette manière, le code est plus propre et plus simple à maintenir.

---

## Création d'un espace de nom d'url 

Cette technique permet de déclarer une route parent d'un ensemble de page. De cette manière, on déclare un pathname parent sur `club`. Par la suite les routes enfants, utiliseront cette url de base pour accéder au composant.

Dans le composant parent, si il n'y a pas de layout commun entre les route enfant, on returne directement le composant `<Outlet />` qui se charge d'afficher le composant parent.

Il faudras forcément utiliser un parent qui retourne un composant `<Outlet />`

```tsx 
import { createRoute, Outlet } from "@tanstack/react-router";
import LoginPage from "../pages/Auth/LoginPage";
import RegisterPage from "../pages/Auth/RegisterPage";
import { rootRoute } from "./root";

// on créer la route de base
const authBaseRoute = createRoute({
	getParentRoute: () => rootRoute,
	path: "/auth",
	// on utilise <Outlet /> pour directement afficher la route enfant
	component: () => <Outlet />,
});

// on créer les routes enfants
const loginRoute = createRoute({
	getParentRoute: () => authBaseRoute,
	path: "/login",
	component: () => <LoginPage />,
});

const registerRoute = createRoute({
	getParentRoute: () => authBaseRoute,
	path: "/register",
	component: () => <RegisterPage />,
});

// on créer une nouvelle variable qui exporte le vrai routeur pour TS.
export const authRouter = authBaseRoute.addChildren([
	loginRoute,
	registerRoute,
]);
```