### Quiz sur RBAC (Role-Based Access Control) dans Kubernetes

#### Instructions :
Répondez aux questions suivantes basées sur le cours complet sur RBAC dans Kubernetes. Ce quiz est conçu pour être facile et aider à renforcer votre compréhension des concepts clés.

#### 1. Introduction à RBAC

1. **Qu'est-ce que RBAC ?**
   - A. Un modèle de contrôle d'accès basé sur les rôles des utilisateurs
   - B. Un système de gestion de bases de données
   - C. Un langage de programmation
   - D. Une méthode de déploiement d'applications
   - E. Un protocole de communication

2. **Pourquoi RBAC est-il important ?**
   - A. Il permet de définir des permissions individuelles pour chaque utilisateur
   - B. Il simplifie la gestion des permissions en fonction des rôles
   - C. Il empêche complètement l'accès des utilisateurs au système
   - D. Il augmente la complexité de la gestion des accès
   - E. Il réduit les coûts de gestion des ressources

3. **Quels sont les principaux avantages de RBAC ?** (Choisissez deux réponses)
   - A. Simplification de la gestion des permissions
   - B. Augmentation des coûts de gestion
   - C. Sécurisation des accès
   - D. Complexification de la configuration
   - E. Accélération des processus de développement

#### 2. Concepts Clés de RBAC

4. **Quels sont les deux composants principaux de RBAC ?**
   - A. Utilisateurs et Rôles
   - B. Services et Endpoints
   - C. Rôles et RoleBindings
   - D. Pods et Nodes
   - E. Clusters et Namespaces

5. **Quel est le rôle d'un RoleBinding ?**
   - A. Créer un nouveau rôle
   - B. Lier des utilisateurs ou groupes à des rôles
   - C. Gérer les ressources du cluster
   - D. Déployer des applications
   - E. Créer des pods et des services

6. **Quelle est la différence entre un Role et un ClusterRole ?**
   - A. Un Role est pour les espaces de noms spécifiques, un ClusterRole est pour le cluster entier
   - B. Un Role est utilisé pour les applications, un ClusterRole pour les utilisateurs
   - C. Un Role est moins sécurisé qu'un ClusterRole
   - D. Il n'y a pas de différence
   - E. Un Role est pour les ressources physiques, un ClusterRole pour les ressources virtuelles

#### 3. Configuration de RBAC

7. **Quelle commande est utilisée pour activer RBAC dans Minikube ?**
   - A. `minikube start --authorization-mode=RBAC`
   - B. `minikube start --extra-config=apiserver.authorization-mode=RBAC`
   - C. `minikube start --config=RBAC`
   - D. `minikube start --enable-RBAC`
   - E. `minikube start --rbac-mode`

#### 4. Création de Rôles et RoleBindings

8. **Quel type de ressource est utilisé pour définir des permissions spécifiques sur des pods ?**
   - A. Namespace
   - B. Deployment
   - C. Role
   - D. ServiceAccount
   - E. ConfigMap

9. **Que fait la commande `kubectl apply -f role.yaml` ?**
   - A. Elle crée un nouveau namespace
   - B. Elle applique les définitions de rôle spécifiées dans le fichier `role.yaml`
   - C. Elle supprime un rôle existant
   - D. Elle liste tous les pods dans le namespace `development`
   - E. Elle met à jour les permissions d'un utilisateur

#### 5. Cas d’Utilisation Communes

10. **Quel type de rôle limiterait les utilisateurs à des actions de lecture uniquement ?**
    - A. admin-role
    - B. read-only-role
    - C. developer-role
    - D. service-account-role
    - E. viewer-role

#### 6. Meilleures Pratiques

11. **Qu'est-ce que le principe de moindre privilège ?**
    - A. Donner aux utilisateurs toutes les permissions disponibles
    - B. Donner aux utilisateurs uniquement les permissions minimales nécessaires
    - C. Ne donner aucune permission aux utilisateurs
    - D. Donner des permissions en fonction de la demande de l'utilisateur
    - E. Donner des permissions temporaires aux utilisateurs

12. **Pourquoi est-il important d'auditer régulièrement les permissions ?**
    - A. Pour augmenter les permissions de tous les utilisateurs
    - B. Pour vérifier que les rôles et bindings sont toujours appropriés
    - C. Pour désactiver RBAC
    - D. Pour révoquer tous les accès des utilisateurs
    - E. Pour simplifier la gestion des utilisateurs

#### 7. Exercices Pratiques

13. **Quelle commande est utilisée pour créer un RoleBinding à partir d'un fichier YAML ?**
    - A. `kubectl create rolebinding -f rolebinding.yaml`
    - B. `kubectl apply -f rolebinding.yaml`
    - C. `kubectl delete -f rolebinding.yaml`
    - D. `kubectl edit -f rolebinding.yaml`
    - E. `kubectl update rolebinding -f rolebinding.yaml`

#### 8. Cas Pratiques

14. **Pourquoi utiliser des Service Accounts pour les applications/bots ?**
    - A. Pour éviter les authentifications manuelles
    - B. Pour fournir un accès sécurisé et automatisé aux ressources du cluster
    - C. Pour éviter l'utilisation de certificats SSL
    - D. Pour réduire les coûts de gestion
    - E. Pour améliorer les performances des applications

