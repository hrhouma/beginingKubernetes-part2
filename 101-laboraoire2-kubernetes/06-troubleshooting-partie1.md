# Description du problème:
- J'avais réalisé le laboratoire sur les taintes et tolérances. J'avais tainté miniikube mon seul noeud et oublié d'enlever la tainte
- EN réalisant le laboratoire1, rien ne fonctionnait !
- kubectlt get deployment.apps ==> 0/3
- kubectl get po ==> 0/1
- kubectl logs nom-du-pod ==> rien
- kubectl get events ==> rien de significatif

- Je vous présente une série de troubleshooting utile.

----
# Troubleshooting partie 1
# Étape 1 : Vérifier les Logs des Pods
kubectl logs <nom_du_pod>
# Étape 2 : Vérifier l'État du Pod
kubectl describe pod <nom_du_pod>
# Étape 3 : Assurer que l'Image Docker est Valide
docker run -p 8080:80 votre_nom_utilisateur_docker_hub/mon-application:v1
# Étape 4 : Vérifier les Fichiers YAML
# Étape 5 : Reconstruire et Pousser l'Image Docker
# Construire l'image Docker
docker build -t votre_nom_utilisateur_docker_hub/mon-application:v1 .
# Connexion à Docker Hub
docker login
# Pousser l'image sur Docker Hub
docker push votre_nom_utilisateur_docker_hub/mon-application:v1
# Étape 6 : Redéployer sur Kubernetes
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
# Étape 7 : Vérification Finale
kubectl get pods
kubectl get deployments

# RÉSULTAT !!! ==> Le problème persiste

----
# Troubleshooting partie 2
- J'avais douté que le problème vient du fait que mon dockerfile n'était pas correcte.
- Je me suis dit, regardons le Dockerfile !!!

```Dockerfile
# Utiliser l'image de base d'Apache
FROM httpd:2.4
# Copier le fichier HTML dans le répertoire approprié

COPY ./index.html /usr/local/apache2/htdocs/
```
Est-ce que la configuration n'est pas valide. Ne manque-t-il pas l'étape d'exposition des ports ?

#  Réponse

L'image de base Apache (`httpd:2.4`) n'a pas besoin de commandes spécifiques pour exposer les ports dans le Dockerfile car l'image officielle de `httpd` configure déjà le serveur pour écouter sur le port 80. Cependant, pour plus de clarté et de bonnes pratiques, vous pouvez ajouter l'instruction `EXPOSE` dans votre Dockerfile.

Je me suis proposé de mettre à jour le fichier Dockerfile  :

```dockerfile
# Utiliser l'image de base d'Apache
FROM httpd:2.4

# Copier le fichier HTML dans le répertoire approprié
COPY ./index.html /usr/local/apache2/htdocs/

# Exposer le port 80
EXPOSE 80
```

### Étapes à Suivre :

1. **Mise à jour du Dockerfile :**
   Mettez à jour votre Dockerfile avec l'instruction `EXPOSE 80` :

   ```dockerfile
   # Utiliser l'image de base d'Apache
   FROM httpd:2.4

   # Copier le fichier HTML dans le répertoire approprié
   COPY ./index.html /usr/local/apache2/htdocs/

   # Exposer le port 80
   EXPOSE 80
   ```

2. **Reconstruction de l'Image Docker :**
   Reconstruisez l'image Docker avec le Dockerfile mis à jour :

   ```bash
   docker build -t votre_nom_utilisateur_docker_hub/mon-application:v1 .
   ```

3. **Pousser l'Image Docker :**
   Connectez-vous à Docker Hub et poussez l'image mise à jour :

   ```bash
   docker login
   docker push votre_nom_utilisateur_docker_hub/mon-application:v1
   ```

4. **Mise à Jour et Application des Fichiers YAML :**
   Si vos fichiers `deployment.yaml` et `service.yaml` sont corrects, vous pouvez simplement les réappliquer. Sinon, mettez à jour les fichiers si nécessaire et appliquez-les de nouveau :

   ```yaml
   # deployment.yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: mon-application
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: mon-application
     template:
       metadata:
         labels:
           app: mon-application
       spec:
         containers:
         - name: mon-application
           image: votre_nom_utilisateur_docker_hub/mon-application:v1
           ports:
           - containerPort: 80
   ```

   ```yaml
   # service.yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: mon-application-service
   spec:
     type: NodePort
     selector:
       app: mon-application
     ports:
       - port: 80
         targetPort: 80
         nodePort: 30007
   ```

   Appliquez les fichiers YAML :

   ```bash
   kubectl apply -f deployment.yaml
   kubectl apply -f service.yaml
   ```

5. **Vérification :**
   Vérifiez l'état des pods et des déploiements :

   ```bash
   kubectl get pods
   kubectl get deployments
   ```

6. **Accès à l'Application :**
   Obtenez l'URL de l'application via Minikube :

   ```bash
   minikube service mon-application-service --url
   ```

### Vérifications Supplémentaires :
Si le problème persiste, vérifiez les logs des pods et leur état avec :

```bash
kubectl logs <nom_du_pod>
kubectl describe pod <nom_du_pod>
```

# RÉSULTAT !!! ==> Le problème persiste

----
# Troubleshooting partie 3

- J'utilise la commande `kubectl logs <nom_du_pod>` mais je n'obtiens aucun résultat. Que puis-je faire pour résoudre ce problème ?

## Réponse : 

Si la commande `kubectl logs <nom_du_pod>` ne retourne aucun résultat, il peut y avoir plusieurs raisons pour cela. Voici quelques étapes pour diagnostiquer le problème :

### Étape 1 : Vérifier l'état des pods
Vérifiez l'état de vos pods pour voir s'ils sont en cours d'exécution ou s'ils ont rencontré des erreurs :

```bash
kubectl get pods
```

### Étape 2 : Décrire le pod
Utilisez `kubectl describe pod` pour obtenir plus de détails sur l'état du pod, y compris les événements et les messages d'erreur éventuels :

```bash
kubectl describe pod <nom_du_pod>
```

### Étape 3 : Vérifier les événements Kubernetes
Les événements Kubernetes peuvent fournir des informations utiles sur les raisons pour lesquelles un pod ne fonctionne pas correctement :

```bash
kubectl get events
```

### Étape 4 : Vérifier l'image Docker
Assurez-vous que l'image Docker est correctement construite et poussée. Vous pouvez tester l'image en local pour vous assurer qu'elle fonctionne correctement :

```bash
docker run -p 8080:80 votre_nom_utilisateur_docker_hub/mon-application:v1
```

### Exemple de Script de Diagnostic
Voici un exemple de script pour vérifier les différents états et logs :

```bash
#!/bin/bash

# Nom du pod à diagnostiquer
POD_NAME=$(kubectl get pods -o jsonpath="{.items[0].metadata.name}")

# Vérifier l'état des pods
echo "État des pods:"
kubectl get pods

# Décrire le pod
echo "Description du pod $POD_NAME:"
kubectl describe pod $POD_NAME

# Vérifier les logs du pod
echo "Logs du pod $POD_NAME:"
kubectl logs $POD_NAME

# Vérifier les événements Kubernetes
echo "Événements Kubernetes:"
kubectl get events
```

### Exemples d'Instructions YAML et Dockerfile Corrects
Voici un rappel des fichiers YAML et Dockerfile pour s'assurer qu'ils sont corrects :

**Dockerfile :**

```dockerfile
# Utiliser l'image de base d'Apache
FROM httpd:2.4

# Copier le fichier HTML dans le répertoire approprié
COPY ./index.html /usr/local/apache2/htdocs/

# Exposer le port 80
EXPOSE 80
```

**deployment.yaml :**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mon-application
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mon-application
  template:
    metadata:
      labels:
        app: mon-application
    spec:
      containers:
      - name: mon-application
        image: votre_nom_utilisateur_docker_hub/mon-application:v1
        ports:
        - containerPort: 80
```

**service.yaml :**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mon-application-service
spec:
  type: NodePort
  selector:
    app: mon-application
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30007
```

----
# Troubleshooting partie 4

- Je me suis rappelé que j'avais déjà fait fonctionner le lab de WordPress, mais je n'ai pas tout supprimé. Comment puis-je tout supprimer ?
- C'est quoi les commandes nécessaires ?
## RÉPONSE :
Pour supprimer toutes les ressources Kubernetes associées à votre déploiement WordPress précédent, vous pouvez suivre ces étapes :

### Étape 1 : Lister toutes les ressources
Tout d'abord, listez toutes les ressources pour vous assurer de connaître ce qui existe :

```bash
kubectl get all
```

### Étape 2 : Supprimer des ressources spécifiques
Si vous connaissez les noms des déploiements, services et autres ressources créés pour WordPress, vous pouvez les supprimer spécifiquement. Remplacez `<nom_de_la_ressource>` et `<type_de_ressource>` par les noms et types appropriés :

```bash
kubectl delete deployment <nom_du_deploiement_wordpress>
kubectl delete service <nom_du_service_wordpress>
kubectl delete pvc <nom_du_pvc_wordpress>
kubectl delete pv <nom_du_pv_wordpress>
```

### Étape 3 : Supprimer le namespace (facultatif)
Si vous avez créé un namespace séparé pour votre déploiement WordPress, vous pouvez supprimer tout le namespace, ce qui supprimera toutes les ressources qu'il contient :

```bash
kubectl delete namespace <namespace_wordpress>
```

### Étape 4 : Supprimer toutes les ressources dans le namespace par défaut (si vous n'utilisez pas un namespace séparé)
Pour supprimer toutes les ressources dans le namespace par défaut, vous pouvez utiliser la commande suivante :

```bash
kubectl delete all --all
```

### Étape 5 : Vérifier la suppression
Pour vous assurer que toutes les ressources sont supprimées, listez à nouveau toutes les ressources :

```bash
kubectl get all
```

### Exemples de commandes
Voici un ensemble d'exemples de commandes en supposant que vos ressources sont nommées `wordpress` et que vous utilisez le namespace par défaut :

```bash
# Lister toutes les ressources pour identifier ce qu'il faut supprimer
kubectl get all

# Supprimer le déploiement et le service WordPress
kubectl delete deployment wordpress
kubectl delete service wordpress

# Si vous avez des PVC et PV associés à WordPress
kubectl delete pvc wordpress-pvc
kubectl delete pv wordpress-pv

# Supprimer toutes les ressources dans le namespace par défaut (à utiliser avec prudence)
kubectl delete all --all
```

### Commandes de nettoyage
Enfin, voici les commandes compilées dans un script shell pour plus de facilité d'utilisation :

```bash
#!/bin/bash

# Supprimer le déploiement et le service WordPress
kubectl delete deployment wordpress
kubectl delete service wordpress

# Supprimer les PVC et PV associés à WordPress
kubectl delete pvc wordpress-pvc
kubectl delete pv wordpress-pv

# Supprimer toutes les ressources dans le namespace par défaut (si nécessaire)
kubectl delete all --all

# Vérifier que toutes les ressources sont supprimées
kubectl get all
```

Enregistrez le script ci-dessus sous le nom `cleanup_wordpress.sh`, rendez-le exécutable et exécutez-le :

```bash
chmod +x cleanup_wordpress.sh
./cleanup_wordpress.sh
```

Ces étapes devraient m' aider à supprimer toutes les ressources Kubernetes associées à mon déploiement WordPress précédent.
# RÉSULTAT !!! ==> Le problème persiste

----
# Troubleshooting partie 5
J'ai exécuté la commande suivante puisque kubectl logs nom-du-pod ne faisait rien
- kubectl logs nom-du-pod ==> PAS DE RÉSULAT
- kubectl edit po nom-du-pod

# J'ai eu ce résultat !!!!!!!!!
```bash
# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2024-07-08T17:59:01Z"
  generateName: mon-application-776fcd9bfb-
  labels:
    app: mon-application
    pod-template-hash: 776fcd9bfb
  name: mon-application-776fcd9bfb-84hqk
  namespace: default
  ownerReferences:
  - apiVersion: apps/v1
    blockOwnerDeletion: true
    controller: true
    kind: ReplicaSet
    name: mon-application-776fcd9bfb
    uid: 5ca22525-950f-4832-8dbc-3ae065bc61ac
  resourceVersion: "48843"
  uid: 541f6692-a23f-43c3-bdca-e894cefcf052
spec:
  containers:
  - image: hrehouma1/mon-application:v1
    imagePullPolicy: IfNotPresent
    name: mon-application
    ports:
    - containerPort: 80
      protocol: TCP
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-g5dzm
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  preemptionPolicy: PreemptLowerPriority
  priority: 0
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: default
  serviceAccountName: default
  terminationGracePeriodSeconds: 30
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
    tolerationSeconds: 300
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
    tolerationSeconds: 300
  volumes:
  - name: kube-api-access-g5dzm
    projected:
      defaultMode: 420
      sources:
      - serviceAccountToken:
          expirationSeconds: 3607
          path: token
      - configMap:
          items:
          - key: ca.crt
            path: ca.crt
          name: kube-root-ca.crt
      - downwardAPI:
          items:
          - fieldRef:
              apiVersion: v1
              fieldPath: metadata.namespace
            path: namespace
status:
  conditions:
  - lastProbeTime: null
    lastTransitionTime: "2024-07-08T17:59:01Z"
    message: '0/1 nodes are available: 1 node(s) had untolerated taint {key1: value1}.
      preemption: 0/1 nodes are available: 1 Preemption is not helpful for scheduling.'
    reason: Unschedulable
    status: "False"
    type: PodScheduled
  phase: Pending
  qosClass: BestEffort
```

# RÉPONSE : 


Le message d'erreur indique que le pod ne peut pas être planifié car il y a une taint non tolérée sur le nœud. Voici les étapes pour résoudre ce problème :

### Étape 1 : Vérifiez les Taints sur le Nœud
Vous pouvez vérifier les taints appliquées sur les nœuds avec la commande suivante :

```bash
kubectl describe nodes
```

Cherchez les sections "Taints" dans la sortie pour voir quelles taints sont appliquées.

### Étape 2 : Ajoutez une Tolérance pour la Taint
Ajoutez une tolérance pour la taint dans la spécification du pod pour permettre au pod d'être planifié sur le nœud avec la taint.

Voici comment ajouter une tolérance dans votre fichier `deployment.yaml` :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mon-application
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mon-application
  template:
    metadata:
      labels:
        app: mon-application
    spec:
      containers:
      - name: mon-application
        image: votre_nom_utilisateur_docker_hub/mon-application:v1
        ports:
        - containerPort: 80
      tolerations:
      - key: "key1"
        operator: "Equal"
        value: "value1"
        effect: "NoSchedule"
```

Assurez-vous de remplacer `key1` et `value1` par les valeurs exactes de la taint appliquée sur le nœud.

### Étape 3 : Appliquer les Modifications
Appliquez les modifications au déploiement :

```bash
kubectl apply -f deployment.yaml
```

### Étape 4 : Vérifiez l'État du Pod
Vérifiez si le pod est maintenant planifié et s'il est en cours d'exécution :

```bash
kubectl get pods
```

### Supprimer les Taints (Optionnel)
Si vous préférez supprimer la taint du nœud pour permettre au pod d'être planifié sans ajouter de tolérance, vous pouvez le faire avec la commande suivante :

1. Identifiez le nœud sur lequel la taint est appliquée :

```bash
kubectl get nodes
```

2. Supprimez la taint du nœud :

```bash
kubectl taint nodes <node-name> key1=value1:NoSchedule-
```

Remplacez `<node-name>` par le nom du nœud et `key1=value1:NoSchedule` par la taint spécifique que vous souhaitez supprimer.

### Conclusion
Ces étapes devraient vous aider à résoudre le problème de taint et permettre à vos pods d'être planifiés correctement sur les nœuds disponibles. Si vous rencontrez toujours des problèmes, veuillez fournir les détails supplémentaires pour une analyse plus approfondie.

----
# Troubleshooting partie 6

# comment enleve cette tainte de minikube ?

# Réponse : 

Pour enlever une taint de Minikube, vous pouvez suivre ces étapes :

### Étape 1 : Identifier le nœud de Minikube
Tout d'abord, identifiez le nom du nœud de Minikube :

```bash
kubectl get nodes
```

### Étape 2 : Vérifier les taints sur le nœud
Ensuite, décrivez le nœud pour vérifier les taints appliquées :

```bash
kubectl describe node <node-name>
```

Remplacez `<node-name>` par le nom du nœud de Minikube que vous avez trouvé à l'étape précédente. Recherchez la section "Taints" dans la sortie.

### Étape 3 : Supprimer la taint
Pour supprimer la taint du nœud, utilisez la commande suivante :

```bash
kubectl taint nodes <node-name> <key>:<value>:<effect>-
```

Remplacez `<node-name>` par le nom du nœud et `<key>:<value>:<effect>` par les détails de la taint que vous souhaitez supprimer. Par exemple, si la taint est `key1=value1:NoSchedule`, la commande serait :

```bash
kubectl taint nodes <node-name> key1=value1:NoSchedule-
```

### Exemple Complet
Supposons que le nom du nœud soit `minikube` et que la taint soit `key1=value1:NoSchedule`. Voici les commandes complètes :

1. Identifier le nœud :

    ```bash
    kubectl get nodes
    ```

2. Vérifier les taints sur le nœud :

    ```bash
    kubectl describe node minikube
    ```

3. Supprimer la taint :

    ```bash
    kubectl taint nodes minikube key1=value1:NoSchedule-
    ```

### Vérification
Après avoir supprimé la taint, vérifiez à nouveau les nœuds pour vous assurer que la taint a été supprimée :

```bash
kubectl describe node minikube
```

### Vérification Finale de l'État du Pod
Vérifiez l'état de votre pod pour voir s'il est désormais planifié et en cours d'exécution :

```bash
kubectl get pods
```

Ces étapes devraient vous aider à supprimer la taint du nœud de Minikube et permettre à vos pods d'être planifiés correctement.


# Réponse finale : 


    ```bash
    kubectl taint nodes minikube key1=value1:NoSchedule-
    ```

  ```bash
    kubectl get po 
  ```

# ça fonctionne !!!!!!!!!!!!!!!
