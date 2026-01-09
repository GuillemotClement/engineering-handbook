# Svelte 

## Composant

Svelte permet de déclarer des composants. Chaque composant combine du HTML, du JavaScript et du CSS.

Il possède une extension en `.svelte`, par exmple `App.svelte`.

## Script 

Les balises `<script>` dans un composant permettent d'ajouter du JavaScript (déclaration de variable, fonction, ..) 

```ts
// App.svelte
<script lang="ts">
	let name = 'Svelte'; 
</script> 

<h1>Hello {name.toUpperCase()}</h1>
```

- `{}` : permet d'afficher une valeur dans le HTML. Il est possible d'ajouter n'importe quel javascript.

## Attribut dynamique 

On peut venir déclarer une variable contenant la valeur d'un attribut. 

```ts 
<script>
	let src = '/tutorial/image.gif';
	const name = "Zebi";
</script>

<img src={src} alt="{name} dances."/>
// syntaxe courte 
<img {src} alt="{name} dances."/>

```

## Style 

Dans chaque composant, on peut y ajouter du CSS. Les règles de styles sont spécifique au composants.

```ts
<p>This is a paragraph.</p> 

<style> 
	p { 
		color: goldenrod; 
		font-family: 'Comic Sans MS', 
		cursive; font-size: 2em; 
	} 
</style>
```

## Nesting de composant 

Il est possible de déclarer de petit composants et de les importants dans d'autre composants.

### Import de composant 

Dans la partie `JS` du composant, on peut venir déclarer l'importe du composant nécessaire.

```ts
<script lang="ts"> 
	import Nested from './Nested.svelte'; // import d'un composant
</script>
```

On peut ensuite venir inclure ce composant dans la partie HTML 

```ts
<p>This is a paragraph.</p> 
<Nested />
```

## HTML brut 

Par défaut, Svelte protège automatiquement contre le HTML. Il ne sera pas interprété, et il est affiché tel quel.

Dans le cas ou l'on souhaite insérer un contenu qui doit être interété, on utilise `{@html ...}`

```ts
<script>
  let html = "<strong>Bonjour</strong> le monde";
</script>

<p>{@html html}</p> 
```

Dans ce cas, bien penser à sécuriser ce contenu pour éviter des faille de sécurité.


## Rune 

### $state

Permet de déclarer une variable réactive. Cette variable au automatiquement mis à jour et géré par svelete.

Un peu comme le `useState` de React mais en plus simple 

```ts
<script>
	let count = $state(0); // on déclare la variable réactive

	function increment() {
		count += 1;
	}

	function decrement(){
		count -= 1;
	}
</script>

<button onclick={increment}>
	Clicked {count}
	{count === 1 ? 'time' : 'times'}
</button>

<button onclick={decrement}>
	-
</button>
```

Les deux boutons permettent d'augmenter ou réduire la valeur du `count`.

On peut venir déclarer des tableaux de valeurs réactive : 

```ts
<script>
	let numbers = $state([1, 2, 3, 4]);

	function addNumber() {
		// on ajoute une valeur dans le tableau reactif
		numbers.push(numbers.length + 1);
	}
</script>

<p>{numbers.join(' + ')} = ...</p>

<button onclick={addNumber}>
	Add a number
</button>
```

La valeur original du tableau ne sera pas modifiée.

### $derived

Permet de définir une valeur calculée automatiquement à partir d'un state. On ne la modifie jamais directement et elle se met à jour lorsque les dépendances change.

C'est l'équivalent du `useMemo` de React.

```ts
<script>
	let numbers = $state([1, 2, 3, 4]);
	// on calcul sa valeur selon le résulat issue du state numbers
	let total = $derived(numbers.reduce((t, n) => t + n, 0));
	
	function addNumber() {
		numbers.push(numbers.length + 1);
	}

</script>

// on affiche le total
<p>{numbers.join(' + ')} = {total} </p>

<button onclick={addNumber}>
	Add a number
</button>

```