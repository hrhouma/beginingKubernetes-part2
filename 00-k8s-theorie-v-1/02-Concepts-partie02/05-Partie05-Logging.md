### Maîtrise des Métriques Kubernetes: Déploiement et Surveillance avec Metrics Server

## Exercice:
**Objectif:**
Déployer le Metrics Server dans votre cluster Kubernetes et analyser les métriques de performance des nœuds et des pods.

**Instructions:**

1. **Déploiement du Metrics Server:**
   - Clonez le dépôt contenant les fichiers de déploiement du Metrics Server.
   - Appliquez les fichiers YAML pour déployer le Metrics Server dans votre cluster.
   - Vérifiez que le Metrics Server est actif et opérationnel.

2. **Collecte des Métriques:**
   - Utilisez la commande `kubectl top nodes` pour afficher les métriques des nœuds.
   - Utilisez la commande `kubectl top pods` pour afficher les métriques des pods.

3. **Analyse des Métriques:**
   - Identifiez le nœud qui consomme le plus de CPU.
   - Identifiez le nœud qui consomme le plus de mémoire.
   - Identifiez le pod qui consomme le plus de mémoire dans l'espace de noms par défaut.
   - Identifiez le pod qui consomme le moins de CPU dans l'espace de noms par défaut.

### Exercice Pratique:

En supposant que vous ayez un cluster avec les nœuds suivants: `node01`, `node02`, `node03`, et un espace de noms par défaut contenant les pods: `tiger`, `elephant`, `lion`, `rabbit` :

1. Exécutez la commande pour afficher les métriques de tous les nœuds.
2. Déterminez quel nœud a la plus haute consommation de CPU et de mémoire.
3. Exécutez la commande pour afficher les métriques de tous les pods dans l'espace de noms par défaut.
4. Identifiez quel pod a la plus haute consommation de mémoire et lequel a la plus basse consommation de CPU.
5. Documentez vos résultats et les commandes que vous avez utilisées dans un fichier `metrics-analysis.txt`.

### Critères de Réussite:

- Le Metrics Server est correctement déployé et actif.
- Les métriques des nœuds et des pods sont correctement collectées et affichées.
- L'analyse des métriques est correctement réalisée et documentée.

### Ressources Fournies:

- Accès au cluster Kubernetes.
- Documentation officielle de Kubernetes pour les commandes `kubectl`.
- Accès au dépôt Git du Metrics Server.

### Livraison:

- Un fichier `metrics-analysis.txt` contenant toutes les commandes exécutées et les analyses des métriques.

---

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

   ou 

   ```sh
   kubectl create -f /kubernetes-metrics-server
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
   - Documentez les résultats dans `metrics-analysis.txt`.

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
   - Créez une règle d'alerte pour notifier lorsque l'utilisation de

 la CPU d'un pod dépasse 80%.

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
