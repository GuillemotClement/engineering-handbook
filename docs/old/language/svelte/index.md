# Svelte 

## COMPOSANT

Svelte permet de déclarer des composants. Chaque composant combine du HTML, du JavaScript et du CSS.

Il possède une extension en `.svelte`, par exemple `App.svelte`.

---
### Script 

Les balises `<script>` dans un composant permettent d'ajouter du JavaScript (déclaration de variable, fonction, ..) 

```ts
<script lang="ts">
	let name = 'Svelte'; 
</script> 

<h1>Hello {name.toUpperCase()}</h1>
```

- `{}` : permet d'afficher une valeur dans le HTML. Il est possible d'ajouter n'importe quel instruction javascript.

Il est également possible d'utiliser une variable dans le HTML avec les `{}`.

```ts 
<script>
	let src = '/tutorial/image.gif';
	const name = "Zebi";
</script>

<img src={src} alt="{name} dances."/>
```

Il existe une syntaxe courte :
```ts
<img {src} alt="{name} dances." />
```

### Style 

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

### Import de composant 
Il est possible de déclarer de petit composants et de les importer dans d'autre composants.
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

### HTML brut 

Par défaut, Svelte protège automatiquement contre le HTML. Il ne sera pas interprété, et il est affiché tel quel.

Dans le cas ou l'on souhaite insérer un contenu qui doit être interété, on utilise `{@html ...}`

```ts
<script>
  let html = "<strong>Bonjour</strong> le monde";
</script>

<p>{@html html}</p> 
```

Dans ce cas, bien penser à sécuriser ce contenu pour éviter des faille de sécurité.

## REACTIVITE 
Permet d'ajouter de la réactivité. Permet de synchroniser le DOM avec le state de l'application, par exemple, pour réagir à un événement.
### $state
Cette rune permet de déclarer une variable réactive. Cette variable est automatiquement mis à jour et géré par svelete.

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

Svelte utilise un proxie, et viens faire les opérations sur ce proxy pour ne pas affecter l'objet initial.

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

### proxy 

Svelte utilise des proxy pour gérer les valeurs réactive. 

Lorsque l'on créer une valeur réactive avec `$state`, Svelte transforme l'objet ou le tableau en Proxy.

Ce proxy surveille chaque modification pour mettre à jour l'interface.

#### snapshot()

Permet de créer une copie figée et brute des données réactive. 

Il permet de transformer le proxy en objet javascript standard.

1. L'objet issue n'est plus lié à l'état original, si le `$state` d'origine est modifé, le snap ne changeras pas.
2. Il descend dans toute la structure de l'objet pour transformer en objet JS.
3. Il est utilisé avant d'envoyer des données à une API, de les sauvegarder dans le `localStorage`, ou faire une comparaison.

```ts
function addNumber() {
	numbers.push(numbers.length + 1);
	console.log($state.snapshot(numbers));
}
```

#### inspect 

Permet de faire un "console.log" d'un proxy
```ts

function addNumber() {
	numbers.push(numbers.length + 1);
	console.log($state.snapshot(numbers));
}

$inspect(numbers);
```

On peut venir customiser comment l'information est afficher avec `$inspect().with(fn)`

```ts
$inspect(numbers).with(console.trace);
```

### $effect 

Un état n'est réactif que si quelque chose le déclenche. Svelte gère automatiquement ceux ci pour mettre à jour le DOM, mais il est possible de déclarer ses propres effets.

La rune `$effect` permet de créer ses propres effets.

Par exemple, on souhaite utilise `setInterval` pour suivre la durée pendant laquelle le composé est monté 

```ts
<script lang="ts">
	let elapsed = $state(0);
	let interval = $state(1000);

	$effect(() => {
		setInterval(() => {
			elapsed += 1;
		}, interval);
	});
</script>
```

Lorsque l'on utilise un `$effect`, il est également important d'ajouter une étape de nettoyage. 

La fonction de nettoyage est appelée immédiatement avant la ré exécution  de la fonction d'effet, et lorsque le composant est détruit.

Si la fonction d'effet ne lit aucun état lors de son exécution, elle ne s'exécute qu'une fois lorsque le composant est monté.

Les effets ne s'exécutent pas pendant le rendu serveur.

```ts
$effect(() => {
	const id = setInterval(() => {
		elapsed += 1;
	}, interval);

	return () => {
		clearInterval(id);
	};
});
```

### Etat globale
Il est possible de créer des runes en dehors d'un composant, pour partager un état globale par exemple.

## PROPS

Les props sont les valeurs transmises d'un composant parent vers un composant enfant.
### $props
On vient déclarer la props dans le composant enfant avec la rune `$props`.

```ts
// App.svelte
<script>
	import Nested from './Nested.svelte';
</script>

// on passe la valeur en props au composant enfant 
<Nested answer={42} />
```

```ts
//Compteur.svelete
<script>
	let { answer } = $props();
</script>

<p>The answer is {answer}</p>
```

Dans le composant enfant, on récupère la valeur `42` provenant du parent.

Il est possible de déclarer une valeur par défaut 

```ts
// App.svelte 
<script>
	import Nested from './Nested.svelte';
</script>

<Nested answer={42} />
<Nested />
```

Et dans le composant enfant, on déclare la valeur par défaut. Dans le deuxième composant, on aura la valeur par défaut d'afficher.
```ts
<script>
	let { answer = 'nikoumouk' } = $props();
</script>

<p>The answer is {answer}</p>
```

#### spread props 

Lorsque l'on souhaite passer plusieurs props dans un composant enfant, il est possible d'utiliser un objet pour le stocker, et de toutes les passer d'un coup avec le spread.
```ts
<script>
	import PackageInfo from './PackageInfo.svelte';
	
	const pkg = {
		name: 'svelte',
		version: 5,
		description: 'blazing fast',
		website: 'https://svelte.dev'
	};
</script>

<PackageInfo
// on passe l'ensemble des propriétés de l'objet pkg
	{...pkg}
/>
```

Dans le composant enfant, on viens toujours déclarer l'ensemble des props récupéré 
```ts
<script>
	let { name, version, description, website } = $props();
	// on peut utiliser le destructuring 
	let stuff = $props() // stuff permet d'accéder ensuite au données
	// on déclarer direct une variable et le reste dans un objet
	let { name, ...stuff } = $props()
</script>

<p>
	The <code>{name}</code> package is {description}. Download version {version} from
	<a href="https://www.npmjs.com/package/{name}">npm</a> and <a href={website}>learn more here</a>
</p>
```

---
## LOGIC 

### if, else
Svelte permet d'ajouter des condition directement dans le HTML.
```ts
<script>
	let count = $state(0);

	function increment() {
		count += 1;
	}
</script>

<button onclick={increment}>
	Clicked {count}
	{count === 1 ? 'time' : 'times'}
</button>

{#if count > 10}
	<p>{count} is greater than 10</p>
{:else if count < 5}
	<p>{count} is less than 5</p>
{:else}
	<p>{count} is between 5 and 10</p>
{/if}
```

### each 

Permet de parcourir un liste de donnée.
```ts
<script>
	const colors = ['red', 'orange', 'yellow', 'green', 'blue', 'indigo', 'violet'];
	let selected = $state(colors[0]);
</script>

<div>
	{#each colors as color}
		<button
			style="background: {color}"
			aria-label={color}
			aria-current={selected === color}
			onclick={() => selected = color}
		></button>
	{/each}
</div>
```

On peut utiliser un index en second argument de l'itération 
```ts
<div>
	{#each colors as color, i}
		<button
			style="background: {color}"
			aria-label={color}
			aria-current={selected === color}
			onclick={() => selected = color}
		>{i + 1}</button>
	{/each}
</div>
```

#### Lier chaque bloc 

Par défaut, Svelte peut avoir un comportement bizarre.

Par exemple, on supprime le premier élément, Svelte garde physiquement le composant 1 sur l'écran mais il change le `name` par celui du 2.

Pour éviter ce problème, on peut ajouter une "clé" pour donner un nom unique à chaque élément.

```ts
{#each things as thing (thing.id)}
  <Thing name={thing.name}/>
{/each}
```

Avec cette clé, Svelte comprends que l'objet avec l'id 1 est supprimé, et il supprime réellement le composant du DOM.


### await 
Svelete permet de gérer des promesse directement dans le HTML 
```ts
<script>
	import { roll } from './utils.js';

	let promise = $state(roll());
</script>

<button onclick={() => promise = roll()}>
	roll the dice
</button>

{#await promise}
	<p>...rolling</p>
{:then number}
	<p>you rolled a {number}!</p>
{:catch error}
	<p style="color: red">{error.message}</p>
{/await}
```


---

## EVENT 

### Event DOM 
On peut ajouter des événements à surveiller en utilisant `on<event>`
```ts
<script>
	let m = $state({ x: 0, y: 0 });

	function onpointermove(event) {
		m.x = event.clientX;
		m.y = event.clientY;
	}
</script>

// syntaxe longue 
<div onpointermove={onpointermove}>
	The pointer is at {Math.round(m.x)} x {Math.round(m.y)}
</div>

// syntaxe courte 
<div {onpointermove}>
	The pointer is at {Math.round(m.x)} x {Math.round(m.y)}
</div>

<style>
	div {
		position: fixed;
		left: 0;
		top: 0;
		width: 100%;
		height: 100%;
		padding: 1rem;
	}
</style>
```

### inline handler 
On peut directement implémenter la fonction qui traite l'evenement sur l'élément 
```ts
<script>
	let m = $state({ x: 0, y: 0 });
</script>

<div
	onpointermove={(event) => {
		m.x = event.clientX;
		m.y = event.clientY;
	}}
>
	The pointer is at {Math.round(m.x)} x {Math.round(m.y)}
</div>

<style>
	div {
		position: fixed;
		left: 0;
		top: 0;
		width: 100%;
		height: 100%;
		padding: 1rem;
	}
</style>
```

### capturing
On peut définir l'evenement pendant son utilisation. Par exemple, pour déclencher des event pendant la saisie par l'utilisateur 
```ts
<div onkeydowncapture={(e) => alert(`<div> ${e.key}`)} role="presentation">
	<input onkeydowncapture={(e) => alert(`<input> ${e.key}`)} />
</div>
```

### Transmettre un event à un composant enfant 
Il est possible de passer des handler d'event à un composant de la même manière que pour une props.

On viens déclarer les props dans le composant enfant : 
```ts
<script>
	let { increment, decrement } = $props();
</script>

<button onclick={decrement}>-1</button>
<button onclick={increment}>+1</button>
```

Et dans le composant parent, on viens passer les handler 
```ts
<script>
	import Stepper from './Stepper.svelte';

	let value = $state(0);
</script>

<p>The current value is {value}</p>

<Stepper 
	increment={() => value += 1}
	decrement={() => value -= 1}
	/>
```













## FETCH 

Svelte utilise un système de proxy pour stocker les données dans le `$state`. 
### $inspect


```ts
<script lang="ts">
  import { onMount } from "svelte";

  type Project = {
    id: string;
    title: string;
  }
// =============================================
// STATE ====================================
// =============================================
let projects = $state<Project[]>([]);
let loading = $state(true);
let error = $state("");

// =============================================
// Montage du composant ====================================
// =============================================
onMount(async() => {
  loadProjects();
})

// =============================================
// FETCH ====================================
// =============================================
async function loadProjects(){
  try{
    const response = await fetch('http://localhost:3000/projects');
    
    if(!response.ok){
      console.error(response);
      throw new Error("failed to fetch data");
    }
    
    projects = await response.json();
  }catch(err){
    error = err instanceof Error ? err.message : "Erreur inconnue";
  }finally{
    loading = false;
  }
}
$inspect(projects); // afficher les données stocker
</script>
```
