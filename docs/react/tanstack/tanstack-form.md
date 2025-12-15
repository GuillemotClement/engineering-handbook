# Tanstack Form 

## Conversion d'un input 

```tsx
// import de la lib zod pour la validation de formulaire
import { z } from "zod";

// déclaration du schema de validation du formulaire
const schema = z.object({
  // ajout d'un champ. La valeur est convertis en number
  age: z.string().transform((age) => Number(age)),
});

// typage du form 
const defaultValues: z.input<typeof schema> = {
  age: '13',
}

// déclaration du formulaire
const form = useForm({
  // on utilise l'objet typé de valeur par défaut
  defaultValues,
  // on définis le schema de validation
  validators: {
    onChange: schema, // prends sur le changement de valeur dans l'input 
  },
  // action déclencher à la soumission du formulaire
  onSubmit: ({ value }) => {
    // on pourras récupérer la valeur transformer via le schema de validation
    const result = schema.parse(value);
    const outputAge: number = result.age 
  }
})
```

## Messsge d'erreurs

[Documentation Error](https://tanstack.com/form/latest/docs/framework/react/guides/dynamic-validation#standard-schema-validation)