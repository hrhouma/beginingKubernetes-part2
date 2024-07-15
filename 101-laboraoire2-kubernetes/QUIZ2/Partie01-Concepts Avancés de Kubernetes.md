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
