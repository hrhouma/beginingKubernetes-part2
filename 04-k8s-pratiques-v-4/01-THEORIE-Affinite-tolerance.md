# Cours sur les Affinités et les Tolérances dans Kubernetes

## Introduction

Dans Kubernetes, les affinités de pods, les anti-affinités et les tolérances sont des mécanismes avancés pour contrôler la planification des pods. Ces fonctionnalités permettent de spécifier où les pods doivent être placés dans un cluster, en fonction de diverses contraintes et préférences. 

## 1. Nodeselector

Le `Nodeselector` est une méthode simple pour contraindre un pod à être programmé uniquement sur un ensemble de nœuds ayant des labels spécifiques. Il utilise des labels de nœud pour filtrer les nœuds admissibles.

### Exemple de configuration Nodeselector :

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

### 2.1 Types d'affinités de nœuds :

1. **requiredDuringSchedulingIgnoredDuringExecution** :
   - Cette stratégie exige que les règles soient respectées pour qu'un pod soit programmé sur un nœud pendant la planification, mais ignore ces règles une fois le pod en cours d'exécution.
   - **Analogie** : Organiser un mariage en choisissant une salle de réception spécifique parce qu'elle répond à tous vos critères essentiels au moment de la réservation. Une fois le mariage commencé, même si la salle perd certaines de ces qualités, l'événement continue sans essayer de changer de lieu.

2. **preferredDuringSchedulingIgnoredDuringExecution** :
   - Cette stratégie indique que le scheduler de Kubernetes essaiera de respecter les règles d'affinité/anti-affinité, mais ce n'est pas strict. Les pods peuvent être programmés même si les préférences ne sont pas entièrement satisfaites.
   - **Analogie** : Choisir un vol pour un voyage. Vous préférez un vol direct et le sélectionnez si possible, mais si ce n'est pas possible, vous êtes prêt à accepter un vol avec escale. Une fois en l'air, même si un vol direct devient disponible, vous restez sur votre vol initial jusqu'à la destination sans changement en cours de route.

3. **requiredDuringSchedulingRequiredDuringExecution** (en développement) :
   - Cette stratégie exigerait que les conditions d'affinité soient respectées à la fois lors de la planification et pendant l'exécution du pod. Si les conditions d'affinité ne sont plus respectées pendant l'exécution, le système prendrait des mesures pour rétablir la conformité.
   - **Analogie** : Vivre dans une ville qui exige que vous habitiez à une certaine distance de votre lieu de travail. Au moment de choisir votre logement, vous devez respecter cette règle. Si votre lieu de travail change ensuite de location, vous êtes également obligé de déménager pour rester conforme à cette exigence de distance.

### Exemple de configuration d'affinité :

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
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: app
            operator: In
            values:
            - frontend
        topologyKey: "kubernetes.io/hostname"
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: app
            operator: In
            values:
            - frontend
        topologyKey: "kubernetes.io/hostname"
```

## 3. Tolérances et Taints

Les `Taints` et `Tolerations` sont utilisés pour empêcher ou autoriser des pods à être programmés sur des nœuds spécifiques. Les taints empêchent les pods d'être programmés sur des nœuds qui ont ces taints, sauf si les pods ont des tolérances correspondantes.

### Exemple de configuration Taints et Tolerations :

Ajoutons une taint à un nœud :

```shell
kubectl taint nodes nodename key=value:NoSchedule
```

Ajoutons une tolérance au pod pour qu'il puisse être programmé sur un nœud avec cette taint :

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

Les affinités, les anti-affinités et les tolérances sont des outils puissants dans Kubernetes qui permettent un contrôle fin de la planification des pods. Comprendre et utiliser ces règles de manière efficace peut aider à assurer que vos pods sont programmés et exécutés de manière optimale en fonction des besoins et des contraintes de votre application.

# Annexe 01 - Résumé sur les Affinités et les Tolérances dans Kubernetes

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

# Annexe 1 

- **Introduction**
  - Affinités de pods, anti-affinités, tolérances : contrôle avancé de la planification des pods

- **Nodeselector**
  - Utilisation de labels de nœud pour contraindre la planification des pods

- **Affinités et Anti-Affinités** (requiredDuringSchedulingIgnoredDuringExecution, preferredDuringSchedulingIgnoredDuringExecution, requiredDuringSchedulingRequiredDuringExecution)

- **Tolérances et Taints**
  - Taints : empêchent la planification sur certains nœuds (NoSchedule, PreferNoSchedule, NoExecute)
  - Tolérances : permettent la planification sur des nœuds avec taints

- **Conclusion**
  - Utilisation des affinités, anti-affinités et tolérances pour un contrôle fin et optimal de la planification des pods
 
# Annexe 2

- Relations entre Nodeselector, Affinités et Anti-Affinités, et Tolérances et Taints, incluant les nœuds et les pods, ainsi que les différentes valeurs possibles :

| Concept                    | Relé à                  | Nœuds  | Pods   | Différentes valeurs possibles                                 | Description                                                                 |
|----------------------------|-------------------------|--------|-------|---------------------------------------------------------------|-----------------------------------------------------------------------------|
| **Nodeselector**           | Labels de nœuds         | ✔      |       | Labels personnalisés (ex : disktype=ssd)                      | Contraint les pods à être programmés uniquement sur des nœuds avec des labels spécifiques. |
| **Affinités**              | Labels de nœuds/pods    | ✔      | ✔     | requiredDuringSchedulingIgnoredDuringExecution, preferredDuringSchedulingIgnoredDuringExecution, requiredDuringSchedulingRequiredDuringExecution (en développement) | Contraintes flexibles et expressives pour la planification basées sur des labels. |
| **Anti-Affinités**         | Labels de nœuds/pods    | ✔      | ✔     | requiredDuringSchedulingIgnoredDuringExecution, preferredDuringSchedulingIgnoredDuringExecution, requiredDuringSchedulingRequiredDuringExecution (en développement) | Contraintes flexibles et expressives pour éviter la planification basées sur des labels. |
| **Tolérances**             | Taints des nœuds        |        | ✔     | key, operator (Equal), value, effect (NoSchedule, PreferNoSchedule, NoExecute) | Permettent aux pods d'être programmés sur des nœuds avec des taints spécifiques. |
| **Taints**                 | Tolérances des pods     | ✔      |       | key, value, effect (NoSchedule, PreferNoSchedule, NoExecute)  | Empêchent les pods sans tolérances correspondantes d'être programmés sur des nœuds. |

### Détails des concepts :

- **Nodeselector** :
  - Utilise des labels de nœuds pour contraindre la planification des pods.
- **Affinités et Anti-Affinités** :
  - Basées sur des labels.
  - **Types d'affinités** : requiredDuringSchedulingIgnoredDuringExecution, preferredDuringSchedulingIgnoredDuringExecution, requiredDuringSchedulingRequiredDuringExecution.
- **Tolérances et Taints** :
  - **Taints** : Appliqués aux nœuds pour repousser certains pods.
  - **Tolérances** : Ajoutées aux pods pour leur permettre d'être programmés sur des nœuds avec des taints correspondants.

### Exemple des valeurs possibles pour les Taints et Tolérances :
- **key** : nom de la clé (ex: `key1`)
- **value** : valeur associée à la clé (ex: `value1`)
- **effect** :
  - **NoSchedule** : Empêche de programmer les pods sur le nœud.
  - **PreferNoSchedule** : Préfère éviter de programmer les pods sur le nœud, mais ce n'est pas strict.
  - **NoExecute** : Empêche de programmer de nouveaux pods et expulse les pods existants qui ne tolèrent pas cette taint.
