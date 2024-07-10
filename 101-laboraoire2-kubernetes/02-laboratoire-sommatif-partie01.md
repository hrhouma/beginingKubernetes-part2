### Commandes pour le TP avec Minikube
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
# Exemple 1
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

#### Étape 1 : Préparation de l'Environnement

**Installer Minikube**
- Commande d'installation : 
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

**Démarrer Minikube**
- Commande pour démarrer Minikube : 
```bash
minikube start
```

#### Étape 2 : Création et Poussée de l'Image Docker

**Créer une image Docker :**

**Créer un fichier `Dockerfile` avec le contenu suivant :**
```dockerfile
# Utiliser l'image de base d'Apache
FROM httpd:2.4

# Copier le fichier HTML dans le répertoire approprié
COPY ./index.html /usr/local/apache2/htdocs/
```

**Créer un fichier `index.html` avec le contenu suivant :**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mon Application</title>
</head>
<body>
    <h1>Bienvenue sur mon site web déployé avec Docker et Kubernetes</h1>
</body>
</html>
```

**Construire l'image Docker :**
- Commande pour construire l'image Docker : 
```bash
docker build -t votre_nom_utilisateur_docker_hub/mon-application:v1 .
```

**Pousser l'image sur Docker Hub :**
- Connexion à Docker Hub : 
```bash
docker login
```
- Commande pour pousser l'image : 
```bash
docker push votre_nom_utilisateur_docker_hub/mon-application:v1
```

#### Étape 3 : Déploiement sur Kubernetes avec Minikube

**Créer les fichiers de déploiement Kubernetes :**

**Fichier `deployment.yaml` :**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mon-application
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mon-application
  template:
    metadata:
      labels:
        app: mon-application
    spec:
      containers:
      - name: mon-application
        image: votre_nom_utilisateur_docker_hub/mon-application:v1
        ports:
        - containerPort: 80
```

**Fichier `service.yaml` :**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: mon-application-service
spec:
  type: NodePort
  selector:
    app: mon-application
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30007
```

**Appliquer les fichiers YAML :**
- Commande pour appliquer les fichiers : 
```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

**Vérifier le déploiement :**
- Commande pour vérifier les pods : 
```bash
kubectl get pods
```

**Exposez votre application avec un service NodePort :**
- Commande pour obtenir l'URL de l'application :
```bash
minikube service mon-application-service --url
```

#### Nettoyage

- Commande pour supprimer le namespace : 
```bash
kubectl delete namespace <votre_namespace>
```
- Commande pour arrêter Minikube : 
```bash
minikube stop
```
- Commande pour supprimer Minikube : 
```bash
minikube delete
```

Ces commandes vous permettront de réaliser toutes les étapes nécessaires pour déployer une application simple sur Kubernetes avec Minikube. N'oubliez pas de documenter chaque étape et d'inclure les captures d'écran nécessaires.

# Résumé: 

- Exemple de fichier de script shell contenant toutes les commandes nécessaires avec les descriptions en commentaires :

```bash
#!/bin/bash

# Étape 1 : Préparation de l'Environnement

# Installer Minikube
# Téléchargez et installez Minikube
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && sudo install minikube-linux-amd64 /usr/local/bin/minikube

# Démarrer Minikube
minikube start

# Étape 2 : Création et Poussée de l'Image Docker

# Créer un fichier Dockerfile
echo "FROM httpd:2.4
COPY ./index.html /usr/local/apache2/htdocs/" > Dockerfile

# Créer un fichier index.html
echo "<!DOCTYPE html>
<html lang=\"en\">
<head>
    <meta charset=\"UTF-8\">
    <meta name=\"viewport\" content=\"width=device-width, initial-scale=1.0\">
    <title>Mon Application</title>
</head>
<body>
    <h1>Bienvenue sur mon site web déployé avec Docker et Kubernetes</h1>
</body>
</html>" > index.html

# Construire l'image Docker
# Remplacez 'votre_nom_utilisateur_docker_hub' par votre nom d'utilisateur Docker Hub
docker build -t votre_nom_utilisateur_docker_hub/mon-application:v1 .

# Connexion à Docker Hub
docker login

# Pousser l'image sur Docker Hub
docker push votre_nom_utilisateur_docker_hub/mon-application:v1

# Étape 3 : Déploiement sur Kubernetes avec Minikube

# Créer le fichier deployment.yaml
echo "apiVersion: apps/v1
kind: Deployment
metadata:
  name: mon-application
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mon-application
  template:
    metadata:
      labels:
        app: mon-application
    spec:
      containers:
      - name: mon-application
        image: votre_nom_utilisateur_docker_hub/mon-application:v1
        ports:
        - containerPort: 80" > deployment.yaml

# Créer le fichier service.yaml
echo "apiVersion: v1
kind: Service
metadata:
  name: mon-application-service
spec:
  type: NodePort
  selector:
    app: mon-application
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30007" > service.yaml

# Appliquer les fichiers YAML
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml

# Vérifier le déploiement
kubectl get pods

# Obtenir l'URL de l'application
minikube service mon-application-service --url

# Nettoyage

# Supprimer le namespace
kubectl delete namespace <votre_namespace>

# Arrêter Minikube
minikube stop

# Supprimer Minikube
minikube delete
```

Pour utiliser ce script :
1. Sauvegardez-le dans un fichier, par exemple `deploy_app.sh`.
2. Rendez-le exécutable avec la commande `chmod +x deploy_app.sh`.
3. Exécutez le script avec `./deploy_app.sh`.

N'oubliez pas de remplacer `votre_nom_utilisateur_docker_hub` par votre nom d'utilisateur Docker Hub et de spécifier le namespace correct lors du nettoyage.

🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
# Exemple 2
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

- Autre exemple de script shell, cette fois en utilisant Apache2 :

```bash
#!/bin/bash

# Étape 1 : Préparation de l'Environnement

# Installer Minikube
# Téléchargez et installez Minikube
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && sudo install minikube-linux-amd64 /usr/local/bin/minikube

# Démarrer Minikube
minikube start

# Étape 2 : Création et Poussée de l'Image Docker

# Créer un fichier Dockerfile pour Apache2
echo "FROM ubuntu:20.04

# Installer Apache2
RUN apt-get update && \
    apt-get install -y apache2 && \
    apt-get clean

# Copier le fichier HTML dans le répertoire approprié
COPY ./index.html /var/www/html/

# Exposer le port 80
EXPOSE 80

# Démarrer Apache2 en avant-plan
CMD [\"/usr/sbin/apache2ctl\", \"-D\", \"FOREGROUND\"]" > Dockerfile

# Créer un fichier index.html
echo "<!DOCTYPE html>
<html lang=\"en\">
<head>
    <meta charset=\"UTF-8\">
    <meta name=\"viewport\" content=\"width=device-width, initial-scale=1.0\">
    <title>Mon Application Apache2</title>
</head>
<body>
    <h1>Bienvenue sur mon site web Apache2 déployé avec Docker et Kubernetes</h1>
</body>
</html>" > index.html

# Construire l'image Docker
# Remplacez 'votre_nom_utilisateur_docker_hub' par votre nom d'utilisateur Docker Hub
docker build -t votre_nom_utilisateur_docker_hub/mon-apache2-application:v1 .

# Connexion à Docker Hub
docker login

# Pousser l'image sur Docker Hub
docker push votre_nom_utilisateur_docker_hub/mon-apache2-application:v1

# Étape 3 : Déploiement sur Kubernetes avec Minikube

# Créer le fichier deployment.yaml
echo "apiVersion: apps/v1
kind: Deployment
metadata:
  name: mon-apache2-application
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mon-apache2-application
  template:
    metadata:
      labels:
        app: mon-apache2-application
    spec:
      containers:
      - name: mon-apache2-application
        image: votre_nom_utilisateur_docker_hub/mon-apache2-application:v1
        ports:
        - containerPort: 80" > deployment.yaml

# Créer le fichier service.yaml
echo "apiVersion: v1
kind: Service
metadata:
  name: mon-apache2-application-service
spec:
  type: NodePort
  selector:
    app: mon-apache2-application
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30007" > service.yaml

# Appliquer les fichiers YAML
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml

# Vérifier le déploiement
kubectl get pods

# Obtenir l'URL de l'application
minikube service mon-apache2-application-service --url

# Nettoyage

# Supprimer le namespace (remplacez <votre_namespace> par votre namespace)
# kubectl delete namespace <votre_namespace>

# Arrêter Minikube
minikube stop

# Supprimer Minikube
minikube delete
```

Pour utiliser ce script :
1. Sauvegardez-le dans un fichier, par exemple `deploy_apache2.sh`.
2. Rendez-le exécutable avec la commande `chmod +x deploy_apache2.sh`.
3. Exécutez le script avec `./deploy_apache2.sh`.

N'oubliez pas de remplacer `votre_nom_utilisateur_docker_hub` par votre nom d'utilisateur Docker Hub et de spécifier le namespace correct lors du nettoyage.

🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
# Exemple 3
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

- Autre exemple de script pour déployer une application Flask en utilisant Docker et Kubernetes avec Minikube, en utilisant l'image de base Alpine Linux.

### Script pour déployer une application Flask

```bash
#!/bin/bash

# Étape 1 : Préparation de l'Environnement

# Installer Minikube
# Téléchargez et installez Minikube
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && sudo install minikube-linux-amd64 /usr/local/bin/minikube

# Démarrer Minikube
minikube start

# Étape 2 : Création et Poussée de l'Image Docker

# Créer un fichier Dockerfile pour Flask sur Alpine
echo "FROM python:3.9-alpine

# Installer Flask
RUN pip install flask

# Copier l'application Flask
COPY ./app /app

# Définir le répertoire de travail
WORKDIR /app

# Exposer le port 5000
EXPOSE 5000

# Démarrer l'application Flask
CMD [\"python\", \"app.py\"]" > Dockerfile

# Créer un répertoire pour l'application Flask
mkdir app

# Créer un fichier app.py pour l'application Flask
echo "from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, World! This is a Flask app running on Alpine Linux.'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)" > app/app.py

# Construire l'image Docker
# Remplacez 'votre_nom_utilisateur_docker_hub' par votre nom d'utilisateur Docker Hub
docker build -t votre_nom_utilisateur_docker_hub/flask-app:v1 .

# Connexion à Docker Hub
docker login

# Pousser l'image sur Docker Hub
docker push votre_nom_utilisateur_docker_hub/flask-app:v1

# Étape 3 : Déploiement sur Kubernetes avec Minikube

# Créer le fichier deployment.yaml
echo "apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask-app
spec:
  replicas: 5
  selector:
    matchLabels:
      app: flask-app
  template:
    metadata:
      labels:
        app: flask-app
    spec:
      containers:
      - name: flask-app
        image: votre_nom_utilisateur_docker_hub/flask-app:v1
        ports:
        - containerPort: 5000" > deployment.yaml

# Créer le fichier service.yaml
echo "apiVersion: v1
kind: Service
metadata:
  name: flask-app-service
spec:
  type: NodePort
  selector:
    app: flask-app
  ports:
    - port: 5000
      targetPort: 5000
      nodePort: 30008" > service.yaml

# Appliquer les fichiers YAML
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml

# Vérifier le déploiement
kubectl get pods

# Obtenir l'URL de l'application
minikube service flask-app-service --url

# Nettoyage

# Supprimer le namespace (remplacez <votre_namespace> par votre namespace)
# kubectl delete namespace <votre_namespace>

# Arrêter Minikube
minikube stop

# Supprimer Minikube
minikube delete
```

Pour utiliser ce script :
1. Sauvegardez-le dans un fichier, par exemple `deploy_flask.sh`.
2. Rendez-le exécutable avec la commande `chmod +x deploy_flask.sh`.
3. Exécutez le script avec `./deploy_flask.sh`.

N'oubliez pas de remplacer `votre_nom_utilisateur_docker_hub` par votre nom d'utilisateur Docker Hub et de spécifier le namespace correct lors du nettoyage.
