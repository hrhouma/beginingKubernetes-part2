# Important
- MÉTHODE 1 : Ce read me ==> créer un cluster sur Ubuntu 22.04
- MÉTHODE 2 : Les fichiers words et pdfs ==>  créer un cluster sur AZURE

# 1 Références : 
- https://kind.sigs.k8s.io/

# 2. Installation de Docker et de Kubernetes avec Kind sur AZURE UBUNTU22.04

- Documentation ci-jointe (fichier word) pour l'installation sur **AZURE + KIND**

# 3. Installation de Docker et de Kubernetes avec Kind sur votre machine locale UBUNTU22.04

Ce guide vous aidera à installer Docker et à configurer un cluster Kubernetes local en utilisant Kind. Ce processus comprend l'installation de Docker, Kind, et kubectl sur une machine avec une architecture AMD64 (x86_64) ou ARM64.

## Prérequis

- Une machine sous Linux
- Accès à un terminal
- Droits d'administrateur (pour l'installation de packages)

## Étapes d'installation

### Installation de Docker

1. Ouvrez un terminal.
2. Naviguez vers le bureau avec `cd Desktop/`.
3. Clonez le dépôt contenant les scripts d'installation de Docker :

   ```bash
   git clone https://github.com/hrhouma/install-docker.git
   ```

4. Installez Git si ce n'est pas déjà fait :

   ```bash
   apt install git
   ```

5. Entrez dans le dossier `install-docker` :

   ```bash
   cd install-docker/
   ```

6. Rendez le script exécutable et lancez-le :

   ```bash
   chmod +x install-docker.sh
   ./install-docker.sh
   ```

### Installation de Kind

7. Téléchargez la version appropriée de Kind selon l'architecture de votre machine :

   - Pour AMD64 / x86_64 :

     ```bash
     [ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.22.0/kind-linux-amd64
     ```

   - Pour ARM64 :

     ```bash
     [ $(uname -m) = aarch64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.22.0/kind-linux-arm64
     ```

8. Rendez le fichier `kind` exécutable et déplacez-le dans un répertoire accessible globalement :

   ```bash
   chmod +x ./kind
   sudo mv ./kind /usr/local/bin/kind
   ```

9. Créez un cluster Kubernetes avec Kind :

   ```bash
   kind create cluster
   ```

### Installation de kubectl

10. Installez `kubectl` pour interagir avec votre cluster Kubernetes :

    ```bash
    sudo apt-get install ca-certificates
    curl -LO -k "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
    chmod +x kubectl
    mv kubectl /usr/local/bin/
    ```

11. Vérifiez la configuration de `kubectl` :

    ```bash
    kubectl config get-contexts
    ```

12. (Optionnel) Pour configurer un cluster avec un fichier de configuration spécifique :

 ```bash
nano kind-config.yaml
 ```

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
```

```bash
kind create cluster --config kind-config.yaml
 ```

13. Pour obtenir des informations sur le cluster :

    ```bash
    kubectl cluster-info --context kind-kind
    ```

### Autres commandes utiles

- Redémarrage de la machine : `reboot`
- Installation du serveur SSH : `apt install openssh-server`
- Vérification de l'adresse IP : `ip a`
- Mise à jour des paquets : `apt update`
- Installation de curl : `apt install curl`

# 4. Références pour le dépannage

Si vous rencontrez des problèmes lors de l'installation ou de l'utilisation de Docker, Kind, ou kubectl, consultez les liens suivants pour obtenir de l'aide :

- [Guide de démarrage rapide de Kind](https://kind.sigs.k8s.io/docs/user/quick-start)
- [Problèmes avec les certificats SSL dans curl](https://stackoverflow.com/questions/24611640/curl-60-ssl-certificate-problem-unable-to-get-local-issuer-certificate)
- [Erreurs lors de l'exécution de la version de kubectl](https://stackoverflow.com/questions/73866855/i-am-getting-an-error-while-running-kubectl-version-although-i-installed-it-by-f)


# 5. Historique des commandes
```ssh
cd Desktop/
git clone https://github.com/hrhouma/install-docker.git
apt install git
git clone https://github.com/hrhouma/install-docker.git
cd install-docker/
chmod +x install-docker.sh
./install-docker.sh
# For AMD64 / x86_64


[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.22.0/kind-linux-amd64
# For ARM64
[ $(uname -m) = aarch64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.22.0/kind-linux-arm64

chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
kind create cluster

sudo apt-get install ca-certificates
curl -LO -k "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
ls
chmod +x kubectl
mv kubectl /usr/local/bin/
kubectl
kubectl config get-contexts
kind create cluster --config kind-config.yaml
ls
kubectl cluster-info --context kind-kind
reboot
apt install openssh-server
ip a
apt update
apt install curl
ip a
kubectl
curl -LO "https://dl.k8s.io/release/v1.24.7/bin/linux/amd64/kubectl"
curl -LO "https://dl.k8s.io/v1.24.7/bin/linux/amd64/kubectl.sha256"
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
sudo install -o root -g root -m 0755 kubectl /home/$USER/.local/bin/kubectl
kubectl
kubectl config get-contexts
kubectl get nodes -o wide
cat <<EOT > kind-config.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
EOT

ls
kind create cluster --config kind-config.yaml
cat kind-config.yaml
kubectl cluster-info --context kind-kind
kind create cluster --name kind-2 --config kind-config.yaml
ls
cat kind-config.yaml
kubectl config get-contexts
kubectl get nodes -o wide
kubectl get po -o wide
kubectl run nginx --image=nginx --dry-run=client -o yaml > haythem.yaml
kubectl get po -o wide
cat haythem.yaml
kubectl create -f haythem.yaml
kubect get po -o wide
kubectl get po -o wide
kubectl delete -f haythem.yaml
kubectl get po -o wide
kubectl run nginx --image=nginx --dry-run=client -o yaml
kubectl run nginx --image=nginx -- --dry-run=client -o yaml
kubectl delete -f haythem.yaml
kubectl get po -o wide
kubectl run nginx --image=nginx  -o yaml
kubectl get po -o wide
kubect get n
kubectl get nodes
kubectl get namespaces
```
  
# Références pour troubleshooting : 
 
- https://kind.sigs.k8s.io/docs/user/quick-start
- https://stackoverflow.com/questions/24611640/curl-60-ssl-certificate-problem-unable-to-get-local-issuer-certificate
- https://stackoverflow.com/questions/73866855/i-am-getting-an-error-while-running-kubectl-version-although-i-installed-it-by-f

# 6. Annexes - Les contextes dans KIND

## Création d'un fichier de configuration pour le premier cluster
```ssh
cat <<EOT > kind-config-1.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
EOT
```
## Création du premier cluster avec une configuration spécifique
```ssh
kind create cluster --name kind-1 --config kind-config-1.yaml
```
## Création d'un fichier de configuration pour le second cluster
```ssh
cat <<EOT > kind-config-2.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
EOT
```
## Création du second cluster avec la configuration spécifique
```ssh
kind create cluster --name kind-2 --config kind-config-2.yaml
```
## Affichage de tous les contextes disponibles pour vérification
```ssh
kubectl config get-contexts
```
## Basculer vers le contexte du premier cluster (kind-1) et vérifier les infos
```ssh
kubectl config use-context kind-kind-1
kubectl cluster-info --context kind-kind-1
```
## Basculer vers le contexte du second cluster (kind-2) et vérifier les infos
```ssh
kubectl config use-context kind-kind-2
kubectl cluster-info --context kind-kind-2
```
## À ce stade, vous pouvez exécuter des commandes kubectl spécifiques au cluster sélectionné,
## comme la création de déploiements, services, etc.

## Pour basculer à nouveau vers le contexte du premier cluster
```ssh
kubectl config use-context kind-kind-1
```
## Et pour revenir au second
```ssh
kubectl config use-context kind-kind-2
```
## Lorsque vous avez terminé, vous pouvez supprimer les clusters si souhaité
```ssh
kind delete cluster --name kind-1
kind delete cluster --name kind-2
```

  


