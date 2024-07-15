### Quiz - Concepts Avancés de Kubernetes

#### Instructions
Répondez aux questions suivantes en choisissant la meilleure réponse parmi les options fournies. Chaque question porte sur un concept avancé de Kubernetes. Prenez le temps de bien lire chaque question avant de répondre.

---

#### Question 1 : Création d'un PersistentVolume

**1.1** Quel fichier YAML correctement configure un PersistentVolume nommé `app-data` avec une capacité de 2Gi, un mode d'accès `ReadWriteOnce`, et un type de volume `hostPath` à l'emplacement `/srv/app-data` ?

A. 
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: app-data
spec:
  capacity:
    storage: 2Gi
  accessModes:
  - ReadOnlyMany
  hostPath:
    path: "/srv/app-data"
```

B. 
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: app-data
spec:
  capacity:
    storage: 2Gi
  accessModes:
  - ReadWriteOnce
  hostPath:
    path: "/srv/app-data"
```

C. 
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: app-data
spec:
  capacity:
    storage: 1Gi
  accessModes:
  - ReadWriteMany
  hostPath:
    path: "/srv/app-data"
```

D. 
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: app-data
spec:
  capacity:
    storage: 2Gi
  accessModes:
  - ReadWriteOnce
  nfs:
    path: "/srv/app-data"
```

---

#### Question 2 : Création d'un PersistentVolumeClaim et d'un Pod

**2.1** Quel fichier YAML configure correctement un PersistentVolumeClaim nommé `app-data-claim` avec une capacité de 5Gi et un mode d'accès `ReadWriteOnce` ?

A.
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: app-data-claim
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
```

B.
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: app-data-claim
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

C.
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: app-data-claim
spec:
  accessModes:
  - ReadOnlyMany
  resources:
    requests:
      storage: 5Gi
```

D.
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: app-data-claim
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
```

**2.2** Quel fichier YAML configure correctement un Pod nommé `app-pod` qui monte le PersistentVolumeClaim `app-data-claim` sur `/usr/share/nginx/html` et utilise l'image `nginx` ?

A.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - mountPath: "/usr/share/nginx/html"
      name: app-volume
  volumes:
  - name: app-volume
    persistentVolumeClaim:
      claimName: app-data-claim
```

B.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app-container
    image: nginx
    volumeMounts:
    - mountPath: "/usr/share/nginx/html"
      name: app-volume
  volumes:
  - name: app-volume
    persistentVolumeClaim:
      claimName: app-data-claim
```

C.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app-container
    image: nginx
    volumeMounts:
    - mountPath: "/usr/share/nginx/html"
      name: pvc-volume
  volumes:
  - name: pvc-volume
    persistentVolumeClaim:
      claimName: app-claim
```

D.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app-container
    image: nginx
    volumeMounts:
    - mountPath: "/var/www/html"
      name: app-volume
  volumes:
  - name: app-volume
    persistentVolumeClaim:
      claimName: app-data-claim
```

---

#### Question 3 : Création d'un ConfigMap et d'un Pod utilisant ce ConfigMap

**3.1** Quel fichier YAML configure correctement un ConfigMap nommé `web-config` avec une clé `index.html` et une valeur `Hello, Kubernetes!` ?

A.
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: web-config
data:
  index.html: |
    Hello, Kubernetes!
```

B.
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: web-config
data:
  index.html: Hello, Kubernetes!
```

C.
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: web-config
data:
  index.html: |
    <html>Hello, Kubernetes!</html>
```

D.
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: web-config
data:
  index.html: |
    Hello, Kubernetes!
  style.css: |
    body { color: blue; }
```

**3.2** Quel fichier YAML configure correctement un Pod nommé `web-server` qui utilise le ConfigMap `web-config` pour servir du contenu statique à partir du chemin `/usr/share/nginx/html` ?

A.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-server
spec:
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - name: web-config-volume
      mountPath: /usr/share/nginx/html
      subPath: index.html
  volumes:
  - name: web-config-volume
    configMap:
      name: web-config
```

B.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-server
spec:
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - name: config-volume
      mountPath: /usr/share/nginx/html
      subPath: index.html
  volumes:
  - name: config-volume
    configMap:
      name: web-config
```

C.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-server
spec:
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - name: web-config-volume
      mountPath: /var/www/html
      subPath: index.html
  volumes:
  - name: web-config-volume
    configMap:
      name: web-config
```

D.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-server
spec:
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - name: web-volume
      mountPath: /usr/share/nginx/html
  volumes:
  - name: web-volume
    configMap:
      name: web-config
```

---

#### Question 4 : Planification d'un Pod avec un Sélecteur de Nœud

**4.1** Quelle commande permet d'étiqueter un nœud avec l'étiquette `disk=spinning` ?

A.
```bash
kubectl label node <node-name> disk=spinning
```

B.
```bash
kubectl label nodes <node-name> disk=spinning
```

C.
```bash
kubectl label node <node-name> disk=spinning-disk
```

D.
```bash
kubectl label nodes <node-name> spinning=disk
```

**4.2** Quel fichier YAML configure correctement un Pod nommé `nginx-kusc00401` avec l'image `nginx` et un sélecteur de nœud `disk=spinning` ?

A.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-kusc00401
spec:
  containers:
  - name: nginx
    image: nginx
  nodeSelector:
    disk: spinning
```

B.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-kusc00401
spec:
  containers:
  - name: nginx
    image: nginx
  nodeSelector:
    disk: spinning-disk
```

C.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-kusc00401
spec:
  containers:
  - name: nginx
    image: nginx
  nodeSelector:
    spinning: disk
```

D.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-kusc00401
spec:
  containers:
  - name: nginx
    image: nginx
  node

Selector:
    type: spinning
```

---

#### Question 5 : Mise à l'échelle d'un Déploiement

**5.1** Quelle commande permet de mettre à l'échelle le déploiement `guestbook` à 5 répliques ?

A.
```bash
kubectl scale --replicas=5 guestbook
```

B.
```bash
kubectl scale --replicas=5 deployment/guestbook
```

C.
```bash
kubectl scale replicas=5 deployment/guestbook
```

D.
```bash
kubectl scale deployment guestbook --replicas=5
```

---

#### Question 6 : Création d'une NetworkPolicy

**6.1** Quel fichier YAML configure correctement une NetworkPolicy nommée `allow-port-from-namespace` dans le namespace `echo` pour permettre aux pods du namespace `internal` de se connecter au port 9200/tcp des pods dans le namespace `echo` ?

A.
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-port-from-namespace
  namespace: echo
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          kubernetes.io/metadata.name: internal
    ports:
    - protocol: TCP
      port: 9200
```

B.
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-port-from-namespace
  namespace: echo
spec:
  podSelector:
    matchLabels:
      app: echo-app
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          kubernetes.io/metadata.name: internal
    ports:
    - protocol: TCP
      port: 9200
```

C.
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-port-from-namespace
  namespace: echo
spec:
  podSelector: {}
  policyTypes:
  - Egress
  egress:
  - to:
    - namespaceSelector:
        matchLabels:
          kubernetes.io/metadata.name: internal
    ports:
    - protocol: TCP
      port: 9200
```

D.
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-port-from-namespace
  namespace: internal
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          kubernetes.io/metadata.name: echo
    ports:
    - protocol: TCP
      port: 9200
```

---

### Réponses attendues
Pour chaque question, cochez la réponse correcte parmi les options fournies. Bonne chance !

# Annexe : 


### Cours : Concepts Avancés de Kubernetes

Bienvenue à ce cours sur les concepts avancés de Kubernetes. Nous allons couvrir plusieurs sujets clés, tels que les PersistentVolumes, PersistentVolumeClaims, ConfigMaps, sélecteurs de nœud, mise à l'échelle des déploiements, et NetworkPolicies. Ce cours est conçu pour vous aider à comprendre ces concepts de A à Z de manière simple et détaillée. À la fin du cours, vous serez en mesure de répondre aux questions du quiz avec confiance.

---

#### 1. PersistentVolume (PV)

**Qu'est-ce qu'un PersistentVolume ?**
Un PersistentVolume (PV) est un stockage alloué par l'administrateur du cluster. Il est un peu comme une clé USB que vous pouvez brancher sur différents ordinateurs (pods) dans votre cluster Kubernetes.

**Pourquoi avons-nous besoin de PersistentVolumes ?**
Les PV sont nécessaires pour stocker les données de manière persistante, même si les pods sont supprimés ou recréés. Cela garantit que les données ne sont pas perdues lorsque les conteneurs sont redémarrés.

**Exemple de création d'un PersistentVolume :**
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: app-data
spec:
  capacity:
    storage: 2Gi
  accessModes:
  - ReadWriteOnce
  hostPath:
    path: "/srv/app-data"
```

- `apiVersion: v1` : La version de l'API Kubernetes utilisée.
- `kind: PersistentVolume` : Le type de ressource.
- `metadata` : Les informations sur le PV, comme son nom.
- `spec` : Les spécifications du PV.
  - `capacity` : La capacité de stockage (2Gi).
  - `accessModes` : Le mode d'accès (ReadWriteOnce, ce qui signifie qu'un seul nœud peut monter le volume en écriture à la fois).
  - `hostPath` : Le chemin sur l'hôte où le volume est stocké.

---

#### 2. PersistentVolumeClaim (PVC)

**Qu'est-ce qu'un PersistentVolumeClaim ?**
Un PersistentVolumeClaim (PVC) est une demande de stockage par un utilisateur. Les PVC sont liés aux PV correspondants pour fournir le stockage demandé aux pods.

**Pourquoi avons-nous besoin de PersistentVolumeClaims ?**
Les PVC permettent aux utilisateurs de demander des volumes sans avoir à connaître les détails de l'infrastructure sous-jacente.

**Exemple de création d'un PersistentVolumeClaim :**
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: app-data-claim
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

- `apiVersion: v1` : La version de l'API Kubernetes utilisée.
- `kind: PersistentVolumeClaim` : Le type de ressource.
- `metadata` : Les informations sur le PVC, comme son nom.
- `spec` : Les spécifications du PVC.
  - `accessModes` : Le mode d'accès (ReadWriteOnce).
  - `resources.requests.storage` : La capacité demandée (5Gi).

---

#### 3. Utilisation des PersistentVolumeClaims dans les Pods

**Comment utiliser un PVC dans un pod ?**
Pour utiliser un PVC dans un pod, vous devez le monter en tant que volume.

**Exemple de configuration d'un Pod utilisant un PVC :**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app-container
    image: nginx
    volumeMounts:
    - mountPath: "/usr/share/nginx/html"
      name: app-volume
  volumes:
  - name: app-volume
    persistentVolumeClaim:
      claimName: app-data-claim
```

- `containers` : Les conteneurs dans le pod.
  - `volumeMounts` : Montages de volumes pour les conteneurs.
    - `mountPath` : Le chemin où le volume est monté dans le conteneur.
    - `name` : Le nom du volume.
- `volumes` : Les volumes utilisés par les conteneurs.
  - `persistentVolumeClaim` : Référence à la PVC.
    - `claimName` : Le nom de la PVC.

---

#### 4. ConfigMap

**Qu'est-ce qu'un ConfigMap ?**
Un ConfigMap est un objet Kubernetes utilisé pour stocker des paires clé-valeur. Il est utilisé pour décorréler les configurations des images conteneurs, afin que les applications soient portables.

**Exemple de création d'un ConfigMap :**
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: web-config
  namespace: default
data:
  index.html: |
    Hello, Kubernetes!
```

- `data` : Les données stockées dans le ConfigMap.
  - `index.html` : Clé pour la donnée.
  - `Hello, Kubernetes!` : Valeur associée à la clé.

**Utilisation d'un ConfigMap dans un Pod :**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-server
  namespace: default
spec:
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - name: web-config-volume
      mountPath: /usr/share/nginx/html
      subPath: index.html
  volumes:
  - name: web-config-volume
    configMap:
      name: web-config
```

- `volumeMounts` : Montages de volumes pour les conteneurs.
  - `subPath` : Spécifie le sous-chemin dans le volume où monter le fichier `index.html`.

---

#### 5. Sélecteurs de Nœud

**Qu'est-ce qu'un Sélecteur de Nœud ?**
Un sélecteur de nœud est une étiquette utilisée pour spécifier sur quel nœud un pod doit être planifié. Cela permet de placer le pod sur un nœud ayant certaines caractéristiques définies par une étiquette.

**Exemple de création d'un Pod avec un sélecteur de nœud :**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-kusc00401
spec:
  containers:
  - name: nginx
    image: nginx
  nodeSelector:
    disk: spinning
```

- `nodeSelector` : Sélecteur de nœud pour placer le pod sur un nœud spécifique ayant l'étiquette `disk: spinning`.

**Commande pour étiqueter un nœud :**
```bash
kubectl label nodes <node-name> disk=spinning
```

---

#### 6. Mise à l'échelle d'un Déploiement

**Qu'est-ce qu'un Déploiement ?**
Un déploiement est une ressource Kubernetes qui gère un ensemble de répliques de pods pour assurer la disponibilité de l'application.

**Comment mettre à l'échelle un déploiement ?**
La mise à l'échelle d'un déploiement consiste à ajuster le nombre de répliques de pods. Kubernetes se charge de créer ou supprimer des pods pour atteindre le nombre spécifié de répliques.

**Commande pour mettre à l'échelle un déploiement :**
```bash
kubectl scale --replicas=5 deployment/guestbook
```

---

#### 7. NetworkPolicy

**Qu'est-ce qu'une NetworkPolicy ?**
Une NetworkPolicy est une ressource Kubernetes utilisée pour contrôler le trafic réseau vers et depuis les pods. Elle permet de définir des règles de sécurité réseau.

**Exemple de création d'une NetworkPolicy :**
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-port-from-namespace
  namespace: echo
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          kubernetes.io/metadata.name: internal
    ports:
    - protocol: TCP
      port: 9200
```

- `podSelector: {}` : Sélectionne tous les pods dans le namespace `echo`.
- `policyTypes` : Type de politique (Ingress pour le trafic entrant).
- `ingress` : Définit les règles de trafic entrant.
  - `from` : Spécifie l'origine du trafic autorisé.
    - `namespaceSelector` : Sélectionne les namespaces autorisés.
      - `matchLabels` : Spécifie les labels des namespaces autorisés (`kubernetes.io/metadata.name: internal`).
  - `ports` : Spécifie les ports autorisés (port 9200 en TCP).
