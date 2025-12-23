# Drizzle 

## Typage 

Drizzle fournis des utiliaires pour typer automatiquement les insertions et les select d'un enregistrement en se basant sur le schema de table.

```ts
// typage pour un select
export type Club = InferSelectModel<typeof club>;
// typage pour une insertion
export type NewClub = InferInsertModel<typeof club>;
````

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
````
