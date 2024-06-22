# 1 - Introduction à Kubernetes

## 1.1. Qu'est-ce que Kubernetes ?

Kubernetes est un système d'orchestration de conteneurs open-source qui automatise le déploiement, la mise à l'échelle et la gestion d'applications conteneurisées. Il a été conçu par Google et est maintenant maintenu par la Cloud Native Computing Foundation. Kubernetes facilite à la fois les déploiements déclaratifs et l'automatisation pour opérer des applications dans des conteneurs à travers des clusters de serveurs.

## 1.2. Fonctionnalités Clés de Kubernetes

### 1.2.1. Déploiement et Gestion

Kubernetes permet de déployer des applications conteneurisées sur un cluster de machines, automatisant leur distribution et leur gestion. Cela inclut le maintien de l'état désiré des applications, la gestion des versions et la capacité de faire des mises à jour progressives (rolling updates) sans temps d'arrêt.

### 1.2.2. Scalabilité

Imaginez un magasin lors du Black Friday où le nombre de clients fluctue considérablement. Kubernetes est comme le responsable du magasin qui ouvre ou ferme des caisses en fonction du nombre de clients. De manière similaire, Kubernetes ajuste le nombre de conteneurs en fonction de la charge, assurant une utilisation optimale des ressources et une réponse rapide aux changements de demande.

### 1.2.3. Haute Disponibilité et Résilience

Considérez une situation où une caisse tombe en panne dans un supermarché. Un responsable réactif remplacera rapidement cette caisse pour éviter toute interruption du service. Kubernetes offre cette même résilience pour les applications en détectant les conteneurs défaillants et en les remplaçant automatiquement, garantissant ainsi une disponibilité continue de l'application.

### 1.2.4. Mises à Jour Progressives (Rolling Updates)

Pour un magasin, introduire un nouveau système de paiement sans perturber les clients nécessite une transition soigneuse. Kubernetes gère de la même manière les mises à jour des applications. Avec les rolling updates, Kubernetes permet de mettre à jour l'application conteneurisée progressivement, sans interruption de service, en remplaçant les anciennes versions des conteneurs par de nouvelles, une à une.

### 1.2.5. Pourquoi Utiliser Kubernetes Aujourd'hui ?

Dans un monde où les applications doivent être fiables, évolutives et disponibles 24h/24 et 7j/7, Kubernetes se présente comme une solution essentielle. Il simplifie la gestion des infrastructures, permet une scalabilité dynamique, assure une haute disponibilité et facilite les mises à jour et la maintenance des applications. Kubernetes transforme non seulement la manière de déployer et de gérer les applications, mais il permet également aux équipes de se concentrer sur l'innovation en réduisant la complexité opérationnelle.


# 2 - Kubernetes, en quoi est-il utile (partie 1) ? Sans VS avec Kubernetes ?


| Situation sans Kubernetes | Avantages avec Kubernetes |
|---------------------------|---------------------------|
| **Intervention manuelle lors de pannes de serveur** | **Récupération automatique**: Kubernetes détecte et remplace les pods défaillants automatiquement, minimisant les interruptions de service. |
| **Difficulté à gérer les pics de trafic** | **Autoscaling**: Kubernetes ajuste automatiquement le nombre de conteneurs en fonction de la demande, gérant efficacement les variations de trafic sans intervention manuelle. |
| **Mises à jour risquées avec potentiel de temps d'arrêt** | **Mises à jour progressives (Rolling Updates)**: Kubernetes permet de déployer des mises à jour sans interruption de service, en remplaçant graduellement les conteneurs avec la nouvelle version. |
| **Répartition de charge statique et inefficace** | **Équilibrage de charge intelligent**: Kubernetes distribue dynamiquement le trafic vers les conteneurs en fonction de leur charge actuelle, optimisant les performances et l'utilisation des ressources. |
| **Gestion complexe des configurations et secrets** | **Gestion centralisée des configurations et secrets**: Kubernetes centralise et sécurise la gestion des configurations et secrets, facilitant les mises à jour et renforçant la sécurité. |
| **Déploiement et gestion des applications laborieux** | **Automatisation du déploiement et de la gestion**: Kubernetes simplifie le déploiement et la gestion des applications conteneurisées à travers des abstractions et des automatisations claires. |
| **Utilisation inégale des ressources dans le cluster** | **Planification intelligente des conteneurs**: Kubernetes place les conteneurs sur les nœuds du cluster en fonction de leurs besoins en ressources, maximisant ainsi l'efficacité. |
| **Isolation et sécurité limitées entre les applications** | **Isolation robuste et sécurité**: Kubernetes offre une isolation forte entre les pods, renforçant la sécurité et réduisant les risques d'interférence entre applications. |
| **Surveillance et logging dispersés et incohérents** | **Surveillance et logging centralisés**: Avec Kubernetes, il est possible de centraliser la surveillance et le logging, offrant une vue d'ensemble claire et facilitant le diagnostic des problèmes. |



# 3 - Kubernetes, en quoi est-il utile (partie 2 - analogie) ? Sans VS avec Kubernetes ?

## Analogie d'un magasin Walmart pendant le Black Friday pour illustrer les avantages de Kubernetes :

| Situation sans Kubernetes | Avantages avec Kubernetes |
|---------------------------|---------------------------|
| **Intervention manuelle lors de pannes de caisses** | **Récupération automatique**: Comme si un nouveau caissier remplaçait instantanément une caisse défaillante, Kubernetes détecte et remplace les pods défaillants automatiquement, assurant un service continu sans temps d'arrêt. |
| **Difficulté à gérer l'affluence pendant le Black Friday** | **Autoscaling**: À l'image d'un Walmart qui ouvre plus de caisses automatiquement lors d'un afflux de clients, Kubernetes ajuste le nombre de conteneurs pour répondre à la demande, évitant les longues files d'attente et optimisant le service. |
| **Mises à jour des systèmes de caisse risquées** | **Mises à jour progressives (Rolling Updates)**: Tout comme on pourrait mettre à jour les systèmes de caisse un par un sans fermer le magasin, Kubernetes permet de déployer des mises à jour sans interrompre le service en remplaçant graduellement les conteneurs. |
| **Répartition inefficace des clients entre les caisses** | **Équilibrage de charge intelligent**: Comme un responsable qui dirige les clients vers les caisses les moins occupées, Kubernetes distribue le trafic vers les conteneurs en fonction de leur charge, améliorant l'efficacité et la satisfaction client. |
| **Gestion complexe des configurations des caisses** | **Gestion centralisée des configurations et secrets**: Kubernetes simplifie la gestion des paramètres et secrets de chaque caisse, comme un système centralisé qui met à jour toutes les caisses simultanément, améliorant la sécurité et l'efficacité. |
| **Déploiement et gestion des caisses laborieux** | **Automatisation du déploiement et de la gestion**: Kubernetes facilite la mise en place et la gestion des caisses, permettant une transition fluide entre les différents modes de service, comme un système automatisé pour ouvrir ou fermer les caisses rapidement. |
| **Utilisation inégale des caisses dans le magasin** | **Planification intelligente des conteneurs**: Comme un manager qui optimise l'utilisation des caisses disponibles en fonction du trafic, Kubernetes place les conteneurs sur les nœuds du cluster en maximisant l'efficacité. |
| **Isolation et sécurité limitées entre les caisses** | **Isolation robuste et sécurité**: Kubernetes garantit une séparation sécurisée entre les différentes caisses, comme des barrières qui empêchent les erreurs d'une caisse d'affecter les autres, renforçant la sécurité et la fiabilité. |
| **Surveillance et logging dispersés des opérations de caisse** | **Surveillance et logging centralisés**: Kubernetes permet de centraliser la surveillance et le logging des activités de toutes les caisses, offrant une vision claire de la performance globale et facilitant le diagnostic rapide des problèmes. |


# 4 - Kubernetes, en quoi est-il utile (partie 3 - résumé) ? Sans VS avec Kubernetes ?



| Situation sans Kubernetes | Solution Technique avec Kubernetes | Explication Vulgarisée |
|---------------------------|------------------------------------|------------------------|
| **Intervention manuelle lors de pannes de serveur** | **Récupération automatique de pods** | Comme un nouveau caissier qui remplace une caisse en panne, Kubernetes lance un nouveau pod pour remplacer celui qui est défaillant, assurant un service continu. |
| **Difficulté à gérer les pics de trafic** | **Autoscaling** | À l'image d'un Walmart ouvrant plus de caisses lors du Black Friday, Kubernetes ajuste le nombre de conteneurs pour gérer les variations de demande sans intervention manuelle. |
| **Mises à jour risquées avec potentiel de temps d'arrêt** | **Mises à jour progressives (Rolling Updates)** | Tout comme mettre à jour les systèmes de caisse un par un sans fermer le magasin, Kubernetes déploie des mises à jour sans interrompre le service en remplaçant graduellement les conteneurs. |
| **Répartition de charge statique et inefficace** | **Équilibrage de charge intelligent** | Comme un responsable qui dirige les clients vers les caisses les moins occupées, Kubernetes répartit le trafic vers les conteneurs en optimisant l'utilisation des ressources. |
| **Gestion complexe des configurations et secrets** | **Gestion centralisée des configurations et secrets** | Kubernetes simplifie la gestion des paramètres et secrets, comme un système centralisé qui met à jour toutes les caisses simultanément pour plus de sécurité et d'efficacité. |
| **Déploiement et gestion des applications laborieux** | **Automatisation du déploiement et de la gestion** | Kubernetes facilite la mise en place et la gestion des applications, permettant des transitions fluides entre différents modes de service, comme ouvrir ou fermer rapidement des caisses. |
| **Utilisation inégale des ressources dans le cluster** | **Planification intelligente des conteneurs** | Comme un manager qui optimise l'utilisation des caisses en fonction du trafic, Kubernetes place les conteneurs sur les nœuds en maximisant l'efficacité. |
| **Isolation et sécurité limitées entre les applications** | **Isolation robuste et sécurité** | Kubernetes assure une séparation sécurisée entre les différents services, comme des barrières qui empêchent les erreurs d'une caisse d'affecter les autres, renforçant la sécurité. |
| **Surveillance et logging dispersés et incohérents** | **Surveillance et logging centralisés** | Kubernetes permet de centraliser la surveillance et le logging des activités, offrant une vision claire de la performance et facilitant le diagnostic rapide des problèmes, comme surveiller toutes les caisses d'un magasin. |



# 5- Composants Clés de Kubernetes

## 5.1. Cluster Kubernetes

- Un cluster Kubernetes est un ensemble de machines, appelées nœuds, qui exécutent des applications conteneurisées. Un cluster a au moins un nœud maître et plusieurs nœuds de travail.

- **Nœud Maître:** Coordonne le cluster
- **Nœuds de Travail:** Exécutent les applications

## 5.2. Pod

- L'unité de base de Kubernetes. Un Pod représente un ou plusieurs conteneurs qui doivent être gérés ensemble sur le même nœud.

## 5.3. Service

- Un Service est une abstraction qui définit un ensemble logique de Pods et une politique d'accès à ces Pods, souvent via le réseau.

## 5.4. Ingress

- Un objet Ingress gère l'accès externe aux services dans un cluster, typiquement HTTP. Il peut fournir l'équilibrage de charge, la terminaison SSL et l'hébergement virtuel basé sur le nom.

## 5.5. ConfigMap et Secret

- Permettent de stocker des données de configuration non confidentielles et confidentielles, respectivement, qui peuvent être utilisées par les pods.

## 5.6. Volume

- Offre un système de stockage aux Pods, permettant de conserver des données au-delà de la durée de vie d'un Pod particulier.

## 5.7. Deployment et StatefulSet

- **Deployment:** Gère les déploiements sans état en créant et en supprimant des Pods pour atteindre l'état désiré.
- **StatefulSet:** Utilisé pour les applications avec un état, gérant le déploiement et le scaling des ensembles de Pods, avec un stockage persistant et une identité unique pour chaque Pod.

## 5.8. DaemonSet

- Assure qu'un ou plusieurs Pods s'exécutent sur tous les nœuds (ou un sous-ensemble) du cluster.

## 5.9. Job et CronJob

- **Job:** Exécute une tâche à terme dans le cluster.
- **CronJob:** Planifie des tâches à exécuter périodiquement.

## 5.10. Helm

- Un gestionnaire de paquets pour Kubernetes, permettant de définir, d'installer, et de gérer les applications Kubernetes.

## 5.2. Architecture et Communication

- **API Server:** Point d'entrée des commandes REST. Toute commande dans Kubernetes passe par l'API Server.
- **Etcd:** Base de données clé-valeur qui stocke toute la configuration et l'état du cluster.
- **Scheduler:** Sélectionne quel nœud exécute le nouveau Pod basé sur la disponibilité des ressources.
- **Controller Manager:** Exécute les contrôleurs de boucle qui régulent l'état du cluster, comme le Replication Controller.
- **Kubelet:** Un agent qui s'exécute sur chaque nœud, s'assurant que les conteneurs sont en cours d'exécution dans un Pod.
- **Kube-proxy:** Gère le réseau sur chaque nœud, permettant la communication entre les Pods à travers le cluster.


# Encore pas très claire ?

- Imaginons que Kubernetes est comme un grand hôtel où les invités (applications) réservent des chambres (ressources). Voici comment fonctionnerait cet hôtel:

### 5.2.1. Kubectl:

- Analogie : C'est comme la réception ou le concierge de l'hôtel.
- Description : Kubectl est l'outil de ligne de commande que vous utilisez pour interagir avec le cluster Kubernetes. Lorsque vous souhaitez réserver une chambre, effectuer un enregistrement ou un départ, vous allez à la réception pour le faire. De la même manière, lorsque vous souhaitez déployer, mettre à jour ou supprimer une application dans Kubernetes, vous utilisez kubectl.

### 5.2.2. Scheduler (ordonnanceur):

Analogie : C'est comme l'assistant de la réception qui décide dans quelle chambre chaque invité sera logé.
Description : Le Scheduler est responsable de décider sur quel nœud (machine) une tâche (pod) sera exécutée. Il prend en compte les ressources disponibles et les besoins du pod pour faire le meilleur choix.

### 5.2.3. Controller (contrôleur):

Analogie : Ce sont comme les gestionnaires d'étage de l'hôtel qui s'assurent que tout est en ordre.
Description : Les contrôleurs sont des boucles qui surveillent l'état des ressources dans Kubernetes et prennent des mesures pour corriger tout écart entre l'état désiré et l'état actuel. Si un invité a demandé un service en chambre et que cela n'a pas été fait, le gestionnaire d'étage s'assure que le service est finalement fourni.

### 5.2.4. Kubelet:

Analogie : C'est comme le personnel de l'hôtel qui s'occupe de chaque chambre.
Description : Chaque nœud (machine) du cluster Kubernetes exécute un agent appelé kubelet. Il s'assure que les conteneurs sont en cours d'exécution dans un pod et rapporte l'état de ce nœud au serveur d'API Kubernetes central. Si un invité a besoin de serviettes supplémentaires dans sa chambre, c'est le rôle du personnel de la chambre (kubelet) de s'en occuper.

### 5.2.5. Conclusion

Cette partie descriptive offre une vue d'ensemble de Kubernetes, touchant à ses composants et fonctionnalités essentiels. Pour un apprentissage exhaustif, il est recommandé de compléter cette introduction par la pratique, l'utilisation de tutoriels détaillés, et l'exploration de la documentation officielle de Kubernetes. Kubernetes est un système puissant et flexible, mais sa maîtrise demande du temps et de l'expérience.


# 6 - Composants de Kubernetes

- **Nœuds (Nodes)**: Hôtes qui exécutent les applications Kubernetes
- **Grappe (Cluster)**: Ensemble de ressources informatiques, de stockage et réseau
- **Conteneurs**: Unités de conditionnement
- **Étiquettes/Sélecteur (Labels/Selector)**: Paires clé-valeur pour l'identification
- **Pods**: Unités de déploiement
- **Déploiement (Deployment)**: Gestion automatique de Pod
- **Contrôleur de Réplication (Replication Controller)**: Assure la disponibilité et la scalabilité
- **ReplicaSet**: Contrôleur de réplication avancé avec l'aide d'expression régulière
- **Services**: Collection de pods exposés en tant que point de terminaison
- **Volumes**: Pour stocker des données de manière permanente
- **Secrets/ConfigMap**: Pour transmettre des secrets et des fichiers de configuration
- **DaemonSet**: Pour exécuter une tâche similaire pod sur chaque Nœud


- Ces termes techniques sont les blocs de construction fondamentaux de Kubernetes et sont utilisés dans le monde entier pour gérer des applications conteneurisées de manière automatique et efficace.

## 6.1.  Analogie

- **Nœuds (Nodes)**: Imaginez un nœud comme un employé dans une entreprise. Chacun a un rôle spécifique et exécute des tâches données. Techniquement, un nœud est un serveur qui exécute des applications Kubernetes.
- **Grappes (Clusters)**: Une grappe est comme un bureau d'entreprise complet avec différents départements; elle fournit toutes les ressources nécessaires aux employés. Dans Kubernetes, un cluster est un ensemble de nœuds qui exécutent vos applications.
- **Conteneurs**: Pensez aux conteneurs comme à des dossiers de projet dans une armoire de bureau, organisant et isolant les ressources pour chaque projet. Techniquement, un conteneur est une méthode d'emballage d'une application et de ses dépendances.
- **Étiquettes/Sélecteurs (Labels/Selector)**: C'est comme un système d'étiquetage dans un classeur pour trouver rapidement le bon document. En technique, il s'agit de paires clé-valeur utilisées pour organiser et sélectionner des ressources Kubernetes.
- **Pods**: Imaginez un pod comme un espace de travail partagé pour un groupe de projets. Techniquement, un pod est la plus petite unité déployable qui peut être créée et gérée dans Kubernetes.
- **Déploiements (Deployments)**: Pensez à un déploiement comme à un gestionnaire de bureau qui automatise la distribution des tâches. Techniquement, c'est une manière d'automatiser la gestion des pods.
- **Contrôleur de Réplication (Replication Controller)**: C'est comme un responsable RH s'assurant qu'il y a toujours le bon nombre d'employés. Techniquement, il veille à ce qu'un nombre spécifié de répliques de pod soit en cours d'exécution.
- **ReplicaSet**: C'est une version avancée du responsable RH qui utilise des critères plus précis pour le recrutement. Techniquement, c'est une amélioration du Replication Controller qui permet un filtrage plus fin.
- **Services**: Imaginez les services comme la réception de l'entreprise, dirigeant les clients vers le bon département. Techniquement, c'est un ensemble de pods fonctionnant comme un seul point de terminaison.
- **Volumes**: Pensez aux volumes comme à des armoires de stockage dans un bureau pour garder les dossiers en sécurité. Techniquement, ils sont utilisés pour stocker des données de manière persistante dans Kubernetes.
- **Secrets/ConfigMap**: C'est comme un coffre-fort dans un bureau ou une carte d'accès qui contient des informations importantes et des configurations. Techniquement, ils sont utilisés pour stocker et passer des secrets et des fichiers de configuration.
- **DaemonSet**: Imaginez un DaemonSet comme un programme de tâches qui s'assure qu'un employé particulier est toujours disponible dans chaque département si nécessaire. Techniquement, il assure qu'un pod est en cours d'exécution sur chaque nœud.


