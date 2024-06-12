# Exercices pour la Gestion des Services dans Kubernetes

# Exercice 1: Création et déploiement d'un Service pour un serveur Nginx

**Objectif :** Configurer et déployer un Service pour exposer un serveur Nginx.

1. **Créez un fichier YAML nommé `nginx-deployment.yaml`** avec le contenu suivant :
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: nginx
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
           image: nginx:latest
           ports:
           - containerPort: 80
   ```

2. **Déployez votre fichier YAML pour le déploiement** :
   ```bash
   kubectl apply -f nginx-deployment.yaml
   ```

3. **Créez un fichier YAML nommé `nginx-service.yaml`** avec le contenu suivant :
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
   ```

4. **Déployez votre fichier YAML pour le Service** :
   ```bash
   kubectl apply -f nginx-service.yaml
   ```

5. **Vérifiez le déploiement et le Service** :
   ```bash
   kubectl get deployments
   kubectl get services
   kubectl describe deployment nginx
   kubectl describe service nginx-service
   ```

6. **Suppression du déploiement et du Service** :
   ```bash
   kubectl delete deployment nginx
   kubectl delete service nginx-service
   ```

### Questions
1. Quelle commande avez-vous utilisée pour créer le déploiement Nginx ?
2. Quelle commande avez-vous utilisée pour créer le Service Nginx ?
3. Quelle est l'adresse IP et le port externe de votre Service Nginx ?

# Exercice 2: Configuration d'un Service pour une application Flask

**Objectif :** Configurer et déployer un Service pour une application Flask.

1. **Créez une application Flask simple** et déployez-la dans Kubernetes.
   - Utilisez l'image Docker `tiangolo/uwsgi-nginx-flask:python3.8`.

2. **Créez un fichier YAML nommé `flask-deployment.yaml`** avec le contenu suivant :
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: flask
   spec:
     replicas: 2
     selector:
       matchLabels:
         app: flask
     template:
       metadata:
         labels:
           app: flask
       spec:
         containers:
         - name: flask
           image: tiangolo/uwsgi-nginx-flask:python3.8
           ports:
           - containerPort: 80
   ```

3. **Déployez votre fichier YAML pour le déploiement** :
   ```bash
   kubectl apply -f flask-deployment.yaml
   ```

4. **Créez un fichier YAML nommé `flask-service.yaml`** avec le contenu suivant :
   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: flask-service
   spec:
     type: ClusterIP
     selector:
       app: flask
     ports:
       - protocol: TCP
         port: 80
         targetPort: 80
   ```

5. **Déployez votre fichier YAML pour le Service** :
   ```bash
   kubectl apply -f flask-service.yaml
   ```

6. **Vérifiez le déploiement et le Service** :
   ```bash
   kubectl get deployments
   kubectl get services
   kubectl describe deployment flask
   kubectl describe service flask-service
   ```

7. **Suppression du déploiement et du Service** :
   ```bash
   kubectl delete deployment flask
   kubectl delete service flask-service
   ```

### Questions
1. Quelle est la différence entre un Service de type `NodePort` et un Service de type `ClusterIP` ?
2. Comment pouvez-vous vérifier que votre Service `flask-service` fonctionne correctement ?

# Exercice 3: Mise à jour d'un Service

**Objectif :** Mettre à jour un Service existant pour qu'il utilise une nouvelle version de l'application.

1. **Modifiez votre fichier de déploiement Nginx pour utiliser une nouvelle image** :
   - Changez l'image à `nginx:alpine`.

2. **Appliquez les modifications au déploiement** :
   ```bash
   kubectl apply -f nginx-deployment.yaml
   ```

3. **Vérifiez que le Service pointe toujours vers les Pods mis à jour** :
   ```bash
   kubectl get pods
   kubectl get services
   kubectl describe service nginx-service
   ```

4. **Suppression du déploiement et du Service** :
   ```bash
   kubectl delete deployment nginx
   kubectl delete service nginx-service
   ```

### Questions
1. Quelle commande avez-vous utilisée pour mettre à jour le déploiement Nginx ?
2. Comment pouvez-vous vérifier que les nouveaux Pods utilisent la nouvelle image ?
3. Pourquoi est-il important de vérifier que le Service fonctionne toujours après la mise à jour ?

# Exercice 4: Balance de Charge avec un Service

**Objectif :** Configurer un Service pour équilibrer la charge entre plusieurs Pods.

1. **Créez un fichier YAML pour un déploiement avec 3 réplicas d'une application** :
   - Utilisez l'image `nginx`.

2. **Créez un fichier YAML pour un Service exposant les Pods** :
   - Utilisez le type de Service `LoadBalancer`.

3. **Déployez le déploiement et le Service** :
   ```bash
   kubectl apply -f <deployment-yaml>
   kubectl apply -f <service-yaml>
   ```

4. **Testez l'équilibrage de charge** :
   - Accédez au Service à plusieurs reprises et observez comment le trafic est distribué.

5. **Suppression du déploiement et du Service** :
   ```bash
   kubectl delete deployment <deployment-name>
   kubectl delete service <service-name>
   ```

### Questions
1. Quelle est la différence entre un Service de type `LoadBalancer` et les autres types de Services ?
2. Comment pouvez-vous vérifier que le Service équilibre correctement la charge entre les Pods ?

Ces exercices vous permettront de renforcer vos connaissances sur les Services dans Kubernetes et de vous familiariser avec les concepts de base nécessaires à leur configuration et à leur gestion.
