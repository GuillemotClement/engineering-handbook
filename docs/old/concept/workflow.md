# Focus 

## METHODOLOGIE

### 1. Créer une branche pour une fonctionnalité

- **Nommage**: `feat/<nom_feature`
- **Exemple**: `feat/create_club`

**Règle** : on implémente que ce qui est nécessaire pour la fonctionnalité.

Dans le cas où une idée de feature, une implémentation pas terrible, on viens noter dans un fichier `TODO.md` lié au projet, ou quelque part qui centralise. (peut être trouver un soft ou idée de projet)

### 2. Définir la feature et le critère fini

Avant de commencer le code, on vient définir les attendu pour cette feature, ce qu'elle doit accomplir.

On définir également le critère minimum d'acceptabilité, le flux minimal complet de bout en bout 

Par exemple: 
`feat/create_club`
- **Backend**: POST /clubs doit existe, et retourner 201 avec les données (id, name)
- **Frontend**: la page de création existe sur `/clubs/new_club`, les données sont bien envoyé, affichage des erreurs du serveur

### 3. Phase backend

#### Test happy path 

On commence par écrire le premier test côté back. 

On commencer par écrire le test `HAPPY PATH`. On test le comportement attendu. 

On code le routeur, et la logique de la fonctionnalité dans la couche controller. On ne se préoccupe pas de séparer la couche service et endpoint.

Si j'ai besoin d'une méthode d'un autre domaine et implémenter, je l'utilsie, sinon si pas encore implémenter, je viens la définir dans le controller directement. Une fois green, on peut venir la séparer et mettre dans le fichier adapté

Une fois que le domaine lié est implémenter, on change la méthode utilisé dans le cas ou on dépant d'une logique métier du domaine. Si on accède à la donnée sans logique métier spécifique, on conserve la méthode du repo 
**Dans ce test, on viens uniquement vérifier que le endpoint existe, et que le code est bien celui attendu.**

Par exemple : 

```ts
// tests/users.test.js
import request from 'supertest';
import app from '../app.js';

test('GET /users returns a list of users', async () => {
  const res = await request(app).get('/users');
  expect(res.statusCode).toBe(200);
  expect(Array.isArray(res.body)).toBe(true);
});
```

#### Implémentation de la feature 

On viens implémenter le code minimum pour que le test passe. 

```ts
// routes/users.js
import express from 'express';
const router = express.Router();

router.get('/users', (req, res) => {
  res.json([{ id: 1, name: 'Clément' }]); // happy path minimal
});

export default router;
```

est ce que je mets en place en utilisant la structure minimal, genre mise en place du router, et implémentation de la logique dans le controller ? 

### 4. Phase frontend

#### Ecriture du premier test front min

On test juste que le composant est bien rendu 

```tsx
// src/__tests__/Users.test.jsx
import { render, screen } from '@testing-library/react';
import Users from '../Users.jsx';
import { rest } from 'msw';
import { setupServer } from 'msw/node';

const server = setupServer(
  rest.get('/users', (req, res, ctx) => res(ctx.json([{ id: 1, name: 'Clément' }])))
);

beforeAll(() => server.listen());
afterAll(() => server.close());

test('renders user list', async () => {
  render(<Users />);
  expect(await screen.findByText('Clément')).toBeInTheDocument();
});
```

#### Implémentation du frontend minimun

```tsx
// src/Users.jsx
import { useEffect, useState } from 'react';

export default function Users() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    fetch('/users')
      .then(res => res.json())
      .then(setUsers);
  }, []);

  return (
    <ul>
      {users.map(u => <li key={u.id}>{u.name}</li>)}
    </ul>
  );
}

```

### 5. Refacto

Une fois que le flux est valider et `GREEN`, on peut commencer à venir découper le code pour obtenir la structure attendu.

#### Backend 

On viens extraire la couche `service`, `controller`, `repository`

```ts
// services/userService.js
export function getUsers() {
  return [{ id: 1, name: 'Clément' }];
}

// routes/users.js
import { getUsers } from '../services/userService.js';

router.get('/users', (req, res) => res.json(getUsers()));
```

#### Frontend 

On viens commencer à découper le composants, créer les hook, et gérer le state. Par exemple, créer un hook pour les user 

```tsx
// hooks/useUsers.jsx
import { useEffect, useState } from 'react';

export default function useUsers() {
  const [users, setUsers] = useState([]);
  useEffect(() => {
    fetch('/users').then(res => res.json()).then(setUsers);
  }, []);
  return users;
}
```

Puis modifier le composant :

```tsx
import useUsers from './hooks/useUsers.jsx';
export default function Users() {
  const users = useUsers();
  return <ul>{users.map(u => <li key={u.id}>{u.name}</li>)}</ul>;
}
```

On peut également venir travailler sur le style (minimum)
### 6. Améliorer la couverture de test

#### Backend 

On viens ajouter les tests des différentes couches, les tests de validation de données, les erreurs eventuel

#### Frontend

On viens ajouter les tests des hooks, des composants isolé 

### 7. Merge

Une fois que la feature atteints l'objectif attendu, on viens merge dans la branche `dev`.

---
## REGLES

- 1 rails = 1 feature = 1 branche 
- Happy path minimal -> vert -> refacto -> tests  -> merge
- Pas de refacto avant que le happy path soit passé
- Pas de touche UI ou backend supplémentaire avant le vert
- Découper les grosse fonctionnalité en sous tâches minimaliste
- Commit atomique => un commit = un chamement (test ok, feature implémenté)