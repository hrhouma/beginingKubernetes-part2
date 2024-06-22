# Leçon 1: Les Objets Kubernetes

## 1.1- Introduction

En tant qu'on a vu à quoi sert Kubernetes et quelle est son architecture, maintenant, nous allons commencer à nous intéresser aux objets Kubernetes. Qu'est-ce qu'un objet?

Un objet, de façon un peu abstraite, est une entité persistante gérée par Kubernetes, ayant un rôle bien spécifié dans le cluster.

## 1.2. Qu'est-ce qu'un Objet Kubernetes?

Dans un cluster Kubernetes, il y a une multitude d'objets, chacun ayant des rôles spécifiques. Pour rappel, dans notre schéma sur l'architecture, nous avions le plan de contrôle, la tête pensante du cluster, et des nodes effectuant tout le travail. Dans ces nodes, il y a des Pods contenant des conteneurs, éléments constitutifs de votre application finale.

Lorsque vous lancez un cluster, la situation initiale est la suivante: vous avez des nodes disponibles en fonction de votre solution d'hébergement. Par exemple, avec Minikube, le plan de contrôle et les nodes tournent sur la même machine. Initialement, votre cluster n'a aucun Pod, rendant impossible toute opération. Les éléments de base comme l'API server sont en place, mais il faut créer des objets pour rendre le cluster fonctionnel.

## 1.3. Utilisation des Objets dans Kubernetes

L'objectif final est de proposer un service, comme Netflix qui propose des séries en haute définition à de nombreux utilisateurs. Cela nécessite énormément de ressources, et Kubernetes met le cluster au service de ce produit. 

Pour fonctionner, Netflix a besoin de plusieurs composants: une application web, une base de données pour stocker les utilisateurs, et une API pour fournir les films. Cette architecture simplifiée permet de montrer comment utiliser les objets Kubernetes pour représenter ces composants.

## 1.4. Types d'Objets Kubernetes

1. **Application Web**: Peut être construite avec n'importe quelle technologie (React, Angular, etc.).
2. **Base de Données**: Stocke les données utilisateur, peut être réalisée avec différentes technologies.
3. **API**: Fournit les films, également construite avec diverses technologies.

On utilise des objets pour représenter et gérer ces composants. Par exemple, on peut créer des instances d'API, une instance de base de données, et une instance d'application web. Ces instances sont déployées sous forme de Pods, chacun contenant un ou plusieurs conteneurs.

Les Pods sont des objets Kubernetes cruciaux car ils maintiennent l'exécution des conteneurs et leur intégration dans le cluster. Il existe également d'autres objets importants comme les Deployments et les Services.

## 1.5. Création et Gestion des Objets

Pour créer ces objets, on utilise des fichiers de configuration (YAML ou JSON). Ces fichiers décrivent les spécifications des objets que l'on souhaite déployer dans le cluster. Une fois les fichiers définis, on les envoie au cluster via un client (comme kubectl). Le cluster crée et maintient les objets en permanence.

## 1.6. Exemples d'Objets Kubernetes

1. **Deployments**: Gèrent la création et la mise à jour des Pods, ainsi que la gestion des réplicas.
2. **ReplicaSets**: Gèrent le nombre de réplicas d'un Pod, garantissant toujours le nombre défini de réplicas.
3. **Services**: Organisent la communication entre les Pods, créant des groupements abstraits pour permettre les interactions nécessaires (par exemple, l'application web communiquant avec l'API et la base de données).

Un autre objet important est l'Ingress, qui associe un nom de domaine à une adresse IP, facilitant l'accès au cluster.

## 1.7. Spécifications et Statut des Objets

Lors de la création d'un objet, on définit des spécifications (ou "specs"). Une fois l'objet créé, il a également un statut. Le but de Kubernetes est de maintenir l'alignement entre les spécifications et le statut réel de l'objet dans le cluster.

Parfois, des divergences peuvent survenir entre les spécifications et le statut, nécessitant une intervention pour restaurer l'alignement.

#### Conclusion

Pour résumer, les objets Kubernetes sont essentiels pour gérer et maintenir les différentes parties d'un cluster. Nous avons présenté des objets comme les Pods, les Deployments, les Services, et l'Ingress. Dans la leçon suivante, nous commencerons à créer notre premier objet, un Pod.
