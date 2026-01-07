# Tanstack Table 

Librairie permettant de gérer facilement la création de tableau.

## Définir un tableau

Dans un premier temps, il faut venir récupérer des données qui seront ensuite affiché dans le tableau. Par exemple, des données en dur pendant le dévelopement

```tsx
const data = [
	{
		id: 1,
		name: "Cyclo Club Melrandais",
		city: "Melrand",
		sport: "trail",
		image: "logo_test.png",
	},
	{
		id: 2,
		name: "ACPB",
		city: "Baud",
		sport: "vélo",
		image: "logo_test.png",
	},
	{
		id: 1,
		name: "AMSELF Trail",
		city: "Fréjus",
		sport: "trail",
		image: "logo_test.png",
	},
];
```

On viens ensuite ajouter le typage des données qui seront passé dans le tableau, puis on créer l'instance du tableau.

```tsx 
// déclaration du typage
type ClubDataProps = {
	id: number;
	name: string;
	city: string;
	sport: string;
	image: string;
};

// instanciation du helper
const columnHelper = createColumnHelper<ClubDataProps>();
```

On viens ensuite définir les cellules du tableau.

```tsx
const columns = [
	columnHelper.accessor("image", {
		header: () => <span>Logo</span>,
		cell: () => (
			<img
				src={logo}
				alt="logo"
				style={{ width: 40, height: 40, objectFit: "contain" }}
			/>
		),
	}),
	columnHelper.accessor("name", {
		header: () => <span>Nom</span>,
		cell: (info) => info.getValue(),
	}),
	columnHelper.accessor("city", {
		header: () => <span>Ville</span>,
		cell: (info) => info.getValue(),
	}),
	columnHelper.accessor("sport", {
		header: () => <span>Sport</span>,
		cell: (info) => info.getValue(),
	}),
	columnHelper.accessor("id", {
		header: () => <span>Action</span>,
		cell: (info) => (
			<Link
				to={"/clubs/$clubId"}
				params={{ clubId: String(info.getValue()) }}
				className="btn"
			>
				Détails
			</Link>
		),
	}),
];
```

- `accessor`: permet de définir quel clé sera ensuite passer dans la cellule. En premier argument, on passe la clé.
- `header`: permet de déclarer le nom header de la colonne 
- `cell`: permet de définir la cellule. 
- `info.getValue()`: cette méthode permet de récupérer la valeur qui correspond à la clé passer à l'accessor.


Une fois le tableau définis, on viens le déclarer dans le composant : 

```tsx
export default function ClubListe() {
  // déclaration du tableau. On lui passe les donnée + la définir du tableau 
	const table = useReactTable({
		data,
		columns,
		getCoreRowModel: getCoreRowModel(),
	});

	return (
		<div className="container mx-auto bordel ">
			<h2 className="text-center text-2xl font-bold my-10">Liste des clubs</h2>
			<table className="table w-100 mx-auto">
				<thead>
					{table.getHeaderGroups().map((headerGroup) => (
						<tr key={headerGroup.id}>
							{headerGroup.headers.map((header) => (
								<th key={header.id} className="text-center">
									{header.isPlaceholder
										? null
										: flexRender(
												header.column.columnDef.header,
												header.getContext(),
											)}
								</th>
							))}
						</tr>
					))}
				</thead>
				<tbody>
					{table.getRowModel().rows.map((row) => (
						<tr key={row.id}>
							{row.getVisibleCells().map((cell) => (
								<td key={cell.id} className="text-center">
									{flexRender(cell.column.columnDef.cell, cell.getContext())}
								</td>
							))}
						</tr>
					))}
				</tbody>
			</table>
		</div>
	);
}
```