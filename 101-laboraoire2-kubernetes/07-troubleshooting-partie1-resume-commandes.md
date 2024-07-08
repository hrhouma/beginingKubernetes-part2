### README: Guide de Déploiement et de Nettoyage Kubernetes avec Minikube

# Description du problème:
- J'avais réalisé le laboratoire sur les taintes et tolérances. J'avais tainté Minikube, mon seul nœud, et oublié d'enlever la taint.
- En réalisant le laboratoire1, rien ne fonctionnait !
- `kubectl get deployment.apps` ==> 0/3
- `kubectl get po` ==> 0/1
- `kubectl logs <nom_du_pod>` ==> rien
- `kubectl get events` ==> rien de significatif

Je vous présente une série de troubleshooting utile.

----

## Troubleshooting Partie 1

```shell
# Étape 1 : Vérifier les Logs des Pods
kubectl logs <nom_du_pod>

# Étape 2 : Vérifier l'État du Pod
kubectl describe pod <nom_du_pod>

# Étape 3 : Assurer que l'Image Docker est Valide
docker run -p 8080:80 votre_nom_utilisateur_docker_hub/mon-application:v1

# Étape 4 : Vérifier les Fichiers YAML

# Étape 5 : Reconstruire et Pousser l'Image Docker
# Construire l'image Docker
docker build -t votre_nom_utilisateur_docker_hub/mon-application:v1 .

# Connexion à Docker Hub
docker login

# Pousser l'image sur Docker Hub
docker push votre_nom_utilisateur_docker_hub/mon-application:v1

# Étape 6 : Redéployer sur Kubernetes
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml

# Étape 7 : Vérification Finale
kubectl get pods
kubectl get deployments
```

## Résultat
Le problème persiste.

----

## Troubleshooting Partie 2

```Dockerfile
# Utiliser l'image de base d'Apache
FROM httpd:2.4

# Copier le fichier HTML dans le répertoire approprié
COPY ./index.html /usr/local/apache2/htdocs/

# Exposer le port 80
EXPOSE 80
```

### Étapes à Suivre

```bash
# Mise à jour du Dockerfile
# Reconstruction de l'Image Docker
docker build -t votre_nom_utilisateur_docker_hub/mon-application:v1 .

# Pousser l'Image Docker
docker login
docker push votre_nom_utilisateur_docker_hub/mon-application:v1

# Mise à Jour et Application des Fichiers YAML
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml

# Vérification
kubectl get pods
kubectl get deployments

# Accès à l'Application via Minikube
minikube service mon-application-service --url

# Vérifications Supplémentaires
kubectl logs <nom_du_pod>
kubectl describe pod <nom_du_pod>
```

## Résultat
Le problème persiste.

----

## Troubleshooting Partie 3

```shell
# Étape 1 : Vérifier l'état des pods
kubectl get pods

# Étape 2 : Décrire le pod
kubectl describe pod <nom_du_pod>

# Étape 3 : Vérifier les événements Kubernetes
kubectl get events

# Étape 4 : Vérifier l'image Docker
docker run -p 8080:80 votre_nom_utilisateur_docker_hub/mon-application:v1
```

### Exemple de Script de Diagnostic

```bash
#!/bin/bash

# Nom du pod à diagnostiquer
POD_NAME=$(kubectl get pods -o jsonpath="{.items[0].metadata.name}")

# Vérifier l'état des pods
echo "État des pods:"
kubectl get pods

# Décrire le pod
echo "Description du pod $POD_NAME:"
kubectl describe pod $POD_NAME

# Vérifier les logs du pod
echo "Logs du pod $POD_NAME:"
kubectl logs $POD_NAME

# Vérifier les événements Kubernetes
echo "Événements Kubernetes:"
kubectl get events
```

## Résultat
Le problème persiste.

----

## Troubleshooting Partie 4

### Supprimer toutes les ressources associées à WordPress

```shell
# Lister toutes les ressources pour identifier ce qu'il faut supprimer
kubectl get all

# Supprimer le déploiement et le service WordPress
kubectl delete deployment wordpress
kubectl delete service wordpress

# Supprimer les PVC et PV associés à WordPress
kubectl delete pvc wordpress-pvc
kubectl delete pv wordpress-pv

# Supprimer toutes les ressources dans le namespace par défaut (à utiliser avec prudence)
kubectl delete all --all

# Vérifier que toutes les ressources sont supprimées
kubectl get all
```

### Commandes de nettoyage

```bash
#!/bin/bash

# Supprimer le déploiement et le service WordPress
kubectl delete deployment wordpress
kubectl delete service wordpress

# Supprimer les PVC et PV associés à WordPress
kubectl delete pvc wordpress-pvc
kubectl delete pv wordpress-pv

# Supprimer toutes les ressources dans le namespace par défaut (si nécessaire)
kubectl delete all --all

# Vérifier que toutes les ressources sont supprimées
kubectl get all
```

## Résultat
Le problème persiste.

----

## Troubleshooting Partie 5

```shell
# Vérifiez les Taints sur le Nœud
kubectl describe nodes

# Ajouter une Tolérance pour la Taint dans le fichier deployment.yaml

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
      tolerations:
      - key: "key1"
        operator: "Equal"
        value: "value1"
        effect: "NoSchedule"

# Appliquer les Modifications
kubectl apply -f deployment.yaml

# Vérifier l'État du Pod
kubectl get pods
```

### Supprimer les Taints (Optionnel)

```shell
# Identifier le nœud
kubectl get nodes

# Supprimer la taint du nœud
kubectl taint nodes <node-name> key1=value1:NoSchedule-
```

## Conclusion

Ces étapes devraient vous aider à résoudre le problème de taint et permettre à vos pods d'être planifiés correctement sur les nœuds disponibles. Si vous rencontrez toujours des problèmes, veuillez fournir les détails supplémentaires pour une analyse plus approfondie.

----

## Troubleshooting Partie 6

### Enlever la Taint de Minikube

```shell
# Identifier le nœud de Minikube
kubectl get nodes

# Vérifier les taints sur le nœud
kubectl describe node <node-name>

# Supprimer la taint
kubectl taint nodes <node-name> key1=value1:NoSchedule-

# Exemple Complet
kubectl get nodes
kubectl describe node minikube
kubectl taint nodes minikube key1=value1:NoSchedule-

# Vérification
kubectl describe node minikube

# Vérification Finale de l'État du Pod
kubectl get pods
```

# Réponse finale

```shell
kubectl taint nodes minikube key1=value1:NoSchedule-
kubectl get po 
```

## Résultat
Ça fonctionne !
