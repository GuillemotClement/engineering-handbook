# TypeScript 

## satisfies

Cet opérateur permet de vérifier qu'une valeur respect un type sans perdre l'inférence précise de Ts.

```ts
type User = {
  id: number
  role: "admin" | "user"
}

const user = {
  id: 1,
  role: "admin",
} satisfies User
```

Ici, TS viens vérifier que l'objet respect `User` et il conserve le type précis inféré.

Résultat `user.role` est de type `"admin"`, mais il garantit que l'objet est compatible avec `User`