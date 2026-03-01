# React 

## COMPOSANT 

### Early return 

```jsx
const Statistics = ({ stats }) => {
	const { good, neutral, bad, total, average, positif } = stats;

	// si on rentre dans la condition, on retourne directment le jsx
	if (total === 0) {
		return <p>No feedback given</p>;
	}

	return (
		<>
			<Result text="good" result={good} />
			<Result text="neutral" result={neutral} />
			<Result text="bad" result={bad} />
			<Result text="all" result={total} />
			<Result text="average" result={average} />
			<Result text="positif" result={positif} />
		</>
	);
};
```

---

## HOOK

### useState 

Permet à un composant React de garder en mémoire une valeur.
Cette valeur n'est jamais remplacer directement, on la remplace.

C'est la mémoire vide du composant, il permet à l'interface d'afficher des données. 

Le state est stocké par React, et permet de garder cette valeur tant que la page n'est pas reload par exemple. A chaque changement de state, c'est uniquement le composant qui est reconstruit.

```jsx
const [varialbe, setVariable] = useState(valueInitial);
```

- `variable` : nom de la variable qui contient la valeur. Initialiser avec la valeur initial définis dans la méthode `useState()`
- `setVariable`: fonction qui peux modifier la valeur du state 
- `valueInitial` : valeur initial de `variable `

Par exemple, dans ce composant, on viens créer plusieurs compteurs permettant d'afficher des informations.

On créer des handler pour chaque bouton, permettant de mettre à jours les valeurs de state `good`, `neutral` et `bad`.

A chaque clique sur les boutons, on viens mettre à jour les state associé. 

On retrouve également 3 variables qui contiennent le résultats d'opération lié au state.

```jsx
const App = () => {
	const [good, setGood] = useState(0);
	const [neutral, setNeutral] = useState(0);
	const [bad, setBad] = useState(0);

	const handleClickGood = () => {
		setGood(good + 1);
	};
	const handleClickNeutral = () => {
		setNeutral(neutral + 1);
	};
	const handleClickBad = () => {
		setBad(bad + 1);
	};

	const total = good + neutral + bad;

	const average = total === 0 ? 0 : (good - bad) / total;

	const positif = total === 0 ? 0 : (good / total) * 100;

	return (
		<div>
			<Title text="give feedback" />
			<div className="">
				<Button text="good" onClick={handleClickGood} />
				<Button text="neutral" onClick={handleClickNeutral} />
				<Button text="bad" onClick={handleClickBad} />
			</div>
			<Title text="statistics" />
			<div className="">
				<Result text="good" result={good} />
				<Result text="neutral" result={neutral} />
				<Result text="bad" result={bad} />
				<Result text="all" result={total} />
				<Result text="average" result={average} />
				<Result text="positif" result={positif} />
			</div>
		</div>
	);
};
```

---

## Array 

### forEach - itérer un tableau 

Prends une callback, et exécute une instruction sur chaque éléments du tableau.

```js
const t = [1, -1, 3]; // déclaration du tableau 

console.log(t.length) // 3 est affiché
console.log(t[1])     // -1 est affiché => affiche le deuxième élément

// parcours le tableau, et execute l'instruction pour chaque élément
t.forEach(value => {
  console.log(value)  // les chiffres 1, -1, 3, 5 sont affichés, chacun sur une ligne
})
```

### concat() - ajouter un éléments dans un tableau 

Avec React, on utilise des structures de données immuables. Pour cette raison, on priviligie cette méthode pour "ajouter" des éléments à un tableau.

Cette méthode créer un nouveau tableau dans lequel le contenu de l'ancien tableau et le nouvel élément sont inclus. La méthode retourne ce nouveau tableau 

```js
const t = [1, -1, 3]

const t2 = t.concat(5)

console.log(t)  // [1, -1, 3] est affiché => premier tableau
console.log(t2) // [1, -1, 3, 5] est affiché => second tableau 
```

### map() - Itérer un tableau 

Permet de parcourir les éléments d'un tableau

La méthode créer un nouveau tableau pour lequel la fonction donnée en paramètre est utilisée pour créer des éléments. 

```js
const t = [1, 2, 3]

const m1 = t.map(value => value * 2) // multipli chaque valeur du tableau
console.log(m1)   // [2, 4, 6] est affiché
```

On peut également l'utiliser pour transformer le tableau 

```js
const m2 = t.map(value => '<li>' + value + '</li>')
console.log(m2)  
// [ '<li>1</li>', '<li>2</li>', '<li>3</li>' ] est affiché
```

On peut donc l'utiliser pour itérer un tableau, et simplifier le passage des props à des composants enfant. 

Par exemple, on as  un tableau d'objet. 
```jsx
const parts = [
	{
		name: "Fundamentals of React",
		exercises: 10,
	},
	{
		name: "Using props to pass data",
		exercises: 7,
	},
	{
		name: "State of a component",
		exercises: 14,
	},
];
```

Ce tableau, est ensuite envoyer à un composant enfant, qui est chargé d'afficher des composant enfants, permettant d'afficher les données.

Il est également nécessaire de passer un attributs `key` pour que react gère l'affiche. Il vaut mieux utiliser un id, cette valeur doit être unique.

```jsx
// dans le compposant App 
return (
	<div>
		<Header course={course} />
		<Content parts={parts} /> // on passe le tableau d'objet 
		<Total total={total} />
	</div>
);
```

On distribue ensuite les données aux enfants :
```jsx
// il reçoit via l'objet props, le tableau d'objet parts 
const Content = (props) => {
	return (
		<>
			// on itérère avec .map pour envoyer chaque objet au composant enfants
			{props.parts.map((part) => (
				<Part part={part} key={part.name} />
			))}
		</>
	);
};

// dans le composant enfant on affiche ensuite les données 
const Part = (props) => {
	return (
		<p>
			{props.part.name} {props.part.exercises}
		</p>
	);
};
```

###  reduce() 

La méthode prend un tableau de plusieurs valeurs, et permet de réduire à une seule et unique valeur. Si la valeur intial n'est pas fournis, Js utilise la valeur du premier élément du tableau comme valeur initial de `accu`.

```js
tableau.reduce((accu, current) => {
  // logique ici
}, valeurInitiale)
```

- `accu` : c'est la variable qui stocke l'ensemble des valeurs. Celle qui est retourner à la fin 
- `current`: contient l'objet ou l'élément du tableau sur lequel on itère 
- `valeurInitiale`: la valeur qui sera contenu dans `accu` au début de l'opération.

Par exemple, on as un tableau d'objet. On souhaite faire le cumul des valeurs pour obtenir un total :

```js
const parts = [
	{
		name: "Fundamentals of React",
		exercises: 10,
	},
	{
		name: "Using props to pass data",
		exercises: 7,
	},
	{
		name: "State of a component",
		exercises: 14,
	},
];
```

On peut utiliser la méthode `reduce`, pour itérer le tableau d'objet, et cumuler chaque valeur pour obtenir le total 

```js
const total = parts.reduce((accu, current) => accu + current.exercises, 0);
```

### déstructuration 

La déstructuration permet de récupérer simplement des éléments d'un array. 

```js
const t = [1, 2, 3, 4, 5]

const [first, second, ...rest] = t; // destructuration

console.log(first, second)  // 1 2 est affiché
console.log(rest)          // [3, 4, 5] est affiché
```

Les variables `first` et `second` viennent récupérer les deux première valeurs du tableau. Les restes est groupé dans la variable `rest`.