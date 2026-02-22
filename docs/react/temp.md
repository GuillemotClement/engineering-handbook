# React 

## Nouveau projet 

```bash
npm create vite@latest <name_projet> -- --template react
```

## COMPOSANT

### Définir un composant 

La première lettre d'un composant doit être en majuscule. 

Tous les éléments d'un composant doivent être inclus dans un élément parent.

```jsx
const App = () => {
  console.log('Hello from component')
  return (
    <div>
      <p>Hello world</p>
    </div>
  )
}
export default App
```

Un composant est une fonction JS qui retourne un composant.

On peut écrire n'importe quel instruction dans le composant, avant de le retourne.

Pour afficher le résultat d'une expression, on utilise les `{ }` avec l'expression Js dans le JSX 

```jsx
const App = () => {
  const now = new Date()
  const a = 10
  const b = 20

  return (
    <div>
      <p>Hello world, it is {now.toString()}</p>
      <p>
        {a} plus {b} is {a + b}
      </p>
    </div>
  )
}
```

### JSX 

Toutes les balises doivent être fermées 

```jsx
<br />
```

### Composant multiple 

```jsx
const Hello = () => {
  return (
    <div>
      <p>Hello world</p>
    </div>
  )
}

const App = () => {
  return (
    <div>
      <h1>Greetings</h1>

	  // appel du composant enfant 
      <Hello />
    </div>
  )
}
```

### Props 

Permet de transmettre de la données au composant enfant.

```jsx
const Hello = (props) => {  return (
    <div>
	    // on récupère la valeur via l'objet props passé en paramètre
	    // la clé correspond à l'argument passé par le parent
      <p>Hello {props.name}</p>    </div>
  )
}

const App = () => {
  return (
    <div>
      <h1>Greetings</h1>
      <Hello name="George" /> // transfmet la string à l'enfant
      <Hello name="Daisy" />
    </div>
  )
}
```

Le composant enfant prends un paramètre `props`. En argument, ce paramètre reçoit un objet qui, a des champs correspondant à toutes les props définis par l'utilisateur du composant.

On peut passer des expression JS, ou des valeurs en dur : 

```jsx
const Hello = (props) => {
	return (
		<div>
			<p>
				Hello {props.name}, you are {props.age} years old
			</p>
		</div>
	);
};

const App = () => {
	const name = "Peter";
	const age = 10;

	return (
		<div>
			<h1>Greeting</h1>
			<Hello name="George" age={26 + 10} />
			<Hello name={name} age={age} /> // on passe les variables
		</div>
	);
};

export default App;
```

### fragments 

Pour éviter d'ajour des éléments `<div>` sans intérêt, on peut venir utiliser des fragments

```jsx
const App = () => {
	const name = "Peter";
	const age = 10;

	return (
		<>
			<h1>Greeting</h1>
			<Hello name={name} age={age} />
			<Footer />
		</>
	);
};
```

---

## ARRAY

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

### map() - créer un nouveau tableau 

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






---

## OBJET

Les objets permettent de stocker des valeurs sous forme de clé valeur. 

```js
const object1 = {
  name: 'Arto Hellas',
  age: 35,
  education: 'PhD',
}

const object2 = {
  name: 'Full Stack web application development',
  level: 'intermediate studies',
  size: 5,
}

const object3 = {
  name: {
    first: 'Dan',
    last: 'Abramov',
  },
  grades: [2, 3, 5, 3],
  department: 'Stanford University',
}
```

Les valeurs peuvent être de n'importe quels types. On utilise la notation "point" ou crochets pour récupérer une valeur de l'objet.

```js
console.log(object1.name)         // Arto Hellas est affiché
const fieldName = 'age' 
console.log(object1[fieldName])    // 35 est affiché
```

On peut également ajouter de nouvelles valeurs à l'objet 

```js
object1.address = 'Helsinki'
object1['secret number'] = 12341
```

On utilise la notation crochet pour utiliser des variables, ou bien des nom avec des caractères spéciaux comme un espace.

### this 

Les fonctions fléchées et fonctions définies à l'aide du mot clé `function` varient en ce qui concerne leur comportement par rapport à `this`, qui fait référence à l'objet lui même.

On peut assigner des méthodes à un objet en définissant des propriétés qui sont des fonctions :

```js
const arto = {
  name: 'Arto Hellas',
  age: 35,
  education: 'PhD',
	// définition d'une méthode 
  greet: function() {
    console.log('hello, my name is ' + this.name)
  },
}

arto.greet()  // "hello, my name is Arto Hellas" est affiché
```

Les méthodes peuvent être affectées aux objets même après la création de l'objet 

```js
const arto = {
  name: 'Arto Hellas',
  age: 35,
  education: 'PhD',
  greet: function() {
    console.log('hello, my name is ' + this.name)
  },
}

// ajout d'une méthode
arto.growOlder = function() {
  this.age += 1
}

console.log(arto.age)   // 35 est affiché
arto.growOlder()
console.log(arto.age)   // 36 est affiché
```

On modife l'objet : 
```js
const arto = {
  name: 'Arto Hellas',
  age: 35,
  education: 'PhD',
  greet: function() {
    console.log('hello, my name is ' + this.name)
  },

  doAddition: function(a, b) {
    console.log(a + b)
  },
}

arto.doAddition(1, 4)        // 5 est affiché

const referenceToAddition = arto.doAddition
referenceToAddition(10, 15)   // 25 est affiché
```

Maintenant, l'objet à une nouvelle méthode `doAddition` qui calcule la somme des nombres donnés en paramètres. 
La méthode est appelé de manière habituel, en utilisant l'objet `arto.doAddition(1,4)`. ou en utilisant une référence de fonction.

Si on essaie de faire la même chose avec la méthode `greet()`, cela ne fonctionne pas.
```js
arto.greet()       // "hello, my name is Arto Hellas" est affiché

const referenceToGreet = arto.greet
referenceToGreet() // affiche "hello, my name is undefined"
```

Lors de l'appel via une référence, la méthode permet la connaissance de ce qu'était le `this` d'origine. 

La valeur de `this` est définie en fonction de COMMENT la méthode s'apelle.
Lors de l'appel de la méthode via une référence, la valeur de this devient l'objet globale, et le résultat final n'est pas celui attenud.

La situation conduisant à la disparition de `this` survient lorsqu'on définis un délais d'attente pour appeler la fonction `greet` en utilisant `setTimeout`

```js
const arto = {
  name: 'Arto Hellas',
  greet: function() {
    console.log('hello, my name is ' + this.name)
  },
}


setTimeout(arto.greet, 1000)
```

Lorsque `setTimeout` appelle la méthode, c'est le moteur JS qui appelle cette méthode, et dans ce cas, c'est l'objet global qui est référencer.

#### bind 

C'est le mécanisme qui permet de préserver le `this` original. 

```js
setTimeout(arto.greet.bind(arto), 1000)
```

L'appel de la méthode `arto.greet.bind(arto)` créer un nouvelle fonction ou `this` pointe vers Arto, indépendament de l'endroit et de la manière dont la méthode est appelée.

En utilisant des fonctions fléchées, il est possible de résoudre certain problème lié à `this`. Ils ne doivent pas être utilisés comme méthode pour les objets car alors `this` ne fonctionne pas du tout.

### spread 

Permet de traiter des objets. Il permet de prendre tous ce qu'il y a dans un objet, et de le copier dans un nouvel objet.

```jsx
const course = {
  name: "Half Stack",
  parts: 3
}

// Je veux créer un nouvel objet avec les mêmes infos + une nouvelle propriété
const updatedCourse = { ...course, exercises: 10 }

/* updatedCourse vaut :
{
  name: "Half Stack",
  parts: 3,
  exercises: 10
}
*/
```

Si on met une propriete apres le spread qui existe deja, elle est ecrasee. Cela permet de mettre a jour un objet.

```js
const user = { name: "John", age: 25 }
// on met a jour la props
const olderJohn = { ...user, age: 26 } // age passe de 25 à 26
const clone = { age: 30, ...user }    // age reste à 25 car le spread arrive APRÈS et écrase le 30
```

Le spread ne prends que le premier niveau.
```js
const school = {
  name: "Helsinki",
  stats: { students: 100 }
}

const newSchool = { ...school }
newSchool.stats.students = 200 // ⚠️ Ça modifie AUSSI l'objet original 'school' !
```





---
## CLASSE

En Js, il n'y a pas de mécanisme de classe comme en Java ou PHP. Il existe des fonctionnalité permettant de simuler des classes.

ES6 a introduit une syntaxe de classe, qui simplifie la définition des classes.

```js
class Person {
  constructor(name, age) {
    this.name = name
    this.age = age
  }
  greet() {
    console.log('hello, my name is ' + this.name)
  }
}

const adam = new Person('Adam Ondra', 35)
adam.greet()

const janja = new Person('Janja Garnbret', 22)
janja.greet()
```

Les classes sont généralement utilisé dans les anciens projet React, ou avec Node.



## FONCTIONS

```js
// déclaration de fonction => déclaration de fonction 
function product(a, b) {
  return a * b
}

const result = product(2, 6)
// result est maintenant 12

// ========================================

// expression de fonction 
const average = function(a, b){
	return (a + b) / 2
}

const result = average(2, 5)
// result est maintenant 3.5

// =======================================

// syntaxe fléché 
const sum = (p1, p2) => {
  console.log(p1)
  console.log(p2)
  return p1 + p2
}

// appel de fonction 
const result = sum(1, 5)
console.log(result)
```

Si la fonction n'as qu'un seul paramètre, on peut utiliser la syntaxe courte 
```js
const square = p => {
  console.log(p)
  return p * p
}
```

Si la fonction n'as qu'une seule expression, on peut encore réduire la déclaration. Le résultat de l'expression est automatiquement retourner.
```js
const square = p => p * p
```

Cette forme est généralement utiliser lors de la manipulation de tableau par exemple : 
```js
const t = [1, 2, 3]
const tSquared = t.map(p => p * p)
// tSquared est devenu [1, 4, 9]
```

### Fonction helpers

Ce sont des fonctions definie dans une autre fonction permettant de definir le comportement du composant.

```jsx
const Hello = (props) => {
// fonction helper
	const bornYear = () => {
		const yearNow = new Date().getFullYear();
		return yearNow - props.age;
	};

	return (
		<div className="">
			<p>
				Hello {props.name}, you are {props.age} years old
			</p>
			// on appelle directement la fonction qui retourne l'anne de naissance
			<p>So you were probably born in {bornYear()}</p>
		</div>
	);
};

const App = () => {
	const name = "Peter";
	const age = 10;

	return (
		<div>
			<h1>Greetings</h1>
			<Hello name="Maya" age={26 + 10} />
			<Hello name={name} age={age} />
		</div>
	);
};

export default App;
```

La logique pour deviner l'annee de naissance est separee dans une fonction qui est appelee lorsque le composant est rendu.

L'age n'as pas besoin d'etre passe en parametre de la fonction car elle peut acceder directement aux props passe au composant.


---

## Destructuration 

La destructuration permet de simplifier la recuperation des valeurs passer via les pros.

Etant donne que les props sont des objets fournis par le composant parent, il est possible de venir les destructurer pour recuperer directement les valeurs.

```jsx
props = {
	name: 'Artos Hellas',
	age: 35
}
```

On peut donc dans un premier temps, stocker les valeurs dans des variables dans le composant.

```jsx
const Hello = (props) => {
// on recupere les valeurs
  const name = props.name
  const age = props.age

  const bornYear = () => new Date().getFullYear() - age

  return (
    <div>

      <p>Hello {name}, you are {age} years old</p>
      <p>So you were probably born in {bornYear()}</p>
    </div>
  )
}
```

On peut egalement venir utiliser la syntaxe des fonctions fleche pour la fonction helpers.

```jsx
// syntaxe courte 
const bornYear = () => new Date().getFullYear() - age

// syntaxe normal
const bornYear = () => {
  return new Date().getFullYear() - age
}
```

On peut egalement simplifier la recuperation des variables, en utilisant la destructuration 

```jsx
const Hello = (props) => {
// destructuration 
  const { name, age } = props
  const bornYear = () => new Date().getFullYear() - age

  return (
    <div>
      <p>Hello {name}, you are {age} years old</p>
      <p>So you were probably born in {bornYear()}</p>
    </div>
  )
}
```

En utilisant le meme nom de variable que le nom de la cle de l'objet, la variable vient automatiquement recuprer cette valeur.

On peut egalement venir destructurer direcctement dans les argument du composant : 

```jsx
const Hello = ({ name, age }) => {
  const bornYear = () => new Date().getFullYear() - age

  return (
    <div>
      <p>
        Hello {name}, you are {age} years old
      </p>
      <p>So you were probably born in {bornYear()}</p>
    </div>
  )
}
```

Les props seront directement disponible dans les variables. Au lieu d'affecter l'integralite de l'objet props dans une variable, on attribue directmeent les valeurs de sprosp dans les variables en destructurant l'objet props passe a la fonction du composant

---

## Re-rendu de page 

### setInterval 

Cette fonction permet de faire une action tous les x temps.

```jsx
setInterval(() => {
	refresh()
	counter += 1
}, 1000)
```

Dans ce code, on viens augmenter la valeur de counter toutes les 1 seconde. 

## HOOK 

Les hook `useState` et `useEffect` ne doivent pas être appelée depuis l'intérieur d'une boucle, d'une expression conditionelle ou tout endroit qui n'est pas une fonction définissant un composant.

Cela permet d'assurer que les hooks sont toujours appelés dans le même ordre.

Les hook ne peuvent être appelés que depuis l'intérieur d'un corps de fonction qui définis un composant React.

```jsx
const App = () => {
  // ceci est ok
  const [age, setAge] = useState(0)
  const [name, setName] = useState('Juha Tauriainen')

  if ( age > 10 ) {
    // ceci ne marche pas!
    const [foobar, setFoobar] = useState(null)
  }

  for ( let i = 0; i < age; i++ ) {
    // toujours pas ok !
    const [rightWay, setRightWay] = useState(false)
  }

  const notGood = () => {
    // et ceci est presqu'un péché !
    const [x, setX] = useState(-1000)
  }

  return (
    //...
  )
}
```


### useState 

Permet de mettre a jour un etat avec React. 

```jsx
import { useState } from 'react' // import du hook 

const App = () => {
  const [ counter, setCounter ] = useState(0) // on declare un state 

	// viens incrementer le compteur toutes les secondes
  setTimeout(
  // fonction qui met a jour le state apres 1s
    () => setCounter(counter + 1), // on augmente la valeur du compteur a chaque seconde
    1000 // duree en ms
  )

  return (
    <div>{counter}</div> // rendu toutes les secondes 
  )
}

export default App
```

- `counter` : recupere la valeur initiale du state, ici 0 definis dans `useState()`
- `setCounter`: permet de mettre a jour la valeur de `counter`.

Lorsque la fonction `useState` est appele, React rend a nouveau le composant. A chaue modification avec la `setCounter`, cela declenche un nouveau rendu du composant.
La valeur de l'etat sera incrementer toutes les seconde jusqu'a ce que l'application se termine.

---

## gestion d'event 

Les event handler permette de surveiller des actions specifique d'un utilisateur, par exemple un clic.

```jsx
import { useState } from "react";

const App = () => {
	const [counter, setCounter] = useState(0);

// fonction handler
	const handleClick = () => {
		console.log("clicked");
	};

	return (
		<div>
			<div>{counter}</div>
			// ajout de l'event
			<button onClick={handleClick}>Plus</button>
		</div>
	);
};

export default App;
```

On definis `onClick` qui permet de surveiller l'evenement sur un element, ici le clic.

Lorsque le bouton est cliquer, cela declenche la fonction handle `handleClick` qui vient afficher dans la console.

On peut simplifier en definissant directement la fonction sur l'element 

```jsx
const App = () => {
  const [ counter, setCounter ] = useState(0)

  return (
    <div>
      <div>{counter}</div>

      <button onClick={() => setCounter(counter + 1)}>
		  plus
		</button>
    </div>
  )
}
```

Dans ce code, le clic sur le bouton viens incrementer la valeur du state. A chaque clic, le composant est redendu.

L'utilisation de fonction handler permet de garder un code plus lisible : 
```jsx
import { useState } from "react";

const App = () => {
	const [counter, setCounter] = useState(0);

	const handleClick = () => {
		setCounter(counter + 1);
	};

	const handleReset = () => {
		setCounter(0);
	};

	return (
		<div>
			<div>{counter}</div>
			<button onClick={handleClick}>Plus</button>
			<button onClick={handleReset}>Reset</button>
		</div>
	);
};

export default App;
```

---

## Communication enfant => parent 

Il est recommande d'ecrire des composant React petits et reutilisable dans une application.

Ici, on vient refactoriser notre composant en 3 composant.

Le premier `Display` charger de l'affichage de la valeur du compteur. 

Une bonne pratique React est de remonter l'etat dans la hierarchie des composants. Pour cela, on viens definis le state dans le composant parent commun.

On commence par definir le composant `Display`

```jsx
const Display = (props) => {
  return (
    <div>{props.counter}</div>
  )
}
```

Dans le composant parent, il suffit de transmettre la valeur au composant enfant

```jsx
import { useState } from "react";

const Display = (props) => {
	return <div>{props.counter}</div>;
};

const App = () => {
	const [counter, setCounter] = useState(0);

	const increaseByOne = () => setCounter(counter + 1);

	const setToZero = () => setCounter(0);

	return (
		<div className="">
			<Display counter={counter} />
		</div>
	);
};

export default App;
```

On creer ensuite un composant `Button` pour les boutons de l'application. Il sera necessaire de passer un handler et le titre du bouton dans le composant 

```jsx
const Button = (props) => {
	return <button onClick={props.onClick}>{props.text}</button>;
};
```

Dans le composant parent, on definis le handler que l'on vient passer au composant enfant 

```jsx
import { useState } from "react";

const Display = (props) => {
	return <div>{props.counter}</div>;
};

const Button = (props) => {
	return <button onClick={props.onClick}>{props.text}</button>;
};

const App = () => {
	const [counter, setCounter] = useState(0);

	const increaseByOne = () => setCounter(counter + 1);
	const decreaseByOne = () => setCounter(counter - 1);
	const setToZero = () => setCounter(0);

	return (
		<div className="">
			<Display counter={counter} />

			<Button onClick={decreaseByOne} text="minus" />
			<Button onClick={setToZero} text="zero" />
			<Button onClick={increaseByOne} text="plus" />
		</div>
	);
};

export default App;
```

Avec cette refactorisation, on obtient un composant `Button` qui peut facilement etre reutiliser.

Par convention, on utilise des nom de type `onSomething` pour les props qui represente des event et `handleSomething` pour les handle qui gere ces event 

### Changement d'etat 

Lorsque l'application demarre, le code dans `App` est execute. Ce code utilise le hook `useState` pour creer l'etat de l'application, en definissant une valeur initiale de la variable `counter`.

Ce composant contient le composant `Display` qui affiche la valeur du compteur `0`, et trois composant `Button`.

Les boutons ont tous des handler qui sont utilise pour changer l'etat du composant.

Lorsque l'un des boutons est clique, le handler est execute modifie l'etat du composant.

L'appel d'une fonction qui modifie l'etat provque le rendu du composant et de ses sous composant.

---

## Etat complexe

Le `useState` permet de creer des etat complexe. 

```jsx
const App = () => {
  const [clicks, setClicks] = useState({
    left: 0, right: 0
  })

// on utilise le spread operateur pour modifier l'objet 
	const handleLeftClick = () => {
	  const newClicks = { 
	    ...clicks, 
	    left: clicks.left + 1 
	  }
	  setClicks(newClicks)
	}
	
	const handleRightClick = () => {
	  const newClicks = { 
	    ...clicks, 
	    right: clicks.right + 1 
	  }
	  setClicks(newClicks)
	}
	
	// syntaxe fleches
	const handleLeftClick = () =>
	  setClicks({ ...clicks, left: clicks.left + 1 })

	const handleRightClick = () =>
	  setClicks({ ...clicks, right: clicks.right + 1 })

  return (
    <div>
      {clicks.left}
      <button onClick={handleLeftClick}>left</button>
      <button onClick={handleRightClick}>right</button>
      {clicks.right}
    </div>
  )
}
```

Dans ce code, on viens creer un state qui contient un objet avec deux valeurs. 

On ne met jamais a jour directement un objet en React, il faut garder les fonction pure. La modification direct peut entrainer des effets secondaires. Le changement d'etat doit toujours etre effectue en definissant l'etat sur un nouvel objet.
Si les proprietes ne sont pas modifier, elle doivent etre copier.

Stocker les états de cette manière, n'est pas forcément le mieux, car cela complique le composant. On préfère créer un state pour chaque état. 

```jsx
const App = () => {
  const [left, setLeft] = useState(0)
  const [right, setRight] = useState(0)
  
const handleLeftClick = () =>
  setClicks({ ...clicks, left: clicks.left + 1 })

const handleRightClick = () =>
  setClicks({ ...clicks, right: clicks.right + 1 })

  return (
    <div>
      {clicks.left}
      <button onClick={handleLeftClick}>left</button>
      <button onClick={handleRightClick}>right</button>
      {clicks.right}
    </div>
  )
}
```

---

### Gestion des tableaux 

On ajoute dans un tableau `allClicks` les différents clic produits.

```jsx
const App = () => {
  const [left, setLeft] = useState(0)
  const [right, setRight] = useState(0)

  const [allClicks, setAll] = useState([])


  const handleLeftClick = () => {
    setAll(allClicks.concat('L'))
    setLeft(left + 1)
  }


  const handleRightClick = () => {
    setAll(allClicks.concat('R'))
    setRight(right + 1)
  }

  return (
    <div>
      {left}
      <button onClick={handleLeftClick}>left</button>
      <button onClick={handleRightClick}>right</button>
      {right}

      <p>{allClicks.join(' ')}</p>
    </div>
  )
}
```

Chaque clic est stocké dans un élément d'état séparé `allClicks` qui est initialisé sous forme de tableau vide. 

Lorsque le bouton gauche est cliqué, on ajoute la letttre L au tableau avec le handler `handleLeftClick`.
Celle ci ajoute dans le tableeau la nouvelle lettre en utilisant `concat()` qui permet d'ajouter un nouvel élément dans un tableau sans modifier le tableau d'origine (on reste pur)

---

## Rendu de collections et modules

### Afficher une liste 

Pour itérer des listes, on utilise la méthode `map()`. Cette méthode crée un nouveau tableau dont les éléments ont été créées à partir des éléments du tableau d'origine.

Cette méthode permet de parcourir un tableau.

```js
// déclaration du tableau 
const notes = [
  {
    id: 1,
    content: 'HTML is easy',
    date: '2019-05-30T17:30:31.098Z',
    important: true
  },
  {
    id: 2,
    content: 'Browser can execute only JavaScript',
    date: '2019-05-30T18:39:34.091Z',
    important: false
  },
  {
    id: 3,
    content: 'GET and POST are the most important methods of HTTP protocol',
    date: '2019-05-30T19:20:14.298Z',
    important: true
  }
]

// on affiche un élément pour chaque ligne du tableau 
return (
  <div>
    <h1>Notes</h1>
    <ul>
      {notes.map(note => 
        <li key={note.id}>
          {note.content}
        </li>
      )}
    </ul>
  </div>
)
```

- `notes` : c'est le tableau que l'on souhaite itérer.
- `note` : argument passé dans la callback. Elle contient la valeur en cours d'itération.

Les éléments générés par la méthode `map()` doivent avoir une valeur de clé unique. Cette valeur permet de déterminer comment mettre à jour la vue générée par un composant lorsque le composant est rendu à nouveau.

La méthode `map()` peut prendre un second argument `i`. Celui ci contient un index des éléments du tableau. Cette méthode n'est pas recommandé, et peut provoquer des problèmes.

```js
<ul>
  {notes.map((note, i) => 
    <li key={i}>
      {note.content}
    </li>
  )}
</ul>
```

---

### Passage d'une liste vers un enfant 

On peut venir créer un composant `Note` dédié à l'affichage de l'élément d'une liste. Dans le composant parent, on viendras passer via des props le contenu d'une note.

```jsx
const Note = ({ note }) => {
  return (
    <li>{note.content}</li>
  )
}

const App = ({ notes }) => {
  return (
    <div>
      <h1>Notes</h1>
      <ul>
        { notes.map(note => 
          <Note key={note.id} note={note} />
        )}
      </ul>
    </div>
  )
}
```

---

## Module

Dans une application React, on utilise un fichier pour un module. 

Par convention, on utilise le répertoire `src/components` pour y placer les composants d'une application. Le fichier est nommé d'après son composant.

Par exemple, le fichier `src/components/Note.jsx` :

```jsx
import React from 'react';

const Note = ({ note }) => {
  return (
    <li>{note.content}</li>
  )
}

export default Note;
```

Le fichier qui définit le composant `App`, peut venir importer le module 

```jsx
import Note from './components/Note';

const App = ({ notes }) => {
  ...
}
```

---

## Formulaire 

### Initialisation useState 

```jsx
const App = (props) => {
  const [notes, setNotes] = useState(props.notes)

  // eventHandler qui se déclenche à la soumission du formulaire
  const addNote = (event) => {
    // empêche le rechargement lors de la soumission du formulaire
    event.preventDefault()
    // on récupère le formualire via le event.target
    console.log('button clicked', event.target)
  }

  return (
    <div>
      <h1>Notes</h1>
      <ul>
        {notes.map(note => 
          <Note key={note.id} note={note} />
        )}
      </ul>
      // on lie le handler sur l'élément form pour récupérer le submit
      <form onSubmit={addNote}>
        <input />
        // le bouton déclenche la soumission
        <button type="submit">save</button>
      </form>   
    </div>
  )
}
```

### Composant contrôlé 

Permet de lier une valeur d'un input à une valeur du `state`. De ce manière l'affichage est synchronisé.

En passant la valeur du state sur l'attribut `value` de l'input, il afficheras la valeur définis dans le stage, mais le champ ne pourras pas être modifé.

Le composant `App` contrôle le comportement de l'input.

Pour permettre l'édition de l'input, il est nécessaire d'ajouter un handler, qui viens surveiller les changements sur l'input et permet de le synchroniser avec le state du composant. `onChange` permet de surveiller les changements qui se produise sur l'input.

```jsx
const App = (props) => {
  const [notes, setNotes] = useState(props.notes)

  // ajout d'un state qui contient la valeur qui sera afficher dans l'input lié
  const [newNote, setNewNote] = useState(
    'a new note...'
  ) 

  const addNote = (event) => {
    event.preventDefault()
    console.log('button clicked', event.target)
  }

  // ajout du handler qui surveille l'input 
  const handleNoteChange = (event) => {
    console.log(event.target.value)
    // event.target.value permet de récupérer la valeur saisie dans l'input
    // il fait référence à l'input surveiller par le handler
    setNewNote(event.target.value)
  }

  return (
    <div>
      <h1>Notes</h1>
      <ul>
        {notes.map(note => 
          <Note key={note.id} note={note} />
        )}
      </ul>
      <form onSubmit={addNote}>
        <input
          // en attribut value, on lui passe la valeur du state => l'input est lié à la valeur du state 
          value={newNote} 
          // on surveille les changement de l'input avec le handler
          onChange={handleNoteChange}
          />
        <button type="submit">save</button>
      </form>   
    </div>
  )
}
```

### Gestion du formulaire 

On peut venir compléter la fonction charger de traiter la soumission du formulaire

```jsx
const addNote = (event) => {
  event.preventDefault(); // on empêche de recharger la page 

  // on créer un objet qui contient les données de la nouvelle note 
  const noteObject = {
    content: newNote, // on récupère la valeur saisie dans l'input 
    date: new Date().toISOString(),
    important: Math.random()< 0.5,
    id: notes.length + 1, // on définis l'id comme la valeur suivante de l'id 
  }

  // on passe dans le state une copie du nouvel objet 
  setNotes(notes.concat(noteObject))
  // on reset l'input du formulaire
  setNewNotes('');
}
```

### Filtrage des éléments affichés 

On ajoute un nouveau state qui garde une trace des notes à afficher 
On viens modifier le composant pour qu'il stocke une liste des notes à afficher. Les éléments afficher dépendant de l'état du composant.

On retrouve un bouton qui permet de déclencher le changemetn d'affichage. Le texte du bouton dépend de la valeur d'état `showAll`

```jsx
import { useState } from 'react'
import Note from './components/Note'

const App = (props) => {
  const [notes, setNotes] = useState(props.notes)
  const [newNote, setNewNote] = useState('') 
  const [showAll, setShowAll] = useState(true) // ajout du nouveau state 

  // ...

  // selon la valeur du state avec un opérateur conditionnel 
  // - affichage de toutes les notes
  // - on affiche que les notes importantes
  const notesToShow = showAll
    ? notes
    : notes.filter(note => note.important === true) // on peut l'ecrire : notes.filter(note => note.important)

  return (
    <div>
      <h1>Notes</h1>
      <div>
        <button onClick={() => setShowAll(!showAll)}>
          show {showAll ? 'important' : 'all' }
        </button>
      </div>
      <ul>

        {notesToShow.map(note =>
          <Note key={note.id} note={note} />
        )}
      </ul>
      // ...
    </div>
  )
}
```