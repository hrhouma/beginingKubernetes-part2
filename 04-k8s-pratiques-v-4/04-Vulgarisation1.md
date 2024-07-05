#  Exemple de l'Utilisation des Taints et Tolérances dans un Processus de Recrutement

# 1 -  Introduction - Exemple d'application pratique (réel) :
Supposons qu'une entreprise (*Noeud*) reçoive 5000 candidatures (*Pods*) par jour. Afin de filtrer les candidats et gérer efficacement le recrutement, l'entreprise décide de mettre en place une exigence stricte de 5 ans d'expérience. Cela représente une **taint** sur les candidatures (nœuds) avec une règle `NoSchedule` pour ceux qui n'ont pas cette expérience.

- **Taint appliquée** : `experience=5ans:NoSchedule` - Les candidatures sans cette expérience ne seront pas considérées.
- **Tolérance ajoutée aux candidats** : Les candidats avec une tolérance `experience=5ans` pourront passer cette barrière initiale.

Cependant, certaines personnes mentent sur leur expérience et sont acceptées. Pendant leur période de travail, une vérification est effectuée pour valider leur expérience réelle. Si elles n'ont pas effectivement 5 ans d'expérience, elles sont soit laissées soit expulsées du poste (effet `NoExecute`), car cette exigence est stricte.

- **Taint stricte** : `experience=5ans:NoExecute` - Les employés qui ne tolèrent pas cette règle sont expulsés.


# 2 - Pratique - Exemple de l'Utilisation des Taints et Tolérances dans un Processus de Recrutement

## Exemple d'une compagnie recevant beaucoup de candidatures

### Contexte :
Une compagnie reçoit environ 5000 candidatures par jour pour divers postes. Pour gérer efficacement ce flux de candidatures et filtrer les candidats non qualifiés, l'entreprise décide d'introduire une exigence stricte : chaque candidat doit avoir au moins 5 ans d'expérience pertinente.

### Mise en place des Taints et Tolérances :

1. **Taint sur les candidatures (nœuds)** :
   - **Taint** : `experience=5ans:NoSchedule`
   - **Description** : Cette taint empêche la planification de candidatures sur des nœuds (postes) si les candidats ne possèdent pas au moins 5 ans d'expérience. 

2. **Tolérance ajoutée aux candidats (pods)** :
   - **Tolérance** :
     ```yaml
     tolerations:
     - key: "experience"
       operator: "Equal"
       value: "5ans"
       effect: "NoSchedule"
     ```
   - **Description** : Les candidats avec cette tolérance peuvent être programmés sur les nœuds (postes) malgré la taint, signifiant qu'ils déclarent avoir 5 ans d'expérience.

### Problème rencontré :
Certaines personnes mentent sur leur expérience, affirmant avoir 5 ans d'expérience alors qu'elles ne les ont pas. Ces candidats sont initialement acceptés et commencent à travailler.

### Gestion stricte des exigences pendant le travail (NoExecute) :

1. **Vérification de l'expérience** :
   - Pendant leur période de travail, une vérification est effectuée pour valider l'expérience réelle des employés.

2. **Taint stricte pour expulsion** :
   - **Taint** : `experience=5ans:NoExecute`
   - **Description** : Les employés qui ne tolèrent pas cette taint sont expulsés du poste si leur expérience réelle ne correspond pas aux 5 ans exigés.

### Exemple réel :
- **Ajout de la taint initiale (NoSchedule)** :
  ```shell
  kubectl taint nodes candidat experience=5ans:NoSchedule
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

- **Ajout de la taint stricte (NoExecute)** :
  ```shell
  kubectl taint nodes candidat experience=5ans:NoExecute
  ```

### Conclusion :
Ce système de taints et tolérances permet à l'entreprise de gérer efficacement le flux élevé de candidatures en filtrant initialement les candidats non qualifiés. Il permet également de vérifier et de maintenir cette exigence pendant la période de travail, expulsant ceux qui ne respectent pas les critères stricts d'expérience.

### Objectif :
Assurer que seuls les candidats réellement qualifiés sont acceptés et maintenus dans l'entreprise, optimisant ainsi les ressources et la qualité de la main-d'œuvre.
