## Communiquer avec l'IA 

- **Persona** : Définit le rôle ou l'expertise que l'IA doit adopter 
- **Contexte** : Fournit des informations supplémentaire sur la situation 
- **Tâche** : Décrire précisément ce que l'IA doit faire 
- **Format** : Indique comment doit être présenté la réponse 
- **Ton** : Détermine l'humeur ou le style de la réponse 

### Persona 

Permet à l'IA de fournir des réponses plus adaptées en fonction du point de vue ou du style souhaité
- *En tant que coach sportif, je dois conseiller pour améliorer le sommeil chez un adulte de 40 ans*

### Contexte 
Le contexte permet à l'IA de mieux comprendre les circonstances de la demande, et adapter sa réponse 
- *En tant que nutritionniste, comment améliorer la concentration des étudiants en période d'examen*

### Tâche 
Définir une tâche avec précision permet à l'IA de comprendre ce qui est attendu 
- *En tant que psychologue scolaire, décris les signes de fatigue mentale et émotionnelle que les étudiants peuvent présenter pendant les périodes d'examen, et propose des stratégies pour les aider à surmonter ces défis et un moyen de suivre les progrès de la stratégie mise en place.*

### Format 
Le format indique comment la réponse doit être présentée, rendant la réponse plus facile à lire et à utiliser
- *En tant que psychologue scolaire, décris les signes de fatigue mentale et émotionnelle que les étudiants peuvent présenter pendant les périodes d'examen, et propose des stratégies pour les aider à surmonter ces défis et un moyen de suivre les progrès de la stratégie mise en place. Je veux la réponse au format tableau : signes -> stratégie -> moyen d'évaluation des progrès.*

### Ton 
Détermine l'humeur ou le style de la réponse, améliorant l'efficacité de la communication 
- *En tant que principal d’un lycée, fait un discours pour la rentrée des classes, je suis assez familier avec mes élèves et adopte un ton très drôle / second degré.*

---

## Bonne pratique

- **Utiliser des séparateurs**: utiliser des délimiteurs comme `###` ou `"""` permet de séparer les instructions du contexte. Les instructions doivent être placées au début de la requête. Cela permet à l'IA de comprendre ce qu'elle doit analyser sans le confondre avec une partie de l'instruction 
- **Neutralité** : Il faut privilégiez un langage clair et neutre. Permet de minimiser les malentendus et permet d'obtenir des réponse appropriées. 
- **Fournir des exemples**: L'ajout d'exemples clairs permet à l'IA de mieux comprendre le contexte et le format de la réponse attendu.
- **Interrogation**: Permettre à l'IA de poser des questions pour affiner les prompts augmente son efficacité. Cela permet d'engager un dialogue permettant de clarifier les détails et d'ajuster la demande en fonction des besoins. `Aide-moi à planifier un événement. De quels détails supplémentaires as-tu besoin pour m’apporter la réponse la plus complète possible ?`

---

## Exemple d'utilisation 

### Créer un article de blog pour une entreprise 

Une entreprise de marketing digital souhaite créer des articles de blog pour expliquer les avantages du marketing de contenu aux petites entreprise.

- **Persona**: Un expert marketing digital qui s'adresse à des gérants de petites entreprises
- **Contexte** : Le marketing de contenu est un sujet complexe, mais il doit être expliqué de manière simple et engageante
- **Tâche** : Rédiger un article de blog informatif complet 
- **Format** : Un article structuré en sections claires avec des exemples concrets
- **Ton**: Pédagogique et accessible, avec une touche de dynamisme
- **Questionnement** : Si besoin de plus de précisions, pose des questions sur le public cible, les attentes spécifiques de l'entreprise, ou tout autre aspect pertinent 

*Imagine que tu es un expert en marketing digital qui parle à des gérants de petites entreprises. Dans un contexte où beaucoup d'entre eux ne comprennent pas encore les avantages du marketing de contenu, écris un article de blog clair et engageant. Ton article doit expliquer pourquoi le marketing de contenu est crucial pour eux, détailler les avantages spécifiques, et inclure des exemples concrets. Le ton doit être pédagogique mais dynamique, et l'article doit être bien structuré avec des sections claires. Si tu as besoin de plus de précisions pour améliorer le contenu, pose-moi des questions sur le public cible ou d'autres aspects importants.*



