# Cours : Kubernetes et Orchestration de Conteneurs

Ce cours est conçu pour offrir une compréhension complète de l'orchestration de conteneurs avec Docker et Kubernetes. Il couvre à la fois les concepts théoriques et les exercices pratiques pour assurer une compréhension approfondie et pratique de la gestion des applications conteneurisées.

## Table des Matières

1. [Lancer une VM Ubuntu](#1-lancer-une-vm-ubuntu)
2. [Aperçu de Docker](#2-apercu-de-docker)
3. [Orchestration avec Docker Swarm](#3-orchestration-avec-docker-swarm)
4. [Théorie de Kubernetes](#4-theorie-de-kubernetes)
5. [Orchestration avec Kubernetes](#5-orchestration-avec-kubernetes)
6. [Créer un Cluster avec KIND](#6-creer-un-cluster-avec-kind)
7. [Comprendre les Pods](#7-comprendre-les-pods)
8. [Introduction aux Services Kubernetes](#8-introduction-aux-services-kubernetes)
9. [CronJobs dans Kubernetes](#9-cronjobs-dans-kubernetes)
10. [Maîtrise des Métriques Kubernetes: Déploiement et Surveillance avec Metrics Server](#10-maitrise-des-metriques-kubernetes-deploiement-et-surveillance-avec-metrics-server)

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

## 8. Introduction aux Services Kubernetes

### Objectifs :
- Comprendre les différents types de services Kubernetes
- Apprendre à déployer et gérer des services

### Pourquoi avons-nous besoin de services dans Kubernetes ?
- **Dynamisme** : Les pods (maisons) dans Kubernetes sont très dynamiques, ils peuvent être déplacés, supprimés, ou dupliqués à tout moment en fonction de la charge de travail. Donc, si vous vous fiez directement à l'adresse d'un pod pour accéder à votre application, cela pourrait ne pas fonctionner la prochaine fois que vous essayez. Un service offre une adresse constante pour accéder à votre application, peu importe les mouvements des pods.
- **Équilibrage de charge** : Imaginons que vous ayez plusieurs instances de votre application pour gérer plus de trafic. Un service distribuera automatiquement les demandes entre ces instances, assurant ainsi que l'application peut gérer efficacement le trafic.

### Types de Services dans Kubernetes

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

### Exemples Concrets

Imaginons que Kubernetes est comme un grand centre commercial avec plein de magasins différents (vos applications). Pour que les clients (les requêtes ou les données) trouvent et accèdent aux magasins, il y a différentes façons de les guider.

1. **ClusterIP** : Imaginez ça comme une adresse secrète interne. C'est comme si chaque magasin avait une porte dérobée utilisée seulement par les employés du centre commercial. Seules les personnes déjà à l'intérieur (dans le cluster) peuvent utiliser cette adresse pour venir voir ce que vous proposez.

2. **NodePort** : C'est comme si, en plus de la porte principale, chaque magasin avait un stand dans le parking. Peu importe à quelle porte vous vous présentez, vous pouvez commander les mêmes choses. Cela signifie que votre magasin (application) peut être atteint de l'extérieur en utilisant l'adresse du centre commercial (le nœud) et un numéro de stand spécifique (le port).

3. **LoadBalancer** : Imaginez maintenant que votre magasin devienne super populaire. Le centre décide de vous donner un coup de main en plaçant quelques employés à l'entrée pour gérer la foule et diriger les clients vers vos caisses de manière efficace. C'est comme avoir votre propre équipe à l'entrée qui s'assure que tout le monde peut accéder à vos offres sans faire la queue.

4. **ExternalName** : Enfin, disons que vous voulez que vos clients puissent commander des pizzas d'un restaurant en dehors du centre commercial. Au lieu de les faire sortir et chercher par eux-mêmes, vous donnez un flyer avec le nom et l'adresse du restaurant. De cette façon, ils peuvent commander directement depuis votre magasin. C'est une manière de connecter les gens à un service qui n'est pas dans le centre (cluster) mais que vous voulez rendre accessible comme s'il l'était.

### Conclusion sur les Services Kubernetes

Les services Kubernetes agissent comme un guide touristique dans la ville de Kubernetes. Ils connaissent tous les emplacements (pods) où vos applications vivent et comment les atteindre de la manière la plus efficace possible, offrant une adresse stable pour accéder à ces applications et distribuant le trafic pour gérer la charge. C'est un concept fondamental qui aide à rendre les applications Kubernetes accessibles et robustes.

---

## 9. CronJobs dans Kubernetes

### Objectifs :
- Comprendre la création et la gestion des CronJobs
- Apprendre à planifier des tâches répétitives

### Introduction aux CronJobs

Les CronJobs dans Kubernetes permettent de planifier l'exécution de tâches répétitives. Cela peut inclure des tâches de maintenance, des sauvegardes de base de données, ou toute autre tâche périodique.

### Définition d'un CronJob

Un CronJob est défini par un fichier YAML qui spécifie le planning (sous forme d'une expression cron), ainsi que la tâche à exécuter (sous forme d'un Job). Voici un exemple de fichier YAML pour un CronJob :

```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: example-cronjob
spec:
  schedule: "*/1 * * * *"  # Exécute la tâche toutes les minutes
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: example
            image: busybox
            args:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure
```

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

#### Étape 3 : Supprimer

 le Job

Une fois que vous avez confirmé que le job a été exécuté avec succès (ou après un certain temps), supprimez-le avec `kubectl delete job myjob1`.

#### Bonus : Création d'un Job via un Fichier YAML

Créez un fichier YAML pour le job (`myjob2.yaml`) avec une spécification qui exécute une commande différente, par exemple, afficher le message "Hello Kubernetes". Appliquez ce fichier et observez l'exécution du job.

### Exercice 2 : Pratique de CronJobs

#### Étape 1 : Préparation de l'environnement

- Assurez-vous que votre base de données est accessible et prête pour le backup.
- Ajoutez un label sur un nœud pour diriger les pods du CronJob sur ce nœud spécifique :
  ```bash
  kubectl label node <nom-du-nœud> app=backup
  ```

#### Étape 2 : Écrire le fichier YAML du CronJob

```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: db-backup
spec:
  schedule: "0 0 * * *"  # Exécution chaque jour à minuit
  jobTemplate:
    spec:
      template:
        spec:
          nodeSelector:
            app: backup
          containers:
          - name: db-backup
            image: mongo
            command:
            - /bin/sh
            - -c
            - mongodump --gzip --host db-service --archive=/backup/db-$(date +"%Y%m%dT%H%M%S").gz
            volumeMounts:
            - name: backup-storage
              mountPath: /backup
          restartPolicy: OnFailure
          volumes:
          - name: backup-storage
            hostPath:
              path: /path/to/backup
```

#### Étape 3 : Déployer le CronJob

```bash
kubectl apply -f db-backup-cronjob.yaml
```

#### Étape 4 : Vérification et validation

- Utilisez `kubectl get cronjobs` pour vérifier la création du CronJob.
- Attendez l'heure planifiée et vérifiez que le job a été exécuté avec succès.
- Utilisez `kubectl logs` pour voir les logs des jobs et confirmer que le backup a été réalisé.

### Conclusion sur les CronJobs

Les CronJobs sont un outil puissant dans Kubernetes pour automatiser des tâches répétitives. En suivant les bonnes pratiques et en utilisant les outils disponibles pour planifier et observer ces tâches, vous pouvez assurer que vos opérations récurrentes se déroulent de manière fiable et efficace.

---

## 10. Maîtrise des Métriques Kubernetes: Déploiement et Surveillance avec Metrics Server

### Objectif :
Déployer le Metrics Server dans votre cluster Kubernetes et analyser les métriques de performance des nœuds et des pods.

### Instructions :

1. **Déploiement du Metrics Server :**
   - Clonez le dépôt contenant les fichiers de déploiement du Metrics Server.
   - Appliquez les fichiers YAML pour déployer le Metrics Server dans votre cluster.
   - Vérifiez que le Metrics Server est actif et opérationnel.

2. **Collecte des Métriques :**
   - Utilisez la commande `kubectl top nodes` pour afficher les métriques des nœuds.
   - Utilisez la commande `kubectl top pods` pour afficher les métriques des pods.

3. **Analyse des Métriques :**
   - Identifiez le nœud qui consomme le plus de CPU.
   - Identifiez le nœud qui consomme le plus de mémoire.
   - Identifiez le pod qui consomme le plus de mémoire dans l'espace de noms par défaut.
   - Identifiez le pod qui consomme le moins de CPU dans l'espace de noms par défaut.

### Exercice Pratique :

En supposant que vous ayez un cluster avec les nœuds suivants: `node01`, `node02`, `node03`, et un espace de noms par défaut contenant les pods: `tiger`, `elephant`, `lion`, `rabbit` :

1. Exécutez la commande pour afficher les métriques de tous les nœuds.
2. Déterminez quel nœud a la plus haute consommation de CPU et de mémoire.
3. Exécutez la commande pour afficher les métriques de tous les pods dans l'espace de noms par défaut.
4. Identifiez quel pod a la plus haute consommation de mémoire et lequel a la plus basse consommation de CPU.
5. Documentez vos résultats et les commandes que vous avez utilisées dans un fichier `metrics-analysis.txt`.

### Critères de Réussite :

- Le Metrics Server est correctement déployé et actif.
- Les métriques des nœuds et des pods sont correctement collectées et affichées.
- L'analyse des métriques est correctement réalisée et documentée.

### Ressources Fournies :

- Accès au cluster Kubernetes.
- Documentation officielle de Kubernetes pour les commandes `kubectl`.
- Accès au dépôt Git du Metrics Server.

### Livraison :

- Un fichier `metrics-analysis.txt` contenant toutes les commandes exécutées et les analyses des métriques.

### Prérequis

- Avoir un cluster Kubernetes opérationnel.
- Avoir `kubectl` configuré pour communiquer avec votre cluster.
- Avoir les droits nécessaires pour créer des ressources dans le cluster.

### Étapes de déploiement du Metrics Server

1. **Cloner le dépôt Git du Metrics Server :**
   ```sh
   git clone https://github.com/kodekloudhub/kubernetes-metrics-server.git
   ```

2. **Naviguer dans le répertoire du dépôt cloné :**
   ```sh
   cd kubernetes-metrics-server/
   ```

3. **Appliquer les configurations avec `kubectl` :**
   Cela créera les différents composants nécessaires pour le Metrics Server.
   ```sh
   kubectl apply -f aggregated-metrics-reader.yaml
   kubectl apply -f auth-reader.yaml
   kubectl apply -f auth-delegator.yaml
   kubectl apply -f metrics-apiservice.yaml
   kubectl apply -f metrics-server-deployment.yaml
   kubectl apply -f metrics-server-service.yaml
   kubectl apply -f resource-reader.yaml
   ```

4. **Vérifier que le Metrics Server est déployé et collecte des données :**
   Cela peut prendre quelques minutes.
   ```sh
   kubectl top node
   ```

5. **Inspecter l'utilisation des ressources :**

   - Pour voir l'utilisation CPU des nœuds :
     ```sh
     kubectl top node
     ```
   
   - Pour voir l'utilisation mémoire des nœuds :
     ```sh
     kubectl top node
     ```
   
   - Pour voir l'utilisation des ressources par les Pods, triée par mémoire :
     ```sh
     kubectl top pod --sort-by='memory'
     ```

   - Pour voir l'utilisation des ressources par les Pods, triée par CPU :
     ```sh
     kubectl top pod --sort-by='cpu'
     ```

### Utilisation des métriques pour le logging et le monitoring

- **Logging :** Les métriques peuvent être utilisées pour comprendre le comportement des applications et identifier les goulots d'étranglement. Des outils comme Elasticsearch, Logstash et Kibana (la stack ELK) ou Grafana et Loki peuvent être utilisés pour stocker, interroger et visualiser les logs.

- **Monitoring :** Les métriques collectées par le Metrics Server peuvent être utilisées par des outils de monitoring comme Prometheus et Grafana pour créer des tableaux de bord qui montrent en temps réel l'utilisation des ressources et pour configurer des alertes basées sur des seuils prédéfinis.

### Bonnes pratiques

- **Configurer des alertes :** Utilisez les métriques pour définir des alertes qui vous avertiront en cas de dépassement de seuils critiques.
  
- **Automatisation :** Intégrez les métriques avec des outils d'automatisation comme Kubernetes Autoscaling pour ajuster les ressources allouées aux applications en fonction de la charge.

- **Surveillance continue :** Surveillez régulièrement les métriques pour détecter les tendances à long terme et optimiser les performances.

---

### Nouveaux Exercices

#### Exercice 1: Déploiement et Analyse Basique

1. **Déployer un Metrics Server:**
   - Clonez le dépôt `https://github.com/kodekloudhub/kubernetes-metrics-server.git`.
   - Appliquez les configurations nécessaires en utilisant `kubectl apply -f`.

2. **Vérification du Déploiement:**
   - Vérifiez que le Metrics Server est en cours d'exécution avec `kubectl get pods -n kube-system`.
   - Utilisez `kubectl top nodes` pour vérifier que les métriques des nœuds sont disponibles.

3. **Collecte et Analyse des Métriques:**
   - Utilisez `kubectl top pods --all-namespaces` pour afficher les métriques des pods dans tous les espaces de noms.
   - Identifiez le pod qui consomme le plus de mémoire et celui qui consomme le plus de CPU.

**Correction:**
   - Commande pour déploiement:
     ```sh
     git clone https://github.com/kodekloudhub/kubernetes-metrics-server.git
     cd kubernetes-metrics-server/
     kubectl apply -f .
     ```
   - Commande pour vérifier les métriques des nœuds:
     ```sh
     kubectl top nodes
     ```
   - Commande pour vérifier les métriques des pods:
     ```sh
     kubectl top pods --all-namespaces
     ```
   -

 Documentez les résultats dans `metrics-analysis.txt`.

---

#### Exercice 2: Surveiller les Ressources d'une Application Spécifique

1. **Déployer une Application de Test:**
   - Créez un fichier YAML pour déployer une application Nginx et MySQL.
   - Déployez cette application dans un espace de noms dédié (`test-namespace`).

2. **Collecter des Métriques:**
   - Utilisez `kubectl top pods -n test-namespace` pour afficher les métriques des pods Nginx et MySQL.

3. **Analyser les Métriques:**
   - Identifiez les différences de consommation de ressources entre Nginx et MySQL.

**Correction:**
   - Exemple de fichier YAML pour l'application de test:
     ```yaml
     apiVersion: v1
     kind: Namespace
     metadata:
       name: test-namespace
     ---
     apiVersion: apps/v1
     kind: Deployment
     metadata:
       name: nginx-deployment
       namespace: test-namespace
     spec:
       replicas: 2
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
             image: nginx
             ports:
             - containerPort: 80
     ---
     apiVersion: apps/v1
     kind: Deployment
     metadata:
       name: mysql-deployment
       namespace: test-namespace
     spec:
       replicas: 1
       selector:
         matchLabels:
           app: mysql
       template:
         metadata:
           labels:
             app: mysql
         spec:
           containers:
           - name: mysql
             image: mysql:5.7
             env:
             - name: MYSQL_ROOT_PASSWORD
               value: password
     ```
   - Commandes pour déployer et vérifier:
     ```sh
     kubectl apply -f test-namespace.yaml
     kubectl top pods -n test-namespace
     ```
   - Analyse à documenter dans `metrics-analysis.txt`.

---

### Exercice 3: Mise en Place d'Alertes

1. **Configurer Prometheus et Alertmanager:**
   - Déployez Prometheus et Alertmanager dans votre cluster Kubernetes.
   - Configurez Prometheus pour collecter les métriques du Metrics Server.

2. **Configurer des Règles d'Alerte:**
   - Créez une règle d'alerte pour notifier lorsque l'utilisation de la CPU d'un pod dépasse 80%.

3. **Tester les Alertes:**
   - Déclenchez manuellement une alerte en augmentant la charge sur un pod.

**Correction:**
   - Commande pour déployer Prometheus:
     ```sh
     kubectl apply -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/master/bundle.yaml
     ```
   - Exemple de règle d'alerte:
     ```yaml
     groups:
     - name: example
       rules:
       - alert: HighCPUUsage
         expr: sum(rate(container_cpu_usage_seconds_total{container!="POD",pod!=""}[1m])) by (pod) > 0.8
         for: 2m
         labels:
           severity: warning
         annotations:
           summary: "High CPU usage detected for pod {{ $labels.pod }}"
           description: "Pod {{ $labels.pod }} is using more than 80% CPU for more than 2 minutes."
     ```
   - Tester les alertes:
     ```sh
     # Augmenter la charge CPU sur un pod
     kubectl run -i --tty load-generator --image=busybox /bin/sh
     # Dans le conteneur, exécutez une charge CPU élevée
     while true; do :; done
     ```
   - Documentez le processus et les résultats dans `alerts-analysis.txt`.

---

En suivant ces exercices, vous renforcez votre compréhension de la surveillance et de la gestion des ressources dans un cluster Kubernetes.
