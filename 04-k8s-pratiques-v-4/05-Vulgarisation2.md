# Pratique - Exemple de l'Utilisation des Affinités et Tolérances dans un Processus de Recrutement

## Exemple d'une compagnie recevant beaucoup de candidatures

### Contexte :
Une compagnie reçoit environ 5000 candidatures par jour pour divers postes. Pour gérer efficacement ce flux de candidatures et filtrer les candidats non qualifiés, l'entreprise décide d'introduire une exigence stricte : chaque candidat doit avoir au moins 5 ans d'expérience pertinente.

### Mise en place des Affinités et Tolérances :

1. **Affinité sur les candidatures (nœuds)** :
   - **Affinité** :
     ```yaml
     affinity:
       nodeAffinity:
         requiredDuringSchedulingIgnoredDuringExecution:
           nodeSelectorTerms:
           - matchExpressions:
             - key: "experience"
               operator: In
               values:
               - "5ans"
     ```
   - **Description** : Cette affinité s'assure que seules les candidatures avec au moins 5 ans d'expérience sont planifiées sur des nœuds (postes).

2. **Tolérance ajoutée aux candidats (pods)** :
   - **Tolérance** :
     ```yaml
     tolerations:
     - key: "experience"
       operator: "Equal"
       value: "5ans"
       effect: "NoSchedule"
     ```
   - **Description** : Les candidats avec cette tolérance peuvent être programmés sur les nœuds (postes) malgré l'affinité stricte, signifiant qu'ils déclarent avoir 5 ans d'expérience.

### Problème rencontré :
Certaines personnes mentent sur leur expérience, affirmant avoir 5 ans d'expérience alors qu'elles ne les ont pas. Ces candidats sont initialement acceptés et commencent à travailler.

### Gestion stricte des exigences pendant le travail (NoExecute) :

1. **Vérification de l'expérience** :
   - Pendant leur période de travail, une vérification est effectuée pour valider l'expérience réelle des employés.
   
2. **Découverte de mensonges** :
   - Malgré la découverte que certains employés ont menti sur leur expérience, ils sont autorisés à continuer à travailler pendant cette phase.

3. **Affinité stricte pour expulsion** :
   - **Affinité** :
     ```yaml
     affinity:
       nodeAffinity:
         requiredDuringSchedulingIgnoredDuringExecution:
           nodeSelectorTerms:
           - matchExpressions:
             - key: "experience"
               operator: In
               values:
               - "5ans"
         requiredDuringExecution:
           nodeSelectorTerms:
           - matchExpressions:
             - key: "experience"
               operator: NotIn
               values:
               - "5ans"
     ```
   - **Description** : Les employés qui ne respectent pas cette affinité stricte sont expulsés du poste si leur expérience réelle ne correspond pas aux 5 ans exigés.

### Exemple réel :
- **Ajout de l'affinité initiale (NoSchedule)** :
  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: candidat
  spec:
    containers:
    - name: candidat-container
      image: candidat-image
    affinity:
      nodeAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
          nodeSelectorTerms:
          - matchExpressions:
            - key: "experience"
              operator: In
              values:
              - "5ans"
  ```

- **Ajout de la tolérance aux candidats** :
  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: candidat
  spec:
    containers:
    - name: candidat-container
      image: candidat-image
    tolerations:
    - key: "experience"
      operator: "Equal"
      value: "5ans"
      effect: "NoSchedule"
  ```

- **Ajout de l'affinité stricte (NoExecute)** :
  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: candidat
  spec:
    containers:
    - name: candidat-container
      image: candidat-image
    affinity:
      nodeAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
          nodeSelectorTerms:
          - matchExpressions:
            - key: "experience"
              operator: In
              values:
              - "5ans"
        requiredDuringExecution:
          nodeSelectorTerms:
          - matchExpressions:
            - key: "experience"
              operator: NotIn
              values:
              - "5ans"
  ```

### Conclusion :
Ce système d'affinités et tolérances permet à l'entreprise de gérer efficacement le flux élevé de candidatures en filtrant initialement les candidats non qualifiés. Il permet également de vérifier et de maintenir cette exigence pendant la période de travail, expulsant ceux qui ne respectent pas les critères stricts d'expérience, même si des mensonges sont découverts pendant l'exécution.

### Objectif :
Assurer que seuls les candidats réellement qualifiés sont acceptés et maintenus dans l'entreprise, optimisant ainsi les ressources et la qualité de la main-d'œuvre.
