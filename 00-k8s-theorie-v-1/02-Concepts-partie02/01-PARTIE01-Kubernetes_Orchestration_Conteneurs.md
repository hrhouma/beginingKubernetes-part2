# Cours : Kubernetes et Orchestration de Conteneurs
- https://github.com/hrhouma/beginingKubernetes-part123/
- https://github.com/hrhouma/kubernetes2/
- https://github.com/hrhouma/kubernetes1/
## Introduction

Ce cours est conçu pour offrir une compréhension complète de l'orchestration de conteneurs avec Docker et Kubernetes. Il couvre à la fois les concepts théoriques et les exercices pratiques pour assurer une compréhension approfondie et pratique de la gestion des applications conteneurisées.

## Table des Matières

1. [Lancer une VM Ubuntu](#1-lancer-une-vm-ubuntu)
2. [Aperçu de Docker](#2-apercu-de-docker)
3. [Orchestration avec Docker Swarm](#3-orchestration-avec-docker-swarm)
4. [Théorie de Kubernetes](#4-theorie-de-kubernetes)
5. [Orchestration avec Kubernetes](#5-orchestration-avec-kubernetes)
6. [Créer un Cluster avec KIND](#6-creer-un-cluster-avec-kind)
7. [Comprendre les Pods](#7-comprendre-les-pods)

---

## 1. Lancer une VM Ubuntu

### Objectifs :
- Configurer une VM Ubuntu 22.04
- Installer des outils essentiels

### Étapes :
1. Lancer une instance sur AWS EC2 avec Ubuntu 22.04.
2. Se connecter à votre instance via SSH.
3. Mettre à jour et upgrader votre système :
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```
4. Installer Git et d'autres outils essentiels :
   ```bash
   sudo apt install git curl wget -y
   ```

### Exercice Pratique :
1. Connectez-vous à votre instance et vérifiez l'installation de Git :
   ```bash
   git --version
   ```

---

## 2. Aperçu de Docker

### Objectifs :
- Comprendre les bases de Docker
- Installer Docker sur Ubuntu

### Bases de Docker :
Docker est une plateforme permettant de développer, expédier et exécuter des applications dans des conteneurs. Les conteneurs sont légers et contiennent tous les composants nécessaires pour exécuter une application.

### Installer Docker :
1. Cloner le dépôt du script d'installation de Docker :
   ```bash
   git clone https://github.com/hrhouma/install-docker.git
   cd install-docker/
   ```
2. Rendre le script exécutable et l'exécuter :
   ```bash
   chmod +x install-docker.sh
   ./install-docker.sh
   ```
3. Vérifier l'installation de Docker :
   ```bash
   docker --version
   docker compose version
   ```

### Exercice Pratique :
1. Exécuter un conteneur de test Docker :
   ```bash
   docker run hello-world
   ```

---

## 3. Orchestration avec Docker Swarm

### Objectifs :
- Configurer un cluster Docker Swarm
- Déployer des services avec Docker Swarm

### Introduction à Docker Swarm :
Docker Swarm est l'outil de clustering et d'orchestration natif de Docker. Il permet de gérer un cluster de moteurs Docker comme un système virtuel unique.

### Configuration de Docker Swarm :
1. Initialiser Docker Swarm sur le nœud manager :
   ```bash
   docker swarm init --advertise-addr <IP_DU_MANAGER>
   ```
2. Ajouter des nœuds workers au Swarm :
   ```bash
   docker swarm join --token <TOKEN> <IP_DU_MANAGER>:2377
   ```

### Exercice Pratique :
1. Déployer un service dans Docker Swarm :
   ```bash
   docker service create --name nginx --replicas 4 --publish 80:80 nginx
   ```

---

## 4. Théorie de Kubernetes

### Objectifs :
- Comprendre les concepts de base de Kubernetes
- Appréhender l'architecture de Kubernetes

### Introduction à Kubernetes :
Kubernetes (K8s) est un système d'orchestration de conteneurs open-source qui automatise le déploiement, la scalabilité et la gestion des applications conteneurisées. Il supporte une vaste gamme de conteneurs et d'environnements d'exécution.

### Concepts Clés :
- **Nœud Maître (Master Node)** : Coordonne le cluster et prend des décisions globales.
- **Nœuds de Travail (Worker Nodes)** : Exécutent les applications conteneurisées.
- **Pods** : Unité de base de déploiement dans Kubernetes, regroupant un ou plusieurs conteneurs.
- **Services** : Abstractions qui définissent un ensemble logique de Pods et une politique d'accès.
- **Ingress** : Gère l'accès externe aux services dans un cluster.

### Architecture de Kubernetes :
- **API Server** : Point central de communication du cluster, expose l'API de Kubernetes.
- **Scheduler** : Sélectionne le nœud le plus approprié pour exécuter un nouveau pod.
- **Controller Manager** : Exécute les processus de contrôle en arrière-plan.
- **Etcd** : Base de données clé-valeur distribuée stockant toutes les informations de configuration et l'état du cluster.
- **Kubelet** : Agent sur chaque nœud de travail, s'assure que les conteneurs sont en cours d'exécution.
- **Kube-proxy** : Gère le réseau sur les nœuds de travail, permet la communication entre les Pods.

### Meilleures Pratiques :
1. **Choix de la Technologie** : Sélectionner la technologie d'orchestration adaptée à vos besoins spécifiques.
2. **Automatisation** : Automatiser autant que possible pour réduire les erreurs humaines.
3. **Monitoring et Logging** : Mettre en place des systèmes robustes pour détecter et résoudre rapidement les problèmes.
4. **Sécurité** : Appliquer les meilleures pratiques de sécurité.

---

## 5. Orchestration avec Kubernetes

### Objectifs :
- Déployer et gérer des applications avec Kubernetes
- Utiliser les fonctionnalités avancées de Kubernetes

### Déploiement de Services :
1. Créer un Pod simple :
   ```bash
   kubectl run nginx --image=nginx --restart=Never
   ```
2. Exposer le Pod en tant que Service :
   ```bash
   kubectl expose pod nginx --port=80 --target-port=80 --name=nginx-service
   ```

### Scaling :
1. Mettre à l'échelle un déploiement :
   ```bash
   kubectl scale deployment nginx --replicas=4
   ```

### Mise à jour continue :
1. Mettre à jour l'image d'un déploiement :
   ```bash
   kubectl set image deployment/nginx nginx=nginx:1.19.1
   ```

### Exercice Pratique :
1. Créer un déploiement pour une application web :
   ```bash
   kubectl create deployment webapp --image=nginx
   ```
2. Exposer l'application en tant que service :
   ```bash
   kubectl expose deployment webapp --type=LoadBalancer --port=80
   ```

---

## 6. Créer un Cluster avec KIND

### Objectifs :
- Installer KIND (Kubernetes IN Docker)
- Créer un cluster Kubernetes local

### Installation de KIND :
1. Télécharger la version appropriée de KIND selon l'architecture de votre machine :
   - Pour AMD64 / x86_64 :
     ```bash
     [ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.22.0/kind-linux-amd64
     ```
   - Pour ARM64 :
     ```bash
     [ $(uname -m) = aarch64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.22.0/kind-linux-arm64
     ```
2. Rendre le fichier `kind` exécutable et le déplacer dans un répertoire accessible globalement :
   ```bash
   chmod +x ./kind
   sudo mv ./kind /usr/local/bin/kind
   ```
3. Créer un cluster Kubernetes avec KIND :
   ```bash
   kind create cluster
   ```

### Installation de kubectl :
1. Installer `kubectl` pour interagir avec votre cluster Kubernetes :
   ```bash
   sudo apt-get install ca-certificates
   curl -LO -k "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
   chmod +x kubectl
   sudo mv kubectl /usr/local/bin/
   ```

### Exercice Pratique :
1. Vérifier la configuration de `kubectl` :
   ```bash
   kubectl config get-contexts
   ```
2. Créer un cluster avec une configuration spécifique :
   ```bash
   cat <<EOF > kind-config.yaml
   kind: Cluster
   apiVersion: kind.x-k8s.io/v1alpha4
   nodes:
   - role: control-plane
   - role: worker
   - role: worker
   EOF
   kind create cluster --config kind-config.yaml
   ```

---

## 7. Comprendre les Pods

### Objectifs :
- Appréhender le concept de Pod dans Kubernetes
- Déployer et gérer des Pods

### Qu'est-ce qu'un Pod ?
Un Pod est l'unité de base de déploiement dans Kubernetes. Il représente un ou plusieurs conteneurs qui doivent être gérés ensemble sur le

 même nœud.

### Déploiement de Pods :
1. Créer un Pod simple :
   ```bash
   cat <<EOF > nginx-pod.yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: nginx
   spec:
     containers:
     - name: nginx
       image: nginx
   EOF
   kubectl apply -f nginx-pod.yaml
   ```

### Exercice Pratique :
1. Créer un Pod MySQL avec une contrainte de déploiement :
   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: db
   spec:
     affinity:
       nodeAffinity:
         requiredDuringSchedulingIgnoredDuringExecution:
           nodeSelectorTerms:
           - matchExpressions:
             - key: disktype
               operator: In
               values:
               - ssd
     containers:
     - name: mysql
       image: mysql:5.7
       env:
       - name: MYSQL_ROOT_PASSWORD
         value: mysqlpwd
       volumeMounts:
       - name: data
         mountPath: /var/lib/mysql
     volumes:
     - name: data
       emptyDir: {}
   ```
2. Appliquer la configuration :
   ```bash
   kubectl apply -f mysql-pod.yaml
   ```
3. Ajouter un label sur un nœud :
   ```bash
   kubectl label nodes <NODE_NAME> disktype=ssd
   ```

---

Avec ces sections, vous avez un aperçu complet et structuré de l'orchestration de conteneurs avec Docker et Kubernetes, jusqu'à la partie A7. Ce cours combine la théorie et la pratique pour une meilleure compréhension et application des concepts.
