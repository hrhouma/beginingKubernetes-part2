# Tutoriel Kubernetes: Gestion des Pods

## Introduction

Ce tutoriel guide les étudiants à travers les étapes de base pour la création, la gestion et la suppression de Pods dans un environnement Kubernetes. En utilisant l'outil en ligne de commande `kubectl` et un fichier de configuration YAML, nous apprendrons comment déployer un serveur web Nginx simple.

## Prérequis

- Avoir installé `kubectl`, l'interface en ligne de commande pour Kubernetes.
- Avoir accès à un cluster Kubernetes, comme Minikube ou un cluster configuré via un fournisseur de cloud.

## Étape 1: Création du fichier YAML

Un Pod est l'unité la plus petite et la plus simple dans Kubernetes. Il représente un ou plusieurs conteneurs qui doivent être gérés ensemble. Commencez par créer un fichier YAML qui définit un Pod contenant un serveur web Nginx.

1. **Ouvrez votre éditeur de texte ou IDE** (ici, Visual Studio Code).
2. **Créez un fichier nommé** `mypod.yaml`.
3. **Ajoutez le contenu suivant au fichier** :

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:alpine
    ports:
    - containerPort: 80
```

Ce fichier définit un Pod nommé `nginx` utilisant l'image Docker `nginx:alpine`, qui est une version légère de Nginx.

## Étape 2: Déploiement du Pod

Avec le fichier YAML prêt, vous pouvez maintenant créer le Pod dans votre cluster Kubernetes.

- **Ouvrez un terminal**.
- **Naviguez au répertoire contenant votre fichier** `mypod.yaml`.
- **Exécutez la commande suivante** pour créer le Pod :

```bash
kubectl apply -f mypod.yaml
```

## Étape 3: Vérification du Pod

Après la création du Pod, il est important de vérifier son état et de s'assurer qu'il fonctionne correctement.

- **Pour lister tous les Pods, exécutez** :

```bash
kubectl get pods
```

- **Pour obtenir des détails plus spécifiques sur le Pod 'nginx', utilisez** :

```bash
kubectl describe pod nginx
```

Ces commandes fournissent des informations sur l'état du Pod, les conteneurs qu'il contient, et d'autres détails utiles comme l'adresse IP du Pod et les ports ouverts.

## Étape 4: Suppression du Pod

Une fois que vous avez fini avec le Pod, ou si vous voulez recommencer le processus, vous pouvez supprimer le Pod.

- **Pour supprimer le Pod 'nginx', exécutez** :

```bash
kubectl delete pod nginx
```

## Conclusion

Ce tutoriel vous a guidé à travers les étapes de base de la gestion des Pods dans Kubernetes, en utilisant `kubectl`. Vous avez appris comment définir un Pod avec un fichier YAML, comment le déployer, vérifier son état, et enfin, comment le supprimer proprement. Utilisez ces compétences pour explorer plus avant Kubernetes et gérer des applications plus complexes.

