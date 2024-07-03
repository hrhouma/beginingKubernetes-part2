# Exemple pour illustrer le concept des namespaces avec Minikube. 
- Les namespaces permettent de diviser les ressources Kubernetes en différentes zones de manière logique. 

### Étapes :

1. **Lancer Minikube** :

    ```bash
    minikube start
    ```

2. **Créer des namespaces** :

    ```bash
    kubectl create namespace dev
    kubectl create namespace prod
    ```

3. **Déployer une application dans le namespace `dev`** :

    Créer un fichier `deployment-dev.yaml` pour définir un déploiement dans le namespace `dev` :

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-deployment
      namespace: dev
    spec:
      replicas: 2
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
            image: nginx:1.14.2
            ports:
            - containerPort: 80
    ```

    Appliquer ce déploiement :

    ```bash
    kubectl apply -f deployment-dev.yaml
    ```

4. **Déployer une application dans le namespace `prod`** :

    Créer un fichier `deployment-prod.yaml` pour définir un déploiement dans le namespace `prod` :

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-deployment
      namespace: prod
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
            image: nginx:1.14.2
            ports:
            - containerPort: 80
    ```

    Appliquer ce déploiement :

    ```bash
    kubectl apply -f deployment-prod.yaml
    ```

5. **Lister les déploiements dans chaque namespace** :

    ```bash
    kubectl get deployments -n dev
    kubectl get deployments -n prod
    ```

6. **Accéder aux logs des pods dans un namespace spécifique** :

    Lister les pods dans le namespace `dev` :

    ```bash
    kubectl get pods -n dev
    ```

    Obtenir les logs d'un pod spécifique dans le namespace `dev` :

    ```bash
    kubectl logs <pod-name> -n dev
    ```

7. **Nettoyer les ressources** :

    Supprimer les déploiements et namespaces :

    ```bash
    kubectl delete -f deployment-dev.yaml
    kubectl delete -f deployment-prod.yaml
    kubectl delete namespace dev
    kubectl delete namespace prod
    ```

### Résumé des commandes :

```bash
minikube start

kubectl create namespace dev
kubectl create namespace prod

kubectl apply -f deployment-dev.yaml
kubectl apply -f deployment-prod.yaml

kubectl get deployments -n dev
kubectl get deployments -n prod

kubectl get pods -n dev
kubectl logs <pod-name> -n dev

kubectl delete -f deployment-dev.yaml
kubectl delete -f deployment-prod.yaml
kubectl delete namespace dev
kubectl delete namespace prod
```

Cet exemple montre comment créer et utiliser des namespaces pour séparer les ressources Kubernetes dans un cluster Minikube.
