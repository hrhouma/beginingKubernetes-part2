# Références : 
- https://schoolofdevops.github.io/kubernetes-the-hard-way/02-client-tools/
- https://medium.com/@areesmoon/installing-minikube-on-ubuntu-20-04-lts-focal-fossa-b10fad9d0511

**Vue d'ensemble**
Avant de parler de Minikube, il est important de comprendre ce qu'est Kubernetes, car Minikube est une forme de Kubernetes souvent utilisée.

Kubernetes est une plateforme open source portable et extensible pour la gestion de charges de travail et de services conteneurisés, qui facilite la configuration déclarative et l'automatisation. En d'autres termes, Kubernetes est un gestionnaire de conteneurs qui gère plusieurs conteneurs pour fournir un service cohérent et évite que le service ne tombe en panne ou soit surchargé en répartissant les charges de manière équilibrée sur tous les conteneurs. Simplement, Kubernetes est un gestionnaire multi-service. Pour plus d'informations sur Kubernetes, veuillez consulter le concept sur son site officiel : [https://kubernetes.io/docs/concepts/overview/](https://kubernetes.io/docs/concepts/overview/).

Minikube est une implémentation légère de Kubernetes qui crée une VM sur votre machine locale et déploie un cluster simple contenant un seul nœud (nœud = machine/serveur). Minikube est disponible pour les systèmes Linux, macOS et Windows. Minikube est le type de Kubernetes le plus simple et le plus facile à utiliser si vous n'avez qu'un seul serveur à faire fonctionner.

**Prérequis**
Un serveur fonctionnant sous l'un des systèmes d'exploitation suivants : Ubuntu 22.04, 20.04, 18.04, 16.04 ou toute autre distribution basée sur Debian comme Linux Mint
Il est recommandé d'utiliser un système d'exploitation fraîchement installé pour éviter tout problème inattendu
Accès à l'utilisateur root

**Étapes d'installation**
**Étape 1. Installation de Docker**
Dans cet article, nous utiliserons Docker comme base pour Minikube. Si Docker n'est pas encore installé sur votre système Ubuntu, utilisez le lien suivant pour l'installer : Installer Docker sur Ubuntu 20.04 LTS (Focal Fossa)

**Étape 2. Mise à jour des paquets système et installation des dépendances de Minikube**
```bash
sudo apt update
sudo apt install -y curl wget apt-transport-https
```
**Étape 3. Installation de Minikube**
Utilisez la commande curl suivante pour télécharger le binaire Minikube le plus récent
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
```
Une fois le binaire téléchargé, utilisez la commande suivante pour installer Minikube
```bash
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```
**Vérification de l'installation de Minikube**
```bash
minikube version
```
minikube version: v1.32.0
commit: 8220a6eb95f0a4d75f7f2d7b14cef975f050512d

**Étape 4. Installation de l'utilitaire kubectl**
kubectl est une ligne de commande utilisée pour interagir avec le cluster Kubernetes. Elle est utilisée pour gérer les déploiements, les ensembles de réplicas, les services, etc. Utilisez la commande suivante pour télécharger la dernière version de kubectl.
### Correction : ici, j'utilise la commande de tuto : https://schoolofdevops.github.io/kubernetes-the-hard-way/02-client-tools/
```bash
wget https://storage.googleapis.com/kubernetes-release/release/v1.15.3/bin/linux/amd64/kubectl
```
Une fois kubectl téléchargé, définissez les permissions exécutables sur le binaire kubectl et déplacez-le dans le chemin /usr/local/bin.
```bash
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```
Maintenant, vérifiez la version de kubectl
```bash
kubectl version -o yaml
```
**Étape 5. Démarrage de Minikube**
Comme nous l'avons déjà mentionné au début, nous utiliserons Docker comme base pour Minikube. Pour démarrer Minikube avec le pilote Docker, exécutez la commande suivante :
```bash
minikube start — driver=docker
```
En cas d'erreur concernant le pilote Docker, utilisez la commande suivante pour forcer le démarrage :
```bash
minikube start --driver=docker --force
```

**Étape 6. Vérification de l'installation**
Utilisez la commande suivante pour vérifier Minikube :
```bash
minikube status
```
Utilisez la commande suivante pour vérifier Kubernetes :
```bash
kubectl cluster-info
```
Utilisez la commande suivante pour tester Kubernetes :
```bash
kubectl get nodes
```
Dans le prochain article, nous apprendrons à configurer Minikube et à accéder au tableau de bord Minikube à distance.
