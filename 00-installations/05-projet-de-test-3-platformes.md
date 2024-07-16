🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
# Partie 1 - Tester avec KIND
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

# Références
- [Monitoring-Kubernetes-Cluster](https://github.com/alihussainia/Monitoring-Kubernetes-Cluster.git)
- [Azure Kubernetes](https://github.com/rudihinds/azure-kube)
- [Article sur Medium](https://medium.com/@akilblanchard09/monitoring-a-kubernetes-cluster-using-prometheus-and-grafana-8e0f21805ea9)

### Étape 1 : Clonage du dépôt GitHub

Tout d'abord, nous allons cloner le dépôt GitHub qui contient les fichiers nécessaires.

```bash
cd Desktop
git clone https://github.com/alihussainia/Monitoring-Kubernetes-Cluster.git
cd Monitoring-Kubernetes-Cluster
```

### Étape 2 : Création du cluster Kubernetes

Nous allons utiliser Kind (Kubernetes IN Docker) pour créer un cluster Kubernetes.

```bash
kind create cluster
```

### Étape 3 : Vérification du cluster

Vérifiez que le cluster a été correctement créé.

```bash
kind get clusters
kubectl cluster-info --context kind-kind
kubectl get nodes
kubectl get all -A
```

### Étape 4 : Déploiement de l'application avec les fichiers YAML

#### `deployment.yml`

Contenu du fichier `deployment.yml` :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-app
  labels:
    app: nginx-app
spec:
  replicas: 4
  selector:
    matchLabels:
      app: nginx-app
  template:
    metadata:
      labels:
        app: nginx-app
    spec:
      containers:
      - name: nginx-app
        image: nginx:1.16.1-alpine
        ports:
        - containerPort: 80
```

Appliquer le déploiement :

```bash
kubectl apply -f deployment.yml
kubectl get deploy
kubectl get pods
```

#### `service.yml`

Contenu du fichier `service.yml` :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

Appliquer le service :

```bash
kubectl apply -f service.yml
kubectl get svc
```

### Étape 5 : Création d'un utilisateur administrateur pour le dashboard

#### `dashboard-adminuser.yml`

Contenu du fichier `dashboard-adminuser.yml` :

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kube-system
```

Appliquer le fichier :

```bash
kubectl apply -f dashboard-adminuser.yml
```

### Étape 6 : Déploiement et accès au dashboard Kubernetes

1. Obtenez le token d'accès :

```bash
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
```

2. Lancez le proxy Kubernetes :

```bash
kubectl proxy
```

3. Accédez au dashboard à partir de votre navigateur en utilisant l'URL suivante :

```
http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
```

### Étape 7 : Déploiement de Prometheus

#### `prometheus.deployment.yml`

Contenu du fichier `prometheus.deployment.yml` :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: prometheus
spec:
  replicas: 1
  selector:
    matchLabels:
      app: prometheus
  template:
    metadata:
      labels:
        app: prometheus
    spec:
      containers:
      - name: prometheus
        image: prom/prometheus
        ports:
        - containerPort: 9090
```

Appliquer le fichier :

```bash
kubectl apply -f prometheus.deployment.yml
kubectl get deploy
kubectl get pods
```

### Étape 8 : Configuration de Grafana

#### `grafana.deployment.yml`

Créez un fichier `grafana.deployment.yml` avec le contenu suivant :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: grafana
spec:
  replicas: 1
  selector:
    matchLabels:
      app: grafana
  template:
    metadata:
      labels:
        app: grafana
    spec:
      containers:
      - name: grafana
        image: grafana/grafana
        ports:
        - containerPort: 3000
```

Appliquer le fichier :

```bash
kubectl apply -f grafana.deployment.yml
kubectl get deploy
kubectl get pods
```

### Étape 9 : Exposition des services Prometheus et Grafana

Créez un fichier `prometheus-service.yml` pour exposer Prometheus :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: prometheus
spec:
  type: NodePort
  selector:
    app: prometheus
  ports:
    - protocol: TCP
      port: 9090
      nodePort: 30000
```

Appliquer le fichier :

```bash
kubectl apply -f prometheus-service.yml
kubectl get svc
```

Créez un fichier `grafana-service.yml` pour exposer Grafana :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: grafana
spec:
  type: NodePort
  selector:
    app: grafana
  ports:
    - protocol: TCP
      port: 3000
      nodePort: 30001
```

Appliquer le fichier :

```bash
kubectl apply -f grafana-service.yml
kubectl get svc
```

### Étape 10 : Accès à Grafana

Accédez à Grafana via votre navigateur en utilisant l'URL suivante :

```
http://localhost:30001
```

Les identifiants par défaut sont :
- Username: admin
- Password: admin

### Étape 11 : Configuration de Prometheus comme source de données dans Grafana

1. Connectez-vous à Grafana.
2. Allez dans **Configuration** -> **Data Sources**.
3. Cliquez sur **Add data source**.
4. Sélectionnez **Prometheus**.
5. Configurez l'URL comme suit :

```
http://prometheus:9090
```

6. Cliquez sur **Save & Test**.

### Conclusion

En suivant ces étapes, vous avez créé un cluster Kubernetes, déployé des applications, configuré le dashboard Kubernetes, déployé Prometheus et Grafana, et configuré Grafana pour utiliser Prometheus comme source de données. Vous pouvez maintenant utiliser Grafana pour visualiser les métriques de votre cluster Kubernetes.

# Annexe 01 - Les contextes Kubernetes

- Pour supprimer un contexte Kubernetes et créer un nouveau cluster avec un contexte spécifique, vous pouvez suivre ces étapes :

### 1. Supprimer un contexte Kubernetes

Pour supprimer un contexte spécifique dans Kubernetes, vous pouvez utiliser la commande `kubectl config delete-context` suivie du nom du contexte. Par exemple :

```sh
kubectl config delete-context <context-name>
```

### 2. Créer un nouveau cluster Kubernetes avec `kind`

`kind` (Kubernetes IN Docker) est un outil pour créer des clusters Kubernetes locaux en utilisant Docker. Pour créer un nouveau cluster avec `kind`, suivez ces étapes :

1. **Installer `kind`** :
   Si vous n'avez pas encore installé `kind`, vous pouvez le faire en suivant les instructions sur la [page GitHub de kind](https://kind.sigs.k8s.io/docs/user/quick-start/#installation).

2. **Créer un nouveau cluster** :
   Utilisez la commande `kind create cluster` pour créer un nouveau cluster. Vous pouvez spécifier le nom du cluster avec le paramètre `--name`. Par exemple :

   ```sh
   kind create cluster --name my-cluster
   ```

3. **Vérifier les clusters disponibles** :
   Vous pouvez vérifier les clusters disponibles et leurs contextes en utilisant :

   ```sh
   kubectl config get-contexts
   ```

### 3. Gérer plusieurs clusters avec des contextes

Vous pouvez gérer plusieurs clusters en utilisant des contextes différents. Pour ajouter un nouveau contexte ou basculer entre les contextes, vous pouvez utiliser les commandes suivantes :

- **Ajouter un nouveau contexte** :
  Vous pouvez ajouter un nouveau contexte en utilisant la commande `kubectl config set-context`. Par exemple :

  ```sh
  kubectl config set-context my-new-context --cluster=my-cluster --user=my-user
  ```

- **Basculer entre les contextes** :
  Pour basculer entre les contextes, utilisez la commande `kubectl config use-context`. Par exemple :

  ```sh
  kubectl config use-context my-new-context
  ```

### Exemple complet

Voici un exemple complet de suppression d'un contexte, création d'un nouveau cluster avec `kind`, et gestion de plusieurs contextes :

```sh
# Supprimer un contexte existant
kubectl config delete-context old

-context

# Créer un nouveau cluster avec kind
kind create cluster --name my-new-cluster

# Vérifier les contextes disponibles
kubectl config get-contexts

# Ajouter un nouveau contexte pour le cluster créé
kubectl config set-context my-new-context --cluster=kind-my-new-cluster --user=kind-my-new-cluster

# Basculer vers le nouveau contexte
kubectl config use-context my-new-context

# Vérifier le contexte actuel
kubectl config current-context
```

Avec ces commandes, vous pouvez gérer efficacement plusieurs clusters Kubernetes en utilisant des contextes différents.

### Commandes supplémentaires à ne pas oublier


```sh
cd ..
git clone https://github.com/alihussainia/Monitoring-Kubernetes-Cluster.git
cd Monitoring-Kubernetes-Cluster
kind create cluster
kind get clusters
kubectl cluster-info
kubectl get nodes
kubectl get all -A

cd Deployment
cat deployment.yml
kubectl apply -f deployment.yml
kubectl get deploy
kubectl get pods
kubectl apply -f service.yml
kubectl get svc

wget https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

vim components.yaml

# Modifier le fichier dans l'éditeur vim (ou de votre choix) et ajouter ceci sous deployment --> containers --> args :

--kubelet-insecure-tls

kubectl create namespace monitoring
cd ..
kubectl create -f ./ -R

kubectl get nodes --selector=kubernetes.io/role!=master -o jsonpath={.items[*].status.addresses[?\(@.type==\"InternalIP\"\)].address}

# Accéder aux métriques :
172.18.0.2:30000/graph
kube_node_created

kubectl top nodes
kubectl top pods

# Dashboard Grafana
Visitez http://[NODEIP]:32000 pour voir le tableau de bord Grafana.
Connectez-vous avec admin et admin pour le nom d'utilisateur et le mot de passe. Vous pouvez réinitialiser le mot de passe à l'écran suivant ou cliquer sur Skip.
Sur la page Welcome to Grafana, cliquez sur l'icône + dans la barre latérale en haut à gauche de l'écran.
Cliquez sur l'élément déroulant nommé imports parmi les options disponibles.
Entrez une valeur ID de 10000 dans la boîte de dialogue Grafana.com Dashboard et cliquez sur Load. Cela chargera le tableau de bord Cluster Monitoring for Kubernetes.
Note : Vous pouvez trouver une liste de tableaux de bord préconfigurés sur https://grafana.com/grafana/dashboards. Chacun aura un ID que vous pouvez copier dans l'écran Import dashboard de Grafana.

Sur l'écran suivant, vous verrez une liste déroulante Select a Prometheus data source. Sélectionnez prometheus dans la liste et cliquez sur Import. Votre tableau de bord sera maintenant chargé.
Cliquez sur le menu déroulant Cluster Monitoring for Kubernetes en haut à gauche de l'écran. Reprenez les étapes pour importer un tableau de bord, mais cette fois, entrez un ID de 8588. Cela chargera le tableau de bord Kubernetes Deployment Statefulset Daemonset metrics.
Si vous cliquez sur le menu déroulant du nom du tableau de bord en haut à gauche de l'écran, vous verrez vos deux tableaux de bord listés et vous pourrez maintenant passer de l'un à l'autre.
Importez le tableau de bord avec un ID de 10694. Ce tableau de bord s'appelle Container Statistics.
À partir de là, vous pouvez charger d'autres tableaux de bord ou créer les vôtres !
```

---

```sh
cd ..
git clone https://github.com/alihussainia/Monitoring-Kubernetes-Cluster.git
cd Monitoring-Kubernetes-Cluster
kind create cluster
kind get clusters
kubectl cluster-info
kubectl get nodes
kubectl get all -A

cd Deployment
cat deployment.yml
kubectl apply -f deployment.yml
kubectl get deploy
kubectl get pods
kubectl apply -f service.yml
kubectl get svc

wget https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

vim components.yaml

# Modifier le fichier dans l'éditeur vim (ou de votre choix) et ajouter ceci sous deployment --> containers --> args:

--kubelet-insecure-tls

kubectl create namespace monitoring
cd ..
kubectl create -f ./ -R

kubectl get nodes --selector=kubernetes.io/role!=master -o jsonpath={.items[*].status.addresses[?\(@.type==\"InternalIP\"\)].address}

# Accéder aux métriques :
172.18.0.2:30000/graph
kube_node_created

kubectl top nodes
kubectl top pods

# Grafana Dashboard
Visit http://[NODEIP]:32000 to view the Grafana dashboard.
Login with admin and admin for the username and password. You can reset the password on the next screen or click Skip.
On the Welcome to Grafana page click on the + sidebar icon in the upper-left corner of the screen.
Click the drop-down item named imports from the available options.
Enter an ID value of 10000 into the Grafana.com Dashboard textbox and click Load. This will load the Cluster Monitoring for Kubernetes dashboard.
Note: You can find a list of pre-configured dashboards at https://grafana.com/grafana/dashboards. Each one will have an id you can copy into the Grafana Import dashboard screen.

On the next screen you'll see a Select a Prometheus data source drop-down. Select prometheus from the list and click Import. Your dashboard will now load.
Click on the Cluster Monitoring for Kubernetes drop-down in the upper-left corner of the screen. Go back through the steps to import a dashboard but this time enter an ID of 8588. This will load the Kubernetes Deployment Statefulset Daemonset metrics dashboard.
If you click on the dashboard name drop-down in the upper-left corner of the screen you'll see both of your dashboards listed and can now switch between them.
Import the dashboard with an ID of 10694. This dashboard is called Container Statistics.
From here you can load other dashboards or create your own!
```


🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
# Partie 2 - Tester avec MINIKUBE
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇



🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
# Partie 3 - Tester avec AZURE + KIND
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇



