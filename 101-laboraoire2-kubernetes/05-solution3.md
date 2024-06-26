Pour exposer un service utilisant `ClusterIP` sur Minikube et le rendre accessible depuis votre navigateur, vous avez plusieurs options, mais l'utilisation d'un Ingress est une méthode courante et efficace. Voici comment vous pouvez configurer un Ingress sur Minikube pour votre service WordPress.

### Étape 1: Activer l'add-on Ingress dans Minikube

Minikube fournit un add-on Ingress que vous pouvez activer facilement.

```bash
minikube addons enable ingress
```

### Étape 2: Créer un Ingress pour WordPress

Vous devez définir un fichier Ingress pour votre service WordPress. Créez un fichier nommé `wordpress-ingress.yaml`.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: wordpress-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: wordpress.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: wordpress
            port:
              number: 80
```

### Étape 3: Modifier votre fichier hosts (local machine)

Pour accéder à votre service WordPress via le nom d'hôte `wordpress.local`, vous devez ajouter une entrée dans votre fichier hosts.

1. Ouvrez votre fichier hosts. Sur la plupart des systèmes Unix-like, ce fichier est situé à `/etc/hosts`. Sous Windows, il est situé à `C:\Windows\System32\drivers\etc\hosts`.
2. Ajoutez la ligne suivante :

   ```plaintext
   127.0.0.1 wordpress.local
   ```

### Étape 4: Déployer vos services et Ingress

1. **Déployer le PersistentVolume et PersistentVolumeClaim pour MySQL :**

   ```bash
   kubectl apply -f mysql-pv.yaml
   kubectl apply -f mysql-pvc.yaml
   ```

2. **Déployer les déploiements et services :**

   ```bash
   kubectl apply -f mysql-deployment.yaml
   kubectl apply -f mysql-service.yaml

   kubectl apply -f wordpress-deployment.yaml
   kubectl apply -f wordpress-service.yaml
   ```

3. **Déployer l'Ingress :**

   ```bash
   kubectl apply -f wordpress-ingress.yaml
   ```

### Étape 5: Accéder au service

Une fois que tout est déployé, vous pouvez accéder à votre service WordPress via le navigateur en utilisant l'URL `http://wordpress.local`.

### Résumé des fichiers et commandes

#### `mysql-deployment.yaml`

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

#### `mysql-service.yaml`

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

#### `mysql-pv.yaml`

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

#### `mysql-pvc.yaml`

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

#### `wordpress-deployment.yaml`

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

#### `wordpress-service.yaml`

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

#### `wordpress-ingress.yaml`

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: wordpress-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: wordpress.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: wordpress
            port:
              number: 80
```

#### Commandes à exécuter

```bash
# Activer l'add-on Ingress
minikube addons enable ingress

# Appliquer les configurations Kubernetes
kubectl apply -f mysql-pv.yaml
kubectl apply -f mysql-pvc.yaml
kubectl apply -f mysql-deployment.yaml
kubectl apply -f mysql-service.yaml
kubectl apply -f wordpress-deployment.yaml
kubectl apply -f wordpress-service.yaml

# Déployer l'Ingress
kubectl apply -f wordpress-ingress.yaml

# Mettre à jour le fichier hosts
echo "127.0.0.1 wordpress.local" | sudo tee -a /etc/hosts
```

Avec ces étapes, vous pouvez déployer votre application Docker Compose sur Kubernetes en utilisant Minikube, des services ClusterIP, et un Ingress pour rendre votre application accessible depuis le navigateur.
