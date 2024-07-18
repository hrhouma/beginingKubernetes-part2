🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
# 01 - Description
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

### 1. **kube-state-metrics-service-account.yaml**
- **Rôle :** Crée un compte de service pour kube-state-metrics, autorisant les processus dans un Pod.
- **Interaction :** Utilisé par le déploiement de kube-state-metrics pour authentifier ses requêtes auprès de l'API Kubernetes.
- **Namespace :** `kube-system`

### 2. **kube-state-metrics-cluster-role.yaml**
- **Rôle :** Définit les permissions d'accès aux ressources du cluster pour kube-state-metrics, comme les pods, services, et noeuds.
- **Interaction :** Référencé dans le ClusterRoleBinding pour attribuer ces permissions au compte de service.
- **Namespace :** Applicable à tout le cluster (ClusterRole).

### 3. **kube-state-metrics-cluster-role-binding.yaml**
- **Rôle :** Attribue le ClusterRole précédemment défini au compte de service kube-state-metrics, permettant l'application des permissions au niveau du cluster.
- **Interaction :** Connecte les permissions du ClusterRole au compte de service spécifié.
- **Namespace :** `kube-system`

### 4. **kube-state-metrics-deployment.yaml**
- **Rôle :** Déploie kube-state-metrics dans le cluster pour collecter et exposer les métriques de l'état des ressources Kubernetes.
- **Interaction :** Utilise le compte de service et les permissions pour fonctionner et exposer les métriques.
- **Namespace :** `kube-system`

### 5. **kube-state-metrics-service.yaml**
- **Rôle :** Crée un service pour exposer l'application kube-state-metrics à l'intérieur du cluster.
- **Interaction :** Permet aux autres services de consulter les métriques collectées par kube-state-metrics.
- **Namespace :** `kube-system`

### 6. **grafana-configmap.yaml**
- **Rôle :** Contient la configuration de Grafana, y compris les tableaux de bord et les sources de données préconfigurés.
- **Interaction :** Chargé par le pod Grafana au démarrage pour configurer l'application.
- **Namespace :** `monitoring`

### 7. **grafana-deployment.yaml**
- **Rôle :** Déploie Grafana pour visualiser les métriques à partir de sources comme Prometheus.
- **Interaction :** Utilise grafana-configmap pour sa configuration et se connecte au service Grafana pour être accessible.
- **Namespace :** `monitoring`

### 8. **grafana-service.yaml**
- **Rôle :** Définit les règles de réseau pour accéder à Grafana depuis le réseau.
- **Interaction :** Fournit un endpoint réseau pour accéder à Grafana.
- **Namespace :** `monitoring`

### 9. **metrics-server-prometheus.deployment.yml**
- **Rôle :** Déploie un serveur de métriques qui utilise Prometheus pour collecter des métriques à travers le cluster.
- **Interaction :** Peut être utilisé pour alimenter des visualisations dans Grafana.
- **Namespace :** `monitoring`

### 10. **Web-UI-dashboard-adminuser.yml**
- **Rôle :** Crée un utilisateur administrateur pour accéder au tableau de bord Kubernetes.
- **Interaction :** Fournit des droits d'accès administratifs au tableau de bord Kubernetes.
- **Namespace :** `kubernetes-dashboard`

### 11. **Web-UI-newDeploy.yml**
- **Rôle :** Déploie une nouvelle application ou service Web dans Kubernetes.
- **Interaction :** Peut interagir avec des services de surveillance comme Prometheus pour la collecte de métriques.
- **Namespace :** Dépend du contenu spécifique du fichier.

### 12. **Deployment-deployment.yml**
- **Rôle :** Définit un déploiement pour une application ou un service spécifique dans Kubernetes.
- **Interaction :** Interagit avec des services Kubernetes pour la gestion de la charge et de la disponibilité de l'application.
- **Namespace :** À spécifier.

### 13. **Deployement-service.yml**
- **Rôle :** Crée un service pour un déploiement spécifique, facilitant l'accès au déploiement via un réseau.
- **Interaction :** Relie le déploiement à un endpoint réseau stable.
- **Namespace :** À spécifier.

### 14. **admin-user-token.yaml**
- **Rôle :** Génère un token d'authentification pour l'utilisateur administrateur, permettant un accès sécurisé.
- **Interaction :** Utilisé pour authentifier l'utilisateur administrateur sur divers services Kubernetes.
- **Namespace :** `kubernetes-dashboard`
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
# 02- Schéma des interactions
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
==> 02 - indications02.md
