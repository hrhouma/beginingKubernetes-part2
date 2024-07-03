# Objectif :
- Comprendre les différences entre les volumes et les volumes persistants en Kubernetes :

### Volumes
- **Éphémères** : Les données sont supprimées quand le pod est supprimé.
- **Usage temporaire** : Utilisé pour stocker des données qui ne doivent pas durer au-delà du cycle de vie du pod.

### Volumes Persistants (Persistent Volumes, PV)
- **Durables** : Les données restent même après la suppression du pod.
- **Usage persistant** : Utilisé pour les données importantes qui doivent survivre au redémarrage des pods, comme les bases de données.

### Comparaison rapide
- **Volumes** : 
  - **Durée de vie courte**.
  - **Utilisé pour des tâches temporaires**.

- **Volumes Persistants (PV)** :
  - **Durée de vie longue**.
  - **Utilisé pour des données importantes et durables**.

### Exemple très simplifié

#### Volume éphémère

```yaml
volumes:
  - name: myvolume
    emptyDir: {}
```

#### Volume persistant

1. **Définir le PV**

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
  hostPath:
    path: /mnt/data
```

2. **Demander le PV avec un PVC**

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
```

3. **Utiliser le PVC dans un Pod**

```yaml
volumes:
  - name: myvolume
    persistentVolumeClaim:
      claimName: mypvc
```
