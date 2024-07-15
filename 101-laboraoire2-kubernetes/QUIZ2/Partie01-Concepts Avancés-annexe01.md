### Cours : Concepts Avancés de Kubernetes

Bienvenue à ce cours sur les concepts avancés de Kubernetes. Nous allons explorer plusieurs sujets importants tels que les PersistentVolumes, PersistentVolumeClaims, ConfigMaps, sélecteurs de nœud, mise à l'échelle des déploiements, et NetworkPolicies. Ce cours est conçu pour vous fournir les connaissances nécessaires pour répondre aux questions du quiz avec confiance. Chaque section inclut un exemple détaillé pour faciliter votre compréhension.

---

#### 1. Création d'un PersistentVolume (PV)

**Qu'est-ce qu'un PersistentVolume (PV) ?**
Un PersistentVolume (PV) est un espace de stockage alloué par l'administrateur du cluster. Il peut être monté par un pod pour stocker des données de manière persistante.

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

**Explications :**
- `apiVersion: v1` : La version de l'API Kubernetes utilisée.
- `kind: PersistentVolume` : Le type de ressource.
- `metadata` : Les informations sur le PV, comme son nom (`app-data`).
- `spec` : Les spécifications du PV.
  - `capacity` : La capacité de stockage (2Gi).
  - `accessModes` : Le mode d'accès (ReadWriteOnce, c'est-à-dire que le volume peut être monté en écriture par un seul nœud à la fois).
  - `hostPath` : Le chemin sur l'hôte où le volume est stocké (`/srv/app-data`).

**Commande pour appliquer la configuration :**

```bash
kubectl apply -f persistentvolume.yaml
```

**Vérification :**

```bash
kubectl get pv app-data
```

---

#### 2. Création d'un PersistentVolumeClaim (PVC)

**Qu'est-ce qu'un PersistentVolumeClaim (PVC) ?**
Un PersistentVolumeClaim (PVC) est une demande de stockage faite par un utilisateur. Kubernetes lie le PVC à un PV disponible qui répond aux critères demandés.

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

**Explications :**
- `apiVersion: v1` : La version de l'API Kubernetes utilisée.
- `kind: PersistentVolumeClaim` : Le type de ressource.
- `metadata` : Les informations sur le PVC, comme son nom (`app-data-claim`).
- `spec` : Les spécifications du PVC.
  - `accessModes` : Le mode d'accès (ReadWriteOnce).
  - `resources.requests.storage` : La capacité demandée (5Gi).

**Commande pour appliquer la configuration :**

```bash
kubectl apply -f pvc.yaml
```

**Vérification :**

```bash
kubectl get pvc app-data-claim
```

---

#### 3. Utilisation d'un PVC dans un Pod

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

**Explications :**
- `containers` : Les conteneurs dans le pod.
  - `volumeMounts` : Montages de volumes pour les conteneurs.
    - `mountPath` : Le chemin où le volume est monté dans le conteneur.
    - `name` : Le nom du volume.
- `volumes` : Les volumes utilisés par les conteneurs.
  - `persistentVolumeClaim` : Référence à la PVC.
    - `claimName` : Le nom de la PVC.

**Commande pour appliquer la configuration :**

```bash
kubectl apply -f pod.yaml
```

**Vérification :**

```bash
kubectl get pod app-pod
```

---

#### 4. Création d'un ConfigMap

**Qu'est-ce qu'un ConfigMap ?**
Un ConfigMap est un objet Kubernetes utilisé pour stocker des paires clé-valeur. Il permet de séparer la configuration de l'application des images conteneurs.

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

**Explications :**
- `data` : Les données stockées dans le ConfigMap.
  - `index.html` : Clé pour la donnée.
  - `Hello, Kubernetes!` : Valeur associée à la clé.

**Commande pour appliquer la configuration :**

```bash
kubectl apply -f configmap.yaml
```

**Vérification :**

```bash
kubectl get configmap web-config
```

---

#### 5. Utilisation d'un ConfigMap dans un Pod

**Comment utiliser un ConfigMap dans un pod ?**
Pour utiliser un ConfigMap dans un pod, vous devez le monter en tant que volume.

**Exemple de configuration d'un Pod utilisant un ConfigMap :**

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

**Explications :**
- `volumeMounts` : Montages de volumes pour les conteneurs.
  - `subPath` : Spécifie le sous-chemin dans le volume où monter le fichier `index.html`.

**Commande pour appliquer la configuration :**

```bash
kubectl apply -f pod.yaml
```

**Vérification :**

```bash
kubectl get pod web-server
```

---

#### 6. Utilisation d'un Sélecteur de Nœud

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

**Explications :**
- `nodeSelector` : Sélecteur de nœud pour placer le pod sur un nœud spécifique ayant l'étiquette `disk: spinning`.

**Commande pour étiqueter un nœud :**

```bash
kubectl label nodes <node-name> disk=spinning
```

**Commande pour appliquer la configuration :**

```bash
kubectl apply -f nginx-pod.yaml
```

**Vérification :**

```bash
kubectl get pods -o wide
```

---

#### 7. Mise à l'échelle d'un Déploiement

**Qu'est-ce qu'un Déploiement ?**
Un déploiement est une ressource Kubernetes qui gère un ensemble de répliques de pods pour assurer la disponibilité de l'application.

**Comment mettre à l'échelle un déploiement ?**
La mise à l'échelle d'un déploiement consiste à ajuster le nombre de répliques de pods. Kubernetes se charge de créer ou supprimer des pods pour atteindre le nombre spécifié de répliques.

**Commande pour mettre à l'échelle un déploiement :**

```bash
kubectl scale --replicas=5 deployment/guestbook
```

**Vérification :**

```bash
kubectl get deployment guestbook
```

---

#### 8. Création d'une NetworkPolicy

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

**Explications :**
- `podSelector: {}` : Sélectionne tous les pods dans le namespace `echo`.
- `policyTypes` : Type de politique (Ingress pour le trafic entrant).
- `ingress` : Définit

 les règles de trafic entrant.
  - `from` : Spécifie l'origine du trafic autorisé.
    - `namespaceSelector` : Sélectionne les namespaces autorisés.
      - `matchLabels` : Spécifie les labels des namespaces autorisés (`kubernetes.io/metadata.name: internal`).
  - `ports` : Spécifie les ports autorisés (port 9200 en TCP).

**Commande pour appliquer la configuration :**

```bash
kubectl apply -f networkpolicy.yaml
```

**Vérification :**

```bash
kubectl get networkpolicy -n echo
```

---

- En suivant ce cours, vous serez bien préparé pour répondre aux questions du quiz.
- Kubernetes est un outil puissant pour la gestion des conteneurs, et la maîtrise de ces concepts avancés vous permettra de tirer pleinement parti de ses capacités.
Bonne chance dans votre apprentissage et lors de votre quiz !
