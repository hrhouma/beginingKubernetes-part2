# Leçon08 : Les ReplicaSets

# 8.1. Introduction

Dans cette leçon, nous allons aborder les RéplicaSets. Les RéplicaSets sont des objets Kubernetes permettant de créer des répliques de Pods au sein d'un cluster. Un RéplicaSet garantit que le nombre spécifié de répliques d'un Pod est en cours d'exécution à tout moment.

# 8.2. Qu'est-ce qu'un RéplicaSet?

Un RéplicaSet permet de créer plusieurs instances d'un même Pod. Chaque Pod répliqué a les mêmes caractéristiques et fonctionne de la même manière. L'objectif principal des RéplicaSets est d'assurer la redondance. Si un Pod ou une Node tombe en panne, les autres Pods prennent le relais, garantissant ainsi la disponibilité continue des services.

# 8.3. Configuration d'un RéplicaSet

Pour configurer un RéplicaSet, il est nécessaire de spécifier plusieurs éléments :

1. **Le nombre de répliques** : Le nombre de Pods souhaités.
2. **Le sélecteur** : Utilisé pour sélectionner les Pods que le RéplicaSet doit gérer.
3. **Le modèle de Pod (template)** : Le modèle de Pod à répliquer.

# 8.4.  Exemple de Fichier de Configuration

Voici un exemple de fichier de configuration pour un RéplicaSet :

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: example-replicaset
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.4
        ports:
        - containerPort: 80
```

Dans cet exemple :
- Le RéplicaSet maintient trois répliques du Pod `nginx`.
- Le sélecteur `app: nginx` permet de sélectionner les Pods à gérer.
- Le modèle de Pod spécifie l'image `nginx:1.15.4` et le port 80.

# 8.5. Fonctionnement des RéplicaSets

Lorsqu'un RéplicaSet détecte qu'un Pod manque, il crée un nouveau Pod basé sur le modèle spécifié. Inversement, si le nombre de Pods dépasse le nombre souhaité, il en supprime jusqu'à atteindre le nombre correct.

# 8.6. Intégration avec Deployments

Bien que les RéplicaSets puissent être créés directement, ils sont généralement gérés par des objets de plus haut niveau comme les Deployments. Un Deployment crée et gère un RéplicaSet, et permet des fonctionnalités supplémentaires comme les mises à jour progressives des Pods.

# 8.7. Création d'un RéplicaSet

Nous allons créer un RéplicaSet directement à partir d'un fichier de configuration.

1. Créez un fichier nommé `replicaset.yaml` :
    ```yaml
    apiVersion: apps/v1
    kind: ReplicaSet
    metadata:
      name: example-replicaset
    spec:
      replicas: 3
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:1.15.4
            ports:
            - containerPort: 80
    ```

2. Appliquez la configuration avec la commande :
    ```sh
    kubectl apply -f replicaset.yaml
    ```

3. Vérifiez les Pods créés :
    ```sh
    kubectl get pods
    ```

Vous devriez voir trois Pods `nginx` en cours d'exécution.

# 8.8. Superviser et Gérer les RéplicaSets

Pour obtenir des détails sur un RéplicaSet, utilisez la commande suivante :
```sh
kubectl describe replicaset example-replicaset
```

Pour décrire un Pod spécifique et voir sa relation avec le RéplicaSet :
```sh
kubectl describe pod <pod-name>
```

# 8.9. Exemple de Gestion

Essayons de supprimer un Pod et voyons comment le RéplicaSet réagit :

1. Supprimez un Pod :
    ```sh
    kubectl delete pod <pod-name>
    ```

2. Vérifiez à nouveau les Pods :
    ```sh
    kubectl get pods
    ```

Vous verrez qu'un nouveau Pod a été créé pour remplacer celui supprimé, maintenant ainsi le nombre de répliques spécifié.

# 8.10. Conclusion

Les RéplicaSets sont essentiels pour maintenir la haute disponibilité des applications déployées sur Kubernetes. Ils assurent que le nombre souhaité de répliques de Pods est en cours d'exécution, même en cas de panne. Passons maintenant à la leçon suivante sur les Deployments, qui fournissent des fonctionnalités avancées de gestion des RéplicaSets.
