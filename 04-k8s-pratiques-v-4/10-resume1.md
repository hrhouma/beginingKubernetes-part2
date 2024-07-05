# Cours sur les Affinités et les Tolérances dans Kubernetes

## Introduction

Dans Kubernetes, les affinités de pods, les anti-affinités et les tolérances sont des mécanismes avancés pour contrôler la planification des pods. Ces fonctionnalités permettent de spécifier où les pods doivent être placés dans un cluster, en fonction de diverses contraintes et préférences.

## 1. Nodeselector

Le `Nodeselector` est une méthode simple pour contraindre un pod à être planifié uniquement sur un ensemble de nœuds ayant des labels spécifiques. Il utilise des labels de nœud pour filtrer les nœuds admissibles.

### Exemple de configuration Nodeselector :

Supposons que vous ayez des nœuds avec le label `disktype=ssd`. Vous pouvez utiliser `nodeselector` pour planifier un pod sur ces nœuds comme suit :

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
   - Cette stratégie exige que les règles soient respectées pour qu'un pod soit planifié sur un nœud pendant la planification, mais ignore ces règles une fois le pod en cours d'exécution.
   - **Analogie** : Organiser un mariage en choisissant une salle de réception spécifique parce qu'elle répond à tous vos critères essentiels au moment de la réservation. Une fois le mariage commencé, même si la salle perd certaines de ces qualités, l'événement continue sans essayer de changer de lieu.

2. **preferredDuringSchedulingIgnoredDuringExecution** :
   - Cette stratégie indique que le scheduler de Kubernetes essaiera de respecter les règles d'affinité/anti-affinité, mais ce n'est pas strict. Les pods peuvent être planifiés même si les préférences ne sont pas entièrement satisfaites.
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

Les `Taints` et `Tolerations` sont utilisés pour empêcher ou autoriser des pods à être planifiés sur des nœuds spécifiques. Les taints empêchent les pods d'être planifiés sur des nœuds qui ont ces taints, sauf si les pods ont des tolérances correspondantes.

### Exemple de configuration Taints et Tolerations :

Ajoutons une taint à un nœud :

```shell
kubectl taint nodes nodename key=value:NoSchedule
```

Ajoutons une tolérance au pod pour qu'il puisse être planifié sur un nœud avec cette taint :

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

Les affinités, les anti-affinités et les tolérances sont des outils puissants dans Kubernetes qui permettent un contrôle fin de la planification des pods. Comprendre et utiliser ces règles de manière efficace peut aider à assurer que vos pods sont planifiés et exécutés de manière optimale en fonction des besoins et des contraintes de votre application.

## Exercices Pratiques

### Exercice 1 : Maîtrise des Taints et Tolérances dans Kubernetes

**Objectif:** Comprendre et utiliser les taints et tolérances pour contrôler le placement des pods sur les nœuds du cluster.

1. **Démarrer Minikube:**
   ```bash
   minikube start
   ```

2. **Appliquer un Taint à un Nœud:**
   ```bash
   kubectl taint nodes minikube key1=value1:NoSchedule
   ```

3. **Créer un Pod avec Tolérance:**
   - Manifeste `pod-with-toleration.yaml`:
     ```yaml
     apiVersion: v1
     kind: Pod
     metadata:
       name: mypod
     spec:
       containers:
       - name: mycontainer
         image: nginx
       tolerations:
       - key: "key1"
         operator: "Equal"
         value: "value1"
         effect: "NoSchedule"
     ```
   - Appliquer le manifeste:
     ```bash
     kubectl apply -f pod-with-toleration.yaml
     ```

4. **Nettoyage:**
   ```bash
   kubectl taint nodes minikube key1=value1:NoSchedule-
   kubectl delete pod mypod
   ```

### Exercice 2 : Comprendre et Appliquer `requiredDuringSchedulingIgnoredDuringExecution`

**Objectif:** Créer un pod nécessitant un nœud spécifique basé sur une étiquette lors de sa planification.

1. **Ajouter une Étiquette à un Nœud:**
   ```bash
   kubectl label nodes minikube zone=primaire
   ```

2. **Créer un Pod avec Affinité de Nœud:**
   - Manifeste `pod-affinity.yaml`:
     ```yaml
     apiVersion: v1
     kind: Pod
     metadata:
       name: pod-affinite
     spec:
       containers:
       - name: nginx
         image: nginx
       affinity:
         nodeAffinity:
           requiredDuringSchedulingIgnoredDuringExecution:
             nodeSelectorTerms:
             - matchExpressions:
               - key: zone
                 operator: In
                 values:
                 - primaire
     ```
   - Appliquer le manifeste:
     ```bash
     kubectl apply -f pod-affinity.yaml
     ```

3. **Nettoyage:**
   ```bash
   kubectl delete -f pod-affinity.yaml
   kubectl label nodes minikube zone-
   ```

### Exercice 3 : Mise en Pratique de `preferredDuringSchedulingIgnoredDuringExecution`

**Objectif:** Créer un pod préférant un nœud spécifique basé sur une étiquette lors de sa planification.

1. **Ajouter des Étiquettes aux Nœuds:**
   ```bash
   kubectl label nodes minikube zone=primaire
   ```

2. **Créer un Pod avec Préférence d'Affinité:**
   - Manifeste `pod-preferred-affinity.yaml`:
     ```yaml
     apiVersion: v1
     kind: Pod
     metadata:
       name: pod-preference
     spec:
       containers:
       - name: nginx
         image: nginx
       affinity:
         nodeAffinity:
           preferredDuringSchedulingIgnoredDuringExecution:
           - weight: 1
             preference:
               matchExpressions:
               - key: zone
                 operator: In
                 values:
                 - primaire
     ```
   - Appliquer le manifeste:
     ```bash
     kubectl apply -f pod-preferred-affinity.yaml
     ```

3. **Nettoyage:**
   ```bash
   kubectl delete pod pod-preference
   kubectl label nodes minikube zone-
   ```

### Exercice 4 : Exploration de `requiredDuringSchedulingRequiredDuringExecution`

**Note:** Cette fonctionnalité est théorique et peut ne pas être implémentée dans toutes les versions de Kubernetes.

**Objectif:** Comprendre le concept de maintenir les exigences d'affinité pendant

 toute la durée de vie d'un pod.

1. **Conception d'un Manifeste de Pod Hypothétique:**
   - Manifeste `pod-gpu-exigeant.yaml`:
     ```yaml
     apiVersion: v1
     kind: Pod
     metadata:
       name: pod-gpu-exigeant
     spec:
       containers:
       - name: application-gpu
         image: application-gpu:latest
       affinity:
         nodeAffinity:
           requiredDuringSchedulingRequiredDuringExecution:
             nodeSelectorTerms:
             - matchExpressions:
               - key: gpu
                 operator: In
                 values:
                 - typeA
     ```

### Exercice 5 : Pratique de Node Selector et Node Affinity

**Objectif:** Comprendre et appliquer Node Selector et Node Affinity pour le placement des pods.

#### Partie 1: Utilisation de Node Selector

1. **Étiqueter un Nœud:**
   ```bash
   kubectl label nodes minikube color=red
   ```

2. **Créer un Pod avec Node Selector:**
   - Manifeste `pod-nodeselector.yaml`:
     ```yaml
     apiVersion: v1
     kind: Pod
     metadata:
       name: nginx
     spec:
       nodeSelector:
         color: red
       containers:
       - name: nginx
         image: nginx
     ```
   - Appliquer le manifeste:
     ```bash
     kubectl apply -f pod-nodeselector.yaml
     ```

#### Partie 2: Utilisation de Node Affinity

1. **Étiqueter un Nœud:**
   ```bash
   kubectl label nodes minikube env=aws hdd=ssd
   ```

2. **Créer un Pod avec Node Affinity:**
   - Manifeste `pod-nodeaffinity.yaml`:
     ```yaml
     apiVersion: v1
     kind: Pod
     metadata:
       name: nginx-affinity
     spec:
       affinity:
         nodeAffinity:
           requiredDuringSchedulingIgnoredDuringExecution:
             nodeSelectorTerms:
             - matchExpressions:
               - key: env
                 operator: In
                 values:
                   - aws
               - key: hdd
                 operator: In
                 values:
                   - ssd
       containers:
       - name: nginx
         image: nginx
     ```
   - Appliquer le manifeste:
     ```bash
     kubectl apply -f pod-nodeaffinity.yaml
     ```

3. **Nettoyage:**
   ```bash
   kubectl delete pod nginx
   kubectl delete pod nginx-affinity
   kubectl label nodes minikube color- env- hdd-
   ```

### Exercice 6 : Compréhension des Concepts de Taints et Tolérances

#### Exercice 1 : Compréhension des Concepts

1. **Qu'est-ce qu'un Taint ?**
2. **Qu'est-ce qu'une Tolérance ?**

#### Exercice 2 : Application Pratique

1. **Identification des Taints sur un Nœud:**
   ```bash
   kubectl describe node minikube | grep Taint
   ```

2. **Création d'un Pod avec Tolérance:**
   - Manifeste `pod-tolerations.yaml`:
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
       - key: "key1"
         operator: "Equal"
         value: "value1"
         effect: "NoSchedule"
     ```

#### Exercice 3 : Analyse et Correction

1. **Analyse de Configuration:**
   ```bash
   kubectl describe pod <pod-name>
   kubectl describe node minikube | grep Taint
   ```

2. **Correction d'une Tolérance:**
   ```yaml
   tolerations:
     - key: "lock"
       operator: "Equal"
       value: "blue"
       effect: "NoSchedule"
   ```

## Conclusion

Ces exercices visent à renforcer la compréhension et l'utilisation des taints et tolérances dans Kubernetes, des concepts clés pour la planification et la gestion des ressources dans un cluster. Ils encouragent également la réflexion sur la manière dont ces mécanismes peuvent être utilisés pour répondre à des exigences opérationnelles et de sécurité spécifiques.
