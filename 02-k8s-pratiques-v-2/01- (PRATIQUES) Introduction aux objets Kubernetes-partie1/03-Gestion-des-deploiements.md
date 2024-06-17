# Tutoriel Kubernetes: Gestion des Déploiements

# 1 - Introduction

Ce tutoriel explique comment gérer les déploiements dans Kubernetes, en utilisant le `kubectl` et des fichiers de configuration YAML. Vous apprendrez à configurer un déploiement pour un serveur web Nginx simple.

# 2 - Prérequis

- Avoir installé `kubectl`.
- Avoir accès à un cluster Kubernetes comme Minikube ou un cluster dans un cloud.

# 3 - Étape 1: Création du fichier YAML pour le Déploiement

Un déploiement assure que le nombre spécifié de réplicas de pods roule en continu. Créez un fichier YAML pour le déploiement qui définit un serveur Nginx.

1. **Ouvrez votre éditeur de texte ou IDE** (Visual Studio Code par exemple).
2. **Créez un fichier nommé** `mydeployment.yaml`.
3. **Ajoutez le contenu suivant au fichier** :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
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
        image: nginx:alpine
        ports:
        - containerPort: 80
```

# 4- Étape 2: Déploiement de l'application

Déployez le fichier YAML dans votre cluster Kubernetes pour créer le déploiement.

- **Ouvrez un terminal**.
- **Naviguez au répertoire contenant** `mydeployment.yaml`.
- **Exécutez la commande** pour déployer :

```bash
kubectl apply -f mydeployment.yaml
```

# 5- Étape 3: Vérification du Déploiement

Après le déploiement, il est essentiel de vérifier son état pour s'assurer qu'il fonctionne comme prévu.

- **Pour obtenir des informations sur le déploiement, exécutez** :

```bash
kubectl get deployments
```


- **Pour obtenir des détails plus spécifiques sur le déploiement 'nginx', utilisez** :

```bash
kubectl describe deployment nginx
```



# 6- Étape 4: Suppression de quelques pods du déploiement et observation si nous avons encore le même nombre de pods

- Affichez les ids de vos pods en exécutant  *kubectl get pods*
- Récupérer l'id de 1 de vos pods
- Comptez le nombre de pods ( Nous avons 3 puisque c'est spécifié dans le déploiement)
- Supprimer ce pod en utilisant son id avec la commande *kubectl delete po id_pod_récupéré*
- Afficher les pods
- Comment vous expliquez que nous avons toujours 3 pods ???????



# 7 - Étape 4: Suppression du Déploiement

Pour supprimer le déploiement lorsque vous n'en avez plus besoin :

- **Exécutez la commande suivante** :

```bash
kubectl delete deployment nginx
```

# 8 - Conclusion

Ce tutoriel a guidé les étapes de la gestion d'un déploiement dans Kubernetes, couvrant la création, le déploiement, la vérification et la suppression. Vous avez appris à utiliser `kubectl` pour gérer des applications plus complexes en production.

# 9 - Résumé des Commandes

```bash
# Créer le fichier YAML pour le déploiement
echo "
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 1
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
        image: nginx:alpine
        ports:
        - containerPort: 80
" > mydeployment.yaml

# Déployer le déploiement
kubectl apply -f mydeployment.yaml

# Lister les déploiements pour vérifier l'état
kubectl get deployments

# Obtenir des détails sur le déploiement 'nginx'
kubectl describe deployment nginx

# Supprimer le déploiement
kubectl delete deployment nginx
```
