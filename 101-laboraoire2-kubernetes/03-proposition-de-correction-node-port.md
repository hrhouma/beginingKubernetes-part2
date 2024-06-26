# Tutoriel : Déployer une application Docker Compose sur Kubernetes avec NodePort

## Étape 1: Préparer les fichiers Docker Compose

Ci-dessous, le fichier `docker-compose.yml` pour une application utilisant MySQL et WordPress :

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

### Commandes Docker utiles (si vous travaillez avec des images personnalisées) :

```bash
nano docker-compose.yaml
cat docker-compose.yaml
docker-compose up -d
docker ps
docker login
docker images
docker tag wordpress:latest hrehouma1/wordpress:1.0
docker tag mysql:5.7 hrehouma1/mysql:5.7
docker push hrehouma1/wordpress:1.0
docker push hrehouma1/mysql:5.7
```

## Étape 2: Convertir la configuration Docker Compose en fichiers de déploiement Kubernetes

### Déploiement Kubernetes pour MySQL

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

### Service Kubernetes pour MySQL

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
  clusterIP: None
```

### PersistentVolume et PersistentVolumeClaim pour MySQL

Créez un fichier `mysql-pv.yaml` et `mysql-pvc.yaml` :

```yaml
# PersistentVolume
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

```yaml
# PersistentVolumeClaim
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

### Déploiement Kubernetes pour WordPress

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

### Service Kubernetes pour WordPress avec NodePort

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
    nodePort: 30080
  type: NodePort
```

## Étape 3: Déployer les services sur Kubernetes

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

3. **Accéder à votre service WordPress :**

   Utilisez la commande suivante pour obtenir l'URL Minikube :

   ```bash
   minikube service wordpress --url
   ```

## Résumé des commandes

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

# Obtenir l'URL du service WordPress
minikube service wordpress --url
```

Ce tutoriel vous guide pour déployer votre application Docker Compose sur Kubernetes en utilisant Minikube, et exposer votre service WordPress via un NodePort. Vous n'avez pas besoin de pusher les images sur Docker Hub puisque `mysql:5.7` et `wordpress:latest` sont déjà disponibles publiquement.


# Annexe 0 : Les différents objets de notre déploiement Docker Compose sur Kubernetes


1. **Services Kubernetes**:
   - Un service pour MySQL.
   - Un service pour WordPress avec NodePort pour accéder depuis l'extérieur.

2. **Déploiements Kubernetes**:
   - Un déploiement pour MySQL, indiquant l'utilisation de l'image `mysql:5.7`.
   - Un déploiement pour WordPress, utilisant l'image `wordpress:latest`.

3. **Volumes et Claims**:
   - Un PersistentVolume (PV) et un PersistentVolumeClaim (PVC) pour MySQL pour la persistance des données.

4. **Connexions/Relations**:
   - Connexion entre le service WordPress et le déploiement WordPress via le port 80.
   - Connexion entre le service MySQL et le déploiement MySQL via le port 3306.
   - Utilisation du volume par le déploiement MySQL pour la persistance des données.



# Annexe 1 :

- Ce schéma va représenter les services, les volumes, les déploiements et les connexions entre eux.

```plaintext
+----------------------+       +-------------------------+       +----------------------+
|    MySQL Service     |       |      WordPress Service   |       | External Access      |
|    (Cluster IP)      |       |      (NodePort 30080)    |       | (via Minikube)       |
|                      |       |                          |       |                      |
|       +-----------+  |       |       +---------------+  |       |       +-----------+  |
|       |           |  |       |       |               |  |       |       |           |  |
|       | MySQL     |<--------|-------| WordPress     |<---------|-------| Browser   |  |
|       | Deployment|  |       |       | Deployment    |  |       |       |           |  |
|       |           |  |       |       |               |  |       |       |           |  |
|       +-----------+  |       |       +---------------+  |       |       +-----------+  |
|                      |       |                          |       |                      |
+----------------------+       +-------------------------+       +----------------------+
       |                          |                                  
       v                          v                                  
+----------------------+       +-------------------------+       
|    Persistent        |       |       WordPress          |       
|    Volume Claim      |       |       Config Files       |       
|    (MySQL Data)      |       |       (WP Content)       |       
|                      |       |                          |       
|       +-----------+  |       |       +---------------+  |       
|       |           |  |       |       |               |  |       
|       | PV        |<--------|-------| PVC            |  |       
|       | (Storage) |  |       |       | (Storage)     |  |       
|       |           |  |       |       |               |  |       
|       +-----------+  |       |       +---------------+  |       
|                      |       |                          |       
+----------------------+       +-------------------------+       
```

Ce diagramme montre comment les composants se connectent et interagissent au sein de votre environnement Kubernetes. Il présente la relation entre les services, les déploiements et les volumes, ainsi que la façon dont l'accès externe est géré via Minikube. Vous pouvez l'ajuster pour correspondre plus précisément à votre configuration si nécessaire.

# Annexe 2 : 


```plaintext
[WordPress Service]
        |
        v
[WordPress Deployment] -----> [WordPress:latest]
        |                             ^
        |                             |
        v                             |
    [NodePort 30080] <------------ [Port 80]
        |
        v
-------------------------------- Kubernetes Cluster ------------------------------
        |
        v
   [MySQL Service]
        |
        v
[MySQL Deployment] ------> [MySQL:5.7]
        |
        v
 [PersistentVolume]
        |
        v
[PersistentVolumeClaim]
```

# Annexe 3

## Objets Kubernetes impliqués dans votre configuration, présentés sous forme de tableau :

| Type d'objet                 | Nom de l'objet           | Description                                       |
|------------------------------|--------------------------|---------------------------------------------------|
| Deployment                   | MySQL Deployment         | Exécute l'image de MySQL.                         |
| Deployment                   | WordPress Deployment     | Exécute l'image de WordPress et se connecte à MySQL. |
| Service                      | MySQL Service            | Expose MySQL à l'intérieur du cluster sur le port TCP 3306. |
| Service                      | WordPress Service        | Expose WordPress à l'extérieur du cluster via un NodePort sur le port TCP 30080. |
| PersistentVolume             | MySQL PersistentVolume   | Fournit un stockage persistant pour les données MySQL. |
| PersistentVolumeClaim        | MySQL PersistentVolumeClaim | Réclame l'espace de stockage spécifié par le PersistentVolume pour MySQL. |

- Ces six objets sont essentiels pour la gestion et l'exposition de votre application WordPress et de la base de données MySQL sur Kubernetes.
