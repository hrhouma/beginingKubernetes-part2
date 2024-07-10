# Partie1 : Démo du scaling avec Docker-swarm
## Objectif : Comprendre l'orchestration d'une manière visuelle
- https://github.com/hrhouma/docker-swarm-demo

# Partie2 : Démo du scaling avec un déploiement depuis la ligne de commande sans passer par un fichier yaml
## Objectif : 
- créer un déploiement avec plusieurs réplicas directement depuis la ligne de commande sans passer par un fichier YAML :

### Étape 1: Démarrer Minikube
Assurez-vous que Minikube est démarré :
```bash
minikube start
```
Exécutez sur une autre ligne de commande
```bash
minikube dashboard
```

### Étape 2: Créer un Namespace
Créez un namespace pour isoler vos ressources :
```bash
kubectl create namespace demo-namespace
```

### Étape 3: Créer un Déploiement
Utilisez `kubectl create deployment` pour lancer un déploiement avec plusieurs réplicas dans le namespace spécifié :
```bash
kubectl create deployment nginx-deployment --image=nginx --replicas=3 -n demo-namespace
```

Si `--replicas` n'est pas disponible directement dans `kubectl create deployment`, vous devrez d'abord créer le déploiement puis ajuster le nombre de réplicas avec `kubectl scale` :
```bash
kubectl create deployment nginx-deployment --image=nginx -n demo-namespace
kubectl scale deployment/nginx-deployment --replicas=3 -n demo-namespace
```
- Obserrvez le dashboard
  
### Étape 4: Vérifier les Pods
Pour voir les pods dans le namespace spécifique :
```bash
kubectl get pods -n demo-namespace
```

### Étape 5: Simuler la panne d'un Pod
Choisissez un pod et supprimez-le pour simuler une panne :
```bash
kubectl delete pod <nom-du-pod> -n demo-namespace
```
- Obserrvez le dashboard
  
### Étape 6: Observer la Recréation du Pod
Kubernetes va automatiquement recréer le pod pour maintenir le nombre de réplicas désirés :
```bash
kubectl get pods -n demo-namespace
```

### Étape 7: Nettoyer
Pour nettoyer vos ressources :
```bash
kubectl delete deployment nginx-deployment -n demo-namespace
kubectl delete namespace demo-namespace
```
Arrêtez Minikube si nécessaire :
```bash
minikube stop
```

# Correction:
- cette commande ne fonctionne plus dans les nouvelles version :

```bash
kubectl run nginx-pod --image=nginx --replicas=3 --port=80 -n demo-namespace
```
- Dans les versions récentes de Kubernetes, la commande `kubectl run` a été simplifiée pour se concentrer principalement sur le lancement rapide de pods simples, sans support pour certaines options comme `--replicas`. 
- Pour créer un déploiement avec plusieurs réplicas, vous devrez utiliser `kubectl create deployment` ou `kubectl apply` avec un fichier YAML.

Cette méthode utilise `kubectl create deployment` et `kubectl scale` pour gérer les déploiements avec des réplicas, conformément aux versions récentes de Kubernetes où `kubectl run` est plus limité en fonctionnalité.


# Partie3 : Démo du scaling avec un déploiement depuis la ligne de commande avec un fichier yaml



### Étape 1: Créer un Namespace
Avant de créer des ressources, créez un namespace spécifique où toutes les opérations suivantes seront effectuées:
```bash
kubectl create namespace demo-namespace
```

### Étape 2: Créer un fichier de déploiement YAML
Créez un fichier appelé `deployment.yaml` avec le contenu suivant pour définir un déploiement. Ce fichier spécifie le déploiement d'un serveur Nginx:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: demo-namespace
spec:
  replicas: 3
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
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```
Notez que `namespace: demo-namespace` est ajouté directement dans le fichier YAML, ce qui signifie que toutes les ressources créées à partir de ce fichier appartiendront automatiquement à ce namespace.

### Étape 3: Déployer l'application
Utilisez `kubectl` pour créer le déploiement à partir de votre fichier YAML:
```bash
kubectl apply -f deployment.yaml
```
Aucun besoin d'ajouter `-n demo-namespace` car le namespace est déjà spécifié dans le fichier YAML.

### Étape 4: Vérifier le déploiement
Pour voir les pods dans le namespace spécifique:
```bash
kubectl get pods -n demo-namespace
```

### Étape 5: Simuler la panne d'un pod
Choisissez un pod et simulez une panne en le supprimant:
```bash
kubectl delete pod <nom-du-pod> -n demo-namespace
```

### Étape 6: Observer la recréation du pod
Observez comment Kubernetes recrée automatiquement le pod:
```bash
kubectl get pods -n demo-namespace
```

### Étape 7: Nettoyer
Supprimez le déploiement et, si désiré, le namespace:
```bash
kubectl delete deployment nginx-deployment -n demo-namespace
kubectl delete namespace demo-namespace
```
Minikube peut être arrêté avec:
```bash
minikube stop
```

