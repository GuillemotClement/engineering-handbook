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

