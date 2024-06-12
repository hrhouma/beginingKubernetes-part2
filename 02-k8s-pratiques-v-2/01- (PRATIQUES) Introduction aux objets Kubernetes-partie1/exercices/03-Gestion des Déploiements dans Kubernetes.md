### Exercices pour la Gestion des Déploiements dans Kubernetes

#### Exercice 1: Création et déploiement d'un serveur Apache

**Objectif :** Configurer et déployer un déploiement pour un serveur web Apache.

1. **Créez un fichier YAML nommé `apache-deployment.yaml`** avec le contenu suivant :
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: apache
   spec:
     replicas: 2
     selector:
       matchLabels:
         app: apache
     template:
       metadata:
         labels:
           app: apache
       spec:
         containers:
         - name: apache
           image: httpd:latest
           ports:
           - containerPort: 80
   ```

2. **Déployez votre fichier YAML** :
   ```bash
   kubectl apply -f apache-deployment.yaml
   ```

3. **Vérifiez le déploiement** :
   ```bash
   kubectl get deployments
   kubectl describe deployment apache
   ```

4. **Suppression du déploiement** :
   ```bash
   kubectl delete deployment apache
   ```

#### Exercice 2: Configuration d'un déploiement Redis pour le caching

**Objectif :** Configurer et déployer un déploiement pour une instance Redis.

1. **Créez un fichier YAML nommé `redis-deployment.yaml`** avec le contenu suivant :
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: redis
   spec:
     replicas: 3
     selector:
       matchLabels:
         app: redis
     template:
       metadata:
         labels:
           app: redis
       spec:
         containers:
         - name: redis
           image: redis:alpine
           ports:
           - containerPort: 6379
   ```

2. **Déployez votre fichier YAML** :
   ```bash
   kubectl apply -f redis-deployment.yaml
   ```

3. **Vérifiez le déploiement** :
   ```bash
   kubectl get deployments
   kubectl describe deployment redis
   ```

4. **Suppression du déploiement** :
   ```bash
   kubectl delete deployment redis
   ```

#### Exercice 3: Mise en place d'un déploiement PostgreSQL pour une application de base de données

**Objectif :** Déployer un serveur PostgreSQL dans un environnement Kubernetes.

1. **Créez un fichier YAML nommé `postgres-deployment.yaml`** avec le contenu suivant :
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: postgres
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: postgres
     template:
       metadata:
         labels:
           app: postgres
       spec:
         containers:
         - name: postgres
           image: postgres:latest
           ports:
           - containerPort: 5432
   ```

2. **Déployez votre fichier YAML** :
   ```bash
   kubectl apply -f postgres-deployment.yaml
   ```

3. **Vérifiez le déploiement** :
   ```bash
   kubectl get deployments
   kubectl describe deployment postgres
   ```

4. **Suppression du déploiement** :
   ```bash
   kubectl delete deployment postgres
   ```

Voici d'autres exercices pour compléter la série, couvrant les applications restantes de votre liste :

#### Exercice 4: Déploiement de MySQL comme base de données relationnelle

**Objectif :** Configurer et déployer MySQL dans un environnement Kubernetes.

1. **Créez un fichier YAML nommé `mysql-deployment.yaml`** avec le contenu suivant :
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
           ports:
           - containerPort: 3306
   ```

2. **Déployez votre fichier YAML** :
   ```bash
   kubectl apply -f mysql-deployment.yaml
   ```

3. **Vérifiez le déploiement** :
   ```bash
   kubectl get deployments
   kubectl describe deployment mysql
   ```

4. **Suppression du déploiement** :
   ```bash
   kubectl delete deployment mysql
   ```

#### Exercice 5: Configuration de Node.js pour des applications serveur

**Objectif :** Déployer une application Node.js simple dans Kubernetes.

1. **Créez un fichier YAML nommé `nodejs-deployment.yaml`** avec le contenu suivant :
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: nodejs-app
   spec:
     replicas: 2
     selector:
       matchLabels:
         app: nodejs
     template:
       metadata:
         labels:
           app: nodejs
       spec:
         containers:
         - name: nodejs
           image: node:14-alpine
           ports:
           - containerPort: 3000
   ```

2. **Déployez votre fichier YAML** :
   ```bash
   kubectl apply -f nodejs-deployment.yaml
   ```

3. **Vérifiez le déploiement** :
   ```bash
   kubectl get deployments
   kubectl describe deployment nodejs-app
   ```

4. **Suppression du déploiement** :
   ```bash
   kubectl delete deployment nodejs-app
   ```

#### Exercice 6: Mise en place de Tomcat pour des applications Java

**Objectif :** Déployer un conteneur de servlets Tomcat.

1. **Créez un fichier YAML nommé `tomcat-deployment.yaml`** avec le contenu suivant :
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: tomcat
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: tomcat
     template:
       metadata:
         labels:
           app: tomcat
       spec:
         containers:
         - name: tomcat
           image: tomcat:9.0
           ports:
           - containerPort: 8080
   ```

2. **Déployez votre fichier YAML** :
   ```bash
   kubectl apply -f tomcat-deployment.yaml
   ```

3. **Vérifiez le déploiement** :
   ```bash
   kubectl get deployments
   kubectl describe deployment tomcat
   ```

4. **Suppression du déploiement** :
   ```bash
   kubectl delete deployment tomcat
   ```

