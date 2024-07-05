# Cours sur les Affinités et les Tolérances dans Kubernetes

## Introduction

Dans Kubernetes, les affinités de pods, les anti-affinités et les tolérances sont des mécanismes avancés pour contrôler la planification des pods. Ces fonctionnalités permettent de spécifier où les pods doivent être placés dans un cluster, en fonction de diverses contraintes et préférences.

## 1. Nodeselector

Le `Nodeselector` est une méthode simple pour contraindre un pod à être programmé uniquement sur un ensemble de nœuds ayant des labels spécifiques. Il utilise des labels de nœud pour filtrer les nœuds admissibles.

### Exemple de configuration Nodeselector

Supposons que vous ayez des nœuds avec le label `disktype=ssd`. Vous pouvez utiliser `nodeselector` pour programmer un pod sur ces nœuds comme suit :

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
  nodeSelector:
    disktype: ssd
```

## 2. Affinités et Anti-Affinités

Les affinités et anti-affinités offrent une manière plus flexible et expressive de spécifier les contraintes de planification basées sur des labels. Il existe trois types principaux d'affinités :

### 2.1 Types d'affinités de nœuds

| Stratégie                                   | Description                                                                                         | Exemple d'Utilisation                                                                                                         | Analogie                                                                                            |
|---------------------------------------------|-----------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| requiredDuringSchedulingIgnoredDuringExecution  | Les contraintes doivent être respectées pour programmer le pod mais ne sont pas réévaluées après son démarrage.             | Utilisé pour s'assurer que les pods sont initialement placés sur des nœuds spécifiques, sans nécessiter une conformité continue. | Organiser un mariage dans une salle spécifique. Si la salle perd certaines qualités, l'événement continue sans changement. |
| preferredDuringSchedulingIgnoredDuringExecution | Le scheduler tente de respecter les contraintes comme des préférences mais leur non-respect n'empêche pas le placement du pod. | Utilisé pour guider le scheduler à co-localiser des pods pour des raisons de performance, sans rendre ces préférences strictes. | Choisir un vol direct si possible, mais accepter un vol avec escale si nécessaire.                     |
| requiredDuringSchedulingRequiredDuringExecution | Les contraintes doivent être respectées à la fois lors de la planification et pendant l'exécution du pod.              | Utilisé pour des exigences critiques qui doivent être respectées tout au long de la vie du pod.                               | Habiter à une certaine distance de son lieu de travail et déménager si le lieu de travail change.      |

### Exemple de Configuration YAML pour les Affinités et Anti-Affinités

#### requiredDuringSchedulingIgnoredDuringExecution

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd
```

#### preferredDuringSchedulingIgnoredDuringExecution

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
  affinity:
    podAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        podAffinityTerm:
          labelSelector:
            matchExpressions:
            - key: app
              operator: In
              values:
              - frontend
          topologyKey: "kubernetes.io/hostname"
```

#### requiredDuringSchedulingRequiredDuringExecution

(Note : Cette fonctionnalité peut être en développement et ne pas être disponible dans toutes les versions de Kubernetes)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingRequiredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd
```

## 3. Tolérances et Taints

Les `Taints` et `Tolerations` sont utilisés pour empêcher ou autoriser des pods à être programmés sur des nœuds spécifiques. Les taints empêchent les pods d'être programmés sur des nœuds qui ont ces taints, sauf si les pods ont des tolérances correspondantes.

### Tableau Comparatif des Tolérances et Taints

| Aspect                        | Description                                                                                                     | Exemple                                                                                 |
|-------------------------------|-----------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| Taints                        | Ajoutées aux nœuds pour empêcher des pods d'y être programmés, sauf s'ils tolèrent ces taints.                  | ```shell kubectl taint nodes nodename key=value:NoSchedule ```                          |
| Tolérances                    | Ajoutées aux pods pour leur permettre d'être programmés sur des nœuds avec des taints correspondantes.          | ```yaml tolerations: - key: "key" operator: "Equal" value: "value" effect: "NoSchedule" ``` |
| Effet NoSchedule              | Empêche de programmer les pods sur le nœud avec cette taint.                                                     | Un nœud avec la taint `key=value:NoSchedule` n'acceptera que les pods tolérant cette taint. |
| Effet PreferNoSchedule        | Tente d'éviter de programmer les pods sur le nœud avec cette taint, mais ce n'est pas strict.                    | Un nœud avec la taint `key=value:PreferNoSchedule` préférera les pods tolérant cette taint, mais ce n'est pas obligatoire. |
| Effet NoExecute               | Empêche de programmer de nouveaux pods et expulse les pods existants qui ne tolèrent pas cette taint.            | Un nœud avec la taint `key=value:NoExecute` expulsera les pods qui ne tolèrent pas cette taint. |

### Exemple de Configuration YAML pour Tolérances et Taints

#### Ajouter une taint à un nœud

```shell
kubectl taint nodes nodename key=value:NoSchedule
```

#### Ajouter une tolérance à un pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
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

## Conclusion

L'utilisation des affinités, des anti-affinités et des tolérances dans Kubernetes permet un contrôle granulaire de la planification des pods, assurant une gestion optimale des ressources et des déploiements. Ces mécanismes permettent de s'adapter aux exigences spécifiques des applications tout en maintenant la stabilité et la performance du cluster.
