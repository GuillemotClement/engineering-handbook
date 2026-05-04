# Next JS 

## Style 

Pour appliquer du style dans un projet **NextJS**, on peut utiliser Tailwind, ou bien les module CSS.

### Style global 

Le fichier `/app/ui/global.css` est le fichier utilisé pour définir le style global du projet.
Celui ci est par convention importé dans le fichier root layout `/app/layout.tsx`.

Pour importer le fichier global css :
```tsx
// importation du css
import '@/app/ui/global.css';
 
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

Lors de la création du projet, le script demande si on souhaite utiliser `Tailwind`. Si on répond oui, alors il sera automatiquement fournis avec et configurer.

### Module CSS 

Il est possible d'utiliser des modules CSS. Pour créer des modules, on viens les ajouter dans le dossier `/app/ui`.
Par convention, on utilise `<file>.module.css` et on ajoute dans ce fichier les règles CSS.

```css
/* /app/ui/home.module.css */
.shape {
  height: 0;
  width: 0;
  border-bottom: 30px solid black;
  border-left: 20px solid transparent;
  border-right: 20px solid transparent;
}
```

On peut ensuite venir ajouter cette classe sur un élément pour appliquer le style souhaité.

```tsx
// /app/page.tsx
import styles from '@/app/ui/home.module.css';

// ...
<div className={styles.shape}
```

### Style conditionnel 

On utilise la lib [clsx](https://www.npmjs.com/package/clsx) pour définir des styles conditionnel.

Par exemple, on souhaite appliquer un style selon la valeur de `status` qui est passer en props au composant. Le status peut être `pending` ou `paid`.

```tsx
// importation de la lib 
import clsx from 'clsx';
 
export default function InvoiceStatus({ status }: { status: string }) {
  return (
    <span
      className={clsx(
        'inline-flex items-center rounded-full px-2 py-1 text-sm',
        {
          'bg-gray-100 text-gray-500': status === 'pending',
          'bg-green-500 text-white': status === 'paid',
        },
      )}
    >
    // ...
)}
```

---

## Font et Image

### Font 

Next gère automatiquement l'optimisation des polices dans l'application lorsque l'on utilise le module `next/font`.

Pour ajouter une nouvelle police, on commence par créer un nouveau fichier `font.ts` dans le dossier `/app/ui`.

Dans ce fichier, on vient importer la police que l'on souhaite utiliser.

```ts
import { Inter } from 'next/font/google';
 
export const inter = Inter({ subsets: ['latin'] });
```

Dans le fichier `/app/layout.tsx`, on vient importer la nouvelle police, et on l'ajoute sur le `<body>`.

```tsx
// /app/layout.tsx
import { inter } from '@/app/ui/fonts';
// ....
<body className={`${inter.className} antialiased`}>{children}</body>
```

La police sera appliquer sur tous les composant du projet.

On pourras également ajouter d'autre police, et les appliquer sur d'autres éléments de la même manière.

### Image

Next peut servir des image statique comme des images depuis le dossier `/public`. 

Le composant `<Images>` permet gère automatiquement l'optimisation des images (responsive, taille d'image selon les clients, préviens le shifting, et lazy load).

Les bonne pratique indique de set les attribut `height` et `width` afin de définir l'espace dédié pour cette image.

```tsx
// /app/page.tsx
// importation de l'image 
import Image from "next/image";

// ajout de l'image dans le composant 
<Image className="hidden md:block"  
       src="/hero-desktop.png"  
       width={1000}  
       height={760}  
       alt="Screenshots of the dashboard project showing desktop version"  
/>
```

---

## Layout et Pages

Next utilise le routage baser sur les dossiers. Chaque dossier représente un segment de route de l'url.

![[Pasted image 20260325191315.png]]

On peut venir créer un fichier `layout.tsx` et `pages.tsx` dans chaque dossier.

`page.tsx` est un fichier spécial qui exporte un composant React. Il est requis pour qu'une route soit accessible.

### Page

Pour créer des routes nested, on peut venir créer un fichier `page.tsx` dans chaque dossier.

![[Pasted image 20260325191707.png]]

Dans cet exemple, `/app/dashboard/page.tsx` est associer à l'url `/dashboard`.

Dans le nouveau fichier `page.tsx`, on viens créer un nouveau composant 

```tsx
export default function Page(){  
    return <p>Dashboard Page</p>  
}
```

### Layout 

Le fichier `layout.tsx` permet de partager un layout pour plusieurs pages.

Par exemple, le layout pour le dashboard, qui sera partager au différents page nested

```tsx
import SideNav from "@/app/ui/dashboard/sidenav";

export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <div className="flex h-screen flex-col md:flex-row md:overflow-hidden">
      <div className="w-full flex-none md:w-64">
        <SideNav />
      </div>
      <div className="grow p-6 md:overflow-y-auto md:p-12">{children}</div>
    </div>
  );
}

```

La layout reçoit une prop `children`. Elle permet de passer le composant pour le rendre dans le layout. 
Ici, il permet de passer la page nested.

Tous les `page` seront rendu à la place de la props `children`. Les page au même niveau, ainsi que les pages nested.

![[Pasted image 20260325195550.png]]
![[Pasted image 20260325195606.png]]

---

## Navigation 

Next fournis un composant `<Link>` permettant de gérer la navigation entre les différentes pages.

```tsx
// /app/ui/nav-links.tsx
import {
  UserGroupIcon,
  HomeIcon,
  DocumentDuplicateIcon,
} from "@heroicons/react/24/outline";
import Link from "next/link";

// Map of links to display in the side navigation.
// Depending on the size of the application, this would be stored in a database.
const links = [
  { name: "Home", href: "/dashboard", icon: HomeIcon },
  {
    name: "Invoices",
    href: "/dashboard/invoices",
    icon: DocumentDuplicateIcon,
  },
  { name: "Customers", href: "/dashboard/customers", icon: UserGroupIcon },
];

export default function NavLinks() {
  return (
    <>
      {links.map((link) => {
        const LinkIcon = link.icon;
        return (
          <Link
            key={link.name}
            href={link.href}
            className="flex h-[48px] grow items-center justify-center gap-2 rounded-md bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600 md:flex-none md:justify-start md:p-2 md:px-3"
          >
            <LinkIcon className="w-6" />
            <p className="hidden md:block">{link.name}</p>
          </Link>
        );
      })}
    </>
  );
}
```

### Active link 

La hook `usePathname()` permet de check le path. Pour pouvoir utiliser le hook, il est nécessaire de passer le composant en composant client.  

La directive `use client` permet cela.

```tsx
// /app/ui/nav-links.tsx

// passe le composant en client 
'use client';

// import du hook 
import { usePathname } from "next/navigation";

// stockage dans la variable du hook
export default function NavLinks(){
	const pathname = usePathname();
	// ....
}
```

On peut ensuite utiliser `clsx` pour ajouter un style conditionel pour les liens active, c'est à dire, le style du lien sur lequel le client est situé.

Lorsque `link.href` match avec le `pathname`, alors on applique sur le `<Link >`.

```tsx
import clsx from "clsx";

// utilisation du style conditonel 
<Link
	key={link.name}
	href={link.href}
	className={clsx(
	  "flex h-[48px] grow items-center justify-center gap-2 rounded-md bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600 md:flex-none md:justify-start md:p-2 md:px-3",
	  {
		"bg-sky-100 text-blue-600": pathname === link.href,
	  },
	)}
>
	<LinkIcon className="w-6" />
	<p className="hidden md:block">{link.name}</p>
  </Link>
```

### Route dynamique

Pour créer une page qui dépend d'un paramètre, par exemple affiché les détails d'une ressource, on utilise un nouveau dossier avec en nom, le nom du paramètre qui sera passé de manière dynamique. Par exemple ` app/notes/[id]`, ou ` id` sera le nom du paramètre.
Dans ce nouveau dossier, on déclare le fichier `page.tsx` et on peut venir récupérer la valeur du paramètre 

```tsx
import { notFound } from "next/navigation";

const NotePage = async ({ params }: { params: Promise<{ id: string }> }) => {
  const { id } = await params; // on récupère la valeur issue de l'url
  const note = getNoteById(Number(id));

  if (!note) {
    notFound(); // déclenche l'affichage de la page notFound()
  }

  return (
    <div>
      <h2>{note.content}</h2>
      <p>{note.important ? "Important" : "Not important"}</p>
    </div>
  );
};

export default NotePage;

```

Dans le dossier ` services`, on vient y ajouter la méthode permettant de récupérer la ressource 

```ts
export const getNoteById = (id: number) => {
  return notes.find((note) => note.id === id);
};
```


On peut ensuite définir le lien permettant d'accéder à la page 

```tsx
<li key={note.id}>
	<Link href={`/notes/${note.id}`}>{note.content}</Link>
	{note.content} {note.important && <strong>(important)</strong>}
  </li>
```



---

## Fetch data 

Dans Next, on peut venir créer des endpoint API en utilisant des **Route Handlers**. 

Pour pouvoir utiliser des fonction permettant de fetch des données, il faut que le composant soit **server componant**.
Il suffit de ne pas ajouter le `use client`

```tsx
import { Card } from "@/app/ui/dashboard/cards";
import RevenueChart from "@/app/ui/dashboard/revenue-chart";
import LatestInvoices from "@/app/ui/dashboard/latest-invoices";
import { lusitana } from "@/app/ui/fonts";
import { fetchRevenue, fetchLatestInvoices, fetchCardData } from "../lib/data";

export default async function Page() {
  const revenue = await fetchRevenue();
  const latestInvoices = await fetchLatestInvoices();
  const {
    numberOfInvoices,
    numberOfCustomers,
    totalPaidInvoices,
    totalPendingInvoices,
  } = await fetchCardData();

  return (
    <main>
      <h1 className={`${lusitana.className} mb-4 text-xl md:text-2xl`}>
        Dashboard
      </h1>
      <div className="grid gap-6 sm:grid-cols-2 lg:grid-cols-4">
        <Card title="Collected" value={totalPaidInvoices} type="collected" />
        <Card title="Pending" value={totalPendingInvoices} type="pending" />
        <Card title="Total Invoices" value={numberOfInvoices} type="invoices" />
        <Card
          title="Total Customers"
          value={numberOfCustomers}
          type="customers"
        />
      </div>
      <div className="mt-6 grid grid-cols-1 gap-6 md:grid-cols-4 lg:grid-cols-8">
        <RevenueChart revenue={revenue} />
        <LatestInvoices latestInvoices={latestInvoices} />
      </div>
    </main>
  );
}
```

Toutes les requêtes permettant de récupérer les données sont effectuer dans un fichier dédié pour alléger le composant.

---

## Architecture 

Le fichiers contenant les fonction permettant de gerer les request, sont placer dans le folder `app/services/{name_entite}`.
Toutes la logique des composants sera placer dedans pour garder des composants propre. Il contient la logique responsable de l'acces aux donnes.

Dans le fichier `app/services/notes.ts`, qui gere la logique lier au note, on retrouve ce  code qui gere la logique par exemple.

```ts
const notes = [
  {
    id: 1,
    content: "next.js utilizes React Server Components",
    important: true,
  },
  { id: 2, content: "next.js is built on top of React", important: true },
  {
    id: 3,
    content: "next.js supports both static and dynamic rendering",
    important: false,
  },
];

let nextId = 4;

export const getNotes = () => {
  return notes;
};

export const addNote = (content: string, important: boolean) => {
  notes.push({ id: nextId++, content, important });
};
```

De cette maniere, le code responsable du composant reste propre 
```tsx
import { getNotes } from "../services/notes";

const Notes = () => {
  const notes = getNotes();

  return (
    <div className="">
      <h2>Notes</h2>
      <ul>
        {notes.map((note) => (
          <li key={note.id}>
            {note.content} {note.important && <strong>(important)</strong>}
          </li>
        ))}
      </ul>
    </div>
  );
};

export default Notes;

```

---

## FORM 

Next permet de gerer les formulaire avec des `Server Actions` qui est une fonction qui run sur le server mais qui peut etre appeler directement depuis le formulaire dans le navigateur.

Ces fonctions seront placer dans le folder `app/actions/{ressource_name}.ts`

```ts
"use server"

import { redirect } from "next/navigation" // pour la redirection
import { addNote } from "../services/notes" // appel des fonction

export const createNote = async (formData: FormData) => {
// acces aux donnees du formulaire
// recupere depuis l'attribut name du l'input dans le formulaire
  const content = formData.get("content") as string
  const important = formData.get("important") === "on"
  addNote(content, important)
  
    // reload le cache pour afficher les nouvelles notes => production
  revalidatePath("/notes");
  
  redirect("/notes") // redirection
}
```

A chaque fois qu'il y une modification de donnees realiser par une fonction server action, il est necessaire de venir recharger le cache pour update les donnes en production. En dev, cette action est faite automatiquement.

Dans le formulaire, il faudra faire référence a la fonction 

```tsx
import { createNote } from "./actions" // HIGHLIGHT LINE

const NewNote = () => {
  return (
    <div>
      <form action={createNote}>
        <div>
          <label>
            Content
            <input type="text" name="content" required />
          </label>
        </div>
        <div>
          <label>
            <input type="checkbox" name="important" />
            Important
          </label>
        </div>
        <button type="submit">Create</button>
      </form>
    </div>
  )
}

export default NewNote
```

Lorsque le formulaire est soumis, les donnes sont envoyer via une requete POST a la fonction action et la page est updated

### Update 

Pour faire un toggle, on peut utiliser ce code 

```tsx
<div>
      <h2>{note.content}</h2>
      <p>{note.important ? "Important" : "Not important"}</p>
      <form action={toggleNoteImportance}>
        <input type="hidden" name="id" value={note.id} />
        <button type="submit">
          {note.important ? "Mark as not important" : "Mark as important"}
        </button>
      </form>
    </div>
```

On passe l'id de la note avec un input hidden. Le formulaire envoie ensuite vers la méthode dans les `actions`

```ts
export const toggleNoteImportance = async (formDate: FormData) => {
  const id = Number(formDate.get("id"));
  toggleImportance(id);
  revalidatePath(`/notes/${id}`);
  revalidatePath("/notes");
};
```

La fonction appelle ensuite la fonction dans le `service`

```ts
export const toggleImportance = (id: number) => {
  const note = notes.find((note) => note.id === id);
  if (note) {
    note.important = !note.important;
  }
};edsz
```

## React Hook Form 

```shell
npm i react-hook-form @hookform/resolvers zod date-fns
```

- ` react-hook-form` : pour gérer le state des formulaires et la validation 
- `@hookform/resolves` : connecter Zod au React Hook Form 
- `zod` : gère la validation
- `date-fns` : manipulation et formatage des date (date picker)

### Validation 

Pour déclencher la validation, il existe 4 cas différents :
- `onSubmit` : par défaut, a utiliser pour le login et validation 
- `onChange` : a chaque appuie sur une touche 
- `onBlur` : lorsque le focus part de l'input => idéal pour les form 
- `onTouched` : validation à la première fois où l'on quitte l'input puis validation en live pendant que l'on tape 
Ce comportement est définis dans la configuration du hook 

```tsx
const form = useForm({
	mode: 'onBlur',
	// ...
})
```

### Fonctionnement 

Le hook permet de gérer facilement un formulaire.
- `register()` retourne une `ref` que RHF attache au input 
- les valeurs sont stocké dans le RHF store interne
- Pas de re-rendu sur les saisis 
- Re-render seulement sur les validation d'erreur qui change, à la soumission ou à l'utilisation de `watch()`

#### register() & Controller()

- `register()` : pour les input natif (`input`, `textarea`, `select`). Syntaxe simplifié 
- `Controller()` : pour les composants custom et controlled

Généralement, les deux types sont utilisé dans un formulaire 

```tsx
function HybridForm() {
  const { register, control, handleSubmit } = useForm()

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      {/* Native input - use register() */}
      <input {...register('email')} placeholder='Email' />

      {/* Custom date picker - use Controller */}
      <Controller
        name='birthDate'
        control={control}
        render={({ field }) => <DatePicker {...field} />}
      />

      {/* Native textarea - use register() */}
      <textarea {...register('bio')} />

      {/* Custom select - use Controller */}
      <Controller
        name='country'
        control={control}
        render={({ field }) => <CustomSelect {...field} />}
      />
    </form>
  )
}
```

### Utilisation 
https://github.com/shadcnstudio/shadcn-forms-masterclass/blob/main/components/registration-form.tsx
#### 1. Définition du schéma Zod 

```tsx
const formSchema = z.object({
  // 1. Progressive String Validation - Multiple rules = specific errors
  fullName: z
    .string()
    .min(1, 'Full name is required.')        // Check exists first
    .min(3, 'Must be at least 3 characters.')  // Then check length
    .max(50, 'Must be at most 50 characters.'),
  // 2. Regex Validation - Complex patterns with clear messages
  password: z
    .string()
    .min(8, 'Password must be at least 8 characters.')
    .regex(
      /^(?=.*[a-z])(?=.*[A-Z])(?=.*\\d)/,
      'Must contain uppercase, lowercase, and number.'
    ),
  // 3. Type-Specific Validation - Date, Number, Enum
  dateOfBirth: z.date({
    required_error: 'Date of birth is required.'
  }),
  ageRange: z
    .number({ invalid_type_error: 'Must be a number.' })
    .int('Must be a whole number.')
    .min(18, 'Must be at least 18.'),
  accountType: z.enum(['personal', 'business', 'enterprise']),
  // 4. Array Validation - For multi-selects and checkboxes
  interests: z
    .array(z.object({ value: z.string(), label: z.string() }))
    .min(1, 'Select at least one.')
    .max(5, 'Max 5 selections.'),
  // 5. Optional Fields
  language: z.string().optional(),
  // 6. Boolean Fields
  emailNotifications: z.boolean()
  // ... +7 more fields (country, bio, newsletterTopics, etc.)
})

type FormValues = z.infer<typeof formSchema> // Auto-generated TypeScript types!
```

#### 2. Définition des options du formulaire pour un select 

Pour gérer les select, on déclare un array contenant les différentes options 

```tsx
const interestOptions: Option[] = [
  { value: 'technology', label: 'Technology' },
  { value: 'design', label: 'Design' },
  { value: 'programming', label: 'Programming' },
  { value: 'business', label: 'Business' },
  { value: 'marketing', label: 'Marketing' },
  { value: 'finance', label: 'Finance' },
  { value: 'health', label: 'Health & Wellness' },
  { value: 'education', label: 'Education' },
  { value: 'art', label: 'Art & Creativity' },
  { value: 'science', label: 'Science' }
]
```

#### 3. Initialisation du form 

```tsx
export function RegistrationForm() {
  const form = useForm<FormValues>({
    resolver: zodResolver(formSchema),  // Connect Zod validation
    defaultValues: {
      fullName: '',
      email: '',
      interests: [],
      emailNotifications: true,  // Smart defaults
      ageRange: undefined, // number
      interests: [], // select
      // ... all fields with initial values
    }
  })

  function onSubmit(data: FormValues) {
    console.log('Form submitted:', data)
  }

  return (
    <form onSubmit={form.handleSubmit(onSubmit)}>
      {/* Form fields */}
    </form>
  )
}
```

- `resolver` : permet de connecter le schéma de validation au formulaire 
- `defaultValues` : set les valeurs par défaut => controlled components
- `form.handleSubmit(onSubmit)` : validation avant d'appelle le submit

#### 4. Controller 

Chaque input utilise cette structure 

```tsx
<Controller
  name='fieldName'
  control={form.control}
  render={({ field, fieldState }) => (
    <Field data-invalid={fieldState.invalid}>
      <FieldLabel htmlFor={field.name}>Label</FieldLabel>
      <Input {...field} id={field.name} aria-invalid={fieldState.invalid} />
      <FieldDescription>Helper text</FieldDescription>
      {fieldState.invalid && <FieldError errors={[fieldState.error]} />}
    </Field>
  )}
/>
```

https://shadcnstudio.com/blog/react-hook-form-zod-shadcn-ui

#####  Input number 
```tsx
<Input type='number'
  onChange={e => field.onChange(
    e.target.value === '' ? undefined : parseInt(e.target.value, 10)
  )}
  value={field.value ?? ''}
/>
```
---

## PRODUCTION

```shell
pnpm build # permet de build le projet
pnpm start # lance le projet builder
```

Au lancement en production, le terminal affiche les page statique avec un `o`

