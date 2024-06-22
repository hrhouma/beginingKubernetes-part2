# Quiz : Distinguer entre ReplicaSet, Deployment et Scheduler

*Quiz pour aider à distinguer entre ReplicaSet, Deployment et Scheduler dans Kubernetes :*

**Question 1:**  
Quel composant de Kubernetes est responsable de la distribution des Pods sur les nœuds du cluster ?
- a) ReplicaSet
- b) Deployment
- c) Scheduler

**Question 2:**  
Quel composant assure qu'un nombre spécifié de réplicas d'un Pod sont en cours d'exécution à tout moment ?
- a) ReplicaSet
- b) Deployment
- c) Scheduler

**Question 3:**  
Quel composant gère les ReplicaSets et fournit des fonctionnalités supplémentaires pour les mises à jour déclaratives des Pods et des ReplicaSets ?
- a) ReplicaSet
- b) Deployment
- c) Scheduler

**Question 4:**  
Lors de la mise à jour d'une application, quel composant crée un nouveau ReplicaSet pour la nouvelle version de l'application et réduit progressivement le nombre de Pods de l'ancien ReplicaSet ?
- a) ReplicaSet
- b) Deployment
- c) Scheduler

**Question 5:**  
Peut-on créer et gérer directement des ReplicaSets sans utiliser de Deployment ?
- a) Oui, mais ce n'est pas recommandé
- b) Non, ce n'est pas possible
- c) Oui, et c'est la meilleure pratique

**Question 6:**  
Quel composant est principalement utilisé pour gérer l'état désiré des applications, y compris la création, la mise à jour et le maintien des ReplicaSets ?
- a) ReplicaSet
- b) Deployment
- c) Scheduler

**Question 7:**  
Quel composant ne fait pas partie des objets déployés par l'utilisateur, mais est plutôt un composant du plan de contrôle de Kubernetes ?
- a) ReplicaSet
- b) Deployment
- c) Scheduler

### Réponses

1. c) Scheduler
2. a) ReplicaSet
3. b) Deployment
4. b) Deployment
5. a) Oui, mais ce n'est pas recommandé
6. b) Deployment
7. c) Scheduler

Utilisez ce quiz pour évaluer votre compréhension des différences et des relations entre le Scheduler, le ReplicaSet et le Deployment dans Kubernetes.
