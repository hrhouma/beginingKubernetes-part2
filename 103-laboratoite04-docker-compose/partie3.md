### Partie 3: Ajouter le Projet à Git et Configurer une Pipeline Jenkins

Dans cette partie, nous allons apprendre à ajouter notre projet à Git et à configurer une pipeline Jenkins pour vérifier les modifications et effectuer des builds automatiquement toutes les 5 minutes.

### Étape 1: Ajouter le Projet à Git

1. **Initialiser un dépôt Git dans le dossier de votre projet :**

   ```sh
   cd /path/to/your/project
   git init
   ```

2. **Ajouter tous les fichiers au dépôt :**

   ```sh
   git add .
   ```

3. **Valider les modifications :**

   ```sh
   git commit -m "Initial commit"
   ```

4. **Ajouter un dépôt distant (par exemple, sur GitHub) :**

   ```sh
   git remote add origin https://github.com/yourusername/yourrepository.git
   ```

5. **Pousser les modifications vers le dépôt distant :**

   ```sh
   git push -u origin master
   ```

### Étape 2: Configurer Jenkins

1. **Installer Jenkins :**

   Si Jenkins n'est pas encore installé, suivez les instructions officielles pour l'installer sur votre système : [Documentation Jenkins](https://www.jenkins.io/doc/book/installing/).

2. **Configurer Jenkins pour Git :**

   - Accédez à Jenkins : `http://your-jenkins-url:8080/`
   - Allez dans `Manage Jenkins` > `Manage Plugins`
   - Installez les plugins nécessaires pour Git (`Git plugin`) et Docker (`Docker plugin`).

### Étape 3: Créer un Fichier Jenkinsfile

Créez un fichier nommé `Jenkinsfile` à la racine de votre projet. Ce fichier définira votre pipeline Jenkins.

#### Jenkinsfile

```groovy
pipeline {
    agent any

    triggers {
        pollSCM('H/5 * * * *') // Vérifie les modifications toutes les 5 minutes
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
                    docker.build('yourimage:latest')
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

### Étape 4: Configurer la Pipeline dans Jenkins

1. **Créer un nouveau projet dans Jenkins :**

   - Allez dans `New Item`, donnez un nom à votre projet et sélectionnez `Pipeline`.
   - Cliquez sur `OK`.

2. **Configurer la pipeline :**

   - Sous la section `Pipeline`, sélectionnez `Pipeline script from SCM`.
   - Choisissez `Git` et entrez l'URL de votre dépôt Git : `https://github.com/yourusername/yourrepository.git`.
   - Assurez-vous que la branche est `master`.
   - Spécifiez le chemin du script : `Jenkinsfile`.

3. **Sauvegarder et lancer la pipeline :**

   - Cliquez sur `Save`.
   - Allez dans `Build Now` pour démarrer la première exécution de la pipeline.

### Étape 5: Vérifier les Builds

1. **Accédez à votre tableau de bord Jenkins :**

   - Vous devriez voir les exécutions de votre pipeline toutes les 5 minutes.
   - Cliquez sur une exécution pour voir les logs et vérifier que tout fonctionne correctement.

2. **Vérifiez que les conteneurs Docker sont en cours d'exécution :**

   ```sh
   docker ps
   ```

   Vous devriez voir les conteneurs en cours d'exécution pour votre application Flask, PostgreSQL et Redis.

### Exemple Complet

Voici un récapitulatif des commandes et étapes :

```sh
# Étape 1: Initialiser le dépôt Git
cd /path/to/your/project
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/yourusername/yourrepository.git
git push -u origin master

# Étape 2: Installer Jenkins et les plugins nécessaires

# Étape 3: Créer le fichier Jenkinsfile
echo 'pipeline {
    agent any
    triggers {
        pollSCM("H/5 * * * *") // Vérifie les modifications toutes les 5 minutes
    }
    stages {
        stage("Clone Repository") {
            steps {
                git branch: "master", url: "https://github.com/yourusername/yourrepository.git"
            }
        }
        stage("Build Docker Images") {
            steps {
                script {
                    docker.build("yourimage:latest")
                }
            }
        }
        stage("Run Containers") {
            steps {
                sh "docker-compose up -d"
            }
        }
    }
}' > Jenkinsfile

# Étape 4: Configurer la pipeline dans Jenkins via l'interface web

# Étape 5: Vérifier les builds via l'interface web et avec Docker
docker ps
```

### Conclusion

En suivant ce tutoriel, vous avez appris à ajouter votre projet à Git et à configurer une pipeline Jenkins pour vérifier les modifications et effectuer des builds automatiquement toutes les 5 minutes. Vous pouvez désormais automatiser le déploiement de votre application Flask avec PostgreSQL et Redis.

# Annexe 1: Configuration de Docker pour Jenkins

Pour utiliser Docker avec Jenkins, certaines configurations sont nécessaires. Voici les étapes détaillées pour s'assurer que Jenkins peut interagir avec Docker correctement.

#### Prérequis

1. **Docker doit être installé sur le serveur Jenkins :**
   - Jenkins utilisera Docker pour construire et exécuter les conteneurs. Assurez-vous que Docker est installé et configuré sur la machine où Jenkins s'exécute.

2. **Permissions Docker pour l'utilisateur Jenkins :**
   - L'utilisateur sous lequel Jenkins s'exécute doit avoir les permissions nécessaires pour accéder au démon Docker (`docker.sock`).

3. **Installer le plugin Docker dans Jenkins :**
   - Installez le plugin Docker dans Jenkins pour permettre à Jenkins de communiquer avec Docker.

#### Étapes pour Configurer Docker avec Jenkins

1. **Installer Docker sur le serveur Jenkins :**

   Suivez les instructions pour installer Docker sur votre système d'exploitation. Voici un exemple pour Ubuntu :

   ```sh
   sudo apt-get update
   sudo apt-get install \
       ca-certificates \
       curl \
       gnupg \
       lsb-release

   sudo mkdir -p /etc/apt/keyrings
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

   echo \
     "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
     $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

   sudo apt-get update
   sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin

   sudo usermod -aG docker $USER
   ```

   Après avoir installé Docker, redémarrez votre machine ou déconnectez-vous et reconnectez-vous pour que les changements de groupe prennent effet.

2. **Configurer l'utilisateur Jenkins pour utiliser Docker :**

   - **Identifier l'utilisateur Jenkins :**
     Jenkins s'exécute généralement sous un utilisateur spécifique, souvent nommé `jenkins`. Pour vérifier l'utilisateur, vous pouvez consulter le fichier de configuration de Jenkins ou utiliser la commande suivante pour voir sous quel utilisateur Jenkins s'exécute :

     ```sh
     ps aux | grep jenkins
     ```

   - **Ajouter l'utilisateur Jenkins au groupe Docker :**
     Utilisez la commande suivante pour ajouter l'utilisateur `jenkins` au groupe `docker` :

     ```sh
     sudo usermod -aG docker jenkins
     ```

   - **Redémarrer Jenkins pour appliquer les changements :**
     Après avoir ajouté l'utilisateur Jenkins au groupe Docker, redémarrez le service Jenkins pour que les modifications prennent effet :

     ```sh
     sudo systemctl restart jenkins
     ```

3. **Installer le plugin Docker dans Jenkins :**

   - Accédez à Jenkins : `http://your-jenkins-url:8080/`
   - Allez dans `Manage Jenkins` > `Manage Plugins`
   - Sous l'onglet `Available`, recherchez `Docker` et installez le plugin `Docker`.

4. **Configurer Jenkins pour utiliser Docker :**

   - Accédez à `Manage Jenkins` > `Configure System`.
   - Faites défiler jusqu'à la section `Cloud` et ajoutez un nouveau cloud `Docker`.
   - Configurez l'URL Docker (par exemple, `unix:///var/run/docker.sock` pour une installation locale de Docker).

#### Vérification de la Configuration

1. **Vérifier les Permissions Docker pour l'utilisateur Jenkins :**

   - **Vérifier que l'utilisateur Jenkins a bien été ajouté au groupe Docker :**
     Connectez-vous en tant qu'utilisateur Jenkins (ou exécutez une commande en tant qu'utilisateur Jenkins) et vérifiez les groupes de l'utilisateur :

     ```sh
     su - jenkins
     groups
     ```

     Vous devriez voir `docker` dans la liste des groupes.

   - **Tester l'accès Docker pour l'utilisateur Jenkins :**
     Toujours en tant qu'utilisateur Jenkins, essayez d'exécuter une commande Docker pour vérifier que l'accès fonctionne :

     ```sh
     docker ps
     ```

     La commande devrait lister les conteneurs en cours d'exécution sans erreurs de permission.

2. **Vérifier les builds Jenkins :**

   - Lancez un build manuellement en cliquant sur `Build Now`.
   - Vérifiez que Jenkins clone le dépôt, construit l'image Docker et lance les conteneurs sans erreurs.

3. **Vérifier les conteneurs Docker :**

   - Assurez-vous que les conteneurs sont en cours d'exécution en utilisant la commande suivante :

     ```sh
     docker ps
     ```

### Exemple Complet des Commandes

Voici un récapitulatif des commandes pour vérifier et configurer les permissions Docker pour l'utilisateur Jenkins :

```sh
# Étape 1: Vérifier l'utilisateur sous lequel Jenkins s'exécute
ps aux | grep jenkins

# Étape 2: Ajouter l'utilisateur Jenkins au groupe Docker
sudo usermod -aG docker jenkins

# Étape 3: Redémarrer Jenkins pour appliquer les changements
sudo systemctl restart jenkins

# Étape 4: Vérifier que l'utilisateur Jenkins a bien été ajouté au groupe Docker
su - jenkins
groups

# Étape 5: Tester l'accès Docker pour l'utilisateur Jenkins
docker ps
```

### Résolution des Problèmes Courants

- **Problème : L'utilisateur Jenkins n'a toujours pas accès à Docker après avoir été ajouté au groupe Docker.**
  - **Solution :** Assurez-vous de redémarrer Jenkins après avoir ajouté l'utilisateur au groupe Docker. Si le problème persiste, redémarrez la machine pour garantir que les changements de groupe sont pris en compte.

- **Problème : Erreur de permission lors de l'exécution des commandes Docker.**
  - **Solution :** Vérifiez que l'utilisateur Jenkins est bien membre du groupe Docker en utilisant la commande `groups` comme montré ci-dessus. Assurez-vous également que le démon Docker est en cours d'exécution.

En suivant ces étapes, vous vous assurerez que Jenkins a les permissions nécessaires pour interagir avec Docker, ce qui est crucial pour que vos pipelines CI/CD fonctionnent correctement.


# Annexe 2: 

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
