# Tutoriel Kubernetes: Introduction aux Services

# 1 - Introduction

Les Services dans Kubernetes permettent de communiquer avec un ou plusieurs Pods. Habituellement, les Pods sont éphémères et peuvent changer fréquemment d'adresse IP. Les Services fournissent une adresse IP fixe et un nom DNS pour un ensemble de Pods, et chargent également la balance du trafic vers ces Pods.

# Référence très importante:

- https://www.youtube.com/watch?v=5lzUpDtmWgM
  
# 2 - Prérequis

- Avoir installé `kubectl`.
- Avoir accès à un cluster Kubernetes.
- Avoir fait le lab précédent 03-Gestion-des-deploiments et avoir crée 1 déploiement nginx avec 3 pods avec cette section
```yaml
  template:
    metadata:
      labels:
        app: nginx
  ```
- Avoir regardé la vidéo sur les services de type nodePort : ##  https://www.youtube.com/watch?v=5lzUpDtmWgM 

# 3 - Étape 1: Création du fichier YAML pour le Service

Pour exposer le déploiement `nginx` créé précédemment, nous utiliserons un Service de type `NodePort` qui rendra l'application accessible de l'extérieur du cluster Kubernetes.

1. **Ouvrez votre éditeur de texte ou IDE**.
2. **Créez un fichier nommé** `myservice.yaml`.
3. **Ajoutez le contenu suivant au fichier** :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30008
```


- particulièrement, cette section est importante 
```yaml
spec:
  type: NodePort
  selector:
    app: nginx
```
puisque nous allons communiquer avec les pods qui ont cette section
```yaml
  template:
    metadata:
      labels:
        app: nginx
```
- En effet, rappelez vous que dans la pratique précédente, nous avons crée ce déploiement

*mydeployment.yaml*
```ssh
kubect apply -f mydeployment.yaml
```

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



# 4- Étape 2: Déploiement du Service

Déployez le Service dans votre cluster Kubernetes pour exposer le déploiement `nginx`.

- **Ouvrez un terminal**.
- **Naviguez au répertoire contenant** `myservice.yaml`.
- **Exécutez la commande** pour créer le Service :

```bash
kubectl apply -f myservice.yaml
```

# 5- Étape 3: Vérification du Service

Après avoir créé le Service, vérifiez qu'il est correctement configuré et qu'il route le trafic vers votre Pod `nginx`.

- **Pour obtenir des informations sur le Service, exécutez** :

```bash
kubectl get services
```

- **Pour obtenir des détails plus spécifiques sur le Service 'nginx-service', utilisez** :

```bash
kubectl describe service nginx-service
```


```bash
minikube ip
curl IP: 30008

```

# 6 - Conclusion

Les Services sont essentiels pour la gestion du trafic réseau dans les applications Kubernetes, fournissant un point d'accès stable aux applications dynamiques. Ce tutoriel vous a guidé à travers la création d'un Service qui expose un déploiement Nginx dans un cluster Kubernetes.

# 7 - Résumé des Commandes (IGNOREZ - ANCIEN)

```bash
# Créer le fichier YAML pour le Service
echo "
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
" > myservice.yaml

# Déployer le Service
kubectl apply -f myservice.yaml

# Lister les services pour vérifier l'état
kubectl get services

# Obtenir des détails sur le service 'nginx-service'
kubectl describe service nginx-service
```

Ce guide fournit une introduction pratique à la gestion des services dans Kubernetes, ce qui est crucial pour la production et la gestion efficace des applications.
