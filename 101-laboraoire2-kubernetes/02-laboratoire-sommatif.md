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

### Date de remise
- Dimanche, 09 juin 2024 (à la fin de la journée)

### Pondération
- 25% de la note finale

---

Ce travail pratique vous permettra de mieux comprendre et maîtriser l'utilisation de Kubernetes pour déployer et gérer des applications conteneurisées. Bonne chance !
