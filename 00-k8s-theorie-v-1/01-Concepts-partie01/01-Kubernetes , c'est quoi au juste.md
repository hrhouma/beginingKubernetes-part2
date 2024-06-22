# Kubernetes , c'est quoi au juste ?

Imaginez que vous avez une boutique en ligne très populaire, surtout pendant le Black Friday, où le trafic sur votre site explose. 
Pour garder votre site opérationnel, performant et réactif, même face à une affluence massive de visiteurs, vous utilisez plusieurs caisses enregistreuses (dans notre cas, des conteneurs) pour traiter les achats rapidement. 
Kubernetes agit comme le gérant de votre supermarché virtuel, s'assurant que toutes les caisses fonctionnent correctement, ouvrant de nouvelles caisses quand c'est nécessaire, ou les fermant pendant les heures creuses.

# Pourquoi Kubernetes pour votre site e-commerce, surtout lors du Black Friday?

1. **Garde les caisses ouvertes :** Si l'une de vos caisses (conteneurs) a un problème ou est "tuée" pour une raison quelconque, Kubernetes est là pour s'assurer qu'une autre caisse s'ouvre immédiatement, sans que vos clients ne s'en aperçoivent. Cela signifie que votre site reste fonctionnel, même en cas de panne.

2. **Adapte le nombre de caisses :** Pendant le Black Friday, votre site va connaître un pic de visiteurs. Kubernetes peut automatiquement ouvrir plus de caisses (augmenter le nombre de conteneurs) pour gérer cet afflux, assurant ainsi que le site reste rapide et réactif. Après le pic, Kubernetes réduira le nombre de caisses pour économiser des ressources, car il n'est pas nécessaire de les garder toutes ouvertes quand il y a moins de clients.

3. **Sécurise les transactions :** Kubernetes permet de mettre en place des réseaux isolés pour chaque caisse, ce qui signifie que même si un pirate parvient à accéder à une caisse, il ne pourra pas automatiquement accéder à toutes les autres. Cela aide à protéger les données de vos clients.

4. **Mise à jour sans fermeture :** Imaginez que vous voulez mettre à jour le logiciel de la caisse sans fermer votre magasin. Kubernetes permet de faire ces mises à jour en douceur, en transférant les clients d'une caisse à une autre sans interruption de service.

# En bref, l'architecture de Kubernetes pour votre site e-commerce :

- **Nœuds Maîtres (les gérants) :** Surveillent l'état du supermarché (cluster), planifient où ouvrir de nouvelles caisses (Pods), et s'assurent que tout fonctionne comme prévu.

- **Nœuds de Travail (les caisses) :** Là où les transactions (processus) ont lieu. Si une caisse a un problème, elle est immédiatement remplacée.

- **Pods (les caisses individuelles) :** Chaque caisse peut avoir une ou plusieurs tiroirs caisse (conteneurs), permettant de traiter plus rapidement les transactions.

- **Service (le service client) :** Assure que les clients (le trafic réseau) sont dirigés vers la bonne caisse, même si les caisses changent.

- **Ingress (l'entrée du magasin) :** Le point par lequel tous les clients entrent, s'assurant qu'ils sont dirigés vers le bon service.

En utilisant Kubernetes pour votre site e-commerce, surtout pendant des périodes de forte affluence comme le Black Friday, vous vous assurez que votre site reste performant, sécurisé, et capable de gérer un volume élevé de trafic sans souci. 
C'est comme avoir un supermarché avec un nombre infini de caisses et un gérant ultra-efficace qui s'assure que tout fonctionne à la perfection.
Cette section vous donnera une vue détaillée de la manière dont les différents composants de Kubernetes interagissent pour gérer et orchestrer des conteneurs à l'échelle d'un cluster.



# Architecture de Kubernetes

L'architecture de Kubernetes est conçue pour être distribuée et pour gérer des applications conteneurisées à grande échelle. 
Un cluster Kubernetes est composé de plusieurs machines physiques ou virtuelles, appelées **nœuds**. 
Il y a deux types de nœuds : **les nœuds maîtres** et **les nœuds de travail WORKERS** OU **nœuds ESCLAVES**.

## Nœuds Maîtres

Les nœuds maîtres sont le cœur du contrôle de Kubernetes, gérant l'état du cluster et coordonnant les tâches entre les nœuds de travail. Les principaux composants sur un nœud maître incluent :

- **API Server (kube-apiserver):** Le point central de communication du cluster, exposant l'API de Kubernetes. Il sert d'interface entre les différents composants et utilisateurs.
- **Scheduler (kube-scheduler):** Sélectionne le nœud le plus approprié pour exécuter un nouveau pod basé sur la disponibilité des ressources et les contraintes de déploiement.
- **Controller Manager (kube-controller-manager):** Exécute les processus de contrôle en arrière-plan. Il regroupe plusieurs contrôleurs qui surveillent l'état du cluster et effectuent des ajustements pour maintenir l'état désiré.
- **Etcd:** Une base de données clé-valeur légère et distribuée qui stocke toutes les informations de configuration et l'état du cluster, agissant comme le cerveau du cluster Kubernetes.
![image](https://github.com/hrhouma/hrhouma-kubernetes2/assets/10111526/a5fced7a-24b9-4ed1-ac5a-8002339f74b4)


## Nœuds de Travail

Les nœuds de travail exécutent les applications conteneurisées. Ils sont supervisés par les nœuds maîtres et contiennent les composants suivants :

- **Kubelet (kubelet):** Un agent qui s'exécute sur chaque nœud de travail, s'assurant que les conteneurs sont en cours d'exécution dans les Pods comme prévu.
- **Kube-proxy (kube-proxy):** Gère le réseau sur les nœuds de travail, permettant la communication entre les Pods à l'intérieur et à l'extérieur du cluster.
- **Container Runtime:** Le logiciel responsable de l'exécution des conteneurs (par exemple, Docker, containerd).


# Mécanismes de Communication

## Communication Maître vers Nœud

- **API Server vers Kubelet:** L'API Server utilise le Kubelet pour démarrer des Pods, collecter leurs états, et exécuter d'autres commandes sur les nœuds de travail.
- **Controller Manager vers Nœuds:** Les contrôleurs interagissent avec l'API Server pour créer, mettre à jour, et supprimer des ressources comme nécessaire, selon l'état désiré du cluster.

## Communication Nœud vers Maître

- **Kubelet vers API Server:** Le Kubelet envoie régulièrement des rapports d'état au serveur API, mettant à jour sur l'état des Pods et des nœuds.

## Communication entre Pods

- **Kube-proxy:** Chaque nœud exécute un kube-proxy qui s'occupe du routage du trafic réseau vers les Pods, en utilisant les règles IPtables ou IPVS sur l'hôte. Cela permet aux Pods de communiquer entre eux, que ce soit sur le même nœud ou entre différents nœuds du cluster.

## Communication Externe

- **Ingress:** Gère l'accès externe au services dans un cluster, fournissant un point d'entrée unique pour le trafic externe.


### Conclusion

L'architecture et les mécanismes de communication de Kubernetes sont conçus pour fournir un système robuste, flexible et évolutif pour la gestion de conteneurs. 
En comprenant comment les différents composants interagissent, vous pouvez mieux concevoir, déployer et gérer vos applications dans un environnement Kubernetes. 
Pour maîtriser Kubernetes, il est crucial de pratiquer et d'expérimenter avec ses nombreux composants et fonctionnalités.

