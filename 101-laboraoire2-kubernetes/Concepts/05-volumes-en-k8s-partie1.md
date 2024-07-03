# Introduction
- Objectif : Comprendre les différences entre les volumes et les volumes persistants en Kubernetes.
- En Kubernetes, les volumes sont utilisés pour fournir un stockage persistant aux pods, permettant ainsi de stocker des données au-delà du cycle de vie d'un pod. Il existe différentes façons de gérer le stockage persistant en Kubernetes, et parmi elles, les volumes persistants (Persistent Volumes, PV) et les volumes temporaires ou classiques (Volumes) sont couramment utilisés. Voici les différences principales :

### Volumes

1. **Définition :** Un volume en Kubernetes est simplement un répertoire accessible aux conteneurs dans un pod. Les volumes sont attachés au cycle de vie du pod, ce qui signifie que lorsque le pod est supprimé, les données stockées dans le volume sont également supprimées.

2. **Types :** Kubernetes supporte différents types de volumes, comme emptyDir, hostPath, configMap, secret, et plus. Par exemple, `emptyDir` crée un volume vide à chaque fois qu'un pod est assigné à un nœud, et ce volume existe aussi longtemps que le pod est en cours d'exécution sur ce nœud.

3. **Utilisation :** Les volumes sont principalement utilisés pour des cas d'utilisation temporaires où la persistance des données au-delà du cycle de vie du pod n'est pas nécessaire.

### Volumes Persistants (Persistent Volumes, PV)

1. **Définition :** Un PV est un morceau de stockage dans le cluster qui a été provisionné par un administrateur ou dynamiquement par l'utilisation de StorageClass. Les PVs existent indépendamment des pods qui les utilisent.

2. **Cycle de Vie :** Contrairement aux volumes classiques, les PVs ont un cycle de vie indépendant des pods. Un PV reste disponible même si un pod est supprimé, permettant ainsi de réutiliser les données stockées par d'autres pods.

3. **Persistent Volume Claims (PVCs) :** Les PVs sont utilisés en conjonction avec des Persistent Volume Claims (PVCs). Un PVC est une requête pour de l'espace de stockage par un utilisateur. Les pods utilisent des PVCs pour réclamer des PVs, ce qui abstrait les détails du stockage sous-jacent et permet une gestion plus flexible et dynamique.

4. **Provisionnement :** Les PVs peuvent être provisionnés statiquement (l'administrateur crée manuellement les PVs) ou dynamiquement (Kubernetes crée automatiquement des PVs en fonction des StorageClasses définies).

5. **Utilisation :** Les PVs sont utilisés pour les applications nécessitant un stockage persistant, comme les bases de données, les systèmes de fichiers partagés, et toute autre application où les données doivent survivre au-delà du cycle de vie des pods.

### En résumé

- **Volumes classiques** sont éphémères et liés au cycle de vie des pods.
- **Volumes Persistants (PVs)** sont des unités de stockage indépendantes qui survivent au-delà du cycle de vie des pods et sont associées aux pods via des PVCs.

### Exemple

Voici un exemple de spécification d'un volume et d'un volume persistant en Kubernetes :

#### Volume

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: mycontainer
    image: myimage
    volumeMounts:
    - mountPath: /myvol
      name: myvolume
  volumes:
  - name: myvolume
    emptyDir: {}
```

#### Volume Persistant

1. **Persistent Volume (PV)**

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mypv
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: mystorageclass
  hostPath:
    path: /mnt/data
```

2. **Persistent Volume Claim (PVC)**

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: mystorageclass
```

3. **Pod utilisant le PVC**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: mycontainer
    image: myimage
    volumeMounts:
    - mountPath: /myvol
      name: myvolume
  volumes:
  - name: myvolume
    persistentVolumeClaim:
      claimName: mypvc
```
