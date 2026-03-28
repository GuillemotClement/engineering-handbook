# Symfony 

## PROJET 

### Support TLS 
```shell
symfony server:ca:install
```

## CLI 

Pour lancer les commande, on préfixe avec `symfony`. Par exemple `symfony composer`. Symfony définit automatiquement certaines variables d'environnements à partir des services Docker.
Elle permet également de sélectionner la meilleure version de PHP.

### Lancement du serveur de développement 

```shell
symfony server:start -d

# ouvrir la page
symfony open:local

# consuler les logs
symfony server:log
```

### Création d'un nouveau projet 

```shell
symfony new guestbook --version=6.4 --php=8.3 --webapp --docker --cloud
```

- `--webapp` : boilerplate pour une application web.
- `--docker`: ajoute la configuration docker 
- `--cloud`: pour le déploiement sur Platform.sh

## Structure 
```
├── bin/
├── composer.json
├── composer.lock
├── config/
├── public/
├── src/
├── symfony.lock
├── var/
└── vendor/
```

- `bin` : point d'entrée de la CLI `console`
- `config` : ensemble des fichiers de configuration.
- `public`: répertoire racine du site web. Le fichier `index.php` est le point d'entrée principale 
- `src`: contient le code source.
- `var`: contient les caches, logs, et fichier générés par l'application
- `vendor`: paquets composer

### Fichier statique

Les images du projet sont à stocker dans `public`.  Par exemple dans un dossier `/public/images`.

Les fichiers seront ensuite disponible via l'url `https://localhost/images/<name>.<format>`.

### Environnement 

Symfony peut prendre trois valeurs `dev` | `prod` | `test`.

Pour modifier l'environnement, on peut le faire via la ligne de commande 

```shell
export APP_ENV=dev
```

Le mieux est d'utiliser le fichier `.env` et de set la valeur d'environnement dans ce fichier.

La bonne pratique est de créer un fichier `.env.local`.

## DEBUG 

### configuration 

On peut ajouter une instruction dans le fichier `php.ini`. Celle ci permet d'afficher dans les log, le path du fichier qui provoque une erreur

```php.ini
+xdebug.file_link_format=vscode://file/%f:%l
```

### dump()

Méthode permettant d'afficher la valeur d'une variable.

```php
dump($request);
```

Dans la méthode, on passe la variable que l'on souhaite afficher.

Dans le navigateur, il faut ensuite venir cliquer sur la cible dans le profile, et cela permet d'afficher la valeur de la variable.

## CONTROLLEUR 

Les controlleurs ont pour responsabilité de retourne une reponse HTTP pour une requête. 

### Maker Bundle 

Symfony fournis des outils pour simplfiier la céation des éléments. Il permet de générer le boileplate pour des classes de Symfony.

```shell
# lister les commande disponible 
symfony console list make
```

### Création d'un controlleur 

```shell
symfony console make:controller <name>Controller
```

Symfony vient générer le code de base d'un controller. Avant chaque méthode, il y a un attribut permettant de le configurer.

```php
final class ConferenceController extends AbstractController
{
    #[Route('/', name: 'homepage')]
    public function index(): Response
    {
        return new Response(<<<EOF
            <html>
                <body>
                    <img src="/images/under-construction.gif" />
                </body>
            </html>
        EOF
    );
    }
}
```

Dans l'exemple, l'attribut indique le endpoint pour accéder à la méthode via le navigateur.

La configuration `name` permet de faire référence à cette route pour les liens dans l'application. On fait référence à ce nom pour utiliser cette route.

### Récupérer un paramètre de l'url 

L'objet `Request` permet de traiter des informations issue de la requête.

```php
#[Route('/', name: 'homepage')]
public function index(Request $request): Response
{
	$greet = "";
	// récupère depuis l'url le paramètre
	if($name = $request->query->get('hello')) {
		$greet = sprintf('<h1>Hello, %s!</h1>', htmlspecialchars($name));
	}

	return new Response(<<<EOF
		<html>
			<body>
				$greet
				<img src="/images/under-construction.gif" />
			</body>
		</html>
		EOF
	);
}
```

En utilisant l'url `https://localhost:8000/?hello=Fabien`, on aura le nom affiche sur la page.

On peut également passer directement dans le endpoint le paramètre 

```php
final class ConferenceController extends AbstractController
{
	#[Route('/hello/{name}', name: 'homepage')]
	public function index(string $name = ''): Response
	{
		$greet = "";
		if($name) {
			$greet = sprintf('<h1>Hello, %s!</h1>', htmlspecialchars($name));
		}

		return new Response(<<<EOF
			<html>
				<body>
					$greet
					<img src="/images/under-construction.gif" />
				</body>
			</html>
		EOF
	);
	}
}
```

Et pour accéder, on utilise alors l'url : `https://localhost:8000/hello/gizmo`

La partie `{name}` de la route est un paramètre de route dymanique. Il sera automatiquement disponible dans une variable fournis à la méthode du controller.

---

## BASE DE DONNEE

### Ajouter une base de donnée
Par réussis à faire fonctionner cette commande.
```shell
# connexion à la DB
symfony run psql
```

### Sauvegarde et restauration 

```shell
# sauvegarde
symfony run pg_dump --data-only > dump.sql

# restauration 
symfony run psql < dump.sql
```

---
## Doctrine 

ORM de symfony permettant de communiquer avec la base de donnée.

### Configuration 

Doctrine utilise le `.env` pour récupérer le DNS permettant de se connecter à la base de donnée.

```env
DATABASE_URL="postgresql://<user>:<pwd>@127.0.0.1:5432/<db>?serverVersion=16&charset=utf8"

```

Le fichier `config/packages/doctrine.yaml` contient la configuration.

### Création d'entité

Les classes du dossier `src/Entity` contient les définitions des tables, et sont utilisé par Doctrine pour générer les tables dans la base de donnée.

```shell
symfony console make:entity <table_name>
```

Cette commande lance un script perettant de définir les champs de la table, et leur type.

Cette commande vient générer ensuite le fichier dans le dossier Entity. Ce fichier contient les champs de la table, avec des attributs pour ajouter des métadonnée utilisé par Doctrine pour mapper la classe à sa table.

### Relation entre table 

Une fois les deux tables à lier généré, on peut ajouter relance la commande avec le nom de l'entity à modifier.

Par exemple, on as deux table `Conference` et `Comment`. On souhaite ajouter une relation one-to-many.

Une conferance pouvant avoir un ou plusieurs commentaire. On viens donc modifier la table Conference pour ajouter cette relation.

On lance cette commande 
```shell
symfony console make:entity Conference
```

```
Your entity already exists! So let's add some new fields!

New property name (press <return> to stop adding fields):
> comments

Field type (enter ? to see all types) [string]:
> OneToMany

What class should this entity be related to?:
> Comment

A new property will also be added to the Comment class...

New field name inside Comment [conference]:
>

Is the Comment.conference property allowed to be null (nullable)? (yes/no) [yes]:
> no

Do you want to activate orphanRemoval on your relationship?
A Comment is "orphaned" when it is removed from its related Conference.
e.g. $conference->removeComment($comment)

NOTE: If a Comment may *change* from one Conference to another, answer "no".

Do you want to automatically delete orphaned App\Entity\Comment objects (orphanRemoval)? (yes/no) [no]:
> yes

updated: src/Entity/Conference.php
updated: src/Entity/Comment.php
```

### Migration 

Une fois les tables définis, on peut venir réaliser la migration pour ajouter les nouvelles table dans la base de donnée.

Dans un premier temps, on vient générer le fichier de migration qui est une classe qui décrit les changements nécessaire pour mettre à jour le schema de base de donnée.

```shell
symfony console make:migration
```

Une fois généré, on peut mettre à jour la base de données :

```shell
symfony console d:m:m
```

---

## INTERFACE ADMINISTRATION

Une interface d'administration est une section prôtégé du site, ou seul les admins peuvent gérer les données du site.

Symfony fournis un bundler permettant de générer automatiquement une interface d'administration.

```shell
symfony composer req "easycorp/easyadmin-bundle:^4"
```

Pour générer une nouvelle interface :
```shell
symfony console make:admin:<name_interface>
```

La commande vient générer une nouvelle classe dans `src/Controller/Admin`. 

Pour accéder à l'interface, on utilise l'url : `/admin`

Si une erreur de "clear cash" arrive, il faut lancer la commande : 

```shell
symfony console cache:clear
```

Pour rendre cet interface utilisable, il faut ensuite venir définir les différentes méthodes.

Pour générer un CRUD :

```shell
symfony console make:admin:crud
```

Il faut faire pour chaque entité que l'on souhaite administrer.

Pour ensuite connecter les entité à l'interface d'administration : 
```php
 public function configureMenuItems(): iterable
{
	yield MenuItem::linkToRoute('Back to the website', 'fas fa-home', 'homepage');
	yield MenuItem::linkToCrud('Conferences', 'fas fa-map-marker-alt', Conference::class);
	yield MenuItem::linkToCrud('Comments', 'fas fa-comments', Comment::class);
}
```

Pour ajouter des informations sur l'interface, 

```php
public function index(): Response
    {
	$routeBuilder = $this->container->get(AdminUrlGenerator::class);
	$url = $routeBuilder->setController(ConferenceCrudController::class)->generateUrl();

	return $this->redirect($url);
    }
```

Pour rendre l'affichage plus lisible, on peut venir modifier la classe de l'entity pour modifier l'affichage :

```php
// Entity/Conference
public function __toString(): string
{
	return $this->city . ' ' . $this->year;
}
```

Dans chaque classe `Crud` généré, on peut ensuite ajouter des personnalitation pour l'affichage 

## INTERFACE 

### Twig 

Moteur de templating. Il échappe automatique les input.

Les fichiers se trouvent dans le dossier `templates/`. Les fichier devront utiliser l'extension `base.html.twig`.

### Block 

Un modèle peut définir des blocks. Ce sont des emplacements où les templates enfant qui étendent le modèle ajoute leur contenu.

Les éléments block seront placé dans l'emplacement définit dans le template `extends`.

```twig
// étends le fichier de base
{% extends 'base.html.twig' %}

{% block title %}Conférence Guestbook{% endblock %}

{% block body %}
    <h2>Give your feedback!</h2>
	// instruction twig pour parcourir la liste
    {% for conference in conferences %}
    // affichage d'élément
        <h4>{{  conference }}</h4>
    {%  endfor %}
    
{% endblock %}
```

### Retourner un template depuis le controller 

Dans la méthode du controller, on viens retourne un fichier twig. 

```php
#[Route('/', name: 'homepage')]
public function index(Environment $twig, ConferenceRepository $conferenceRepository): Response
{
	return new Response($twig->render('conference/index.html.twig', [
		'conferences' => $conferenceRepository->findAll(),
	]));
}
```

- `Environment` : c'est le point d'entrée de twif
- `ConferenceRepository` : permet d'accéder au méthode permettant de travailler avec l'entité et récupérer des information depuis la base de donnée
- `render()` : la méthode qui permet de générer le rendu du template. Elle prend en premier argument le fichier à rendre, et en second les donnée passé dans le template. 

### Créer un page detail 

```php
#[Route('/conference/{id}', name: 'conference')]
public function show(Environment $twig, Conference $conference, CommentRepository $commentRepository): Response
{
	return new Response($twig->render('conference/show.html.twig', [
		'conference' => $conference,
		'comments' => $commentRepository->findBy(['conference' => $conference], ['createdAt' => 'DESC'])
	]));
}
```

Symfony est capable de récupérer automatiquement la ressource, en récupérant automatiquement l'identifiant issue de l'url.

On peut ensuite venir créer le fichier de template. L'utilise des `|` permet d'utiliser des filtres twig qui vient transformer la valeur.

```twig
{% extends 'base.html.twig' %}

{% block titile %}Conférence Guestbook - {{ conference }}{% endblock %}

{% block body %}

    <h2>{{ conference }} Conférence</h2>

    {% if comments|length > 0 %}
        {% for comment in comments %}
            {% if comment.photofilename %}
                <img src="{{ asset('uploads/photos/' ~ comment.photofilename) }}" style="max-width: 200px" />
            {% endif %}

            <h4>{{  comment.author }}</h4>
            <small>
                {{ comment.createdAt|format_datetime('medium', 'short') }}
            </small>

            <p>{{ comment.text }}</p>
        {% endfor %}
    {% else %}
        <div class="">No comments have benn posted yet for this conferences.</div>
    {% endif %}
{% endblock %}
```

### Navigation 

Pour ajouter des liens dans les template, on ne passe pas directement le chemin du fichier. On utilise la fonction `path()` avec le nom de la route passé en argument.

En second paramètre, on peut passer les paramètre url . Ces données sous transmises sous forme d'objet.
```twig
{% for conference in conferences %}
    <h4>{{ conference }}</h4>
    <p>
      <a href="{{ path('conference', { id: conference.id}) }}">View</a>
    </p>
{% endfor %}
```

### Pagination 

Pour mettre ne place la pagination, on ajoute une nouvelle méthode dans le Repository. Cette méthode retourne un nombre limité de ressources pour éviter de surcharger.

```php
public function getCommentPaginator(Conference $conference, int $offset): Paginator
	{
		$query = $this->createQueryBuilder('c')
			->andWhere('c.conference = :conference')
			->setParameter('conference', $conference)
			->orderBy('c.createdAt', 'DESC')
			->setMaxResults(self::COMMENTS_PER_PAGE)
			->setFirstResult($offset)
			->getQuery();

		return new Paginator($query);
	}
```

Il faut ensuite mettre à jour la méthode du controller pour ajouter la pagination 

```php
	#[Route('/conference/{id}', name: 'conference')]
	public function show(Request $request, Environment $twig, Conference $conference, CommentRepository $commentRepository): Response
	{
		$offset = max(0, $request->query->getInt('offset', 0));
		$paginator = $commentRepository->getCommentPaginator($conference, $offset);

		dump($conference);

		return new Response($twig->render('conference/show.html.twig', [
			'conference' => $conference,
			'comments' => $paginator,
			'previous' => $offset - CommentRepository::COMMENTS_PER_PAGE,
			'next' => min(count($paginator), $offset + $commentRepository::COMMENTS_PER_PAGE)
		]));
	}
}
```

On vient ensuite mettre à jour le template

```twig
{% if previous >= 0 %}
      <a href="{{ path('conference',  {id: conference.id, offset: previous}) }}">Previous</a>
    {% endif %}
    {% if next < comments|length %}
      <a href="{{ path('conference', {id: conference.id, offset: next }) }}">Next</a>
    {% endif %}
```

### Optimisation du controller

Au lieu d'utiliser un environnement passer dans la méthode du controller, on peut directement utiliser la méthode de la classe parent 

```php
#[Route('/', name: 'homepage')]
	public function index(ConferenceRepository $conferenceRepository): Response
	{
		return $this->render('conference/index.html.twig', [
			'conferences' => $conferenceRepository->findAll(),
		]);
	}
```

## SESSION 

### Stocker les sessions en base de données

On commence par modifier les info de session avec le fichier `config/package/framework.yml`.

Il faut modifier la valeur pour `handler_id`

```yml
handler_id: '%end(resolve:DATABASE_URL)%'
```

On viens ensuite créer une table `sessions`.

```shell
# table sessions automatiquement généré
symfony console make:migration
symfony console d:m:m
```