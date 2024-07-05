# README - Exemple de l'Utilisation des Affinités et Anti-Affinités dans un Processus de Recrutement (Example2)

## Exemple d'application pratique (réel) :

Supposons qu'une entreprise reçoive 5000 candidatures par jour. Afin de gérer efficacement ce flux de candidatures et optimiser la répartition des candidats selon leurs compétences et les besoins des départements, l'entreprise décide d'utiliser des affinités et anti-affinités.

### Affinités pour regrouper les candidats avec les départements :

L'entreprise souhaite que les candidats ayant des compétences spécifiques soient regroupés dans des départements où ces compétences sont nécessaires. Cela représente une affinité sur les candidatures (pods) pour les départements (nœuds) avec une règle `requiredDuringSchedulingIgnoredDuringExecution`.

- **Affinité appliquée** : Les candidats ayant des compétences en développement logiciel (`skill=software`) doivent être placés dans le département IT.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: candidat-software
spec:
  containers:
  - name: candidat-container
    image: candidat-image
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: skill
            operator: In
            values:
            - software
```

### Anti-Affinités pour séparer les candidats selon des critères spécifiques :

L'entreprise souhaite éviter que des candidats ayant des compétences conflictuelles soient placés ensemble. Cela représente une anti-affinité pour éloigner les candidats (pods) des départements où leurs compétences ne sont pas appropriées (nœuds).

- **Anti-Affinité appliquée** : Les candidats avec des compétences en marketing (`skill=marketing`) ne doivent pas être placés dans le département IT.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: candidat-marketing
spec:
  containers:
  - name: candidat-container
    image: candidat-image
  affinity:
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: skill
            operator: In
            values:
            - marketing
        topologyKey: "kubernetes.io/hostname"
```

### Problème rencontré :
Certaines candidatures peuvent déclarer des compétences qui ne correspondent pas exactement aux besoins des départements, entraînant des affectations sous-optimales.

### Gestion de l'optimisation pendant le travail :

Pendant la période de travail, les compétences des candidats sont évaluées pour s'assurer qu'elles correspondent aux besoins du département où ils sont placés.

- **Affinité stricte pour maintien de l'assignation** : Les candidats doivent maintenir des compétences qui correspondent aux besoins du département.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: candidat-software
spec:
  containers:
  - name: candidat-container
    image: candidat-image
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: skill
            operator: In
            values:
            - software
```

### Conclusion :

Ce système d'affinités et d'anti-affinités permet à l'entreprise de gérer efficacement le flux élevé de candidatures en optimisant la répartition des candidats selon leurs compétences et les besoins spécifiques des départements. Il permet également de maintenir cette optimisation pendant la période de travail en vérifiant que les compétences des candidats restent alignées avec les exigences du département.

### Objectif :

Assurer que les candidats sont placés dans les départements où leurs compétences sont les plus utiles et éviter les conflits de compétences, optimisant ainsi l'utilisation des ressources humaines et la performance globale de l'entreprise.
