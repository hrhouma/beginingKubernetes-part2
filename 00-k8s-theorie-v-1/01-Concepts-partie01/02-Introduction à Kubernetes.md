# Introduction à Kubernetes - partie 2

Kubernetes est un système d'orchestration de conteneurs open-source qui automatise le déploiement, la mise à l'échelle et la gestion d'applications conteneurisées. Il a été conçu par Google et est maintenant maintenu par la Cloud Native Computing Foundation. Kubernetes facilite à la fois les déploiements déclaratifs et l'automatisation pour opérer des applications dans des conteneurs à travers des clusters de serveurs.

## Composants Clés de Kubernetes

#### 1. Cluster Kubernetes

Un cluster Kubernetes est un ensemble de machines, appelées nœuds, qui exécutent des applications conteneurisées. Un cluster a au moins un nœud maître et plusieurs nœuds de travail.

- **Nœud Maître:** Coordonne le cluster
- **Nœuds de Travail:** Exécutent les applications

#### 2. Pod

L'unité de base de Kubernetes. Un Pod représente un ou plusieurs conteneurs qui doivent être gérés ensemble sur le même nœud.

#### 3. Service

Un Service est une abstraction qui définit un ensemble logique de Pods et une politique d'accès à ces Pods, souvent via le réseau.

#### 4. Ingress

Un objet Ingress gère l'accès externe aux services dans un cluster, typiquement HTTP. Il peut fournir l'équilibrage de charge, la terminaison SSL et l'hébergement virtuel basé sur le nom.

#### 5. ConfigMap et Secret

Permettent de stocker des données de configuration non confidentielles et confidentielles, respectivement, qui peuvent être utilisées par les pods.

#### 6. Volume

Offre un système de stockage aux Pods, permettant de conserver des données au-delà de la durée de vie d'un Pod particulier.

#### 7. Deployment et StatefulSet

- **Deployment:** Gère les déploiements sans état en créant et en supprimant des Pods pour atteindre l'état désiré.
- **StatefulSet:** Utilisé pour les applications avec un état, gérant le déploiement et le scaling des ensembles de Pods, avec un stockage persistant et une identité unique pour chaque Pod.

#### 8. DaemonSet

Assure qu'un ou plusieurs Pods s'exécutent sur tous les nœuds (ou un sous-ensemble) du cluster.

#### 9. Job et CronJob

- **Job:** Exécute une tâche à terme dans le cluster.
- **CronJob:** Planifie des tâches à exécuter périodiquement.

#### 10. Helm

Un gestionnaire de paquets pour Kubernetes, permettant de définir, d'installer, et de gérer les applications Kubernetes.

### Architecture et Communication

- **API Server:** Point d'entrée des commandes REST. Toute commande dans Kubernetes passe par l'API Server.
- **Etcd:** Base de données clé-valeur qui stocke toute la configuration et l'état du cluster.
- **Scheduler:** Sélectionne quel nœud exécute le nouveau Pod basé sur la disponibilité des ressources.
- **Controller Manager:** Exécute les contrôleurs de boucle qui régulent l'état du cluster, comme le Replication Controller.
- **Kubelet:** Un agent qui s'exécute sur chaque nœud, s'assurant que les conteneurs sont en cours d'exécution dans un Pod.
- **Kube-proxy:** Gère le réseau sur chaque nœud, permettant la communication entre les Pods à travers le cluster.


# Encore pas très claire ?

Imaginons que Kubernetes est comme un grand hôtel où les invités (applications) réservent des chambres (ressources). Voici comment fonctionnerait cet hôtel:

## Kubectl:

Analogie : C'est comme la réception ou le concierge de l'hôtel.
Description : Kubectl est l'outil de ligne de commande que vous utilisez pour interagir avec le cluster Kubernetes. Lorsque vous souhaitez réserver une chambre, effectuer un enregistrement ou un départ, vous allez à la réception pour le faire. De la même manière, lorsque vous souhaitez déployer, mettre à jour ou supprimer une application dans Kubernetes, vous utilisez kubectl.

## Scheduler (ordonnanceur):

Analogie : C'est comme l'assistant de la réception qui décide dans quelle chambre chaque invité sera logé.
Description : Le Scheduler est responsable de décider sur quel nœud (machine) une tâche (pod) sera exécutée. Il prend en compte les ressources disponibles et les besoins du pod pour faire le meilleur choix.

## Controller (contrôleur):

Analogie : Ce sont comme les gestionnaires d'étage de l'hôtel qui s'assurent que tout est en ordre.
Description : Les contrôleurs sont des boucles qui surveillent l'état des ressources dans Kubernetes et prennent des mesures pour corriger tout écart entre l'état désiré et l'état actuel. Si un invité a demandé un service en chambre et que cela n'a pas été fait, le gestionnaire d'étage s'assure que le service est finalement fourni.

## Kubelet:

Analogie : C'est comme le personnel de l'hôtel qui s'occupe de chaque chambre.
Description : Chaque nœud (machine) du cluster Kubernetes exécute un agent appelé kubelet. Il s'assure que les conteneurs sont en cours d'exécution dans un pod et rapporte l'état de ce nœud au serveur d'API Kubernetes central. Si un invité a besoin de serviettes supplémentaires dans sa chambre, c'est le rôle du personnel de la chambre (kubelet) de s'en occuper.
