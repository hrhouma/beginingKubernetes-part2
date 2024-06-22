#  Introduction aux Pods
- Pour la théorie, commencez par l'annexe 1 ci-bas.
- Pour la pratique , restez ici Nous allons commencer à créer notre premier Pod. Habituellement, on ne crée pas directement les Pods dans Kubernetes, mais pour cette leçon, nous allons le faire. Je vais également vous présenter plusieurs commandes de kubectl, que nous appellerons "kubectl" ou "kub control".

# 3.1. Création d'un Fichier YAML

1. **Créer le Fichier**:
   - Nom du fichier: `mypod.yaml`.

2. **Définir la Version et le Type d'Objet**:
   ```yaml
   apiVersion: v1
   kind: Pod
   ```

3. **Ajouter les Métadonnées**:
   ```yaml
   metadata:
     name: nginx-pod
   ```

4. **Spécifier les Containers**:
   ```yaml
   spec:
     containers:
     - name: nginx
       image: nginx:stable-alpine
       ports:
       - containerPort: 80
   ```

# 3.2. Déployer le Pod

Pour ajouter notre Pod au cluster, nous utilisons la commande suivante :
```sh
kubectl apply -f mypod.yaml
```
Cela crée le Pod défini dans le fichier YAML.

# 3.4. Vérification du Pod

Pour vérifier que le Pod a été créé, utilisez :
```sh
kubectl get pods
```
Cela listera tous les Pods dans le cluster. Vous pouvez aussi obtenir plus de détails avec :
```sh
kubectl get pods -o wide
```

# 3.5. Obtenir des Informations Détaillées

Pour obtenir des informations détaillées sur un Pod spécifique :
```sh
kubectl describe pod nginx-pod
```
Cette commande fournit des informations détaillées sur le Pod, y compris les événements associés à sa création et son état actuel.

# 3.6. Suppression du Pod

Pour supprimer le Pod que nous avons créé :
```sh
kubectl delete pod nginx-pod
```
Ensuite, vérifiez que le Pod a été supprimé :
```sh
kubectl get pods
```

## Conclusion

Voilà pour cette introduction sur les Pods. Il y a encore beaucoup à dire sur les Pods, y compris d'autres façons de les créer, que nous verrons dans un chapitre dédié. Pour l'instant, nous allons continuer à introduire d'autres types d'objets Kubernetes.

# Annexe 1 - Introduction théorique aux Pods

# Introduction

Nous supposons que l'application a déjà été développée et intégrée en images Docker, disponibles dans un dépôt Docker comme Docker Hub, pour que Kubernetes puisse les télécharger. Nous supposons également que le cluster Kubernetes a déjà été mis en place et fonctionne, que ce soit une configuration à un seul nœud ou à plusieurs nœuds. Tous les services doivent être en état de fonctionnement.

# Contexte des Pods

Avec Kubernetes, notre objectif final est de déployer notre application sous forme de conteneurs sur un ensemble de machines configurées en tant que nœuds de travail dans un cluster. Cependant, Kubernetes ne déploie pas directement les conteneurs sur les nœuds de travail. Les conteneurs sont encapsulés dans un objet Kubernetes appelé Pod. Un Pod est une instance unique d'une application. C'est l'objet le plus petit que vous pouvez créer dans Kubernetes.

# Création et Mise à l'Échelle des Pods

Voyons le cas le plus simple, où vous avez un cluster Kubernetes à un seul nœud avec une seule instance de votre application fonctionnant dans un conteneur Docker encapsulé dans un Pod. Si le nombre d'utilisateurs accédant à votre application augmente et que vous devez mettre à l'échelle votre application, vous devez ajouter des instances supplémentaires de votre application web pour partager la charge. Où créer ces nouvelles instances ? On ne crée pas une nouvelle instance de conteneur dans le même Pod, mais on crée un nouveau Pod avec une nouvelle instance de la même application.

# Gestion de la Capacité

Si la base d'utilisateurs augmente encore et que votre nœud actuel n'a pas suffisamment de capacité, vous pouvez toujours déployer des Pods supplémentaires sur un nouveau nœud dans le cluster, augmentant ainsi la capacité physique du cluster. Les Pods ont généralement une relation de 1 à 1 avec les conteneurs exécutant votre application. Pour mettre à l'échelle, vous créez de nouveaux Pods, et pour réduire, vous supprimez des Pods existants. Vous n'ajoutez pas de conteneurs supplémentaires à un Pod existant pour mettre à l'échelle votre application.

# Conteneurs Multiples dans un Pod

Bien que les Pods aient généralement une relation de 1 à 1 avec les conteneurs, il est possible d'avoir plusieurs conteneurs dans un seul Pod. Cela arrive souvent lorsque vous avez un conteneur auxiliaire qui effectue des tâches de support pour votre application web, comme traiter des données utilisateur ou des fichiers téléchargés. Ces conteneurs auxiliaires peuvent vivre aux côtés de votre conteneur d'application dans le même Pod, partageant le même espace réseau et de stockage, et communiquant directement entre eux.

# Exemple de Docker sans Kubernetes

Supposons que nous développions un processus ou un script pour déployer notre application sur un hôte Docker. Nous déploierions simplement notre application avec une commande `docker run` et notre application fonctionnerait bien. Lorsque la charge augmente, nous déployons plus d'instances de notre application en exécutant plusieurs fois la commande `docker run`. Cette approche fonctionne jusqu'à ce que notre application devienne plus complexe, nécessitant des conteneurs auxiliaires qui doivent communiquer directement avec les conteneurs d'application.

# Avantages des Pods

Avec Kubernetes, tout cela est automatisé. Nous définissons simplement les conteneurs qu'un Pod doit contenir. Les conteneurs d'un Pod auront par défaut accès au même stockage, au même espace réseau, et partageront le même sort : ils seront créés et détruits ensemble. Même si notre application n'est pas complexe et n'a qu'un seul conteneur, Kubernetes nous oblige à créer des Pods, ce qui est bénéfique à long terme car cela permet de gérer les changements architecturaux et la mise à l'échelle future.

# Déploiement des Pods

Nous utilisons la commande `kubectl run` pour déployer un conteneur Docker en créant un Pod. Cette commande crée d'abord un Pod et déploie une instance de l'image Docker nginx. Pour spécifier l'image de l'application, nous utilisons le paramètre `--image`. Par exemple :
```sh
kubectl run nginx --image=nginx
```
Cette commande télécharge l'image nginx depuis le dépôt Docker Hub. Une fois le Pod créé, nous pouvons voir la liste des Pods disponibles avec :
```sh
kubectl get pods
```
Cela montre les Pods dans l'état de création de conteneur, qui passe bientôt à l'état de fonctionnement.

## Conclusion

Voilà pour cette leçon. Nous n'avons pas encore abordé comment les utilisateurs peuvent accéder au serveur web nginx, mais nous verrons cela plus tard dans une leçon sur le réseautage et les services. Pour l'instant, nous avons appris à déployer un Pod.
