# 2 - Décrire les Objets Kubernetes (Object Spec)

Dans cette leçon, nous allons parler plus en détail de l'API Server, des versions et de l'API. 

# 2.1. API Server

Le cluster Kubernetes a un contrôle plan, un serveur qui contrôle toutes les nodes du cluster. Ce contrôle plan héberge un programme très important appelé API Server. C'est via ce serveur que nous pouvons donner des instructions au cluster et récupérer des informations. L'API Server agit comme un point d'entrée avec une adresse où nous pouvons envoyer des requêtes HTTP. 

Lorsque nous envoyons des requêtes HTTP à l'API Server, cela entraîne des actions ou récupère des informations selon le type de requête. Par exemple, lorsque nous voulons créer des objets, les détails des requêtes sont formatés en JSON. Bien que nous utilisions des fichiers YAML, ces fichiers sont interprétés par kubectl (le CLI) qui génère les requêtes HTTP pour nous.

# 2.2. Authentification et Sécurité

Lorsque vous utilisez Minikube ou un cluster de production, il y a toujours une authentification lors de l'envoi de ces requêtes HTTP, généralement via des certificats. Par défaut, Minikube crée un dossier caché `.minikube` contenant ces certificats, facilitant ainsi la sécurisation des communications.

# 2.3. Points de Terminaison (Endpoints)

Les endpoints sont les adresses où nous envoyons nos requêtes HTTP pour interagir avec l'API Server. Par exemple:
- `/healthz` pour vérifier la santé du cluster
- `/metrics` pour récupérer des métriques
- `/version` pour obtenir la version de différents composants du cluster
- `/logs` pour récupérer les logs du cluster

Ces endpoints nous permettent de créer et de gérer les objets sur le cluster. L'API core regroupe un grand nombre d'objets Kubernetes, et nous découvrirons ces objets au fur et à mesure.

# 2.4. Versions de l'API

Pour la plupart des actions, il faut spécifier la version de Kubernetes et de l'objet à utiliser. Il y a principalement trois versions:
1. **v1**: Objet stable, testé et prêt pour la production.
2. **alpha**: Fonctionnalité en développement avec peu de tests, à utiliser pour des besoins spécifiques ou des tests.
3. **beta**: Fonctionnalité plus stable, avec plus de tests, mais pas encore totalement intégrée dans la version stable.

Par défaut, nous utilisons souvent la version stable `v1`. Une fois que nous avons précisé la version de l'élément, nous renseignons le nom de la ressource, comme `Pod` ou `Deployment`.

# 2.5. Structure des Objets Kubernetes

Les objets Kubernetes sont décrits dans des fichiers YAML contenant des éléments communs:
- **apiVersion**: La version de l'API.
- **kind**: Le type d'objet, comme `Pod` ou `Deployment`.
- **metadata**: Des informations supplémentaires comme le nom et les labels de l'objet.
- **spec**: Les spécifications de l'objet, détaillant sa configuration.

# 2.6. Exemple de Syntaxe YAML

Voici comment nous décrivons les objets Kubernetes dans des fichiers YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: nginx
```

Dans cet exemple, nous avons un Pod nommé `my-pod` contenant un conteneur utilisant l'image `nginx`.

Les fichiers YAML utilisent des paires clé-valeur pour définir la configuration. Les objets peuvent être imbriqués et des listes peuvent être utilisées pour des collections d'éléments.

#### Conclusion

Nous avons vu comment l'API Server, les endpoints, les versions et la structure des objets fonctionnent dans Kubernetes. Dans la leçon suivante, nous allons commencer à créer notre premier Pod.
