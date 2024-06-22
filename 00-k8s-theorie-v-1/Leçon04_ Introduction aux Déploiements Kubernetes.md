# Introduction aux Déploiements Kubernetes

# 1 - Introduction

Dans cette leçon, nous allons aborder l'objet Deployment de Kubernetes. Il s'agit d'une introduction pour comprendre le fonctionnement de base et l'intérêt de cet objet. Nous allons progressivement voir comment utiliser les déploiements sans entrer trop dans les détails techniques dès le début.

# 2 -  Présentation de l'Objet Deployment

Jusqu'à présent, nous avons vu comment créer un Pod directement via un fichier de configuration et utiliser kubectl pour déployer ce Pod dans notre cluster. Cependant, ce Pod est isolé et n'a pas de mécanisme de surveillance particulier. Cela signifie qu'une fois créé, il fonctionne, mais sans contrôle continu ou gestion automatique en cas de défaillance.

Pour résoudre ce problème, nous utilisons des objets comme les Deployments qui permettent de gérer les Pods plus finement en fonction de certaines règles. Les Deployments sont particulièrement utiles pour les Pods stateless (sans état), comme les serveurs web Nginx.

# 3 - Création et Gestion des Déployments

Pour créer un Deployment, nous définissons notre objet Deployment dans un fichier de configuration YAML. Voici un exemple simple :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
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
        image: nginx:stable-alpine
        ports:
        - containerPort: 80
```

# 4 - Explication du Fichier YAML

1. **apiVersion**: Indique la version de l'API utilisée.
2. **kind**: Spécifie le type d'objet, ici `Deployment`.
3. **metadata**: Contient des informations supplémentaires, comme le nom du déploiement.
4. **spec**: Spécifie les détails du déploiement, y compris le nombre de réplicas, le sélecteur pour identifier les Pods, et le modèle (template) du Pod.
   - **replicas**: Nombre de réplicas du Pod.
   - **selector**: Critères pour sélectionner les Pods.
   - **template**: Modèle de Pod, contenant les métadonnées et les spécifications du conteneur.

# 5 - Déploiement et Surveillance des Pods

Une fois le Deployment créé, Kubernetes crée un ReplicaSet pour gérer les réplicas des Pods. Le contrôleur de déploiement surveille constamment les Pods et s'assure qu'ils correspondent aux spécifications définies. Si un Pod tombe, le contrôleur recrée automatiquement un nouveau Pod pour maintenir le nombre de réplicas désiré.

# 6 - Commandes Kubectl

Pour appliquer notre fichier de configuration et créer le Deployment :
```sh
kubectl apply -f mydeployment.yaml
```

Pour vérifier les Deployments :
```sh
kubectl get deployments
```

Pour obtenir des détails sur un Deployment spécifique :
```sh
kubectl describe deployment nginx-deployment
```

# 7 - Utilisation du Tableau de Bord

Vous pouvez également utiliser le tableau de bord Kubernetes pour visualiser les Deployments, les Pods, et les ReplicaSets. Le tableau de bord fournit une interface utilisateur conviviale pour surveiller et gérer les objets dans votre cluster.

# 8 - Suppression d'un Deployment

Pour supprimer un Deployment et tous les Pods associés :
```sh
kubectl delete deployment nginx-deployment
```
Cela supprime également tous les ReplicaSets et Pods créés par ce Deployment.

#### Conclusion

Les Deployments sont essentiels pour gérer les Pods dans Kubernetes, offrant des fonctionnalités de surveillance et de mise à l'échelle automatiques. Dans la leçon suivante, nous aborderons les Services, qui permettent de rendre les applications accessibles aux utilisateurs.
