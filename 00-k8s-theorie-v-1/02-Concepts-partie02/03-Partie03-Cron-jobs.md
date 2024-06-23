### Table des Matières

1. [Introduction aux CronJobs](#introduction-aux-cronjobs)
2. [Syntaxe et Planification](#syntaxe-et-planification)
3. [Création d'un CronJob](#création-dun-cronjob)
4. [Observation et Gestion des CronJobs](#observation-et-gestion-des-cronjobs)
5. [Pratique : Exemple de CronJob](#pratique-exemple-de-cronjob)
   - [Étape 1 : Préparation de l'environnement](#étape-1-préparation-de-lenvironnement)
   - [Étape 2 : Écrire le fichier YAML du CronJob](#étape-2-écrire-le-fichier-yaml-du-cronjob)
   - [Étape 3 : Déployer le CronJob](#étape-3-déployer-le-cronjob)
   - [Étape 4 : Vérification et validation](#étape-4-vérification-et-validation)
6. [Conclusion](#conclusion)

---

## 1. Introduction aux CronJobs

Les CronJobs dans Kubernetes sont des ressources qui permettent de planifier des tâches répétitives à des intervalles réguliers. Ils sont particulièrement utiles pour des tâches comme les sauvegardes de bases de données, les envois de rapports périodiques, ou toute autre tâche nécessitant une exécution programmée.

---

## 2. Syntaxe et Planification

La syntaxe de planification des CronJobs suit le format des tâches cron traditionnelles que l'on trouve dans les systèmes Unix. La planification est définie en utilisant cinq champs pour les minutes, heures, jours du mois, mois, et jours de la semaine.

- Exemple de planification : 
  ```bash
  * * * * *  # Exécution chaque minute
  ```

Pour aider à la création de ces expressions, l'outil [Crontab.guru](https://crontab.guru/) peut être très utile. Il permet de visualiser facilement les expressions cron et de s'assurer qu'elles correspondent bien à l'intervalle de temps désiré.

---

## 3. Création d'un CronJob

Voici un exemple de fichier YAML pour un CronJob qui exécute un script simple toutes les minutes :

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: example-cronjob
spec:
  schedule: "* * * * *"  # Exécution chaque minute
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: example
            image: busybox
            args:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure
```

Pour déployer ce CronJob, utilisez la commande suivante :

```bash
kubectl apply -f example-cronjob.yaml
```

---

## 4. Observation et Gestion des CronJobs

Pour observer les CronJobs et les jobs qu'ils créent, utilisez les commandes suivantes :

- Pour lister les CronJobs :
  ```bash
  kubectl get cronjob
  ```

- Pour voir les détails d'un CronJob spécifique :
  ```bash
  kubectl describe cronjob example-cronjob
  ```

- Pour lister les jobs créés par un CronJob :
  ```bash
  kubectl get jobs --selector=job-name=example-cronjob
  ```

En cas de problème, les stratégies de redémarrage et la gestion des échecs doivent être bien configurées. La spécification `restartPolicy: OnFailure` permet de relancer le job en cas d'échec.

---

## 5. Pratique : Exemple de CronJob

### Étape 1 : Préparation de l'environnement

- Assurez-vous que votre base de données est accessible et prête pour le backup.
- Ajoutez un label sur un nœud pour diriger les pods du CronJob sur ce nœud spécifique :
  ```bash
  kubectl label node <nom-du-nœud> app=backup
  ```

### Étape 2 : Écrire le fichier YAML du CronJob

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: db-backup
spec:
  schedule: "0 0 * * *"  # Exécution chaque jour à minuit
  jobTemplate:
    spec:
      template:
        spec:
          nodeSelector:
            app: backup
          containers:
          - name: db-backup
            image: mongo
            command:
            - /bin/sh
            - -c
            - mongodump --gzip --host db-service --archive=/backup/db-$(date +"%Y%m%dT%H%M%S").gz
            volumeMounts:
            - name: backup-storage
              mountPath: /backup
          restartPolicy: OnFailure
          volumes:
          - name: backup-storage
            hostPath:
              path: /path/to/backup
```

### Étape 3 : Déployer le CronJob

```bash
kubectl apply -f db-backup-cronjob.yaml
```

### Étape 4 : Vérification et validation

- Utilisez `kubectl get cronjobs` pour vérifier la création du CronJob.
- Attendez l'heure planifiée et vérifiez que le job a été exécuté avec succès.
- Utilisez `kubectl logs` pour voir les logs des jobs et confirmer que le backup a été réalisé.

---

## 6. Conclusion

Les CronJobs sont un outil puissant dans Kubernetes pour automatiser des tâches répétitives. En suivant les bonnes pratiques et en utilisant les outils disponibles pour planifier et observer ces tâches, vous pouvez assurer que vos opérations récurrentes se déroulent de manière fiable et efficace.

---

### Exercice : Création et Gestion de Jobs

**Objectif :** Apprendre à créer, observer et gérer des jobs dans Kubernetes.

1. **Créer un job :** Utilisez la commande suivante pour créer un job qui exécute une tâche simple (`sleep 30`).
   ```bash
   kubectl create job myjob1 --image=busybox -- /bin/sh -c "sleep 30"
   ```
   **Explication :** Cette commande crée un job nommé `myjob1` utilisant l'image `busybox` pour exécuter la commande `sleep 30`.

2. **Observer le job :** Utilisez `kubectl get jobs` pour voir l'état du job, puis `kubectl describe job myjob1` pour voir les détails et l'état d'exécution du job.

3. **Supprimer le job :** Une fois que vous avez confirmé que le job a été exécuté avec succès, supprimez-le avec `kubectl delete job myjob1`.

4. **Bonus :** Créez un fichier YAML pour le job (`myjob2.yaml`) avec une spécification qui exécute une commande différente, par exemple, afficher le message "Hello Kubernetes". Appliquez ce fichier et observez l'exécution du job.

**Fichier YAML Bonus :**

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: myjob2
spec:
  template:
    spec:
      containers:
      - name: hello-kubernetes
        image: busybox
        command:
        - /bin/sh
        - -c
        - echo Hello Kubernetes
      restartPolicy: Never
```

Pour déployer ce job, utilisez :

```bash
kubectl apply -f myjob2.yaml
```

---

### Correction Exercice 1 : Création et Gestion de Jobs

**Étape 1 : Création d'un Job**

```bash
kubectl create job myjob1 --image=busybox -- /bin/sh -c "sleep 30"
```

**Explication :** Cette commande crée un job nommé `myjob1` utilisant l'image `busybox` pour exécuter la commande `sleep 30`.

**Étape 2 : Observer le Job**

- **Liste des jobs :** 
  ```bash
  kubectl get jobs
  ```
- **Détails du job :** 
  ```bash
  kubectl describe job myjob1
  ```

**Étape 3 : Supprimer le Job**

```bash
kubectl delete job myjob1
```

**Étape 4 : Bonus - Créer un fichier YAML pour le Job**

**Fichier YAML :**

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: myjob2
spec:
  template:
    spec:
      containers:
      - name: hello-kubernetes
        image: busybox
        command:
        - /bin/sh
        - -c
        - echo Hello Kubernetes
      restartPolicy: Never
```

**Déploiement du Job :**

```bash
kubectl apply -f myjob2.yaml
```

---

Ce document contient toutes les informations nécessaires pour comprendre, créer, observer et gérer les CronJobs et les jobs dans Kubernetes, avec des exemples concrets et des explications détaillées pour chaque étape.
