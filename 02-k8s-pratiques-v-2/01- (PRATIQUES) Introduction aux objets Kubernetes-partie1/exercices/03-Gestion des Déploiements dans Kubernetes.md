# Exercices pour la Gestion des Déploiements dans Kubernetes

# Exercice 1: Création et déploiement d'un serveur Apache

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

# Exercice 2: Configuration d'un déploiement Redis pour le caching

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

# Exercice 3: Mise en place d'un déploiement PostgreSQL pour une application de base de données

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

# Exercice 4: Déploiement de MySQL comme base de données relationnelle

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

# Exercice 5: Configuration de Node.js pour des applications serveur

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

# Exercice 6: Mise en place de Tomcat pour des applications Java

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


Voici les exercices manquants pour les applications restantes :

# Exercice 7: Configuration de Elasticsearch pour l'analyse de données

**Objectif :** Déployer Elasticsearch, un moteur de recherche et d'analyse puissant.

1. **Créez un fichier YAML nommé `elasticsearch-deployment.yaml`** avec le contenu suivant :
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: elasticsearch
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: elasticsearch
     template:
       metadata:
         labels:
           app: elasticsearch
       spec:
         containers:
         - name: elasticsearch
           image: elasticsearch:7.10.0
           ports:
           - containerPort: 9200
           - containerPort: 9300
   ```

2. **Déployez votre fichier YAML** :
   ```bash
   kubectl apply -f elasticsearch-deployment.yaml
   ```

3. **Vérifiez le déploiement** :
   ```bash
   kubectl get deployments
   kubectl describe deployment elasticsearch
   ```

4. **Suppression du déploiement** :
   ```bash
   kubectl delete deployment elasticsearch
   ```

# Exercice 8: Mise en place de RabbitMQ pour la gestion de messages

**Objectif :** Configurer et déployer RabbitMQ, un broker de messages.

1. **Créez un fichier YAML nommé `rabbitmq-deployment.yaml`** avec le contenu suivant :
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: rabbitmq
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: rabbitmq
     template:
       metadata:
         labels:
           app: rabbitmq
       spec:
         containers:
         - name: rabbitmq
           image: rabbitmq:3-management
           ports:
           - containerPort: 15672  # Port for management interface
           - containerPort: 5672  # Port for messaging
   ```

2. **Déployez votre fichier YAML** :
   ```bash
   kubectl apply -f rabbitmq-deployment.yaml
   ```

3. **Vérifiez le déploiement** :
   ```bash
   kubectl get deployments
   kubectl describe deployment rabbitmq
   ```

4. **Suppression du déploiement** :
   ```bash
   kubectl delete deployment rabbitmq
   ```

- Exercices pour le déploiement de Grafana, Jenkins, un environnement Java avec Maven, et Prometheus, complétant ainsi la gamme d'applications pour notre cours sur la gestion des déploiements dans Kubernetes.

# Exercice 9: Déploiement de Grafana pour la visualisation des données

**Objectif :** Déployer Grafana, une plateforme pour la visualisation et l'analyse de données.

1. **Créez un fichier YAML nommé `grafana-deployment.yaml`** avec le contenu suivant :
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: grafana
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: grafana
     template:
       metadata:
         labels:
           app: grafana
       spec:
         containers:
         - name: grafana
           image: grafana/grafana:latest
           ports:
           - containerPort: 3000
   ```

2. **Déployez votre fichier YAML** :
   ```bash
   kubectl apply -f grafana-deployment.yaml
   ```

3. **Vérifiez le déploiement** :
   ```bash
   kubectl get deployments
   kubectl describe deployment grafana
   ```

4. **Suppression du déploiement** :
   ```bash
   kubectl delete deployment grafana
   ```

# Exercice 10: Configuration de Jenkins pour l'intégration continue

**Objectif :** Déployer Jenkins, un outil d'automatisation, notamment pour l'intégration continue.

1. **Créez un fichier YAML nommé `jenkins-deployment.yaml`** avec le contenu suivant :
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: jenkins
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: jenkins
     template:
       metadata:
         labels:
           app: jenkins
       spec:
         containers:
         - name: jenkins
           image: jenkins/jenkins:lts
           ports:
           - containerPort: 8080
   ```

2. **Déployez votre fichier YAML** :
   ```bash
   kubectl apply -f jenkins-deployment.yaml
   ```

3. **Vérifiez le déploiement** :
   ```bash
   kubectl get deployments
   kubectl describe deployment jenkins
   ```

4. **Suppression du déploiement** :
   ```bash
   kubectl delete deployment jenkins
   ```

# Exercice 11: Déploiement de Java avec Maven

**Objectif :** Configurer et déployer un environnement Java avec Maven pour la gestion de projets.

1. **Créez un fichier YAML nommé `java-maven-deployment.yaml`** avec le contenu suivant :
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: java-maven
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: java-maven
     template:
       metadata:
         labels:
           app: java-maven
       spec:
         containers:
         - name: java-maven
           image: maven:3.9-openjdk-11
           ports:
           - containerPort: 8080
   ```

2. **Déployez votre fichier YAML** :
   ```bash
   kubectl apply -f java-maven-deployment.yaml
   ```

3. **Vérifiez le déploiement** :
   ```bash
   kubectl get deployments
   kubectl describe deployment java-maven
   ```

4. **Suppression du déploiement** :
   ```bash
   kubectl delete deployment java-maven
   ```

# Exercice 12: Configuration de Prometheus pour le monitoring

**Objectif :** Déployer Prometheus pour la surveillance des systèmes et des services.

1. **Créez un fichier YAML nommé `prometheus-deployment.yaml`** avec le contenu suivant :
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: prometheus
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: prometheus
     template:
       metadata:
         labels:
           app: prometheus
       spec:
         containers:
         - name: prometheus
           image: prom/prometheus:v2.26.0
           ports:
           - containerPort: 9090
   ```

2. **Déployez votre fichier YAML** :
   ```bash


   kubectl apply -f prometheus-deployment.yaml
   ```

3. **Vérifiez le déploiement** :
   ```bash
   kubectl get deployments
   kubectl describe deployment prometheus
   ```

4. **Suppression du déploiement** :
   ```bash
   kubectl delete deployment prometheus
   ```

## Exercices avec des déploiements multi-conteneurs dans Kubernetes :

# Exercice 13: Déploiement de WordPress avec MySQL

**Objectif :** Configurer et déployer un environnement WordPress avec une base de données MySQL dans le même Pod.

1. **Créez un fichier YAML nommé `wordpress-mysql-deployment.yaml`** avec le contenu suivant :
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
           ports:
           - containerPort: 80
           env:
           - name: WORDPRESS_DB_HOST
             value: localhost:3306
           - name: WORDPRESS_DB_USER
             value: wordpress
           - name: WORDPRESS_DB_PASSWORD
             value: wordpress
         - name: mysql
           image: mysql:5.7
           env:
           - name: MYSQL_ROOT_PASSWORD
             value: wordpress
           - name: MYSQL_DATABASE
             value: wordpress
           - name: MYSQL_USER
             value: wordpress
           - name: MYSQL_PASSWORD
             value: wordpress
   ```

2. **Déployez votre fichier YAML** :
   ```bash
   kubectl apply -f wordpress-mysql-deployment.yaml
   ```

3. **Vérifiez le déploiement** :
   ```bash
   kubectl get deployments
   kubectl describe deployment wordpress
   ```

4. **Suppression du déploiement** :
   ```bash
   kubectl delete deployment wordpress
   ```

### Exercice 14: Déploiement de Jenkins avec SonarQube

**Objectif :** Configurer et déployer Jenkins avec SonarQube dans le même Pod pour l'intégration et l'analyse de code continue.

1. **Créez un fichier YAML nommé `jenkins-sonarqube-deployment.yaml`** avec le contenu suivant :
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: jenkins-sonarqube
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: jenkins-sonarqube
     template:
       metadata:
         labels:
           app: jenkins-sonarqube
       spec:
         containers:
         - name: jenkins
           image: jenkins/jenkins:lts
           ports:
           - containerPort: 8080
         - name: sonarqube
           image: sonarqube:latest
           ports:
           - containerPort: 9000
   ```

2. **Déployez votre fichier YAML** :
   ```bash
   kubectl apply -f jenkins-sonarqube-deployment.yaml
   ```

3. **Vérifiez le déploiement** :
   ```bash
   kubectl get deployments
   kubectl describe deployment jenkins-sonarqube
   ```

4. **Suppression du déploiement** :
   ```bash
   kubectl delete deployment jenkins-sonarqube
   ```

### Exercice 15: Déploiement de Prometheus avec Grafana pour le monitoring

**Objectif :** Configurer et déployer Prometheus pour la collecte de métriques et Grafana pour la visualisation, dans le même Pod.

1. **Créez un fichier YAML nommé `prometheus-grafana-deployment.yaml`** avec le contenu suivant :
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: prometheus-grafana
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: prometheus-grafana
     template:
       metadata:
         labels:
           app: prometheus-grafana
       spec:
         containers:
         - name: prometheus
           image: prom/prometheus:v2.26.0
           ports:
           - containerPort: 9090
         - name: grafana
           image: grafana/grafana:latest
           ports:
           - containerPort: 3000
   ```

2. **Déployez votre fichier YAML** :
   ```bash
   kubectl apply -f prometheus-grafana-deployment.yaml
   ```

3. **Vérifiez le déploiement** :
   ```bash
   kubectl get deployments
   kubectl describe deployment prometheus-grafana
   ```

4. **Suppression du déploiement** :
   ```bash
   kubectl delete deployment prometheus-grafana
   ```

- Ce déploiement combiné vous permettra de voir comment deux services peuvent coopérer dans un même Pod pour fournir une solution complète de monitoring.
- Ils apprendront comment les conteneurs peuvent partager des ressources et communiquer efficacement, tout en maintenant une isolation nécessaire pour la sécurité et la gestion.
