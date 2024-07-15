# Cours Complet sur Ingress dans Kubernetes

### Table des Matières
1. [Introduction à Ingress](#introduction-à-ingress)
2. [Concepts Clés](#concepts-clés)
   - [Qu'est-ce qu'une Ingress Resource ?](#quest-ce-quune-ingress-resource-)
   - [Qu'est-ce qu'un Ingress Controller ?](#quest-ce-quun-ingress-controller-)
3. [Architecture de l'Ingress](#architecture-de-lingress)
4. [Configuration de Base](#configuration-de-base)
5. [Exemples Avancés](#exemples-avancés)
   - [Règles de Routage](#règles-de-routage)
   - [Terminaison SSL/TLS](#terminaison-ssltls)
   - [Redirections et Réécritures](#redirections-et-réécritures)
   - [Authentification](#authentification)
6. [Déploiement d'un Ingress Controller](#déploiement-dun-ingress-controller)
   - [Installation de l'Ingress Controller NGINX](#installation-de-lingress-controller-nginx)
   - [Installation de l'Ingress Controller Traefik](#installation-de-lingress-controller-traefik)
7. [Pratiques de Sécurité](#pratiques-de-sécurité)
8. [Monitoring et Dépannage](#monitoring-et-dépannage)
9. [Conclusion et Bonnes Pratiques](#conclusion-et-bonnes-pratiques)
10. [Exercices Pratiques](#exercices-pratiques)

---

## Introduction à Ingress
[Retour en haut](#cours-complet-sur-ingress-dans-kubernetes)

**Ingress** dans Kubernetes est un objet qui gère l'accès externe aux services dans un cluster. Imaginez que vous avez plusieurs applications web (services) dans votre cluster Kubernetes, et que vous voulez que des utilisateurs sur Internet puissent y accéder. Ingress agit comme un portier, redirigeant les utilisateurs vers le bon service en fonction des règles que vous définissez.

### Objectif
L'objectif principal de l'Ingress est de centraliser la gestion des routes d'accès aux services de votre cluster Kubernetes, permettant une meilleure gestion et une configuration plus simple des accès externes.

## Concepts Clés
[Retour en haut](#cours-complet-sur-ingress-dans-kubernetes)

### Qu'est-ce qu'une Ingress Resource ?
[Retour en haut](#cours-complet-sur-ingress-dans-kubernetes)

Une **Ingress Resource** est un objet dans Kubernetes qui contient des règles pour rediriger le trafic HTTP ou HTTPS vers vos services. Pensez-y comme un panneau de signalisation indiquant quel chemin prendre pour atteindre chaque service.

### Objectif
L'objectif de l'Ingress Resource est de définir des règles précises pour diriger le trafic entrant vers les services appropriés, simplifiant ainsi la gestion du routage.

### Qu'est-ce qu'un Ingress Controller ?
[Retour en haut](#cours-complet-sur-ingress-dans-kubernetes)

Un **Ingress Controller** est un composant qui lit les règles définies dans les Ingress Resources et configure un serveur proxy (comme NGINX) pour appliquer ces règles. Sans un Ingress Controller, les Ingress Resources ne peuvent pas fonctionner. Le Controller est comme un chef de la circulation qui s'assure que les voitures suivent les panneaux de signalisation.

### Objectif
L'objectif de l'Ingress Controller est de mettre en œuvre les règles définies dans les Ingress Resources et de gérer le trafic entrant en conséquence.

## Architecture de l'Ingress
[Retour en haut](#cours-complet-sur-ingress-dans-kubernetes)

L'architecture d'un Ingress comprend :
- **Client**: Utilisateur ou application qui envoie des requêtes HTTP/HTTPS.
- **Ingress Controller**: Gère et applique les règles de routage définies dans les Ingress Resources.
- **Service**: Point d'entrée pour un ensemble de pods dans Kubernetes.
- **Pod**: Conteneur ou ensemble de conteneurs exécutant vos applications.

### Objectif
L'objectif de cette architecture est de fournir une vue d'ensemble claire de la manière dont le trafic est géré et routé dans un cluster Kubernetes.

## Configuration de Base
[Retour en haut](#cours-complet-sur-ingress-dans-kubernetes)

Voici comment configurer un Ingress basique pour rediriger le trafic vers un service :

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: example-service
            port:
              number: 80
```

### Objectif
Cet exemple montre comment créer une règle simple qui dirige tout le trafic de `example.com` vers le service `example-service` sur le port 80. Cela permet de centraliser et de simplifier la gestion des accès externes.

## Exemples Avancés
[Retour en haut](#cours-complet-sur-ingress-dans-kubernetes)

### Règles de Routage
[Retour en haut](#cours-complet-sur-ingress-dans-kubernetes)

Vous pouvez définir des règles pour diriger le trafic en fonction du chemin ou de l'hôte :

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: advanced-ingress
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /app1
        pathType: Prefix
        backend:
          service:
            name: app1-service
            port:
              number: 80
      - path: /app2
        pathType: Prefix
        backend:
          service:
            name: app2-service
            port:
              number: 80
```

### Objectif
Cet exemple montre comment configurer des règles de routage basées sur le chemin de l'URL, permettant de diriger les utilisateurs vers différents services en fonction du chemin demandé (`/app1` vers `app1-service` et `/app2` vers `app2-service`).

### Terminaison SSL/TLS
[Retour en haut](#cours-complet-sur-ingress-dans-kubernetes)

Pour sécuriser les connexions avec SSL/TLS :

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: tls-ingress
spec:
  tls:
  - hosts:
    - example.com
    secretName: tls-secret
  rules:
  - host: example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: tls-service
            port:
              number: 443
```

### Objectif
Ce code montre comment configurer l'Ingress pour utiliser des certificats SSL/TLS, garantissant que le trafic entre les utilisateurs et le service est chiffré et sécurisé.

### Redirections et Réécritures
[Retour en haut](#cours-complet-sur-ingress-dans-kubernetes)

Pour rediriger et réécrire des chemins :

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: rewrite-ingress
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /old-path
        pathType: Prefix
        backend:
          service:
            name: new-service
            port:
              number: 80
        pathRewrite:
          /old-path: /new-path
```

### Objectif
Cet exemple montre comment rediriger les requêtes de `/old-path` vers `/new-path` sur le service `new-service`, permettant une gestion flexible et dynamique des chemins d'URL.

### Authentification
[Retour en haut](#cours-complet-sur-ingress-dans-kubernetes)

L'authentification peut être configurée via des annotations spécifiques à l'Ingress Controller, comme NGINX. Par exemple, pour NGINX :

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: auth-ingress
  annotations:
    nginx.ingress.kubernetes.io/auth-type: "basic"
    nginx.ingress.kubernetes.io/auth-secret: "auth-secret"
    nginx.ingress.kubernetes.io/auth-realm: "Authentication Required"
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: auth-service
            port:
              number: 80
```

### Objectif
Ce code montre comment configurer une authentification de base pour protéger les accès à vos services, ajoutant une couche de sécurité supplémentaire.

## Déploiement d'un Ingress Controller
[Retour en haut](#cours-complet-sur-ingress-dans-kubernetes)

### Installation de l'Ingress Controller NGINX
[Retour en haut](#cours-complet-sur-ingress-dans-kubernetes)

1. Ajouter le dépôt Helm pour NGINX Ingress Controller :

```sh
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```

### Objectif
Ajouter le dépôt Helm permet d'accéder aux packages Helm pour installer NGINX Ingress Controller facilement.

2. Créer un namespace pour l'Ingress Controller :

```sh
kubectl create namespace ingress-nginx
```


### Objectif
Créer un namespace dédié pour isoler les ressources de l'Ingress Controller, assurant une meilleure organisation et gestion des ressources dans le cluster.

3. Installer l'Ingress Controller avec Helm :

```sh
helm install my-ingress-nginx ingress-nginx/ingress-nginx --namespace ingress-nginx
```

### Objectif
Cette commande installe NGINX Ingress Controller dans le namespace `ingress-nginx`, en utilisant Helm pour simplifier le déploiement et la gestion des composants Kubernetes.

4. Vérifier le service LoadBalancer :

```sh
kubectl --namespace ingress-nginx get services -o wide -w ingress-nginx-controller
```

### Objectif
Vérifier que le service LoadBalancer est en cours d'exécution et qu'il est correctement configuré pour diriger le trafic externe vers l'Ingress Controller.

### Installation de l'Ingress Controller Traefik
[Retour en haut](#cours-complet-sur-ingress-dans-kubernetes)

1. Ajouter le dépôt Helm pour Traefik :

```sh
helm repo add traefik https://helm.traefik.io/traefik
```

### Objectif
Ajouter le dépôt Helm de Traefik permet d'accéder aux packages Helm pour installer Traefik Ingress Controller facilement.

2. Installer Traefik avec Helm :

```sh
helm install traefik traefik/traefik --namespace kube-system
```

### Objectif
Cette commande installe Traefik Ingress Controller dans le namespace `kube-system`, en utilisant Helm pour simplifier le déploiement et la gestion des composants Kubernetes.

## Pratiques de Sécurité
[Retour en haut](#cours-complet-sur-ingress-dans-kubernetes)

- Utilisez des certificats SSL/TLS pour chiffrer le trafic.
- Mettez en place des règles d'authentification et d'autorisation.
- Restreignez les accès en utilisant des politiques réseau.

### Objectif
Ces pratiques de sécurité visent à protéger les données en transit, contrôler les accès aux services et réduire les risques d'attaques réseau.

## Monitoring et Dépannage
[Retour en haut](#cours-complet-sur-ingress-dans-kubernetes)

- Utilisez des outils comme Prometheus et Grafana pour surveiller les métriques de votre Ingress Controller.
- Vérifiez les logs des pods de l'Ingress Controller pour diagnostiquer les problèmes.

### Objectif
Surveiller et dépanner les Ingress Controllers permet de maintenir une haute disponibilité et une performance optimale des services exposés.

## Conclusion et Bonnes Pratiques
[Retour en haut](#cours-complet-sur-ingress-dans-kubernetes)

- Testez vos configurations Ingress dans un environnement de développement avant de les déployer en production.
- Tenez vos Ingress Controllers à jour pour bénéficier des dernières fonctionnalités et correctifs de sécurité.

### Objectif
Les bonnes pratiques assurent une gestion efficace et sécurisée des Ingress, minimisant les erreurs en production et maximisant la robustesse de l'infrastructure.

## Exercices Pratiques
[Retour en haut](#cours-complet-sur-ingress-dans-kubernetes)

1. **Créer un Ingress Resource pour une application web avec des chemins multiples.**
   - **Objectif**: Apprendre à diriger le trafic vers différents services en fonction du chemin de l'URL.

   ```yaml
   apiVersion: networking.k8s.io/v1
   kind: Ingress
   metadata:
     name: multi-path-ingress
   spec:
     rules:
     - host: myapp.com
       http:
         paths:
         - path: /app1
           pathType: Prefix
           backend:
             service:
               name: app1-service
               port:
                 number: 80
         - path: /app2
           pathType: Prefix
           backend:
             service:
               name: app2-service
               port:
                 number: 80
   ```

2. **Configurer un Ingress Resource avec SSL/TLS termination.**
   - **Objectif**: Sécuriser les connexions avec des certificats SSL/TLS.

   ```yaml
   apiVersion: networking.k8s.io/v1
   kind: Ingress
   metadata:
     name: secure-ingress
   spec:
     tls:
     - hosts:
       - secureapp.com
       secretName: tls-secret
     rules:
     - host: secureapp.com
       http:
         paths:
         - path: /
           pathType: Prefix
           backend:
             service:
               name: secure-service
               port:
                 number: 443
   ```

3. **Déployer et configurer un NGINX Ingress Controller dans un cluster Kubernetes.**
   - **Objectif**: Apprendre à installer et configurer un Ingress Controller pour gérer le trafic entrant.

   Suivez les étapes décrites dans la section [Installation de l'Ingress Controller NGINX](#installation-de-lingress-controller-nginx).

---

### Détails et Explications Approfondies

#### Déploiement d'un NGINX Ingress Controller
[Retour en haut](#cours-complet-sur-ingress-dans-kubernetes)

##### Utilisation de Helm

1. Ajouter le dépôt Helm pour NGINX Ingress Controller :

```sh
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```

### Objectif
Ajouter le dépôt Helm permet d'accéder aux packages Helm pour installer NGINX Ingress Controller facilement.

2. Créer un namespace pour l'Ingress Controller :

```sh
kubectl create namespace ingress-nginx
```

### Objectif
Créer un namespace dédié pour isoler les ressources de l'Ingress Controller, assurant une meilleure organisation et gestion des ressources dans le cluster.

3. Installer l'Ingress Controller avec Helm :

```sh
helm install my-ingress-nginx ingress-nginx/ingress-nginx --namespace ingress-nginx
```

### Objectif
Cette commande installe NGINX Ingress Controller dans le namespace `ingress-nginx`, en utilisant Helm pour simplifier le déploiement et la gestion des composants Kubernetes.

4. Vérifier le service LoadBalancer :

```sh
kubectl --namespace ingress-nginx get services -o wide -w ingress-nginx-controller
```

### Objectif
Vérifier que le service LoadBalancer est en cours d'exécution et qu'il est correctement configuré pour diriger le trafic externe vers l'Ingress Controller.

##### Utilisation de Manifestes YAML

1. Créer un namespace pour l'Ingress Controller :

```sh
kubectl create namespace ingress-nginx
```

### Objectif
Créer un namespace dédié pour isoler les ressources de l'Ingress Controller.

2. Déployer l'Ingress Controller avec un fichier YAML :

```sh
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.10.0/deploy/static/provider/aws/deploy.yaml -n ingress-nginx
```

### Objectif
Déployer l'Ingress Controller en utilisant un fichier YAML permet d'avoir un contrôle fin sur les composants déployés et de personnaliser la configuration selon les besoins spécifiques.

Cela crée les déploiements, services, ConfigMaps et autres ressources nécessaires pour exécuter l'Ingress Controller NGINX.

#### Test du NGINX Ingress Controller
[Retour en haut](#cours-complet-sur-ingress-dans-kubernetes)

1. Créer un namespace pour les applications de test :

```sh
kubectl create namespace ingress-testing
```

### Objectif
Créer un namespace dédié pour isoler les applications de test, assurant une meilleure organisation et gestion des ressources dans le cluster.

2. Déployer les applications de test (par exemple, une application de blogging et une application de streaming) :

```sh
kubectl -n ingress-testing apply -f https://raw.githubusercontent.com/anveshmuppeda/kubernetes-ingress/main/nginx-ingress/hands-on/blogging-application/deploy.yaml
kubectl -n ingress-testing apply -f https://raw.githubusercontent.com/anveshmuppeda/kubernetes-ingress/main/nginx-ingress/hands-on/streaming-application/deploy.yaml
```

### Objectif
Déployer des applications de test permet de vérifier le fonctionnement de l'Ingress Controller et de s'assurer qu'il route correctement le trafic vers les bons services.

3. Vérifier les pods et les services :

```sh
kubectl get pods,svc -n ingress-testing
```

### Objectif
Vérifier que les pods et les services sont en cours d'exécution et qu'ils sont correctement configurés.

4. Créer et appliquer les ressources Ingress pour les applications de test :

##### Ingress pour l'application de blogging

Créer un fichier `blog-ingress.yaml` :

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: blogging-ingress
  namespace: ingress-testing
  annotations:
    nginx.ingress.kubernetes.io/add-base-url: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  ingressClassName: nginx
  rules:
  - host: "http://<your-domain.com>"
    http:
      paths:
      - path: /blogs/
        pathType: Prefix
        backend:
          service:
            name: blogging-svc
            port:
              number: 80
```

### Objectif
Créer une ressource Ingress pour l'application de blogging permet de diriger le trafic vers le service `blogging-svc` en fonction du chemin `/blogs/`.

Appliquer le fichier :

```sh
kubectl apply -f blog-ingress.yaml -n ingress-testing
```

##### Ingress pour

 l'application de streaming

Créer un fichier `stream-ingress.yaml` :

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: streaming-ingress
  namespace: ingress-testing
  annotations:
    nginx.ingress.kubernetes.io/add-base-url: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  ingressClassName: nginx
  rules:
  - host: "http://<your-domain.com>"
    http:
      paths:
      - path: /streaming/
        pathType: Prefix
        backend:
          service:
            name: streaming-svc
            port:
              number: 80
```

### Objectif
Créer une ressource Ingress pour l'application de streaming permet de diriger le trafic vers le service `streaming-svc` en fonction du chemin `/streaming/`.

Appliquer le fichier :

```sh
kubectl apply -f stream-ingress.yaml -n ingress-testing
```

5. Vérifier les ressources Ingress :

```sh
kubectl get ingress -n ingress-testing
```

### Objectif
Vérifier que les ressources Ingress sont correctement créées et configurées.

6. Accéder aux applications via les URLs définies :

- Application de blogging : `http://<loadbalancer-url>/blogs/`
- Application de streaming : `http://<loadbalancer-url>/streaming/`

### Objectif
S'assurer que le trafic est correctement routé vers les applications de test, en utilisant les chemins définis dans les ressources Ingress.

Cette configuration permet de router efficacement le trafic entrant vers les applications cibles dans votre cluster Kubernetes, offrant une expérience utilisateur fluide et bien structurée.

---

En espérant que ce cours détaillé sur Ingress dans Kubernetes vous sera utile pour maîtriser la gestion de l'accès externe dans Kubernetes. 
