### Laboratoire Kubernetes : Gestion de Pods avec Différentes Applications

#### Objectif
Apprendre à déployer et gérer des Pods dans Kubernetes pour différentes applications en utilisant `kubectl` et des fichiers de configuration YAML.

#### Prérequis
- `kubectl` installé et configuré pour interagir avec un cluster Kubernetes (tel que Minikube ou un cluster de cloud).
- Connaissance de base des commandes Linux et de l'utilisation des éditeurs de texte ou IDE (par exemple, Visual Studio Code).

#### Instructions

1. **Introduction**
   - Assurez-vous d'avoir accès à un cluster Kubernetes.
   - Consultez la feuille de triche fournie pour les commandes `kubectl` de base.

2. **Étape 1: Création des Fichiers YAML**
   - Choisissez de déployer un Pod contenant l'une des applications suivantes et créez un fichier YAML approprié pour votre choix:
     - **Apache** (Serveur Web)
     - **MongoDB** (Base de données NoSQL)
     - **Nginx** (Serveur Web et Reverse Proxy)
     - **Redis** (Base de données en mémoire)
     - **PostgreSQL** (Système de gestion de base de données relationnelle)
     - **MySQL** (Système de gestion de base de données relationnelle)
     - **Elasticsearch** (Moteur de recherche et d'analyse)
     - **RabbitMQ** (Broker de messages)
     - **Node.js** (Environnement d'exécution JavaScript pour le serveur)
     - **Tomcat** (Conteneur de servlets pour applications Java)
   
   Exemple de fichier YAML pour Apache:
   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: apache-server
   spec:
     containers:
     - name: apache
       image: httpd:latest
       ports:
       - containerPort: 80
   ```

3. **Étape 2: Déploiement du Pod**
   - Déployez votre Pod en exécutant la commande suivante dans votre terminal, en vous assurant de naviguer au répertoire contenant votre fichier YAML:
     ```bash
     kubectl apply -f <nom_de_votre_fichier>.yaml
     ```

4. **Étape 3: Vérification et Observation**
   - Vérifiez l'état de votre Pod avec:
     ```bash
     kubectl get pods
     ```
   - Obtenez des détails spécifiques sur votre Pod avec:
     ```bash
     kubectl describe pod <nom_du_pod>
     ```

5. **Étape 4: Suppression du Pod**
   - Une fois les observations terminées, supprimez votre Pod avec:
     ```bash
     kubectl delete pod <nom_du_pod>
     ```

6. **Conclusion et Retour**
   - Discutez des résultats obtenus avec vos collègues et instructeur.
   - Réfléchissez aux défis rencontrés et aux différences entre les configurations des différentes applications.

#### Évaluation
- Présentation de votre fichier YAML et justification des choix de configuration.
- Évaluation basée sur la réussite du déploiement du Pod et la capacité à gérer les problèmes potentiels.

Ce laboratoire offre une excellente opportunité de pratiquer la gestion de Pods dans Kubernetes avec une gamme variée d'applications très utilisées, renforçant la compréhension pratique de l'orchestration des conteneurs.
