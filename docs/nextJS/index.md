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