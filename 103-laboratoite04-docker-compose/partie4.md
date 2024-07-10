### Partie 4: Utiliser Jenkins pour Pousser le Code SCM et les Images Docker vers Docker Hub

Dans cette partie, nous allons apprendre à configurer Jenkins pour pousser le code vers un dépôt SCM et les images Docker vers Docker Hub. Nous allons utiliser des hooks pour automatiser ce processus.

#### Prérequis

1. **Compte Docker Hub :**
   - Assurez-vous d'avoir un compte Docker Hub. Vous pouvez créer un compte gratuit sur [Docker Hub](https://hub.docker.com/).

2. **Jenkins Configuré avec Docker :**
   - Assurez-vous que Jenkins est configuré pour utiliser Docker, comme décrit dans l'annexe précédente.

3. **GitHub ou un autre SCM :**
   - Un dépôt GitHub (ou un autre SCM) pour votre code source.

#### Étape 1: Configurer les Informations d'Authentification pour Docker Hub dans Jenkins

1. **Ajouter des Identifiants Docker Hub :**

   - Accédez à Jenkins : `http://your-jenkins-url:8080/`
   - Allez dans `Manage Jenkins` > `Manage Credentials` > `(global)` > `Add Credentials`
   - Sélectionnez `Username with password` et ajoutez votre nom d'utilisateur et mot de passe Docker Hub.
   - Donnez un ID (par exemple, `docker-hub-creds`).

#### Étape 2: Configurer le Jenkinsfile pour Pousser vers Docker Hub

1. **Modifier le Jenkinsfile pour inclure les étapes de push :**

   ```groovy
   pipeline {
       agent any

       triggers {
           pollSCM('H/5 * * * *') // Vérifie les modifications toutes les 5 minutes
       }

       environment {
           DOCKER_CREDENTIALS_ID = 'docker-hub-creds'
           DOCKER_IMAGE = 'yourusername/yourimage:latest'
       }

       stages {
           stage('Clone Repository') {
               steps {
                   git branch: 'master', url: 'https://github.com/yourusername/yourrepository.git'
               }
           }
           stage('Build Docker Images') {
               steps {
                   script {
                       docker.build(env.DOCKER_IMAGE)
                   }
               }
           }
           stage('Push to Docker Hub') {
               steps {
                   script {
                       docker.withRegistry('https://index.docker.io/v1/', env.DOCKER_CREDENTIALS_ID) {
                           docker.image(env.DOCKER_IMAGE).push()
                       }
                   }
               }
           }
           stage('Run Containers') {
               steps {
                   sh 'docker-compose up -d'
               }
           }
       }
   }
   ```

#### Étape 3: Configurer les Hooks SCM pour Automatiser le Déclenchement de Builds

1. **Configurer un Webhook dans GitHub :**

   - Accédez à votre dépôt GitHub.
   - Allez dans `Settings` > `Webhooks` > `Add webhook`.
   - Entrez l'URL de votre Jenkins suivi de `/github-webhook/` (par exemple, `http://your-jenkins-url:8080/github-webhook/`).
   - Sélectionnez `Just the push event`.
   - Cliquez sur `Add webhook`.

#### Étape 4: Pousser le Code vers SCM et Déclencher le Build

1. **Pousser le Code vers le Dépôt GitHub :**

   ```sh
   git add .
   git commit -m "Added Jenkinsfile for CI/CD pipeline"
   git push origin master
   ```

2. **Vérifier que le Build Jenkins est Déclenché Automatiquement :**

   - Allez dans votre tableau de bord Jenkins.
   - Vous devriez voir un nouveau build déclenché automatiquement par le webhook.

3. **Vérifier que l'Image Docker est Poussée vers Docker Hub :**

   - Allez dans Docker Hub et vérifiez que l'image `yourusername/yourimage:latest` est présente.

### Exemple Complet des Commandes

```sh
# Ajouter un dépôt distant et pousser les modifications vers GitHub
cd /path/to/your/project
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/yourusername/yourrepository.git
git push -u origin master

# Ajouter un Webhook dans GitHub
# Accédez à Settings > Webhooks > Add webhook dans votre dépôt GitHub

# Pousser le code avec le Jenkinsfile vers GitHub
git add Jenkinsfile
git commit -m "Added Jenkinsfile for CI/CD pipeline"
git push origin master
```

### Conclusion

En suivant ce tutoriel, vous avez appris à :

1. Ajouter votre projet à Git et configurer une pipeline Jenkins pour vérifier les modifications toutes les 5 minutes.
2. Pousser les images Docker vers Docker Hub en utilisant Jenkins.
3. Utiliser des webhooks SCM pour automatiser le déclenchement de builds Jenkins.

Avec ces étapes, vous pouvez automatiser efficacement le déploiement de votre application, en s'assurant que chaque changement dans le code source déclenche une nouvelle construction et un déploiement automatique des conteneurs Docker.

# Annexe : 

# Récupérer et Configurer les Informations d'Authentification Docker dans Jenkins

Pour utiliser les identifiants Docker Hub dans votre `Jenkinsfile`, vous devez d'abord les ajouter dans Jenkins et les récupérer correctement dans votre pipeline. Voici les étapes détaillées pour ajouter et configurer ces informations :

# Étape 1: Ajouter des Identifiants Docker Hub dans Jenkins

1. **Accéder à Jenkins :**

   - Ouvrez votre navigateur et accédez à Jenkins à l'adresse `http://your-jenkins-url:8080/`.

2. **Accéder à la Gestion des Identifiants :**

   - Allez dans `Manage Jenkins` > `Manage Credentials`.

3. **Ajouter de Nouveaux Identifiants :**

   - Cliquez sur `(global)` pour ajouter des identifiants globalement.
   - Cliquez sur `Add Credentials`.
   - Sélectionnez `Username with password`.
   - Remplissez le formulaire avec vos informations Docker Hub :
     - **Username:** Votre nom d'utilisateur Docker Hub.
     - **Password:** Votre mot de passe Docker Hub.
     - **ID:** Donnez un ID descriptif, par exemple, `docker-hub-creds`.

   - Cliquez sur `OK` pour enregistrer les identifiants.

# Étape 2: Configurer le Jenkinsfile pour Utiliser les Identifiants Docker Hub

1. **Créer ou Modifier le Jenkinsfile à la racine de votre projet :**

   ```sh
   touch Jenkinsfile
   ```

2. **Ajouter le contenu suivant dans le Jenkinsfile :**

   ```groovy
   pipeline {
       agent any

       triggers {
           pollSCM('H/5 * * * *') // Vérifie les modifications toutes les 5 minutes
       }

       environment {
           DOCKER_CREDENTIALS_ID = 'docker-hub-creds' // ID des identifiants Docker Hub ajoutés dans Jenkins
           DOCKER_IMAGE = 'yourusername/yourimage:latest' // Nom de l'image Docker à construire et pousser
       }

       stages {
           stage('Clone Repository') {
               steps {
                   git branch: 'master', url: 'https://github.com/yourusername/yourrepository.git'
               }
           }
           stage('Build Docker Images') {
               steps {
                   script {
                       docker.build(env.DOCKER_IMAGE) // Construit l'image Docker
                   }
               }
           }
           stage('Push to Docker Hub') {
               steps {
                   script {
                       docker.withRegistry('https://index.docker.io/v1/', env.DOCKER_CREDENTIALS_ID) {
                           docker.image(env.DOCKER_IMAGE).push() // Pousse l'image Docker vers Docker Hub
                       }
                   }
               }
           }
           stage('Run Containers') {
               steps {
                   sh 'docker-compose up -d' // Démarre les conteneurs
               }
           }
       }
   }
   ```

# Exemple Complet des Commandes

Voici un récapitulatif des étapes pour ajouter les identifiants Docker Hub dans Jenkins et configurer le Jenkinsfile :

# Ajouter des Identifiants Docker Hub dans Jenkins

1. **Accéder à Jenkins :**
   ```sh
   http://your-jenkins-url:8080/
   ```

2. **Ajouter des Identifiants :**
   - `Manage Jenkins` > `Manage Credentials` > `(global)` > `Add Credentials`
   - Sélectionnez `Username with password` et remplissez les informations :
     - **Username:** `VotreNomUtilisateurDockerHub`
     - **Password:** `VotreMotDePasseDockerHub`
     - **ID:** `docker-hub-creds`
   - Cliquez sur `OK`.

# Configurer le Jenkinsfile

1. **Créer ou Modifier le Jenkinsfile :**
   ```sh
   touch Jenkinsfile
   ```

2. **Ajouter le Contenu suivant dans le Jenkinsfile :**

   ```groovy
   pipeline {
       agent any

       triggers {
           pollSCM('H/5 * * * *') // Vérifie les modifications toutes les 5 minutes
       }

       environment {
           DOCKER_CREDENTIALS_ID = 'docker-hub-creds' // ID des identifiants Docker Hub
           DOCKER_IMAGE = 'yourusername/yourimage:latest' // Nom de l'image Docker à construire et pousser
       }

       stages {
           stage('Clone Repository') {
               steps {
                   git branch: 'master', url: 'https://github.com/yourusername/yourrepository.git'
               }
           }
           stage('Build Docker Images') {
               steps {
                   script {
                       docker.build(env.DOCKER_IMAGE) // Construit l'image Docker
                   }
               }
           }
           stage('Push to Docker Hub') {
               steps {
                   script {
                       docker.withRegistry('https://index.docker.io/v1/', env.DOCKER_CREDENTIALS_ID) {
                           docker.image(env.DOCKER_IMAGE).push() // Pousse l'image Docker vers Docker Hub
                       }
                   }
               }
           }
           stage('Run Containers') {
               steps {
                   sh 'docker-compose up -d' // Démarre les conteneurs
               }
           }
       }
   }
   ```

# Résumé

En suivant ces étapes, vous avez appris à :

1. Ajouter des identifiants Docker Hub dans Jenkins.
2. Configurer un Jenkinsfile pour utiliser ces identifiants et pousser les images Docker vers Docker Hub.

Avec cette configuration, Jenkins vérifiera automatiquement les modifications dans votre dépôt SCM, construira l'image Docker et la poussera vers Docker Hub en utilisant les identifiants configurés.
