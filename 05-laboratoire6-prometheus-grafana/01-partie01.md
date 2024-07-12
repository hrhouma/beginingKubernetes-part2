# Configuration et Déploiement de Prometheus et Node Exporter

Ce guide décrit les étapes pour télécharger, configurer et déployer Prometheus et Node Exporter sur un système Linux.

## Prérequis

- Système d'exploitation Linux
- Accès à Internet
- Privilèges root ou sudo

## Téléchargement et Configuration de Prometheus

### 1. Télécharger Prometheus

```bash
wget https://github.com/prometheus/prometheus/releases/download/v2.13.0/prometheus-2.13.0.linux-amd64.tar.gz
```

### 2. Extraire le contenu de l'archive tar

```bash
tar -xzvf prometheus-2.13.0.linux-amd64.tar.gz
```

### 3. Naviguer dans le répertoire extrait

```bash
cd prometheus-2.13.0.linux-amd64/
ls -lrt
```

### 4. Éditer le fichier de configuration `prometheus.yml`

```bash
vi prometheus.yml
```

### 5. Ajouter la configuration de base pour Prometheus

```yaml
scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']
```

### 6. Sauvegarder et quitter l'éditeur

Dans `vi`, tapez `:wq` pour sauvegarder et quitter.

### 7. Démarrer Prometheus

```bash
./prometheus
```

### 8. Accéder à l'interface web de Prometheus

- URL de Prometheus : `http://localhost:9090`
- URL des cibles (Targets) : `http://localhost:9090/targets`
- URL des métriques : `http://localhost:9090/metrics`
- URL des graphiques : `http://localhost:9090/graph`

## Téléchargement et Configuration de Node Exporter

### 1. Extraire le contenu de l'archive tar pour Node Exporter

```bash
tar -xzvf node_exporter-0.15.2.linux-amd64.tar.gz
```

### 2. Naviguer dans le répertoire extrait

```bash
cd node_exporter-0.15.2.linux-amd64/
```

### 3. Démarrer Node Exporter en arrière-plan

```bash
nohup ./node_exporter &
```

### 4. Vérifier la sortie de Node Exporter

```bash
less nohup.out
```

### 5. Accéder aux métriques de Node Exporter

- URL : `http://localhost:9100/metrics`

## Ajouter la configuration pour Node Exporter dans Prometheus

### 1. Naviguer dans le répertoire Prometheus

```bash
cd prometheus-2.13.0.linux-amd64/
```

### 2. Éditer le fichier de configuration `prometheus.yml`

```bash
vi prometheus.yml
```

### 3. Ajouter la configuration pour Node Exporter

```yaml
scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node'
    static_configs:
      - targets: ['localhost:9100']
```

### 4. Sauvegarder et quitter l'éditeur

Dans `vi`, tapez `:wq` pour sauvegarder et quitter.

### 5. Redémarrer Prometheus pour appliquer les changements

```bash
./prometheus
```

## Utilisation de l'interface web de Prometheus

### Accéder aux métriques et vérifier les cibles

- URL des cibles (Targets) : `http://localhost:9090/targets`
- URL des métriques : `http://localhost:9090/metrics`

### Requêtes de métriques spécifiques dans Prometheus

- Utiliser la barre de recherche sous l'onglet **Graph** et exécuter des requêtes comme `node_cpu`.

---

Ce guide couvre toutes les étapes nécessaires pour télécharger, configurer et démarrer Prometheus et Node Exporter, ainsi que pour accéder aux interfaces web correspondantes et exécuter des requêtes de métriques spécifiques.
```

Avec ce fichier README.md, vous avez toutes les instructions nécessaires pour installer et configurer Prometheus et Node Exporter de A à Z.
