### Tutoriel : Déployer une application Docker Compose sur Kubernetes avec Minikube et ClusterIP

Dans ce tutoriel, nous allons transformer une application Docker Compose utilisant MySQL et WordPress en déploiements Kubernetes avec des services ClusterIP.

#### Étape 1: Préparer les fichiers Docker Compose

Voici votre fichier `docker-compose.yml` pour une application avec MySQL et WordPress :

```yaml
version: '3.3'

services:
   db:
     image: mysql:5.7
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: somewordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8000:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
       WORDPRESS_DB_NAME: wordpress
volumes:
    db_data: {}
```

#### Étape 2: Convertir la configuration Docker Compose en fichiers de déploiement Kubernetes

##### Déploiement Kubernetes pour MySQL

Créez un fichier `mysql-deployment.yaml` :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:5.7
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: "somewordpress"
        - name: MYSQL_DATABASE
          value: "wordpress"
        - name: MYSQL_USER
          value: "wordpress"
        - name: MYSQL_PASSWORD
          value: "wordpress"
        volumeMounts:
        - name: mysql-storage
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-storage
        persistentVolumeClaim:
          claimName: mysql-pvc
```

##### Service Kubernetes pour MySQL

Créez un fichier `mysql-service.yaml` :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  selector:
    app: mysql
  ports:
  - protocol: TCP
    port: 3306
    targetPort: 3306
```

##### PersistentVolume et PersistentVolumeClaim pour MySQL

Créez un fichier `mysql-pv.yaml` :

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
```

Créez un fichier `mysql-pvc.yaml` :

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

##### Déploiement Kubernetes pour WordPress

Créez un fichier `wordpress-deployment.yaml` :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wordpress
  template:
    metadata:
      labels:
        app: wordpress
    spec:
      containers:
      - name: wordpress
        image: wordpress:latest
        env:
        - name: WORDPRESS_DB_HOST
          value: "mysql:3306"
        - name: WORDPRESS_DB_USER
          value: "wordpress"
        - name: WORDPRESS_DB_PASSWORD
          value: "wordpress"
        - name: WORDPRESS_DB_NAME
          value: "wordpress"
        ports:
        - containerPort: 80
```

##### Service Kubernetes pour WordPress avec ClusterIP

Créez un fichier `wordpress-service.yaml` :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: wordpress
spec:
  selector:
    app: wordpress
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```

#### Étape 3: Déployer les services sur Kubernetes

1. **Appliquer le PersistentVolume et PersistentVolumeClaim pour MySQL :**

   ```bash
   kubectl apply -f mysql-pv.yaml
   kubectl apply -f mysql-pvc.yaml
   ```

2. **Appliquer les déploiements et services :**

   ```bash
   kubectl apply -f mysql-deployment.yaml
   kubectl apply -f mysql-service.yaml

   kubectl apply -f wordpress-deployment.yaml
   kubectl apply -f wordpress-service.yaml
   ```

3. **Exposer le service WordPress via Minikube :**

   Utilisez la commande suivante pour accéder à votre service WordPress :

   ```bash
   minikube service wordpress
   ```

### Résumé des commandes

```bash
# Se connecter à Docker Hub (si nécessaire)
docker login

# Appliquer les configurations Kubernetes
kubectl apply -f mysql-pv.yaml
kubectl apply -f mysql-pvc.yaml
kubectl apply -f mysql-deployment.yaml
kubectl apply -f mysql-service.yaml
kubectl apply -f wordpress-deployment.yaml
kubectl apply -f wordpress-service.yaml

# Exposer et accéder au service WordPress via Minikube
minikube service wordpress
```

Avec ces étapes, vous pouvez déployer votre application Docker Compose sur Kubernetes en utilisant Minikube et des services ClusterIP. Vous n'avez pas besoin de pusher les images sur Docker Hub puisque `mysql:5.7` et `wordpress:latest` sont déjà disponibles publiquement. Utilisez Minikube pour accéder à votre service WordPress facilement.
