# Tanstack Query 

## Requête GETdetail 

Pour faire une requête allant chercher une ressource particulière on utilise `query`.

On déclare le typage de la données qui sera retourner par la requête 
```tsx 
// typage des données Club 
type ClubDetail = {
	id: number;
	name: string;
	description: string;
	image: string;
	street: string;
	numberStreet: string;
	city: string;
	zipCode: string;
	country: string;
};
// typage des donnée Sport
type Sport = {
	id: number;
	name: string;
};
// typage de l'objet de repsonse
type ResponseData = {
	club: ClubDetail;
	sports: Sport[];
};
```

```tsx
const { data, isPending, isError } = useQuery({
		// on définis un nom pour le cache
    queryKey: ["club"],
    // on déclare la fonction qui fait la requête
		queryFn: async () => {
			// await wait(2000); // fonction utilitaire pour ajouter un delais pour check les loading
      // on récupère la response, et on utilise Axios pour envoyer la requête
			const response = await api.get<ResponseData>(`/club/${clubId}`);
			// on retourne la donnée ou un tableau vide
      return response.data ?? [];
		},
	});

// on return un composant lors du premier render.
if (isPending) {
  return <div>Chargement des données</div>;
}

if (isError) {
  return <div>Erreur lors du chargement des données</div>;
}

// on peut ensuite utiliser les donnée retourner. 	
const { club, sports } = data;

console.log(club);
console.log(sports);
```