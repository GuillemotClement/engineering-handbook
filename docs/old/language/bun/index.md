# Bun 

## Nouveau projet 

```shell
# lance le script de création d'un nouveau projet
bun create <project_name>

# une fois généré
cd <project>
# installation des dépendances
bun install 
# lancement du script bun run dev
```

En choisissant le template avec `Hono`, la configuration initial sera directement mis en place.

### Lint 

```shell
# installation d'eslint 
bun install -g eslint 

# ajout des règles pour Hono 
bun install --dev @hono/eslint-config
```

On viens ensuite ajouter le ficher de config pour `eslint`.

```cjs
//.eslint.cjs 
module.exports = {
	extends: ["@hono/eslint-config"]
}
```
## Commande 

```shell
bun run <name_script>
```
