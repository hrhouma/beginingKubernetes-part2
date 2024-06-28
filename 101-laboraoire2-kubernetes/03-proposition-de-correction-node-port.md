# (P1/4) Déployer une application Docker Compose sur Kubernetes avec NodePort

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



# (P2/4) Histoire de Marc et son Déploiement Kubernetes

Marc, un développeur passionné de technologies web, travaille sur une application e-commerce. Pour s'assurer que son application soit toujours disponible et scalable, il décide de migrer son déploiement local Docker Compose vers Kubernetes. Son application utilise WordPress pour le front-end et MySQL pour la base de données.

# 1 - Préparation des Fichiers Docker Compose

Marc commence par définir son fichier `docker-compose.yml` pour orchestrer ses conteneurs MySQL et WordPress.

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

Marc utilise ensuite les commandes Docker pour vérifier et démarrer ses services en arrière-plan :

```sh
nano docker-compose.yaml
cat docker-compose.yaml
docker-compose up -d
docker ps
```

# 2 -  Conversion en Kubernetes

Afin de profiter des fonctionnalités avancées de Kubernetes, Marc convertit son fichier Docker Compose en plusieurs fichiers YAML de déploiement Kubernetes.

#### Déploiement de MySQL

Marc crée le fichier `mysql-deployment.yaml` pour définir un `Deployment` Kubernetes pour MySQL. Ce `Deployment` spécifie l'image Docker `mysql:5.7`, les variables d'environnement nécessaires, et monte un volume persistant pour stocker les données.

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

# 3 - Service MySQL

Ensuite, Marc crée un service `ClusterIP` pour MySQL (`mysql-service.yaml`), qui permet à WordPress de se connecter à MySQL à l'intérieur du cluster Kubernetes via l'adresse `mysql:3306`.

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

# 4 - Volume Persistant

Pour garantir que les données de la base de données MySQL soient persistantes, Marc configure un `PersistentVolume` (PV) et un `PersistentVolumeClaim` (PVC).

```yaml
# PersistentVolume (mysql-pv.yaml)
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

# PersistentVolumeClaim (mysql-pvc.yaml)
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

# 5 - Déploiement de WordPress

Marc crée un autre `Deployment` pour WordPress (`wordpress-deployment.yaml`), où il spécifie l'image Docker `wordpress:latest` et les variables d'environnement pour se connecter à la base de données MySQL.

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

# 6 -  Service WordPress

Marc configure enfin un service `NodePort` pour exposer WordPress à l'extérieur du cluster (`wordpress-service.yaml`), permettant aux utilisateurs externes d'accéder à l'application via `http://<IP_du_noeud>:30080`.

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

### Déploiement et Accessibilité

Pour déployer toutes ces configurations sur Kubernetes, Marc exécute les commandes suivantes :

```sh
kubectl apply -f mysql-pv.yaml
kubectl apply -f mysql-pvc.yaml
kubectl apply -f mysql-deployment.yaml
kubectl apply -f mysql-service.yaml
kubectl apply -f wordpress-deployment.yaml
kubectl apply -f wordpress-service.yaml
```

Pour vérifier que tout fonctionne correctement et obtenir l'URL de son service WordPress, il utilise :

```sh
minikube service wordpress --url
```

# 7 - Accès Externe

Marc est ravi de voir que son application est accessible via `http://<IP_du_noeud>:30080`. Il sait maintenant que son application e-commerce peut gérer des volumes de trafic élevés tout en garantissant que les données des clients sont sécurisées et persistantes.

### Résumé des Objets Kubernetes

| Type d'objet        | Nom de l'objet           | Description                                                                 |
|---------------------|--------------------------|-----------------------------------------------------------------------------|
| **Deployment**      | MySQL Deployment         | Gère la création et la gestion du conteneur MySQL, incluant les variables d'environnement et le montage de volume. |
| **Deployment**      | WordPress Deployment     | Gère la création et la gestion du conteneur WordPress, incluant les variables d'environnement pour la connexion à MySQL. |
| **Service**         | MySQL Service            | Expose MySQL à l'intérieur du cluster via un `ClusterIP` sur le port 3306.  |
| **Service**         | WordPress Service        | Expose WordPress à l'extérieur du cluster via un `NodePort` sur le port 30080. |
| **PersistentVolume**| MySQL PersistentVolume   | Fournit une ressource de stockage physique pour les données MySQL.          |
| **PersistentVolumeClaim** | MySQL PersistentVolumeClaim | Demande de stockage pour les données MySQL, utilisant le PV configuré. |


# (P3/4) Instructions pour l'Évaluation

**Instructions :**

Veuillez vous référer aux annexes ci-dessous pour compléter la partie (P3/4) de ce travail. Assurez-vous que vos réponses sont claires, bien formatées et en bon français, car des points seront attribués en fonction de ces critères.

# Questions de Développement basées sur l'Histoire de Marc et son Déploiement Kubernetes

# Questions sur la Préparation et le Docker Compose

1. **Pourquoi Marc utilise-t-il un volume persistant (`db_data`) dans son fichier `docker-compose.yml` pour le service MySQL ?**
   - Référez-vous à l'Annexe 0 pour comprendre l'importance des volumes persistants dans Docker Compose. Pensez à la persistance des données même après l'arrêt ou le redémarrage des conteneurs.

2. **Quelle est la fonction des variables d'environnement définies dans le service WordPress du fichier `docker-compose.yml` ? Donnez deux exemples.**
   - Consultez l'Annexe 0 pour des exemples de variables d'environnement utilisées dans Docker Compose. Ces variables aident à configurer le service, comme `WORDPRESS_DB_HOST` pour définir l'adresse de la base de données.

# Questions sur la Conversion en Kubernetes

3. **Expliquez pourquoi Marc a besoin de créer un fichier `mysql-deployment.yaml` pour déployer MySQL sur Kubernetes.**
   - L'Annexe 1 fournit des détails sur la conversion des configurations Docker Compose en fichiers de déploiement Kubernetes. Les déploiements en Kubernetes permettent de gérer des applications conteneurisées, d'assurer la mise à l'échelle et de les maintenir en état de fonctionnement.

4. **Dans le fichier `mysql-deployment.yaml`, quel est le rôle de la section `volumeMounts` et pourquoi est-elle importante ?**
   - Lisez la section Déploiement de MySQL dans l'Annexe 1 pour plus d'informations sur les `volumeMounts`. Ils montent des volumes de stockage persistants à l'intérieur du conteneur pour stocker les données de MySQL.

5. **Comment le service MySQL (`mysql-service.yaml`) permet-il aux autres pods de se connecter à la base de données MySQL ?**
   - Reportez-vous à la description du Service MySQL dans l'Annexe 1 pour comprendre la connectivité des pods. Ce service crée une adresse stable et un port que les autres pods peuvent utiliser pour se connecter à MySQL.

# Questions sur les Services et l'Accessibilité

6. **Pourquoi Marc choisit-il un service de type `NodePort` pour exposer WordPress à l'extérieur du cluster Kubernetes ?**
   - Consultez la section Service WordPress dans l'Annexe 1 pour les raisons de l'utilisation d'un NodePort. Un NodePort expose le service sur un port spécifique de chaque nœud du cluster, permettant un accès externe.

7. **Décrivez le processus permettant à un utilisateur externe, comme Marc, d'accéder à l'application WordPress via un service `NodePort`.**
   - Référez-vous à l'Annexe 1 et au diagramme de l'Annexe 2 pour comprendre le processus d'accès externe. L'utilisateur utilise l'adresse IP du nœud et le numéro de port configuré pour accéder à l'application.

8. **Quelle commande Marc utilise-t-il pour obtenir l'URL du service WordPress une fois déployé sur Minikube ?**
   - Lisez la section Déploiement et Accessibilité dans l'Annexe 1 pour la commande exacte. Utilisez `minikube service wordpress --url`.

# Questions sur les Volumes Persistants

9. **Quelle est la différence entre un `PersistentVolume` (PV) et un `PersistentVolumeClaim` (PVC) ? Pourquoi sont-ils cruciaux pour le déploiement de MySQL de Marc ?**
   - Reportez-vous à la section Volume Persistant dans l'Annexe 1 pour les distinctions entre PV et PVC. Le PV fournit l'espace de stockage physique tandis que le PVC demande cet espace pour une utilisation spécifique.

10. **Expliquez comment le `PersistentVolumeClaim` (PVC) garantit la persistance des données pour MySQL.**
    - Consultez l'Annexe 1 et les explications sur le PVC pour comprendre son rôle dans la persistance des données. Le PVC lie l'espace de stockage au déploiement MySQL pour assurer que les données sont conservées.

# Questions sur les Objets Kubernetes

11. **Quel est le rôle principal d'un `Deployment` dans Kubernetes et comment Marc l'utilise-t-il pour ses services MySQL et WordPress ?**
    - Référez-vous à la section Déploiements Kubernetes dans l'Annexe 1 pour le rôle des `Deployments`. Les déploiements gèrent le cycle de vie des applications conteneurisées, assurant le déploiement, la mise à jour et la disponibilité continue.

12. **Pourquoi est-il important de définir les variables d'environnement dans les fichiers de déploiement Kubernetes pour les conteneurs MySQL et WordPress ? Donnez un exemple pour chaque conteneur.**
    - Consultez les fichiers `mysql-deployment.yaml` et `wordpress-deployment.yaml` dans l'Annexe 1 pour des exemples de variables d'environnement. Par exemple, `MYSQL_ROOT_PASSWORD` pour MySQL et `WORDPRESS_DB_HOST` pour WordPress.

13. **Marc a configuré un `PersistentVolume` avec `hostPath`. Quelles seraient les implications de cette configuration si le cluster Kubernetes est déployé sur plusieurs nœuds ?**
    - Lisez les implications de l'utilisation de `hostPath` dans l'Annexe 1 pour comprendre les conséquences dans un cluster multi-nœuds. `hostPath` lie le stockage à un nœud spécifique, ce qui peut poser des problèmes de disponibilité si le pod est déplacé vers un autre nœud.

# Résumé des Objets Kubernetes

14. **Complétez le tableau suivant en expliquant brièvement le rôle de chaque objet Kubernetes utilisé par Marc :**

| Type d'objet                 | Nom de l'objet           | Description                                       |
|------------------------------|--------------------------|---------------------------------------------------|
| Deployment                   | MySQL Deployment         | Gère le déploiement et la mise à l'échelle de MySQL sur Kubernetes. |
| Deployment                   | WordPress Deployment     | Gère le déploiement et la mise à l'échelle de WordPress sur Kubernetes. |
| Service                      | MySQL Service            | Expose MySQL à l'intérieur du cluster pour d'autres pods.            |
| Service                      | WordPress Service        | Expose WordPress à l'extérieur du cluster via un NodePort.          |
| PersistentVolume             | MySQL PersistentVolume   | Fournit un espace de stockage persistant pour les données MySQL.    |
| PersistentVolumeClaim        | MySQL PersistentVolumeClaim | Demande l'utilisation de l'espace de stockage fourni par le PV. |

- Référez-vous à l'Annexe 1 pour des descriptions détaillées des objets Kubernetes utilisés par Marc.

# Annexes

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

# Annexe 1 : Déploiement Kubernetes de Marc

#### Déploiement de MySQL

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

#### Service MySQL

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

#### PersistentVolume et PersistentVolumeClaim

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

#### Déploiement de WordPress

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

#### Service WordPress

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

### Déploiement et Accessibilité

Pour déployer toutes ces configurations sur Kubernetes, Marc exécute les commandes suivantes :

```sh
kubectl apply -f mysql-pv.yaml
kubectl apply -f mysql-pvc.yaml
kubectl apply -f mysql-deployment.yaml
kubectl apply -f mysql-service.yaml
kubectl apply -f wordpress-deployment.yaml
kubectl apply -f wordpress-service.yaml
```

Pour vérifier que tout fonctionne correctement et obtenir l'URL de son service WordPress, il utilise :

```sh
minikube service wordpress --url
```

### Annexe 2 : Diagramme des Objets Kubernetes

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

## Remarque :
Les points sont attribués en fonction de la clarté, de la mise en forme de la remise et de la qualité du français utilisé.

# (P4/4) Correction - à venir
