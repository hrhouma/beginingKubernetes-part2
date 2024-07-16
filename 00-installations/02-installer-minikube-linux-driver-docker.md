🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
# 1 - Références : 
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
- https://schoolofdevops.github.io/kubernetes-the-hard-way/02-client-tools/
- https://medium.com/@areesmoon/installing-minikube-on-ubuntu-20-04-lts-focal-fossa-b10fad9d0511

🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
### 2 - Commandes à exécuter dans une machine Ubuntu 22.04: 
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

```bash
su
#ou sudo -s
pwd
git clone https://github.com/hrhouma/install-docker.git
cd install-docker/
chmod +x install-docker.sh
./install-docker.sh
#ou sh install-docker.sh
docker version
docker compose version
apt-install docker-compose
docker-compose version
```

```bash
sudo apt update
sudo apt install -y curl wget apt-transport-https
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
minikube version
wget https://storage.googleapis.com/kubernetes-release/release/v1.15.3/bin/linux/amd64/kubectl
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
kubectl version -o yaml
minikube start --driver=docker
minikube start --driver=docker --force
minikube status
kubectl cluster-info
kubectl get nodes
```

```bash
minikube stop
minikube delete
minikube start --driver=docker
sudo usermod -aG docker $USER
newgrp docker
```


🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
### 2 - Commandes troubleshooting Ubuntu 22.04: 
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

![image](https://github.com/user-attachments/assets/ef2a0353-6d25-4c1a-8dd2-e83b54bc9e79)

Pour résoudre les problèmes mentionnés dans votre capture d'écran où Minikube échoue à démarrer en raison de problèmes de permissions Docker et pour ajouter l'utilisateur "eleve" à la liste des sudoers, suivez ces étapes :

### Corriger le Problème de Permission Docker
L'erreur que vous rencontrez indique que l'utilisateur "eleve" n'a pas les permissions nécessaires pour accéder au socket du démon Docker. C'est un problème courant lorsque Docker est installé mais que l'utilisateur n'est pas ajouté au groupe Docker. Pour résoudre cela, vous devez ajouter l'utilisateur au groupe Docker comme suggéré :

```bash
sudo usermod -aG docker eleve
```

Après avoir exécuté cette commande, vous devrez vous déconnecter et vous reconnecter pour que les changements prennent effet. Cela accordera à l'utilisateur "eleve" les permissions nécessaires pour exécuter des commandes Docker sans nécessiter sudo.

### Ajouter "eleve" aux Sudoers
Pour donner à "eleve" les privilèges sudo, vous avez plusieurs options :

#### Utiliser la Commande visudo :
Il est plus sûr de modifier le fichier sudoers en utilisant `visudo` car il vérifie les erreurs de syntaxe :
```bash
sudo visudo
```
À la fin du fichier, ajoutez la ligne suivante pour accorder à "eleve" des privilèges sudo complets :
```sql
eleve ALL=(ALL) NOPASSWD:ALL
```
Sauvegardez et quittez l'éditeur (généralement avec CTRL+X dans nano, en confirmant avec Y pour enregistrer les changements).

#### Ajout Directement à Sudoers.d :
Alternativement, vous pouvez créer un fichier dans le répertoire `sudoers.d` :
```bash
echo "eleve ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/eleve
```
Cette approche est avantageuse pour gérer plusieurs entrées personnalisées.

### Tester les Changements
Après avoir effectué ces changements, vous devriez tester que :

- L'utilisateur "eleve" peut exécuter des commandes Docker sans sudo.
- L'utilisateur "eleve" a des privilèges sudo sans qu'on lui demande de mot de passe.

Exécutez la commande suivante pour tester l'accès Docker :
```bash
su - eleve
docker run hello-world
```
Et pour tester l'accès sudo :
```bash
sudo whoami
```
La sortie devrait être `root` si la modification des sudoers a réussi.

Cette configuration résoudra le problème de permission et accordera les privilèges administratifs nécessaires à l'utilisateur "eleve".

```bash
minikube stop
minikube delete
minikube start --driver=docker
sudo usermod -aG docker $USER
newgrp docker
```


🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
# 3 - ANNEXE 1 - DÉTAILS : 
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

**Vue d'ensemble**

- Avant de parler de Minikube, il est important de comprendre ce qu'est Kubernetes, car Minikube est une forme de Kubernetes souvent utilisée.

- Kubernetes est une plateforme open source portable et extensible pour la gestion de charges de travail et de services conteneurisés, qui facilite la configuration déclarative et l'automatisation. En d'autres termes, Kubernetes est un gestionnaire de conteneurs qui gère plusieurs conteneurs pour fournir un service cohérent et évite que le service ne tombe en panne ou soit surchargé en répartissant les charges de manière équilibrée sur tous les conteneurs. Simplement, Kubernetes est un gestionnaire multi-service. Pour plus d'informations sur Kubernetes, veuillez consulter le concept sur son site officiel : [https://kubernetes.io/docs/concepts/overview/](https://kubernetes.io/docs/concepts/overview/).

- Minikube est une implémentation légère de Kubernetes qui crée une VM sur votre machine locale et déploie un cluster simple contenant un seul nœud (nœud = machine/serveur). Minikube est disponible pour les systèmes Linux, macOS et Windows. Minikube est le type de Kubernetes le plus simple et le plus facile à utiliser si vous n'avez qu'un seul serveur à faire fonctionner.

**Prérequis**

Un serveur fonctionnant sous l'un des systèmes d'exploitation suivants : Ubuntu 22.04, 20.04, 18.04, 16.04 ou toute autre distribution basée sur Debian comme Linux Mint
Il est recommandé d'utiliser un système d'exploitation fraîchement installé pour éviter tout problème inattendu
Accès à l'utilisateur root

**Étapes d'installation**

**Étape 1. Installation de Docker**

- Dans cet article, nous utiliserons Docker comme base pour Minikube. Si Docker n'est pas encore installé sur votre système Ubuntu, utilisez le lien suivant pour l'installer : Installer Docker sur Ubuntu 20.04 LTS (Focal Fossa)

**Étape 2. Mise à jour des paquets système et installation des dépendances de Minikube**
```bash
sudo apt update
sudo apt install -y curl wget apt-transport-https
```
**Étape 3. Installation de Minikube**
Utilisez la commande curl suivante pour télécharger le binaire Minikube le plus récent
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
```
Une fois le binaire téléchargé, utilisez la commande suivante pour installer Minikube
```bash
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```
**Vérification de l'installation de Minikube**
```bash
minikube version
```
minikube version: v1.32.0
commit: 8220a6eb95f0a4d75f7f2d7b14cef975f050512d

**Étape 4. Installation de l'utilitaire kubectl**
kubectl est une ligne de commande utilisée pour interagir avec le cluster Kubernetes. Elle est utilisée pour gérer les déploiements, les ensembles de réplicas, les services, etc. Utilisez la commande suivante pour télécharger la dernière version de kubectl.
### Correction : ici, j'utilise la commande de tuto : https://schoolofdevops.github.io/kubernetes-the-hard-way/02-client-tools/
```bash
wget https://storage.googleapis.com/kubernetes-release/release/v1.15.3/bin/linux/amd64/kubectl
```
Une fois kubectl téléchargé, définissez les permissions exécutables sur le binaire kubectl et déplacez-le dans le chemin /usr/local/bin.
```bash
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```
Maintenant, vérifiez la version de kubectl
```bash
kubectl version -o yaml
```
**Étape 5. Démarrage de Minikube**
Comme nous l'avons déjà mentionné au début, nous utiliserons Docker comme base pour Minikube. Pour démarrer Minikube avec le pilote Docker, exécutez la commande suivante :
```bash
minikube start — driver=docker
```
En cas d'erreur concernant le pilote Docker, utilisez la commande suivante pour forcer le démarrage :
```bash
minikube start --driver=docker --force
```

**Étape 6. Vérification de l'installation**
Utilisez la commande suivante pour vérifier Minikube :
```bash
minikube status
```
Utilisez la commande suivante pour vérifier Kubernetes :
```bash
kubectl cluster-info
```
Utilisez la commande suivante pour tester Kubernetes :
```bash
kubectl get nodes
```

# ANNEXE 1 : 

### Étapes d'installation de Minikube et Kubernetes

**1. Installation de Docker**
```bash
# Installer Docker (voir instructions spécifiques à Ubuntu 20.04 LTS Focal Fossa)
```

**2. Mise à jour des paquets système et installation des dépendances de Minikube**
```bash
sudo apt update
sudo apt install -y curl wget apt-transport-https
```

**3. Installation de Minikube**
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

**4. Installation de l'utilitaire kubectl**
```bash
wget https://storage.googleapis.com/kubernetes-release/release/v1.15.3/bin/linux/amd64/kubectl
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

**5. Démarrage de Minikube**
```bash
minikube start --driver=docker
# En cas d'erreur avec le pilote Docker
minikube start --driver=docker --force
```

**6. Vérification de l'installation**
```bash
minikube status
kubectl cluster-info
kubectl get nodes
```

Ces commandes couvrent l'installation de Docker (prérequis pour Minikube), la mise à jour des paquets système, l'installation de Minikube, la mise en place de `kubectl`, le démarrage de Minikube, et les vérifications pour s'assurer que tout fonctionne correctement.

🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
# 4 - ANNEXE 2 : PERSPECTIVES
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
 

- Dans le prochain article, nous apprendrons à configurer Minikube et à accéder au tableau de bord Minikube à distance ==> https://medium.com/@areesmoon/installing-minikube-on-ubuntu-20-04-lts-focal-fossa-b10fad9d0511


