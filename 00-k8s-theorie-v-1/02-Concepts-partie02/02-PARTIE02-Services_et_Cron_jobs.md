
Je vais maintenant créer un plan de contenu avec des liens pour chaque section, afin de faciliter la navigation dans le document.

---

## Plan de Contenu

1. [Introduction aux Services Kubernetes](#introduction-aux-services-kubernetes)
2. [Pourquoi avons-nous besoin de services dans Kubernetes ?](#pourquoi-avons-nous-besoin-de-services-dans-kubernetes)
3. [Types de Services dans Kubernetes](#types-de-services-dans-kubernetes)
   - [ClusterIP](#clusterip)
   - [NodePort](#nodeport)
   - [LoadBalancer](#loadbalancer)
   - [ExternalName](#externalname)
4. [Comment fonctionnent les Services ?](#comment-fonctionnent-les-services)
5. [Exemples Concrets](#exemples-concrets)
6. [Conclusion sur les Services Kubernetes](#conclusion-sur-les-services-kubernetes)
7. [Introduction aux CronJobs dans Kubernetes](#introduction-aux-cronjobs-dans-kubernetes)
8. [Définition d'un CronJob](#définition-dun-cronjob)
9. [Exercice 1 : Création et Gestion de Jobs](#exercice-1--création-et-gestion-de-jobs)
10. [Exercice 2 : Pratique de CronJobs](#exercice-2--pratique-de-cronjobs)
11. [Conclusion sur les CronJobs](#conclusion-sur-les-cronjobs)

---

## Introduction aux Services Kubernetes

Imaginez que Kubernetes est comme une ville pour vos applications, et cette ville est construite avec de nombreux petits conteneurs (vos applications ou parties de vos applications). Chacun de ces conteneurs vit dans une maison (pod), et ces maisons sont situées dans différents quartiers (nœuds). Maintenant, si vous voulez que quelqu'un (utilisateur ou autre application) visite l'un de ces conteneurs, il aurait besoin de savoir où il se trouve exactement. C'est là que les services Kubernetes entrent en jeu. Un service Kubernetes agit comme un point de rencontre fixe ou une adresse postale pour accéder à ces conteneurs, peu importe où ils se déplacent dans la ville (cluster).

### Pourquoi avons-nous besoin de services dans Kubernetes ?

- **Dynamisme** : Les pods (maisons) dans Kubernetes sont très dynamiques, ils peuvent être déplacés, supprimés, ou dupliqués à tout moment en fonction de la charge de travail. Donc, si vous vous fiez directement à l'adresse d'un pod pour accéder à votre application, cela pourrait ne pas fonctionner la prochaine fois que vous essayez. Un service offre une adresse constante pour accéder à votre application, peu importe les mouvements des pods.
- **Équilibrage de charge** : Imaginons que vous ayez plusieurs instances de votre application pour gérer plus de trafic. Un service distribuera automatiquement les demandes entre ces instances, assurant ainsi que l'application peut gérer efficacement le trafic.

### Types de Services dans Kubernetes

Il existe principalement quatre types de services dans Kubernetes :

#### ClusterIP

- **Description** : C'est le type de service par défaut dans Kubernetes. Un service ClusterIP rend votre application accessible à l'intérieur du cluster via une adresse IP interne. Cela signifie que seuls les autres pods à l'intérieur du même cluster peuvent communiquer avec l'application exposée par ce service. Ce type de service est utile pour des cas d'utilisation où vous n'avez pas besoin d'exposer votre application à l'extérieur du cluster.
- **Utilisation** : Idéal pour les applications backend ou les bases de données qui n'ont pas besoin d'être accessibles depuis l'extérieur du cluster.

#### NodePort

- **Description** : Un service NodePort expose l'application sur un port spécifique sur tous les nœuds (serveurs) du cluster. Kubernetes alloue un port (par défaut dans la plage 30000-32767) sur chaque nœud et toute requête envoyée à ce port sur n'importe quel nœud est redirigée vers l'application. Ce type de service rend l'application accessible depuis l'extérieur du cluster en utilisant `<NodeIP>:<NodePort>`.
- **Utilisation** : Convient pour les scénarios de test ou de développement où vous souhaitez accéder facilement à votre application depuis l'extérieur du cluster mais sans les coûts ou la complexité d'un équilibreur de charge externe.

#### LoadBalancer

- **Description** : Ce type de service intègre des équilibreurs de charge externes disponibles dans les environnements de cloud computing pour exposer l'application à l'Internet. Lorsque vous créez un service LoadBalancer, il provisionne automatiquement un équilibreur de charge externe (si disponible) et lui assigne une adresse IP publique. Le trafic reçu par l'équilibreur de charge est automatiquement routé vers l'application, même à travers plusieurs nœuds du cluster.
- **Utilisation** : Idéal pour les applications de production nécessitant une haute disponibilité, un accès global et une capacité à gérer des volumes de trafic élevés. C'est la méthode privilégiée pour exposer une application à l'Internet.

#### ExternalName

- **Description** : Ce type de service est un peu différent. Il ne redirige pas le trafic vers un IP ou un port spécifique mais permet plutôt de renvoyer un alias à un nom de domaine externe. Lorsqu'un pod accède à ce service, la requête est redirigée vers l'adresse externe spécifiée. Cela est utile pour intégrer des services externes au sein de votre cluster, en permettant aux pods d'accéder à ces services externes en utilisant des noms de service internes.
- **Utilisation** : Très utile pour accéder à des services externes au cluster, comme des bases de données hébergées dans le cloud, des API externes, ou tout autre service accessible via un nom de domaine.

### Comment fonctionnent les Services ?

Imaginez que vous avez une application web qui fonctionne dans trois pods différents. Vous créez un service de type ClusterIP pour cette application. Lorsqu'une demande est faite à l'adresse IP du service, Kubernetes s'assure que la demande est dirigée vers l'un des trois pods de manière équilibrée. Si un pod tombe en panne et est remplacé, le service continue de fonctionner sans interruption car il connaît les emplacements actifs de tous les pods de l'application.

### Exemples Concrets

Imaginons que Kubernetes est comme un grand centre commercial avec plein de magasins différents (vos applications). Pour que les clients (les requêtes ou les données) trouvent et accèdent aux magasins, il y a différentes façons de les guider.

1. **ClusterIP** : Imaginez ça comme une adresse secrète interne. C'est comme si chaque magasin avait une porte dérobée utilisée seulement par les employés du centre commercial. Seules les personnes déjà à l'intérieur (dans le cluster) peuvent utiliser cette adresse pour venir voir ce que vous proposez.

2. **NodePort** : C'est comme si, en plus de la porte principale, chaque magasin avait un stand dans le parking. Peu importe à quelle porte vous vous présentez, vous pouvez commander les mêmes choses. Cela signifie que votre magasin (application) peut être atteint de l'extérieur en utilisant l'adresse du centre commercial (le nœud) et un numéro de stand spécifique (le port).

3. **LoadBalancer** : Imaginez maintenant que votre magasin devienne super populaire. Le centre décide de vous donner un coup de main en plaçant quelques employés à l'entrée pour gérer la foule et diriger les clients vers vos caisses de manière efficace. C'est comme avoir votre propre équipe à l'entrée qui s'assure que tout le monde peut accéder à vos offres sans faire la queue.

4. **ExternalName** : Enfin, disons que vous voulez que vos clients puissent commander des pizzas d'un restaurant en dehors du centre commercial. Au lieu de les faire sortir et chercher par eux-mêmes, vous donnez un flyer avec le nom et l'adresse du restaurant. De cette façon, ils peuvent commander directement depuis votre magasin. C'est une manière de connecter les gens à un service qui n'est pas dans le centre (cluster) mais que vous voulez rendre accessible comme s'il l'était.

### Comment les services Kubernetes permettent-ils aux clients extérieurs de se connecter à des applications à l'intérieur du cluster ?

Concentrons-nous sur cette partie : Connecter l'extérieur à l'intérieur. Imaginons un parc d'attractions cette fois, où vos services dans Kubernetes sont les différentes attractions. Les clients à l'extérieur du parc veulent y entrer pour profiter des attractions (vos services). Voici comment cela fonctionne :

1. **NodePort** : Pensez au parc ayant plusieurs portes (les nœuds de votre cluster). Chaque porte a un employé qui tient un panneau avec le numéro de l'attraction (le port). Peu importe par quelle porte vous entrez, si vous suivez le numéro sur le panneau, vous serez guidé vers l'attraction souhaitée. Donc, un client à l'extérieur peut choisir n'importe quelle porte (nœud), utiliser le numéro spécifique (port), et accéder à l'attraction (service) à l'intérieur du parc.

2. **LoadBalancer** : Maintenant, imaginez que le parc devienne extrêmement populaire, avec des milliers de personnes essayant d'entrer en même temps. Le parc décide d'installer un grand panneau électronique à l'entrée qui dirige automatiquement les visiteurs vers la porte la moins encombrée, garantissant ainsi que tout le monde entre rapidement et en douceur. C'est comme avoir un équilibreur de charge externe; il prend les demandes d'entrée et les distribue de manière équitable à travers les portes disponibles (nœuds), s'assurant que personne n'attend trop longtemps et que les attractions ne sont pas surchargées.

En utilisant ces deux méthodes, **NodePort** et **LoadBalancer**, Kubernetes permet aux clients à l

'extérieur (sur Internet) de se connecter facilement à un service à l'intérieur du cluster. NodePort est comme choisir manuellement une porte d'entrée, tandis que LoadBalancer fait tout le travail pour vous, vous assurant la meilleure porte d'entrée en fonction de la situation actuelle.

### ClusterIP et ExternalName : pas de moyen de l'extérieur à l'intérieur ?

Effectivement, **ClusterIP** et **ExternalName** jouent un rôle différent dans la connectivité au sein de Kubernetes, et leurs interactions avec l'extérieur sont moins directes.

#### ClusterIP

- **Description** : Fournit une adresse IP interne au cluster qui n'est accessible que de l'intérieur du cluster. Pour qu'un client à l'extérieur du cluster se connecte à un service utilisant **ClusterIP**, il faudrait passer par quelque chose comme **NodePort** ou **LoadBalancer**, qui agirait comme un intermédiaire.

#### ExternalName

- **Description** : Permet à une attraction du parc (service dans le cluster) de dire "Hey, au lieu de venir ici, pourquoi ne pas visiter ce super endroit à l'extérieur ?". Il crée une sorte de raccourci ou d'alias dans le système de téléphones internes du parc qui redirige directement les appels vers un numéro externe.

### Conclusion sur les Services Kubernetes

Les services Kubernetes agissent comme un guide touristique dans la ville de Kubernetes. Ils connaissent tous les emplacements (pods) où vos applications vivent et comment les atteindre de la manière la plus efficace possible, offrant une adresse stable pour accéder à ces applications et distribuant le trafic pour gérer la charge. C'est un concept fondamental qui aide à rendre les applications Kubernetes accessibles et robustes.

## Introduction aux CronJobs dans Kubernetes

Les CronJobs dans Kubernetes permettent de planifier l'exécution de tâches répétitives. Cela peut inclure des tâches de maintenance, des sauvegardes de base de données, ou toute autre tâche périodique.

### Définition d'un CronJob

Un CronJob est défini par un fichier YAML qui spécifie le planning (sous forme d'une expression cron), ainsi que la tâche à exécuter (sous forme d'un Job). Voici un exemple de fichier YAML pour un CronJob :

```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: example-cronjob
spec:
  schedule: "*/1 * * * *"  # Exécute la tâche toutes les minutes
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

### Exercice 1 : Création et Gestion de Jobs

**Objectif :** Apprendre à créer, observer et gérer des jobs dans Kubernetes.

#### Étape 1 : Création d'un Job

Créez un job qui exécute une tâche simple (`sleep 30`) en utilisant la commande suivante :

```bash
kubectl create job myjob1 --image=busybox -- /bin/sh -c "sleep 30"
```

Cette commande crée un job nommé `myjob1` qui exécute l'image `busybox` avec la commande `sleep 30`.

#### Étape 2 : Observer le Job

Utilisez `kubectl get jobs` pour voir l'état du job, puis `kubectl describe job myjob1` pour voir les détails et l'état d'exécution du job.

#### Étape 3 : Supprimer le Job

Une fois que vous avez confirmé que le job a été exécuté avec succès (ou après un certain temps), supprimez-le avec `kubectl delete job myjob1`.

#### Bonus : Création d'un Job via un Fichier YAML

Créez un fichier YAML pour le job (`myjob2.yaml`) avec une spécification qui exécute une commande différente, par exemple, afficher le message "Hello Kubernetes". Appliquez ce fichier et observez l'exécution du job.

### Exercice 2 : Pratique de CronJobs

Dans cet exercice, vous allez créer un *Job* qui servira à faire le dump d'une base de données *mongo*. Vous créerez ensuite un *CronJob* qui créera un dump à interval régulier.

#### Étape 1 : Création d'un Pod Mongo

Créez un fichier *mongo-pod.yaml* avec la spécification suivante :

```yaml
apiVersion: v1             
kind: Pod                  
metadata:
  name: db
  labels:
    app: db
spec:
  containers:
  - name: mongo
    image: mongo:4.0
```

Lancez ce Pod avec la commande :

```bash
kubectl apply -f mongo-pod.yaml
```

#### Étape 2 : Exposition de la Base Mongo

Créez un fichier *mongo-svc.yaml* avec la spécification suivante :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: db
spec:
  selector:
    app: db
  type: ClusterIP
  ports:
  - port: 27017
```

Lancez ce Service avec la commande :

```bash
kubectl apply -f mongo-svc.yaml
```

#### Étape 3 : Ajout d'un Label sur un Node

Ajoutez le label *app=dump* sur l'un des nodes de votre cluster :

```bash
kubectl label node <NODE_NAME> app=dump
```

#### Étape 4 : Définition d'un Job pour le Dump de la Base de Données

Créez un fichier *mongo-dump-job.yaml* avec la spécification suivante :

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: dump
spec:
  template:
    spec:
      restartPolicy: Never
      nodeSelector:
        app: dump
      containers:
      - name: mongo
        image: mongo:4.0
        command:
        - /bin/bash
        - -c
        - mongodump --gzip --host db --archive=/dump/db.gz
        volumeMounts:
        - name: dump
          mountPath: /dump
      volumes:
      - name: dump
        hostPath:
          path: /dump
```

Lancez ce Job avec la commande :

```bash
kubectl apply -f mongo-dump-job.yaml
```

#### Étape 5 : Définition d'un CronJob pour les Dumps Réguliers

Créez un fichier *mongo-dump-cronjob.yaml* avec la spécification suivante :

```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: dump
spec:
  schedule: "*/1 * * * *"  # Exécute toutes les minutes
  jobTemplate:
    spec:
      template:
        spec:
          nodeSelector:
            app: dump
          containers:
          - name: mongo
            image: mongo:4.0
            command:
            - /bin/bash
            - -c
            - mongodump --gzip --host db --archive=/dump/$(date +"%Y%m%dT%H%M%S")-db.gz
            volumeMounts:
            - name: dump
              mountPath: /dump
          restartPolicy: OnFailure
          volumes:
          - name: dump
            hostPath:
              path: /dump
```

Lancez ce CronJob avec la commande :

```bash
kubectl apply -f mongo-dump-cronjob.yaml
```

#### Étape 6 : Vérification des Dumps

Lancez un Pod *test* pour vérifier les dumps créés :

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test
spec:
  nodeSelector:
    app: dump
  containers:
  - name: test
    image: alpine:3.15
    command:
    - "sleep"
    - "10000"
    volumeMounts:
    - name: dump
      mountPath: /dump
  volumes:
  - name: dump
    hostPath:
      path: /dump
```

Lancez un shell interactif dans le container du pod *test* et vérifiez les dumps :

```bash
kubectl exec -ti test -- sh
# ls /dump
```

#### Étape 7 : Nettoyage

Supprimez les Job et CronJob créés :

```bash
kubectl delete job dump
kubectl delete cronjob dump
kubectl delete pod test
kubectl delete pod db
kubectl delete service db
```

### Conclusion sur les CronJobs

Ce guide vous a fourni une compréhension détaillée des services Kubernetes et des CronJobs. Vous avez appris comment créer, observer, et gérer des jobs ainsi que des tâches planifiées, renforçant ainsi vos compétences en gestion de Kubernetes.
