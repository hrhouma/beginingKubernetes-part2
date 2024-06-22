# Introduction
Ce laboratoire pratique vous guidera à travers les concepts de base de Kubernetes en utilisant Minikube, un outil qui exécute un cluster Kubernetes à nœud unique localement.

# Préparation
Assurez-vous que Minikube est installé sur votre machine. Vous pouvez suivre le guide officiel d'installation de [Minikube](https://minikube.sigs.k8s.io/docs/start/).

# Démarrer Minikube
1. Démarrez le cluster Minikube :
    ```bash
    minikube start
    ```
2. Vérifiez l'état du cluster :
    ```bash
    kubectl cluster-info
    ```

# Initialiser le Cluster
Minikube initialise automatiquement le cluster. Vous pouvez directement configurer kubectl pour utiliser Minikube :
```bash
kubectl config use-context minikube
```

# Déployer un Réseau de Pods
Minikube est livré avec divers plugins réseau. Pour simplifier, nous utiliserons le plugin réseau par défaut :
```bash
kubectl apply -f https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')
```

# Déployer l'Application DockerCoins
1. Clonez le dépôt DockerCoins :
    ```bash
    git clone https://github.com/dockersamples/dockercoins
    cd dockercoins
    ```

2. Construisez et taguez les images pour Minikube :
    ```bash
    eval $(minikube docker-env)
    docker-compose -f docker-compose.yml build
    ```

3. Déployez Redis :
    ```bash
    kubectl run redis --image=redis
    kubectl expose deployment redis --port 6379
    ```

4. Déployez les autres services :
    ```bash
    for SERVICE in hasher rng webui worker; do
      kubectl run $SERVICE --image=dockercoins/$SERVICE -l app=$SERVICE
    done
    ```

5. Exposez les services nécessaires :
    ```bash
    kubectl expose deployment rng --port 80
    kubectl expose deployment hasher --port 80
    ```

# Accéder à l'Interface Web
Minikube fournit un service pour exposer les applications sur votre machine locale. Utilisez la commande Minikube service pour accéder à l'interface web :
```bash
kubectl expose deployment webui --type=NodePort --port=80
minikube service webui
```

# Nettoyage
Pour nettoyer votre cluster :
```bash
kubectl delete deployment redis rng hasher webui worker
kubectl delete service redis rng hasher webui
minikube stop
minikube delete
```

# Concepts Additionnels de Kubernetes
# Comprendre kubectl
- Utilisez `kubectl get` pour lister les ressources.
- Utilisez `kubectl describe` pour obtenir des informations détaillées sur les ressources.
- Utilisez `kubectl logs` pour afficher les journaux des pods.
- Utilisez `kubectl scale` pour mettre à l'échelle les déploiements.

# Déploiements et ReplicaSets
- Créez des déploiements pour gérer les applications sans état.
- Mettez à l'échelle les déploiements avec `kubectl scale`.

# Services
- Les services ClusterIP fournissent un accès interne.
- Les services NodePort exposent les applications sur un port statique sur chaque nœud.

# Mises à Jour Progressives
- Mettez à jour les déploiements avec `kubectl set image`.
- Surveillez les déploiements avec `kubectl rollout status`.

# Conclusion
Ce laboratoire adapté fournit une introduction de base à Kubernetes en utilisant Minikube. Il couvre les opérations essentielles telles que le démarrage d'un cluster, le déploiement d'applications et la gestion des ressources. Minikube est un excellent outil pour le développement et l'expérimentation locaux de Kubernetes.
# Références :
- https://training.play-with-kubernetes.com/kubernetes-workshop/
