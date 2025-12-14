# Tanstack Router 

Permet de définir le router d'une application React. 

## Création d'une route 

```tsx
const indexRoute = createRoute({
  // on définit le layout parent 
  getParentRoute: () => rootRoute,
  // on définit l'url pour accéder au composant 
  path: "/",
  // on définit le composant à accéder 
  component: () => <Homepage />
})
```

- `path` : si on utilise un autre composant que le `rootRoute`, le path se cumule.