# DaemonSet-partie 02
- Un DaemonSet dans Kubernetes est un type de contrôleur qui garantit qu'une copie d'un pod spécifique fonctionne sur chaque nœud d'un cluster Kubernetes. Il est souvent utilisé pour les applications de type "daemon" qui doivent tourner sur chaque nœud, comme les agents de monitoring, les agents de logs, ou d'autres utilitaires de gestion de nœud.

Voici les principales caractéristiques et utilisations d'un DaemonSet :

1. **Déploiement uniforme** : Assure que chaque nœud a une instance d'un pod en cours d'exécution.
2. **Mise à jour automatique** : Lorsqu'un nouveau nœud est ajouté au cluster, le DaemonSet déploie automatiquement une instance du pod sur ce nœud.
3. **Haute disponibilité** : Si un nœud tombe en panne et revient en ligne, le DaemonSet recrée automatiquement les pods nécessaires.

### Exemple de YAML pour un DaemonSet

Voici un exemple simple de configuration YAML pour un DaemonSet qui déploie un pod avec une application de monitoring :

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: monitoring-daemonset
  labels:
    app: monitoring
spec:
  selector:
    matchLabels:
      app: monitoring
  template:
    metadata:
      labels:
        app: monitoring
    spec:
      containers:
      - name: monitoring-container
        image: monitoring-image:latest
        ports:
        - containerPort: 80
```

### Utilisation typique

- **Monitoring** : Déploiement d'outils comme Prometheus Node Exporter pour surveiller les performances de chaque nœud.
- **Logs** : Déploiement de collecteurs de logs comme Fluentd ou Logstash pour centraliser les logs de tous les nœuds.
- **Sécurité** : Déploiement d'agents de sécurité qui inspectent le trafic réseau ou vérifient l'intégrité des fichiers sur chaque nœud.

### Commandes Kubernetes

Pour créer un DaemonSet, vous pouvez utiliser la commande suivante :

```sh
kubectl apply -f daemonset.yaml
```

Pour vérifier le statut des DaemonSets dans votre cluster :

```sh
kubectl get daemonsets
```

Pour obtenir plus de détails sur un DaemonSet spécifique :

```sh
kubectl describe daemonset <nom-du-daemonset>
```

Les DaemonSets sont un outil essentiel dans Kubernetes pour gérer les déploiements qui nécessitent une présence uniforme sur tous les nœuds du cluster.
