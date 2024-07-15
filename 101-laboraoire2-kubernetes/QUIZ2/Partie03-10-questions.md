### Quiz sur Ingress dans Kubernetes

1. **Qu'est-ce qu'une Ingress Resource dans Kubernetes ?**
   - A. Un type de service qui expose des ports internes du cluster à l'extérieur.
   - B. Un objet qui contient des règles pour rediriger le trafic HTTP ou HTTPS vers des services Kubernetes.
   - C. Un outil de surveillance pour les clusters Kubernetes.
   - D. Un conteneur qui exécute des applications web dans Kubernetes.
   - E. Une base de données pour stocker les configurations.

2. **Quel est le rôle principal d'un Ingress Controller dans Kubernetes ?**
   - A. Gérer les volumes de stockage dans le cluster.
   - B. Configurer un serveur proxy pour appliquer les règles définies dans les Ingress Resources.
   - C. Exécuter des tâches planifiées à intervalles réguliers.
   - D. Monitorer les performances des pods dans le cluster.
   - E. Déployer des mises à jour d'applications.

3. **Pourquoi est-il important de créer un namespace dédié pour l'Ingress Controller ?**
   - A. Pour faciliter la gestion et l'isolation des ressources liées à l'Ingress Controller.
   - B. Pour réduire la consommation de mémoire du cluster.
   - C. Pour augmenter la vitesse de déploiement des applications.
   - D. Pour améliorer la sécurité des pods.
   - E. Pour tester de nouvelles fonctionnalités sans affecter le reste du cluster.

4. **Quelle commande Helm est utilisée pour installer le NGINX Ingress Controller ?**
   - A. `helm repo add nginx-controller`
   - B. `helm install nginx-ingress`
   - C. `helm install my-ingress-nginx ingress-nginx/ingress-nginx --namespace ingress-nginx`
   - D. `helm deploy ingress-nginx`
   - E. `helm create ingress-nginx`

5. **Quel est l'objectif principal de l'utilisation de SSL/TLS avec une ressource Ingress ?**
   - A. Augmenter la vitesse de connexion.
   - B. Permettre aux utilisateurs de se connecter sans mot de passe.
   - C. Chiffrer le trafic pour sécuriser les données en transit.
   - D. Réduire la charge sur les serveurs.
   - E. Faciliter la gestion des mises à jour.

6. **Quelle annotation est utilisée pour configurer une authentification de base avec NGINX Ingress ?**
   - A. `nginx.ingress.kubernetes.io/auth-type: "oauth"`
   - B. `nginx.ingress.kubernetes.io/auth-secret: "basic-auth"`
   - C. `nginx.ingress.kubernetes.io/auth-type: "basic"`
   - D. `nginx.ingress.kubernetes.io/auth-method: "token"`
   - E. `nginx.ingress.kubernetes.io/auth-type: "jwt"`

7. **Quelle commande permet de vérifier les services LoadBalancer dans le namespace ingress-nginx ?**
   - A. `kubectl get services -n ingress-nginx`
   - B. `kubectl get pods -n ingress-nginx`
   - C. `kubectl describe services -n ingress-nginx`
   - D. `kubectl --namespace ingress-nginx get services -o wide -w ingress-nginx-controller`
   - E. `kubectl get svc -A`

8. **Que permet de faire l'annotation `nginx.ingress.kubernetes.io/rewrite-target` dans une ressource Ingress ?**
   - A. Bloquer l'accès à certaines URL.
   - B. Rediriger les requêtes HTTP vers HTTPS.
   - C. Réécrire le chemin de la requête avant de la transmettre au backend.
   - D. Activer la compression des réponses HTTP.
   - E. Changer le nom de domaine de la requête.

9. **Quel est le principal avantage de l'utilisation d'un Ingress Controller ?**
   - A. Il permet de créer des conteneurs plus rapidement.
   - B. Il facilite la gestion centralisée des règles de routage et d'accès aux services.
   - C. Il améliore les performances des applications web.
   - D. Il permet de surveiller les performances des pods en temps réel.
   - E. Il simplifie la gestion des volumes de stockage.

10. **Qu'est-ce que l'annotation `nginx.ingress.kubernetes.io/add-base-url` fait dans une ressource Ingress ?**
    - A. Elle ajoute une URL de base aux requêtes avant de les envoyer au backend.
    - B. Elle redirige les requêtes vers une URL alternative en cas d'échec.
    - C. Elle bloque les requêtes provenant de certaines IP.
    - D. Elle active l'authentification multi-facteurs pour les requêtes entrantes.
    - E. Elle compresse les réponses HTTP pour les rendre plus rapides.
