# Drizzle 

## Seeding 

Drizzle permet de créer des seed pour fournis des données initiale à la base de donnée.

Dans une nouveau fichier `src/db/seed.ts`, on viens définir les données initiale à stocker dans la base de données.

```ts
import { db } from "../libs/drizzle";
import { sport } from "./schema";

async function main() {
	console.log("Début de seeding ....");

	const dataToInsert = [
		{ name: "Vélo sur route" },
		{ name: "Trail" },
		{ name: "VTT" },
		{ name: "Athlétisme" },
		{ name: "Cyclo-cross" },
		{ name: "Triathlon" },
	];

	await db.insert(sport).values(dataToInsert).onConflictDoNothing();

	console.log("Sedding terminé !");
	process.exit(0);
}

main().catch((err) => {
	console.error(err);
	process.exit(1);
});
```

On viens également ajouter un script dans le `package.json` afin de lancer le seed et avoir des données initial dans la base de données.

```json
"seed": "tsx --env-file=.env src/db/seed.ts",
```

## Typage 

Drizzle fournis des utiliaires pour typer automatiquement les insertions et les select d'un enregistrement en se basant sur le schema de table.

```ts
// typage pour un select
export type Club = InferSelectModel<typeof club>;
// typage pour une insertion
export type NewClub = InferInsertModel<typeof club>;
```

---

## Select 

### isNull
```ts
export const clubRepository = {
	async getClubs() {
		const result = await db.select().from(club).where(isNull(club.deletedAt));
		return result ?? null;
	},
};
```
