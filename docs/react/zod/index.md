# Zod 

## Valider des number 

Il existe un soucis entre Zod et Tanstack Form. Il faut utiliser une regex puis convertir lors de la soumission du formulaire.

```tsx
const schema = z.object({
  age: z.string().regex(/^\d*$/, "La valeur doit être un nombre"),
})
```
