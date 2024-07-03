# Travail Pratique : Kubernetes
## Attention : Ce travail comporte deux parties !

# Contexte
Le but de ce travail pratique est :
- d'apprendre à déployer des applications conteneurisées sur Kubernetes ;
- de comprendre les concepts de base de Kubernetes ;
- d'utiliser Minikube ou k3s pour déployer des applications à partir de fichiers Docker Compose.

## Ce travail doit se réaliser à l'aide d'une station Linux serveur et d'un client (Linux ou Windows) avec navigateur (browser).



# PARTIE 1 (70%)

#### Introduction
Ce laboratoire pratique vous guidera à travers les concepts de base de Kubernetes en utilisant Minikube ou k3s. Vous apprendrez à déployer des applications conteneurisées en utilisant des fichiers Docker Compose et à les exécuter sur un cluster Kubernetes.

### Configuration IP du serveur
Complétez le tableau suivant :
| Élément                        | Commande          | Résultat   |
|--------------------------------|-------------------|------------|
| Adresse IP                     | `ip a`            | ...        |
| Masque de sous-réseau          | `ip a`            | ...        |
| Passerelle par défaut          | `ip route`        | ...        |
| Serveur DNS                    | `cat /etc/resolv.conf` | ...        |
| Adresse MAC de l’interface     | `ip link show`    | ...        |

### Déploiement d'une application avec Minikube/k3s

1. **Installer Minikube ou k3s**
   - Suivez les instructions d'installation pour [Minikube](https://minikube.sigs.k8s.io/docs/start/) ou [k3s](https://k3s.io/).

2. **Démarrer Minikube ou k3s**
   - Minikube :
     ```bash
     minikube start
     ```
   - k3s :
     ```bash
     curl -sfL https://get.k3s.io | sh -
     ```

3. **Déployer une application avec un fichier Docker Compose**
   - Choisissez une des applications suivantes :
     - WordPress et MySQL
     - Redis et une application Node.js
     - Nginx et une application Python (Flask)
     - Prometheus et Grafana
   - Convertissez le fichier `docker-compose.yaml` en fichiers de déploiement Kubernetes. Vous pouvez utiliser des outils comme `kompose` pour simplifier cette tâche.

4. **Exemple de conversion avec `kompose`**
   - Installez `kompose` :
     ```bash
     curl -L https://github.com/kubernetes/kompose/releases/download/v1.22.0/kompose-linux-amd64 -o kompose
     chmod +x kompose
     sudo mv kompose /usr/local/bin/kompose
     ```
   - Convertissez votre fichier `docker-compose.yaml` :
     ```bash
     kompose convert
     ```
   - Déployez les fichiers générés sur Kubernetes :
     ```bash
     kubectl apply -f .
     ```

5. **Vérifiez le déploiement**
   - Assurez-vous que tous les pods sont en cours d'exécution :
     ```bash
     kubectl get pods
     ```
   - Exposez le service pour accéder à l'application. Vous avez le choix entre ClusterIP, NodePort et Ingress :

     - **ClusterIP :**
       ```bash
       kubectl expose deployment <nom-deployment> --type=ClusterIP --port=80
       ```
     - **NodePort (pour Minikube) :**
       ```bash
       kubectl expose deployment <nom-deployment> --type=NodePort --port=80
       minikube service <nom-deployment> --url
       ```
     - **Ingress :**
       - Créez un fichier `ingress.yaml` :
         ```yaml
         apiVersion: networking.k8s.io/v1
         kind: Ingress
         metadata:
           name: <nom-ingress>
           annotations:
             nginx.ingress.kubernetes.io/rewrite-target: /
         spec:
           rules:
           - host: <votre-domaine>
             http:
               paths:
               - path: /
                 pathType: Prefix
                 backend:
                   service:
                     name: <nom-deployment>
                     port:
                       number: 80
         ```
       - Appliquez le fichier Ingress :
         ```bash
         kubectl apply -f ingress.yaml
         ```

6. **Documentez votre processus**
   - Notez toutes les commandes utilisées et les résultats obtenus.
   - Incluez des captures d'écran montrant les conteneurs en cours d'exécution et l'accès à l'application via le navigateur.

### Concepts Additionnels

#### Namespace
Les namespaces permettent de séparer les ressources au sein d'un même cluster. Créez un namespace pour votre projet :
```bash
kubectl create namespace <nom-namespace>
kubectl config set-context --current --namespace=<nom-namespace>
```

#### Scaling des Déploiements
Mettez à l'échelle votre déploiement pour gérer plus de trafic :
```bash
kubectl scale deployment <nom-deployment> --replicas=3
```

#### Suppression des Déploiements
Pour supprimer un déploiement et ses ressources associées :
```bash
kubectl delete deployment <nom-deployment>
kubectl delete service <nom-service>
kubectl delete ingress <nom-ingress>
```

#### Containerisation et Hébergement sur Docker Hub
1. **Créer une image Docker :**
   - Créez un fichier `Dockerfile` :
     ```dockerfile
     FROM python:3.8-slim
     WORKDIR /app
     COPY . /app
     RUN pip install -r requirements.txt
     CMD ["python", "app.py"]
     ```

2. **Construire et pousser l'image sur Docker Hub :**
   - Construisez l'image :
     ```bash
     docker build -t <votre-utilisateur-dockerhub>/mon-application .
     ```
   - Connectez-vous à Docker Hub :
     ```bash
     docker login
     ```
   - Poussez l'image :
     ```bash
     docker push <votre-utilisateur-dockerhub>/mon-application
     ```

3. **Créer un Pod à partir de l'image :**
   - Créez un fichier `pod.yaml` :
     ```yaml
     apiVersion: v1
     kind: Pod
     metadata:
       name: mon-application
     spec:
       containers:
       - name: mon-application
         image: <votre-utilisateur-dockerhub>/mon-application
         ports:
         - containerPort: 80
     ```
   - Appliquez le fichier :
     ```bash
     kubectl apply -f pod.yaml
     ```

4. **Vérifiez et connectez-vous à l'application :**
   - Vérifiez que le pod est en cours d'exécution :
     ```bash
     kubectl get pods
     ```
   - Accédez à l'application en fonction du service configuré (ClusterIP, NodePort ou Ingress).

### Nettoyage
Pour nettoyer votre cluster :
```bash
kubectl delete namespace <nom-namespace>
minikube stop
minikube delete
```

# PARTIE 2 (30%)

#### Objectif de la partie 2 :
L'objectif de la partie 2 est de vous familiariser avec la création de déploiements et de services sur Kubernetes en utilisant des fichiers YAML. Vous allez configurer deux services avec une relation de dépendance.

### Instructions :
1. **Prérequis :**
   - Avoir Minikube ou k3s installé sur votre machine.
   - Connaître les bases de Kubernetes.

2. **Création du projet :**
   - Créez un nouveau dossier pour votre projet et naviguez à l'intérieur de celui-ci.

3. **Configuration des déploiements Kubernetes :**
   - Dans ce dossier, créez des fichiers de déploiement YAML pour vos services.
   - Configurez deux services avec une relation de dépendance.

4. **Choix des conteneurs :**
   - Vous pouvez choisir parmi les exemples suivants ou en proposer d'autres, en respectant les exigences de dépendance :
     - Wiki.js et PostgreSQL
     - WordPress et MySQL
     - Redis et une application Node.js
     - Nginx et une application Python (Flask)
     - Elasticsearch et Kibana
     - Prometheus et Grafana

5. **Exemple de fichier YAML pour WordPress et MySQL :**

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  selector:
    app: wordpress
  ports:
    - protocol: TCP
      port: 3306
      targetPort: 3306
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
spec:
  selector:
    matchLabels:
      app: wordpress
  template:
    metadata:
      labels:
        app: wordpress


    spec:
      containers:
        - name: mysql
          image: mysql:5.7
          env:
            - name: MYSQL_ROOT_PASSWORD
              value: "somewordpress"
            - name: MYSQL_DATABASE
              value: "wordpress"
            - name: MYSQL_USER
              value: "wordpress"
            - name: MYSQL_PASSWORD
              value: "wordpress"
          volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql
      volumes:
        - name: mysql-persistent-storage
          persistentVolumeClaim:
            claimName: mysql-pv-claim
---
apiVersion: v1
kind: Service
metadata:
  name: wordpress
spec:
  selector:
    app: wordpress
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: NodePort
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress
spec:
  selector:
    matchLabels:
      app: wordpress
  template:
    metadata:
      labels:
        app: wordpress
    spec:
      containers:
        - name: wordpress
          image: wordpress:latest
          env:
            - name: WORDPRESS_DB_HOST
              value: mysql:3306
            - name: WORDPRESS_DB_USER
              value: wordpress
            - name: WORDPRESS_DB_PASSWORD
              value: wordpress
            - name: WORDPRESS_DB_NAME
              value: wordpress
          ports:
            - containerPort: 80
```

6. **Testez votre configuration :**
   - Lancez les déploiements avec la commande `kubectl apply -f .`.
   - Vérifiez que les services dépendent correctement l'un de l'autre.
   - Accédez à l'interface web de l'application pour confirmer son bon fonctionnement.

7. **Documentation :**
   - Rédigez un document expliquant votre configuration et les étapes que vous avez suivies pour créer et tester vos fichiers YAML.
   - Incluez des captures d'écran montrant les conteneurs en cours d'exécution et l'interface de l'application choisie.

8. **Soumission :**
   - Soumettez vos fichiers YAML ainsi que votre document de documentation via la plateforme Léa avant la date limite.

### Critères d'Évaluation :
- **Validité des fichiers YAML :** Assurez-vous que les fichiers sont valides et que les services démarrent comme prévu.
- **Documentation :** La clarté et la précision de la documentation seront évaluées.
- **Fonctionnalité :** Vérifiez que l'application fonctionne correctement et peut se connecter à son service dépendant.

### Équipes
- Deux (minimum) à trois (maximum) étudiants par équipe

### Pondération
- 25% de la note finale

---

Ce travail pratique vous permettra de mieux comprendre et maîtriser l'utilisation de Kubernetes pour déployer et gérer des applications conteneurisées. Bonne chance !

# Grille d'évaluation :

### Grille d'Évaluation pour le Travail Pratique Kubernetes

| Critères d'Évaluation                            | Description                                                                                                                                                 | Points  |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|---------|
| **Installation et Configuration**                |                                                                                                                                                             | **/10** |
| - Installation de Minikube/k3s                   | Minikube ou k3s est correctement installé et démarré.                                                                                                       | 5       |
| - Configuration IP du serveur                    | Les informations réseau (adresse IP, masque de sous-réseau, passerelle, DNS, adresse MAC) sont correctement identifiées et documentées.                      | 5       |
| **Déploiement d'Applications Conteneurisées**    |                                                                                                                                                             | **/20** |
| - Conversion de Docker Compose à Kubernetes      | Le fichier `docker-compose.yaml` est correctement converti en fichiers YAML Kubernetes (utilisation de `kompose` ou autre méthode).                         | 10      |
| - Déploiement des Applications                   | Les fichiers YAML sont appliqués correctement et les pods sont en cours d'exécution.                                                                         | 10      |
| **Exposure et Accès aux Services**               |                                                                                                                                                             | **/15** |
| - Utilisation de ClusterIP, NodePort ou Ingress  | Les services sont exposés correctement en utilisant ClusterIP, NodePort ou Ingress.                                                                          | 5       |
| - Accès à l'application                          | L'application est accessible via l'URL ou l'adresse IP fournie, avec capture d'écran à l'appui.                                                              | 5       |
| - Documentation des étapes                       | Les étapes pour exposer et accéder à l'application sont bien documentées.                                                                                    | 5       |
| **Namespaces et Organisation des Ressources**    |                                                                                                                                                             | **/10** |
| - Création et utilisation de Namespaces          | Les namespaces sont créés et utilisés pour organiser les ressources.                                                                                         | 5       |
| - Vérification des namespaces                    | Les commandes pour vérifier les namespaces et les ressources dans les namespaces sont utilisées et documentées.                                              | 5       |
| **Scaling et Gestion des Déploiements**          |                                                                                                                                                             | **/15** |
| - Scaling des déploiements                       | Le scaling des déploiements est effectué correctement (augmentation ou réduction du nombre de réplicas).                                                      | 10      |
| - Documentation du scaling                       | Les étapes de scaling sont bien documentées avec des captures d'écran.                                                                                       | 5       |
| **Containerisation et Hébergement**              |                                                                                                                                                             | **/15** |
| - Création d'une image Docker                    | Une image Docker est créée correctement à partir d'un Dockerfile.                                                                                            | 5       |
| - Hébergement sur Docker Hub                     | L'image est poussée sur Docker Hub et est accessible publiquement.                                                                                           | 5       |
| - Utilisation de l'image dans Kubernetes         | L'image Docker est utilisée pour créer des pods dans Kubernetes.                                                                                             | 5       |
| **Nettoyage et Suppression des Ressources**      |                                                                                                                                                             | **/5**  |
| - Suppression des déploiements et services       | Les déploiements, services et autres ressources sont correctement supprimés après usage.                                                                     | 5       |
| **Documentation Générale**                       |                                                                                                                                                             | **/10** |
| - Clarté et Précision                            | La documentation est claire, précise et facile à suivre.                                                                                                     | 5       |
| - Capture d'écran et exemples                    | Les captures d'écran et les exemples sont pertinents et illustrent correctement les étapes et résultats attendus.                                             | 5       |
| **Partie 2 : Configuration Avancée**             |                                                                                                                                                             | **/20** |
| - Déploiements avec dépendances                  | Les déploiements sont correctement configurés avec des relations de dépendance entre les services.                                                            | 10      |
| - Test et validation des déploiements            | Les déploiements sont testés et validés pour s'assurer que les services dépendent correctement l'un de l'autre et fonctionnent comme prévu.                   | 5       |
| - Documentation des configurations avancées      | La documentation explique clairement les configurations avancées et les étapes de déploiement avec captures d'écran à l'appui.                                | 5       |
| **Total**                                        |                                                                                                                                                             | **/120**|

### Remarques :
- La grille d'évaluation est divisée en plusieurs sections correspondant aux concepts clés couverts dans le laboratoire.
- Chaque section est subdivisée en critères spécifiques avec des points attribués en fonction de l'accomplissement des tâches et de la qualité de la documentation.
- Les étudiants doivent fournir des captures d'écran et une documentation détaillée pour chaque étape afin de valider leur travail.
- Les critères de la partie 2 sont centrés sur la configuration avancée et les relations de dépendance entre les services.

- # ANNEXE - Concepts appris

### Concepts Couverts dans ce Laboratoire

Ce laboratoire couvre une variété de concepts essentiels pour comprendre et utiliser Kubernetes. Voici les principaux concepts abordés :

#### Partie 1 : Introduction à Kubernetes avec Minikube/k3s

1. **Installation de Minikube/k3s :**
   - Mise en place d'un environnement Kubernetes local en utilisant Minikube ou k3s.
   
2. **Configuration IP du serveur :**
   - Identification et configuration des éléments réseau de base (adresse IP, masque de sous-réseau, passerelle par défaut, serveur DNS, adresse MAC).

3. **Déploiement d'Applications Conteneurisées :**
   - Conversion de fichiers `docker-compose.yaml` en fichiers de déploiement Kubernetes.
   - Utilisation de `kompose` pour automatiser cette conversion.
   - Création et application de fichiers YAML pour déployer des applications sur Kubernetes.

4. **Vérification du Déploiement :**
   - Utilisation de commandes Kubernetes pour vérifier l'état des pods et des services (`kubectl get pods`, `kubectl get services`).
   - Accès aux applications déployées via différents types de services (ClusterIP, NodePort, Ingress).

5. **Exposure des Services :**
   - Comprendre et utiliser les différents types de services Kubernetes pour exposer les applications :
     - **ClusterIP :** Pour un accès interne au cluster.
     - **NodePort :** Pour un accès externe via un port spécifique sur chaque nœud.
     - **Ingress :** Pour un accès externe via des règles d'URL et des hôtes.

6. **Namespaces :**
   - Utilisation des namespaces pour organiser et isoler les ressources au sein du cluster.

7. **Scaling des Déploiements :**
   - Mise à l'échelle des déploiements pour gérer des charges de travail variables (`kubectl scale deployment`).

8. **Suppression des Déploiements et des Services :**
   - Commandes pour supprimer les ressources Kubernetes (`kubectl delete`).

9. **Containerisation et Hébergement sur Docker Hub :**
   - Création d'une image Docker à partir d'un `Dockerfile`.
   - Hébergement de l'image sur Docker Hub.
   - Création et déploiement de pods Kubernetes à partir d'images Docker.

10. **Nettoyage du Cluster :**
    - Commandes pour arrêter et supprimer les clusters Minikube (`minikube stop`, `minikube delete`).

#### Partie 2 : Configuration Avancée avec Kubernetes

1. **Création de Déploiements avec des Relations de Dépendance :**
   - Création de fichiers de déploiement YAML pour des applications interconnectées.
   - Définition des dépendances entre les services (par exemple, WordPress dépendant de MySQL).

2. **Test et Validation des Déploiements :**
   - Vérification que les services dépendent correctement l'un de l'autre et fonctionnent comme prévu.

3. **Documentation des Configurations :**
   - Écriture de documents expliquant les configurations et les étapes de déploiement, y compris des captures d'écran pour montrer les résultats.

### Concepts de Kubernetes Abordés

- **Pods :** L'unité de base de déploiement dans Kubernetes, représentant une ou plusieurs conteneurs déployés ensemble sur un même nœud.
- **Services :** Ressources Kubernetes qui exposent un ensemble de pods en tant que service réseau.
- **Deployments :** Ressources de gestion de la mise à l'échelle et de la mise à jour des applications conteneurisées.
- **Scaling :** Mise à l'échelle horizontale des applications pour répondre à la demande.
- **Namespaces :** Isolation et organisation des ressources dans un cluster Kubernetes.
- **Ingress :** Gestion des accès externes aux services au sein du cluster.
- **Persistent Volumes et Claims :** Gestion du stockage persistant pour les pods.
- **Docker :** Containerisation des applications et hébergement d'images sur Docker Hub.
- **Networking :** Concepts de mise en réseau et configuration des services pour l'accès interne et externe.

Ces concepts et pratiques vous permettent de développer une compréhension approfondie de la gestion des applications conteneurisées sur Kubernetes et de leur cycle de vie, depuis la containerisation jusqu'au déploiement, à la mise à l'échelle et à la gestion.


# Annexe 1 - 
Pour cet examen pratique sur Kubernetes, vous n'avez pas besoin d'un nom de domaine réel. Vous pouvez effectuer tout le travail en local grâce à des outils comme Minikube ou k3s, qui vous permettent de simuler un environnement Kubernetes sur votre propre machine. Cela inclut la configuration du réseau, le déploiement des applications, et l'exposition des services sans nécessiter un domaine internet.

Votre examen requiert l'utilisation d'Ingress pour exposer des services, vous pouvez simplement modifier le fichier `/etc/hosts` de votre système pour simuler un nom de domaine en le faisant pointer vers l'adresse IP locale de votre Minikube ou k3s. Vous pouvez procéder de cette façon :

1. Déterminez l'adresse IP de votre Minikube ou k3s (souvent c'est `192.168.49.2` pour Minikube).
2. Ajoutez une ligne à votre fichier `/etc/hosts` de cette forme :
   ```
   192.168.49.2 mondomaine.local
   ```
   Remplacez `192.168.49.2` par l'adresse IP réelle et `mondomaine.local` par le nom fictif que vous souhaitez utiliser.

En utilisant cette méthode, vous pouvez accéder à vos applications via un navigateur en utilisant le nom de domaine fictif, tout en travaillant entièrement en local. Cela vous évite d'avoir à enregistrer et payer pour un nom de domaine réel, tout en vous permettant de compléter tous les aspects de votre travail pratique.
