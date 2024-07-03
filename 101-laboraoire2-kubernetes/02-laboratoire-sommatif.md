# Introduction aux Travaux Pratiques

Ces travaux pratiques ont pour objectif de vous initier à l'utilisation de Docker et Kubernetes pour le déploiement d'applications conteneurisées. Vous apprendrez à construire des images Docker, à les pousser sur Docker Hub, et à déployer ces images sur un cluster Kubernetes en utilisant Minikube. Vous devrez documenter chaque étape, inclure les commandes utilisées, les résultats obtenus, et des captures d'écran pour démontrer le bon fonctionnement de vos applications.

---

# Partie 1 du TP : Déploiement d'une Application Simple avec Minikube

## Contexte
Le but de ce TP est :
- d'apprendre à construire une image Docker ;
- de la pousser au Docker Hub ;
- de déployer cette image sur Kubernetes en utilisant Minikube.

## Ce travail doit se réaliser à l'aide d'une station Linux serveur et d'un client (Linux ou Windows) avec navigateur.

---

## Étapes du TP

### Étape 1 : Préparation de l'Environnement

1. **Installer Minikube**
   - Suivez les instructions d'installation pour [Minikube](https://minikube.sigs.k8s.io/docs/start/).

   Commande d'installation : `________`

### Étape 2 : Création et Poussée de l'Image Docker

1. **Créer une image Docker :**
   - Choisissez une application de votre choix et créez un fichier `Dockerfile`.

2. **Construire l'image Docker :**
   - Utilisez la commande suivante pour construire votre image Docker :

     Commande pour construire l'image : `________`

3. **Pousser l'image sur Docker Hub :**
   - Connectez-vous à Docker Hub et poussez l'image créée.

   Commandes pour se connecter et pousser l'image :
   - Connexion : `________`
   - Pousser l'image : `________`

### Étape 3 : Déploiement sur Kubernetes avec Minikube

1. **Créer les fichiers de déploiement Kubernetes :**
   - Créez les fichiers YAML nécessaires pour déployer votre application sur Kubernetes (Deployment, Service, etc.).

2. **Appliquer les fichiers YAML :**
   - Utilisez la commande suivante pour appliquer vos fichiers de déploiement :

     Commande pour appliquer les fichiers : `________`

3. **Vérifier le déploiement :**
   - Assurez-vous que les pods sont en cours d'exécution :

     Commande pour vérifier les pods : `________`

4. **Exposez votre application avec un service NodePort :**
   - Utilisez un service NodePort pour exposer votre application et obtenir l'URL.

   Commandes pour exposer et obtenir l'URL :
   - Exposer : `________`
   - Obtenir l'URL : `________`

### Étape 4 : Documentation

1. **Documentez toutes les étapes :**
   - Notez toutes les commandes utilisées et les résultats obtenus.
   - Incluez des captures d'écran montrant les conteneurs en cours d'exécution et l'accès à l'application via le navigateur.

### Tableau de Résultats

Complétez le tableau suivant avec les informations nécessaires :

| Étape                           | Commande utilisée           | Résultat/Observation                                                                 |
|---------------------------------|-----------------------------|--------------------------------------------------------------------------------------|
| Installation de Minikube        | `________`                  |                                                                                      |
| Création de l'image Docker      | `________`                  |                                                                                      |
| Poussée de l'image sur Docker Hub | `________`                |                                                                                      |
| Création des fichiers YAML      | `________`                  |                                                                                      |
| Application des fichiers YAML   | `________`                  |                                                                                      |
| Vérification des pods           | `________`                  |                                                                                      |
| Exposition du service           | `________`                  |                                                                                      |
| Obtention de l'URL              | `________`                  |                                                                                      |

### Nettoyage

Pour nettoyer votre cluster :

Commande pour supprimer le namespace : `________`

Commande pour arrêter Minikube : `________`

Commande pour supprimer Minikube : `________`

---

## Grille d'Évaluation (Sur 50 Points)

| Critères d'Évaluation                            | Description                                                                                                    | Points  |
|--------------------------------------------------|----------------------------------------------------------------------------------------------------------------|---------|
| **Installation et Configuration**                |                                                                                                                | **/10** |
| - Installation de Minikube                       | Minikube est correctement installé et démarré.                                                                 | 5       |
| - Configuration IP du serveur                    | Les informations réseau (adresse IP, masque de sous-réseau, passerelle, DNS, adresse MAC) sont documentées.     | 5       |
| **Création et Poussée de l'Image Docker**        |                                                                                                                | **/10** |
| - Création de l'image Docker                     | L'image Docker est créée correctement à partir du Dockerfile.                                                  | 5       |
| - Poussée de l'image sur Docker Hub              | L'image est poussée sur Docker Hub et est accessible publiquement.                                             | 5       |
| **Déploiement sur Kubernetes**                   |                                                                                                                | **/20** |
| - Création des fichiers de déploiement YAML      | Les fichiers YAML sont correctement créés et appliqués.                                                        | 10      |
| - Déploiement des Applications                   | Les pods sont en cours d'exécution et les services sont exposés correctement.                                   | 10      |
| **Documentation et Vérification**                |                                                                                                                | **/10** |
| - Documentation des étapes                       | Les étapes pour créer, pousser et déployer l'image sont bien documentées.                                       | 5       |
| - Vérification de l'accès à l'application        | L'application est accessible via l'URL fournie, avec capture d'écran à l'appui.                                 | 5       |
| **Total**                                        |                                                                                                                | **/50** |

---

Ce travail pratique vous permettra de mieux comprendre et maîtriser l'utilisation de Kubernetes pour déployer et gérer des applications conteneurisées. Bonne chance !

---

# Partie 2 du TP : Déploiement d'une Application Multi-Conteneurs avec Minikube

## Contexte
Le but de ce TP est :
- d'apprendre à créer et utiliser un fichier Docker Compose pour définir une application multi-conteneurs ;
- de pousser les images des conteneurs au Docker Hub ;
- de déployer cette application sur Kubernetes en utilisant Minikube.

## Ce travail doit se réaliser à l'aide d'une station Linux serveur et d'un client (Linux ou Windows) avec navigateur.

---

## Étapes du TP

### Étape 1 : Préparation de l'Environnement

1. **Changer de contexte ou créer un nouveau cluster avec Minikube**
   - Si vous souhaitez garder l'ancien contexte, vous pouvez créer un nouveau profil avec Minikube. Sinon, vous pouvez arrêter et supprimer l'ancien cluster.

   - Pour créer un nouveau profil :
     ```bash
     minikube start -p nouveau-profil
     ```

   - Pour arrêter et supprimer l'ancien cluster :
     ```bash
     minikube stop
     minikube delete
     minikube start
     ```

### Étape 2 : Création et Poussée des Images Docker

1. **Créer les images Docker :**
   - Choisissez une application avec une dépendance entre deux conteneurs (par exemple, Redis et une application Node.js, Nginx et une application Python (Flask), Prometheus et Grafana).
   - Créez les fichiers `Dockerfile` nécessaires pour chaque conteneur.

2. **Construire les images Docker :**
   - Utilisez les commandes suivantes pour construire vos images Docker :

     Commande pour construire les images : `________`

3. **Pousser les images sur Docker Hub :**
   - Connectez-vous à Docker Hub et poussez les images créées.

   Commandes pour se connecter et pousser les images :
   - Connexion : `________`
   - Pousser les images : `________`

### Étape 3 : Création du Fichier Docker Compose

1. **Créer un fichier `docker-compose.yml` :**
   - Définissez votre application multi-conteneurs avec les services et les dépendances nécessaires.

2. **Vérifier le fonctionnement avec Docker Compose :**
   - Assurez-vous que votre application fonctionne correctement en utilisant Docker Compose.

### Étape 4 : Déploiement sur Kubernetes avec Minikube

1. **Convertir le fichier Docker Compose en fichiers de déploiement Kubernetes :**
   - Créez les fichiers YAML nécessaires pour déployer votre application sur Kubernetes (Deployment, Service, etc.).

2. **Appliquer les fichiers YAML :**
   - Utilisez la commande suivante pour appliquer vos fichiers de déploiement :

     Commande pour appliquer les fichiers : `________`

3. **Vérifier le déploiement :**
   - Assurez-vous que tous les pods sont en cours d'exécution :

     Commande pour vérifier les pods : `________`

4. **Exposez votre application avec un service NodePort :**
   - Utilisez un service NodePort pour exposer votre application et obtenir l'URL.

   Commandes pour exposer et obtenir l'URL :
   - Exposer : `________`
   - Obtenir l'URL : `________`

### Étape 5 : Documentation

1. **Documentez toutes les étapes :**
   - Notez toutes les commandes utilisées et les résultats obtenus.
   - Incluez des captures d'écran montrant les conteneurs en cours d'exécution et l'accès à l'application via le navigateur.

### Tableau de Résultats

Complétez le tableau suivant avec les informations nécessaires :

| Étape                           | Commande utilisée

           | Résultat/Observation                                                                 |
|---------------------------------|-----------------------------|--------------------------------------------------------------------------------------|
| Création des images Docker      | `________`                  |                                                                                      |
| Poussée des images sur Docker Hub | `________`                |                                                                                      |
| Création des fichiers YAML      | `________`                  |                                                                                      |
| Application des fichiers YAML   | `________`                  |                                                                                      |
| Vérification des pods           | `________`                  |                                                                                      |
| Exposition du service           | `________`                  |                                                                                      |
| Obtention de l'URL              | `________`                  |                                                                                      |

### Nettoyage

Pour nettoyer votre cluster :

Commande pour supprimer le namespace : `________`

Commande pour arrêter Minikube : `________`

Commande pour supprimer Minikube : `________`

---

## Grille d'Évaluation (Sur 50 Points)

| Critères d'Évaluation                            | Description                                                                                                    | Points  |
|--------------------------------------------------|----------------------------------------------------------------------------------------------------------------|---------|
| **Création et Poussée des Images Docker**        |                                                                                                                | **/15** |
| - Création des images Docker                     | Les images Docker sont créées correctement à partir des Dockerfiles.                                           | 10      |
| - Poussée des images sur Docker Hub              | Les images sont poussées sur Docker Hub et sont accessibles publiquement.                                      | 5       |
| **Déploiement sur Kubernetes**                   |                                                                                                                | **/25** |
| - Création des fichiers de déploiement YAML      | Les fichiers YAML sont correctement créés et appliqués.                                                        | 15      |
| - Déploiement des Applications                   | Les pods sont en cours d'exécution et les services sont exposés correctement.                                   | 10      |
| **Documentation et Vérification**                |                                                                                                                | **/10** |
| - Documentation des étapes                       | Les étapes pour créer, pousser et déployer les images sont bien documentées.                                    | 5       |
| - Vérification de l'accès à l'application        | L'application est accessible via l'URL fournie, avec capture d'écran à l'appui.                                 | 5       |
| **Total**                                        |                                                                                                                | **/50** |

---

## Annexe : Changer les Ressources d'un Cluster Minikube

Si vous avez besoin de modifier les ressources (CPU, mémoire) de votre cluster Minikube, voici quelques méthodes pour le faire.

### Changer les Ressources en Utilisant un Nouveau Profil

1. Listez les profils disponibles :
   ```bash
   minikube profile list
   ```

2. Supprimez le profil que vous souhaitez modifier :
   ```bash
   minikube delete --profile <nom-du-profil>
   ```

3. Créez un nouveau profil avec les ressources souhaitées :
   ```bash
   minikube start --vm-driver=virtualbox --cpus 8 --memory 16g
   ```

### Changer les Ressources sans Supprimer le Cluster

1. Arrêtez Minikube :
   ```bash
   minikube stop
   ```

2. Démarrez Minikube avec les nouvelles ressources :
   ```bash
   minikube start --memory=4096 --cpus=2
   ```
