# Leçon09 : Les Deployments en détail

# 1 - Introduction

Dans cette leçon, nous allons explorer l'objet Deployment en détail. Les Deployments sont un composant essentiel de Kubernetes, permettant de gérer les mises à jour et la disponibilité continue des applications sans downtime.

# 2 - Pourquoi Utiliser un Deployment?

Un Deployment permet de déployer des mises à jour sans downtime, de gérer les mises à jour échouées et de s'assurer que l'application reste disponible pour les utilisateurs en tout temps. Il permet de maintenir plusieurs répliques de Pods, garantissant la redondance et la résilience.

# 3 -  Fonctionnement d'un Deployment

Lorsqu'un Deployment est appliqué, il crée un ReplicaSet pour maintenir le nombre de Pods spécifié. Si une mise à jour est nécessaire, le Deployment crée un nouveau ReplicaSet avec les nouvelles spécifications, puis remplace progressivement les anciens Pods par les nouveaux Pods, garantissant ainsi une transition fluide sans interruption de service.

# 4 - Exemple de Fichier de Configuration pour un Deployment

Voici un exemple de fichier de configuration pour un Deployment :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
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
- Le Deployment crée et maintient trois répliques du Pod `nginx`.
- Le sélecteur `app: nginx` permet de sélectionner les Pods à gérer.
- Le modèle de Pod spécifie l'image `nginx:1.15.4` et le port 80.

# 5 -  Rolling Updates

Lors d'une mise à jour, Kubernetes utilise le mécanisme de Rolling Update pour remplacer progressivement les anciens Pods par les nouveaux, en s'assurant qu'il y a toujours des Pods disponibles pour servir les requêtes.

1. **Créer un nouveau ReplicaSet** : Le Deployment crée un nouveau ReplicaSet avec les nouvelles spécifications.
2. **Ajouter de nouveaux Pods** : Un nouveau Pod est créé.
3. **Supprimer les anciens Pods** : Un ancien Pod est supprimé.
4. **Répéter jusqu'à ce que tous les Pods soient remplacés**.

Ce processus garantit que le service reste disponible pendant toute la durée de la mise à jour.

# 6 - Gestion des Échecs

Si une mise à jour échoue (par exemple, en raison d'une image de conteneur incorrecte), Kubernetes ne poursuit pas la mise à jour et conserve les anciens Pods en cours d'exécution. L'administrateur peut alors effectuer un rollback pour revenir à la version précédente du Deployment.

## 6.1 - Exemple de Rolling Update

1. Créez un fichier nommé `deployment.yaml` avec le contenu ci-dessus.
2. Appliquez le Deployment :
    ```sh
    kubectl apply -f deployment.yaml
    ```
3. Vérifiez les Pods créés :
    ```sh
    kubectl get pods
    ```

## 6.2 - Mise à Jour d'un Deployment

Pour mettre à jour un Deployment, modifiez le fichier de configuration et réappliquez-le. Par exemple, pour mettre à jour l'image à `nginx:1.15.5` :

1. Modifiez `deployment.yaml` :
    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-deployment
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
            image: nginx:1.15.5
            ports:
            - containerPort: 80
    ```

2. Réappliquez le Deployment :
    ```sh
    kubectl apply -f deployment.yaml
    ```

3. Surveillez la mise à jour :
    ```sh
    kubectl rollout status deployment/nginx-deployment
    ```

## 6.3 - Rollback

Pour effectuer un rollback à une version précédente du Deployment :

1. Consultez l'historique des déploiements :
    ```sh
    kubectl rollout history deployment/nginx-deployment
    ```

2. Effectuez un rollback à la version précédente :
    ```sh
    kubectl rollout undo deployment/nginx-deployment
    ```

3. Vérifiez le statut du rollback :
    ```sh
    kubectl rollout status deployment/nginx-deployment
    ```

# 7 - Conclusion

Les Deployments sont essentiels pour gérer les mises à jour et assurer la disponibilité continue des applications dans Kubernetes. Ils permettent de déployer des mises à jour en douceur, de gérer les échecs et de revenir à des versions antérieures en cas de problème. Passons maintenant à la leçon suivante où nous mettrons en pratique ces concepts avec un projet concret.
