# Tanstack Form

- [x] : prolème avec Biome pour les noChildProp qui reste présent même avec la règle => priviliéger l'implémentation du snipper pour éviter le soucis avec biome ou eslint
- [x] : problème avec le typage entre Zop et TS où j'ai pas trouvé de solution => définir le typage de retour cf le snippet [issue](https://github.com/colinhacks/zod/issues/4938)
- [ ] : voir pour gérer les erreur retourner par le serveur => pas encore implémenter

## Déclarer un form

- `coerce`: utilisé dans le schema de Zod. Il permet de convertir le HTML dans le type souhaité. Le schema viens effectuer conversion, et on peut récupérer directement le bon type.

- Avec les nombre, il faut également venir définr une valeur minimal pour éviter le message d'erreur `NaN` si le champ est vide.

- Pour le typage, Zod fournit un typage en se basant sur le schema. Pour cela on utilise `z.infer`. Permet de typer les données issue du formulaire.

- Pour le cas des nombre, on viens ajouter un typage pour les valeurs saisis dans le formulaire, et éviter les problème avec TS.

```tsx
import { type AnyFieldApi, useForm } from "@tanstack/react-form";
import { useState } from "react";
import { unknown, z } from "zod";
import FormFieldInfo from "../../../components/Form/FormFieldInfo";

const schema = z.object({
  age: z.coerce // conversion HTML => zod
    .number<string>() // on attend un type Number
    .min(13, { error: "L'âge est requis et doit être > 0" }) // valeur min autorisé => permet d'éviter le message NaN
    .max(120, { error: "Age invalide" }), // valuer max autoriser
});

type FcmFormData = z.infer<typeof schema>; // typage des données retourné par le formulaire

// typage des valeurs par défaut.
const defaultValues = {
  age: "" as string | number, // pour les nombre il est nécessaire d'utiliser ce typage.
};

export default function FcmPage() {
  // on déclare le hook permettant de gérer le formulaire
  const form = useForm({
    defaultValues, // valeur par défaut avec le typage
    validators: {
      // on viens définir l'action qui déclenche la validation Zod.
      // il est possible d'utiliser d'autre event.
      // @ts-expect-error => on ignore la rèlge TS parce que j'ai pas trouvé comment résoudre le soucis.
      onChange: schema,
    },
    onSubmit: ({ value }) => {
      // code exécuter lorsque le formulaire est soumis.
      const data: FcmFormData = schema.parse(value);
      // Ici, value.age est garanti d'être un Nombre validé
      console.log(data.age); // on obtient bien un number ici
    },
  });

  return (
    <>
      <form
        onSubmit={(e) => {
          e.preventDefault();
          e.stopPropagation();
          form.handleSubmit();
        }}
        className="w-80 md:w-150"
      >
        <h2>Fréquence Cardique</h2>

        <form.Field name="age">
          {(field) => (
            <FormField
              field={field}
              isRequired={true}
              label="Âge"
              type="number"
            />
          )}
        </form.Field>

        <form.Subscribe
          selector={(state) => [state.canSubmit, state.isSubmitting]}
        >
          {([canSubmit, isSubmitting]) => (
            <FormSubscribe
              canSubmit={canSubmit}
              isSubmitting={isSubmitting}
              onReset={() => form.reset()}
            />
          )}
        </form.Subscribe>
      </form>
    </>
  );
}
```

Composant Form

```tsx
// composant title form
type FormTitleProps = {
  title: string;
};

export default function FormTitle({ title }: FormTitleProps) {
  return <h2 className="text-center text-2xl font-bold">{title}</h2>;
}

// ======================================================================
// composant du champs du formulaire
import type { AnyFieldApi } from "@tanstack/react-form";
import FormFieldInfo from "./FormFieldInfo";
import FormInput from "./FormInput";

type FormFieldProps = {
  field: AnyFieldApi;
  label: string;
  isRequired: boolean;
  type: string;
};

export default function FormField({
  field,
  label,
  isRequired,
  type,
}: FormFieldProps) {
  return (
    <div className="my-5 w-full">
      <label className="input w-full" htmlFor={field.name}>
        <span className="label">
          {label}{" "}
          {isRequired && (
            <span className="text-red-500 font-bold text-xs">*</span>
          )}
        </span>
        <FormInput field={field} type={type} />
      </label>
      <FormFieldInfo field={field} />
    </div>
  );
}

// ====================================================================
// composant input
import type { AnyFieldApi } from "@tanstack/react-form";

type FormInputProps = {
  field: AnyFieldApi;
  type: string;
};

export default function FormInput({ field, type }: FormInputProps) {
  return (
    <input
      type={type}
      id={field.name}
      name={field.name}
      value={field.state.value}
      onBlur={field.handleBlur}
      onChange={(e) => field.handleChange(e.target.value)}
    />
  );
}
// ================================================================
// composant display error
import type { AnyFieldApi } from "@tanstack/react-form";

type FormFieldInfoProps = {
  field: AnyFieldApi;
};

// TODO: voir pour une refacto du code fournis par Gemini

export default function FormFieldInfo({ field }: FormFieldInfoProps) {
  const { isTouched, errors, isValidating } = field.state.meta;
  const hasErrors = isTouched && errors.length > 0;

  return (
    <div className="text-center mt-2">
      {hasErrors ? (
        <em
          role="alert"
          style={{ color: "red", fontSize: "0.875rem", display: "block" }}
        >
          {errors
            .map((error) => {
              // Si l'erreur est un objet (Zod), on affiche .message
              // Si c'est déjà une string, on l'affiche directement
              return typeof error === "object" && error !== null
                ? error.message
                : error;
            })
            .join(", ")}
        </em>
      ) : null}

      {isValidating ? (
        <span style={{ fontSize: "0.875rem", color: "blue" }}>
          Validation...
        </span>
      ) : null}
    </div>
  );
}

// ==============================================================
// composant formAction
type FormSubscribeProps = {
  canSubmit: boolean;
  isSubmitting: boolean;
  onReset: () => void;
};

export default function FormSubscribe({
  canSubmit,
  isSubmitting,
  onReset,
}: FormSubscribeProps) {
  return (
    <div className="flex justify-center gap-x-5">
      <button type="reset" onClick={onReset} className="btn btn-neutral">
        Effacer
      </button>

      <button type="submit" disabled={!canSubmit} className="btn btn-primary">
        {isSubmitting ? "..." : "Calculer"}
      </button>
    </div>
  );
}
```


