### Commandes pour le TP avec Minikube

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
