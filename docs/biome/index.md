# Biome 

Remplace `esLint` et `Prettier`.


## Tailwind 

Pour ne pas avoir d'erreur avec Tailwind et le plugin `DaisuUi`, il faut modifier la config, en ajoutant une règle : 

```json
{
  "$schema": "https://biomejs.dev/schemas/2.3.8/schema.json",
  //rules
  "css": {
    "parser": {
      "tailwindDirectives": true
    }
  }
}
```
