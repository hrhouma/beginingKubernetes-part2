## Cours : Kubernetes et Orchestration de Conteneurs

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
8. [Services Kubernetes](#8-services-kubernetes)
9. [Surveillance avec Metrics Server](#9-surveillance-avec-metrics-server)
10. [Travaux Pratiques et Exercices](#10-travaux-pratiques-et-exercices)

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


Un Pod est l'unité de base de déploiement dans Kubernetes. Il représente un ou plusieurs conteneurs qui doivent être gérés ensemble sur le même nœud.

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

## 8. Services Kubernetes

### Introduction aux Services Kubernetes

Imaginez que Kubernetes est comme une ville pour vos applications, et cette ville est construite avec de nombreux petits conteneurs (vos applications ou parties de vos applications). Chacun de ces conteneurs vit dans une maison (pod), et ces maisons sont situées dans différents quartiers (nœuds). Maintenant, si vous voulez que quelqu'un (utilisateur ou autre application) visite l'un de ces conteneurs, il aurait besoin de savoir où il se trouve exactement. C'est là que les services Kubernetes entrent en jeu. Un service Kubernetes agit comme un point de rencontre fixe ou une adresse postale pour accéder à ces conteneurs, peu importe où ils se déplacent dans la ville (cluster).

### Pourquoi avons-nous besoin de services dans Kubernetes ?

- **Dynamisme** : Les pods (maisons) dans Kubernetes sont très dynamiques, ils peuvent être déplacés, supprimés, ou dupliqués à tout moment en fonction de la charge de travail. Donc, si vous vous fiez directement à l'adresse d'un pod pour accéder à votre application, cela pourrait ne pas fonctionner la prochaine fois que vous essayez. Un service offre une adresse constante pour accéder à votre application, peu importe les mouvements des pods.
- **Équilibrage de charge** : Imaginons que vous ayez plusieurs instances de votre application pour gérer plus de trafic. Un service distribuera automatiquement les demandes entre ces instances, assurant ainsi que l'application peut gérer efficacement le trafic.

### Types de Services dans Kubernetes

Il existe principalement quatre types de services dans Kubernetes :

#### ClusterIP

- **Description** : C'est le type de service par défaut dans Kubernetes. Un service ClusterIP rend votre application accessible à l'intérieur du cluster via une adresse IP interne. Cela signifie que seuls les autres pods à l'intérieur du même cluster peuvent communiquer avec l'application exposée par ce service. Ce type de service est utile pour des cas d'utilisation où vous n'avez pas besoin d'exposer votre application à l'extérieur du cluster.
- **Utilisation** : Idéal pour les applications backend ou les bases de données qui n'ont pas besoin d'être accessibles depuis l'extérieur du cluster.

#### NodePort

- **Description** : Un service NodePort expose l'application sur un port spécifique sur tous les nœuds (serveurs) du cluster. Kubernetes alloue un port (par défaut dans la plage 30000-32767) sur chaque nœud et toute requête envoyée à ce port sur n'importe quel nœud est redirigée vers l'application. Ce type de service rend l'application accessible depuis l'extérieur du cluster en utilisant `<NodeIP>:<NodePort>`.
- **Utilisation** : Convient pour les scénarios de test ou de développement où vous souhaitez accéder facilement à votre application depuis l'extérieur du cluster mais sans les coûts ou la complexité d'un équilibreur de charge externe.

#### LoadBalancer

- **Description** : Ce type de service intègre des équilibreurs de charge externes disponibles dans les environnements de cloud computing pour exposer l'application à l'Internet. Lorsque vous créez un service LoadBalancer, il provisionne automatiquement un équilibreur de charge externe (si disponible) et lui assigne une adresse IP publique. Le trafic reçu par l'équilibreur de charge est automatiquement routé vers l'application, même à travers plusieurs nœuds du cluster.
- **Utilisation** : Idéal pour les applications de production nécessitant une haute disponibilité, un accès global et une capacité à gérer des volumes de trafic élevés. C'est la méthode privilégiée pour exposer une application à l'Internet.

#### ExternalName

- **Description** : Ce type de service est un peu différent. Il ne redirige pas le trafic vers un IP ou un port spécifique mais permet plutôt de renvoyer un alias à un nom de domaine externe. Lorsqu'un pod accède à ce service, la requête est redirigée vers l'adresse externe spécifiée. Cela est utile pour intégrer des services externes au sein de votre cluster, en permettant aux pods d'accéder à ces services externes en utilisant des noms de service internes.
- **Utilisation** : Très utile pour accéder à des services externes au cluster, comme des bases de données hébergées dans le cloud, des API externes, ou tout autre service accessible via un nom de domaine.

### Comment fonctionnent les Services ?

Imaginez que vous avez une application web qui fonctionne dans trois pods différents. Vous créez un service de type ClusterIP pour cette application. Lorsqu'une demande est faite à l'adresse IP du service, Kubernetes s'assure que la demande est dirigée vers l'un des trois pods de manière équilibrée. Si un pod tombe en panne et est remplacé, le service continue de fonctionner sans interruption car il connaît les emplacements actifs de tous les pods de l'application.

### Exemples Concrets

Imaginons que Kubernetes est comme un grand centre commercial avec plein de magasins différents (vos applications). Pour que les clients (les requêtes ou les données) trouvent et accèdent aux magasins, il y a différentes façons de les guider.

1. **ClusterIP** : Imaginez ça comme une adresse secrète interne. C'est comme si chaque magasin avait une porte dérobée utilisée seulement par les employés du centre commercial. Seules les personnes déjà à l'intérieur (dans le cluster) peuvent utiliser cette adresse pour venir voir ce que vous proposez.

2. **NodePort** : C'est comme si, en plus de la porte principale, chaque magasin avait un stand dans le parking. Peu importe à quelle porte vous vous présentez, vous pouvez commander les mêmes choses. Cela signifie que votre magasin (application) peut être atteint de l'extérieur en utilisant l'adresse du centre commercial (le nœud) et un numéro de stand spécifique (le port).

3. **LoadBalancer** : Imaginez maintenant que votre magasin devienne super populaire. Le centre décide de vous donner un coup de main en plaçant quelques employés à l'entrée pour gérer la foule et diriger les clients vers vos caisses de manière efficace. C'est comme avoir votre propre équipe à l'entrée qui s'assure que tout le monde peut accéder à vos offres sans faire la queue.

4. **ExternalName** : Enfin, disons que vous voulez que vos clients puissent commander des pizzas d'un restaurant en dehors du centre commercial. Au lieu de les faire sortir et chercher par eux-mêmes, vous donnez un flyer avec le nom et l'adresse du restaurant. De cette façon, ils peuvent commander directement depuis votre magasin. C'est une manière de connecter les gens à un service qui n'est pas dans le centre (cluster) mais que vous voulez rendre accessible comme s'il l'était.

### Comment les services Kubernetes permettent-ils aux clients extérieurs de se connecter à des applications à l'intérieur du cluster ?

Concentrons-nous sur cette partie : Connecter l'extérieur à l'intérieur. Imaginons un parc d'attractions cette fois, où vos services dans Kubernetes sont les différentes attractions. Les clients à l'extérieur du parc veulent y entrer pour profiter des attractions (vos services). Voici comment cela fonctionne :

1. **NodePort** : Pensez au parc ayant plusieurs portes (les nœuds de votre cluster). Chaque porte a un employé qui tient un panneau avec le numéro de l'attraction (le port). Peu importe par quelle porte vous entrez, si vous suivez le numéro sur le panneau, vous serez guidé vers l'attraction souhaitée. Donc, un client à l'extérieur peut choisir n'importe quelle porte (nœud), utiliser le numéro spécifique (port), et accéder à l'attraction (service) à l'intérieur du parc.

2. **LoadBalancer** : Maintenant, imaginez que le parc devienne extrêmement populaire, avec des milliers de personnes essayant d'entrer en même temps. Le parc décide d'installer un grand panneau électronique à l'entrée qui dirige automatiquement les visiteurs vers la porte la moins encombrée, garantissant ainsi que tout le monde entre rapidement et en douceur. C'est comme avoir un équilibreur de charge externe; il prend les demandes d'entrée et les distribue de manière équitable à travers les portes disponibles (nœuds), s

'assurant que personne n'attend trop longtemps et que les attractions ne sont pas surchargées.

En utilisant ces deux méthodes, **NodePort** et **LoadBalancer**, Kubernetes permet aux clients à l'extérieur (sur Internet) de se connecter facilement à un service à l'intérieur du cluster. NodePort est comme choisir manuellement une porte d'entrée, tandis que LoadBalancer fait tout le travail pour vous, vous assurant la meilleure porte d'entrée en fonction de la situation actuelle.

### ClusterIP et ExternalName : pas de moyen de l'extérieur à l'intérieur ?

Effectivement, **ClusterIP** et **ExternalName** jouent un rôle différent dans la connectivité au sein de Kubernetes, et leurs interactions avec l'extérieur sont moins directes.

#### ClusterIP

- **Description** : Fournit une adresse IP interne au cluster qui n'est accessible que de l'intérieur du cluster. Pour qu'un client à l'extérieur du cluster se connecte à un service utilisant **ClusterIP**, il faudrait passer par quelque chose comme **NodePort** ou **LoadBalancer**, qui agirait comme un intermédiaire.

#### ExternalName

- **Description** : Permet à une attraction du parc (service dans le cluster) de dire "Hey, au lieu de venir ici, pourquoi ne pas visiter ce super endroit à l'extérieur ?". Il crée une sorte de raccourci ou d'alias dans le système de téléphones internes du parc qui redirige directement les appels vers un numéro externe.

### Conclusion sur les Services Kubernetes

Les services Kubernetes agissent comme un guide touristique dans la ville de Kubernetes. Ils connaissent tous les emplacements (pods) où vos applications vivent et comment les atteindre de la manière la plus efficace possible, offrant une adresse stable pour accéder à ces applications et distribuant le trafic pour gérer la charge. C'est un concept fondamental qui aide à rendre les applications Kubernetes accessibles et robustes.

---

## 9. Surveillance avec Metrics Server

### Objectifs :
- Installer et configurer Metrics Server
- Utiliser Metrics Server pour surveiller les ressources des nœuds et des pods

### Introduction à Metrics Server :
Metrics Server est un composant de Kubernetes utilisé pour collecter des métriques sur l'utilisation des ressources des nœuds et des pods. Ces métriques peuvent ensuite être utilisées pour la surveillance, l'autoscaling, et d'autres fins.

### Installation de Metrics Server :
1. Cloner le dépôt de Metrics Server :
   ```bash
   git clone https://github.com/kodekloudhub/kubernetes-metrics-server.git
   cd kubernetes-metrics-server/
   ```
2. Appliquer les fichiers YAML pour déployer Metrics Server :
   ```bash
   kubectl apply -f .
   ```

### Utilisation de Metrics Server :
1. Vérifier que Metrics Server est en cours d'exécution :
   ```bash
   kubectl get pods -n kube-system
   ```
2. Utiliser `kubectl top` pour afficher les métriques des nœuds et des pods :
   ```bash
   kubectl top nodes
   kubectl top pods --all-namespaces
   ```

### Exercice Pratique :
1. Déployer Metrics Server et vérifier son bon fonctionnement.
2. Collecter les métriques des nœuds et des pods dans un cluster Kubernetes.
3. Analyser les métriques pour identifier les ressources les plus utilisées.

---

## 10. Travaux Pratiques et Exercices

### Exercice 1 : Création et Gestion de Jobs

**Objectif :** Apprendre à créer, observer et gérer des jobs dans Kubernetes.

#### Étape 1 : Création d'un Job

Créez un job qui exécute une tâche simple (`sleep 30`) en utilisant la commande suivante :

```bash
kubectl create job myjob1 --image=busybox -- /bin/sh -c "sleep 30"
```

Cette commande crée un job nommé `myjob1` qui exécute l'image `busybox` avec la commande `sleep 30`.

#### Étape 2 : Observer le Job

Utilisez `kubectl get jobs` pour voir l'état du job, puis `kubectl describe job myjob1` pour voir les détails et l'état d'exécution du job.

#### Étape 3 : Supprimer le Job

Une fois que vous avez confirmé que le job a été exécuté avec succès (ou après un certain temps), supprimez-le avec `kubectl delete job myjob1`.

#### Bonus : Création d'un Job via un Fichier YAML

Créez un fichier YAML pour le job (`myjob2.yaml`) avec une spécification qui exécute une commande différente, par exemple, afficher le message "Hello Kubernetes". Appliquez ce fichier et observez l'exécution du job.

### Exercice 2 : Pratique de CronJobs

Dans cet exercice, vous allez créer un *Job* qui servira à faire le dump d'une base de données *mongo*. Vous créerez ensuite un *CronJob* qui créera un dump à interval régulier.

#### Étape 1 : Création d'un Pod Mongo

Créez un fichier *mongo-pod.yaml* avec la spécification suivante :

```yaml
apiVersion: v1             
kind: Pod                  
metadata:
  name: db
  labels:
    app: db
spec:
  containers:
  - name: mongo
    image: mongo:4.0
```

Lancez ce Pod avec la commande :

```bash
kubectl apply -f mongo-pod.yaml
```

#### Étape 2 : Exposition de la Base Mongo

Créez un fichier *mongo-svc.yaml* avec la spécification suivante :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: db
spec:
  selector:
    app: db
  type: ClusterIP
  ports:
  - port: 27017
```

Lancez ce Service avec la commande :

```bash
kubectl apply -f mongo-svc.yaml
```

#### Étape 3 : Ajout d'un Label sur un Node

Ajoutez le label *app=dump* sur l'un des nodes de votre cluster :

```bash
kubectl label node <NODE_NAME> app=dump
```

#### Étape 4 : Définition d'un Job pour le Dump de la Base de Données

Créez un fichier *mongo-dump-job.yaml* avec la spécification suivante :

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: dump
spec:
  template:
    spec:
      restartPolicy: Never
      nodeSelector:
        app: dump
      containers:
      - name: mongo
        image: mongo:4.0
        command:
        - /bin/bash
        - -c
        - mongodump --gzip --host db --archive=/dump/db.gz
        volumeMounts:
        - name: dump
          mountPath: /dump
      volumes:
      - name: dump
        hostPath:
          path: /dump
```

Lancez ce Job avec la commande :

```bash
kubectl apply -f mongo-dump-job.yaml
```

#### Étape 5 : Définition d'un CronJob pour les Dumps Réguliers

Créez un fichier *mongo-dump-cronjob.yaml* avec la spécification suivante :

```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: dump
spec:
  schedule: "*/1 * * * *"  # Exécute toutes les minutes
  jobTemplate:
    spec:
      template:
        spec:
          nodeSelector:
            app: dump
          containers:
          - name: mongo
            image: mongo:4.0
            command:
            - /bin/bash
            - -c
            - mongodump --gzip --host db --archive=/dump/$(date +"%Y%m%dT%H%M%S")-db.gz
            volumeMounts:
            - name: dump
              mountPath: /dump
          restartPolicy: OnFailure
          volumes:
          - name: dump
            hostPath:
              path: /dump
```

Lancez ce CronJob avec la commande :

```bash
kubectl apply -f mongo-dump-cronjob.yaml
```

#### Étape 6 : Vérification des Dumps

Lancez un Pod *test* pour vérifier les dumps créés :

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test
spec:
  nodeSelector:
    app: dump
  containers:
  - name: test
    image: alpine:3.15
    command:
    - "sleep"
    - "10000"
    volumeMounts:
    - name: dump
      mountPath: /dump
  volumes:
  - name: dump
    hostPath:
      path: /dump
```

Lancez un shell interactif dans le container du pod *test* et vérifiez les dumps :

```bash
kubectl exec -ti test -- sh
# ls /dump
```

#### Étape 7 : Nettoyage

Supprimez les Job et CronJob créés :

```bash
kubectl delete job dump
kubectl delete cronjob dump
kubectl delete pod test
kubectl delete pod db
kubectl delete service db
```

### Conclusion sur les CronJobs

Ce guide vous a fourni une compréhension détaillée des services Kubernetes et des CronJobs. Vous avez appris comment créer, observer, et gérer des jobs ainsi que des tâches planifiées, renforçant ainsi vos compétences en gestion de Kubernetes.

---

## 11. Avancé : Configurer et Gérer les Taints et Tolerations

### Objectifs :


- Appréhender l'utilisation des taints et tolerations dans Kubernetes
- Configurer des taints et des tolerations pour contrôler le placement des pods

### Introduction :
Les **taints** et **tolerations** sont des mécanismes permettant de contrôler quels pods peuvent être planifiés sur quels nœuds. Les taints permettent de marquer un nœud avec une restriction, et les tolerations permettent aux pods de lever ces restrictions et d'être planifiés sur ces nœuds.

### Configurer une Taint sur un Nœud :
1. Ajouter une taint à un nœud :
   ```bash
   kubectl taint nodes <NODE_NAME> key=value:NoSchedule
   ```
   Cette commande ajoute une taint au nœud spécifié, empêchant les pods sans la toleration correspondante d'être planifiés sur ce nœud.

### Configurer une Toleration sur un Pod :
1. Ajouter une toleration à un pod en utilisant un fichier YAML :
   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: mypod
   spec:
     containers:
     - name: nginx
       image: nginx
     tolerations:
     - key: "key"
       operator: "Equal"
       value: "value"
       effect: "NoSchedule"
   ```
2. Appliquer la configuration :
   ```bash
   kubectl apply -f mypod.yaml
   ```

### Exercice Pratique :
1. Ajouter une taint à un nœud pour restreindre les pods réguliers d'y être planifiés.
2. Créer un pod avec la toleration correspondante pour lever la restriction et permettre au pod d'être planifié sur ce nœud.
3. Observer et vérifier le placement du pod en utilisant `kubectl describe pod mypod`.

---

## 12. Avancé : Configuration de Helm et Déploiement d'Applications

### Objectifs :
- Installer et configurer Helm
- Utiliser Helm pour déployer des applications dans un cluster Kubernetes

### Introduction :
**Helm** est un gestionnaire de packages pour Kubernetes, similaire à `apt` pour Ubuntu ou `yum` pour CentOS. Il permet de définir, installer et mettre à jour des applications Kubernetes en utilisant des chartes Helm.

### Installation de Helm :
1. Télécharger et installer Helm :
   ```bash
   curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
   ```
2. Vérifier l'installation de Helm :
   ```bash
   helm version
   ```

### Utilisation de Helm :
1. Ajouter un dépôt Helm :
   ```bash
   helm repo add stable https://charts.helm.sh/stable
   helm repo update
   ```
2. Installer une application à partir d'un chart Helm :
   ```bash
   helm install mynginx stable/nginx-ingress
   ```
3. Lister les déploiements Helm :
   ```bash
   helm list
   ```

### Exercice Pratique :
1. Ajouter le dépôt Bitnami et installer WordPress :
   ```bash
   helm repo add bitnami https://charts.bitnami.com/bitnami
   helm install mywordpress bitnami/wordpress
   ```
2. Mettre à jour l'application WordPress :
   ```bash
   helm upgrade mywordpress bitnami/wordpress --set wordpressUsername=admin,wordpressPassword=secretpassword
   ```
3. Désinstaller l'application WordPress :
   ```bash
   helm uninstall mywordpress
   ```

---

## Conclusion

Ce cours vous a fourni une compréhension complète de Kubernetes et Docker, couvrant à la fois les bases et les concepts avancés de l'orchestration de conteneurs. Vous avez appris à configurer, déployer, et gérer des applications conteneurisées à l'aide de Docker, Docker Swarm, et Kubernetes. Vous avez également exploré des concepts avancés tels que les taints et tolerations, et l'utilisation de Helm pour déployer des applications. En suivant les exercices pratiques, vous avez renforcé vos compétences en gestion de clusters Kubernetes et en orchestration de conteneurs.

## Ressources Supplémentaires

- [Documentation officielle de Kubernetes](https://kubernetes.io/docs/)
- [Guide pratique de Docker](https://docs.docker.com/get-started/)
- [Helm Charts](https://helm.sh/docs/)
- [Kubernetes Patterns](https://www.oreilly.com/library/view/kubernetes-patterns/9781492050272/)

Continuez à explorer ces ressources et à pratiquer avec des exercices pratiques pour approfondir votre compréhension et vos compétences en orchestration de conteneurs avec Kubernetes.
