### Cours Complet sur RBAC (Role-Based Access Control) dans Kubernetes

#### Table des Matières

1. [Introduction à RBAC](#introduction-à-rbac)
    - [Définition et importance](#définition-et-importance)
    - [Concepts de base de la sécurité dans Kubernetes](#concepts-de-base-de-la-sécurité-dans-kubernetes)
2. [Concepts Clés de RBAC](#concepts-clés-de-rbac)
    - [Utilisateurs et groupes](#utilisateurs-et-groupes)
    - [Rôles et RoleBindings](#rôles-et-rolebindings)
    - [ClusterRoles et ClusterRoleBindings](#clusterroles-et-clusterrolebindings)
3. [Configuration de RBAC](#configuration-de-rbac)
    - [Activer RBAC dans Kubernetes](#activer-rbac-dans-kubernetes)
4. [Création de Rôles et RoleBindings](#création-de-rôles-et-rolebindings)
    - [Exemples de Role et RoleBinding](#exemples-de-role-et-rolebinding)
    - [Utiliser les ClusterRoles et ClusterRoleBindings](#utiliser-les-clusterroles-et-clusterrolebindings)
5. [Cas d’Utilisation Communes](#cas-dutilisation-communes)
    - [Gestion des accès pour les développeurs](#gestion-des-accès-pour-les-développeurs)
    - [Limiter l'accès pour les utilisateurs en lecture seule](#limiter-laccès-pour-les-utilisateurs-en-lecture-seule)
6. [Meilleures Pratiques](#meilleures-pratiques)
    - [Principe de moindre privilège](#principe-de-moindre-privilège)
    - [Auditer les permissions](#auditer-les-permissions)
7. [Exercices Pratiques](#exercices-pratiques)
    - [Créer et tester des rôles dans un cluster de test](#créer-et-tester-des-rôles-dans-un-cluster-de-test)
8. [Cas Pratiques](#cas-pratiques)
    - [Gérer l'accès des développeurs, utilisateurs finaux et applications/bots](#gérer-laccès-des-développeurs-utilisateurs-finaux-et-applicationsbots)

---

### 1. Introduction à RBAC
[Retour à la Table des Matières](#table-des-matières)

#### Définition et Importance
[Retour à la Table des Matières](#table-des-matières)

Role-Based Access Control (RBAC) est un modèle de contrôle d'accès qui simplifie la gestion des permissions des utilisateurs dans un système. Dans RBAC, l'accès est accordé en fonction des rôles assignés aux utilisateurs, ce qui simplifie la gestion des contrôles d'accès et assure la sécurité.

RBAC permet aux administrateurs de spécifier les droits d'accès en fonction des rôles des utilisateurs au lieu de les définir individuellement. Cela signifie que lorsqu'un utilisateur change de fonction au sein de l'organisation, ses permissions peuvent être mises à jour en changeant simplement son rôle.

Exemple : 
- Un développeur a besoin d'accéder aux ressources de développement pour déployer du code.
- Un administrateur a besoin d'un accès plus étendu pour gérer et maintenir le cluster Kubernetes.

RBAC est essentiel pour assurer que seuls les utilisateurs autorisés puissent effectuer des actions spécifiques, minimisant ainsi les risques de sécurité.

#### Concepts de base de la sécurité dans Kubernetes
[Retour à la Table des Matières](#table-des-matières)

Kubernetes utilise plusieurs mécanismes de sécurité pour protéger le cluster, tels que :
- Les contrôles d'accès basés sur les attributs (ABAC)
- Role-Based Access Control (RBAC)
- Les identités des utilisateurs

RBAC est particulièrement efficace pour gérer les accès de manière centralisée et cohérente. Il permet de définir des rôles qui regroupent des permissions spécifiques, et de les attribuer à des utilisateurs ou groupes.

---

### 2. Concepts Clés de RBAC
[Retour à la Table des Matières](#table-des-matières)

#### Utilisateurs et Groupes
[Retour à la Table des Matières](#table-des-matières)

- **Utilisateurs** : Représentent des entités humaines ou des services nécessitant un accès au cluster. Les utilisateurs sont authentifiés via des certificats, des tokens ou des identifiants et mots de passe.
- **Groupes** : Regroupent plusieurs utilisateurs pour simplifier la gestion des permissions. Par exemple, un groupe "développeurs" peut inclure tous les utilisateurs qui sont des développeurs.

#### Rôles et RoleBindings
[Retour à la Table des Matières](#table-des-matières)

- **Rôles** : Définissent un ensemble de permissions. Il existe des rôles pour des espaces de noms spécifiques (Roles) et des rôles au niveau du cluster (ClusterRoles). Un rôle peut spécifier des actions telles que "lire", "écrire", "mettre à jour" ou "supprimer" des ressources.
- **RoleBindings** : Lient des utilisateurs ou groupes à des rôles, appliquant ainsi les permissions définies par ces rôles. Un RoleBinding peut lier un rôle à un utilisateur ou à un groupe dans un espace de noms spécifique.

#### ClusterRoles et ClusterRoleBindings
[Retour à la Table des Matières](#table-des-matières)

- **ClusterRoles** : Similaires aux rôles, mais applicables à l'échelle du cluster. Ils définissent des permissions qui s'appliquent à toutes les ressources du cluster.
- **ClusterRoleBindings** : Lient des des utilisateurs ou groupes à des ClusterRoles, appliquant les permissions au niveau du cluster. Un ClusterRoleBinding peut lier un ClusterRole à un utilisateur ou à un groupe, permettant ainsi un contrôle d'accès global à l'échelle du cluster.

---

### 3. Configuration de RBAC
[Retour à la Table des Matières](#table-des-matières)

#### Activer RBAC dans Kubernetes
[Retour à la Table des Matières](#table-des-matières)

Pour activer RBAC dans Kubernetes, il faut s'assurer que le cluster Kubernetes est configuré avec l'option `--authorization-mode=RBAC` dans les composants API Server. Cela peut généralement être configuré lors de l'installation de Kubernetes ou en modifiant les fichiers de configuration des composants du cluster.

Exemple pour activer RBAC dans Minikube :
```bash
minikube start --extra-config=apiserver.authorization-mode=RBAC
```

---

### 4. Création de Rôles et RoleBindings
[Retour à la Table des Matières](#table-des-matières)

#### Exemples de Role et RoleBinding
[Retour à la Table des Matières](#table-des-matières)

Pour créer un rôle permettant d'effectuer des opérations de lecture sur des pods dans un espace de noms spécifique, nous utilisons le manifeste suivant :

**role.yaml**
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: development
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

Pour appliquer ce rôle :
```bash
kubectl apply -f role.yaml
```

Pour lier le rôle `pod-reader` à un utilisateur `jane`, nous utilisons le manifeste suivant :

**rolebinding.yaml**
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: development
subjects:
- kind: User
  name: jane
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

Pour appliquer ce RoleBinding :
```bash
kubectl apply -f rolebinding.yaml
```

#### Utiliser les ClusterRoles et ClusterRoleBindings
[Retour à la Table des Matières](#table-des-matières)

Exemple de ClusterRole et ClusterRoleBinding pour gérer les nœuds dans le cluster :

**clusterrole.yaml**
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-admin
rules:
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["get", "watch", "list"]
```

Pour appliquer ce ClusterRole :
```bash
kubectl apply -f clusterrole.yaml
```

**clusterrolebinding.yaml**
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-binding
subjects:
- kind: User
  name: john
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
```

Pour appliquer ce ClusterRoleBinding :
```bash
kubectl apply -f clusterrolebinding.yaml
```

---

### 5. Cas d’Utilisation Communes
[Retour à la Table des Matières](#table-des-matières)

#### Gestion des accès pour les développeurs
[Retour à la Table des Matières](#table-des-matières)

Pour attribuer des permissions spécifiques aux développeurs pour qu'ils puissent déployer et gérer des applications sans affecter l'infrastructure du cluster, nous pouvons créer des rôles et des RoleBindings adaptés.

Exemple de rôle pour les développeurs :
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: development
  name: developer-role
rules:
- apiGroups: [""]
  resources: ["pods", "services", "deployments"]
  verbs: ["create", "delete", "get", "list", "patch", "update", "watch"]
```

Exemple de RoleBinding pour lier ce rôle aux utilisateurs du groupe développeurs :
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: dev-binding
  namespace: development
subjects:
- kind: Group
  name: developers
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer-role
  apiGroup: rbac.authorization.k8s.io
```

#### Limiter l'accès pour les utilisateurs en lecture seule
[Retour à la Table des Matières](#table-des-matières)

Pour créer des rôles en lecture seule, nous pouvons définir un rôle qui ne permet que les opérations de lecture (get, list, watch).

Exemple de rôle en lecture seule :
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: development
  name: read-only-role
rules:
- apiGroups: [""]
  resources: ["pods", "services", "deployments"]
  verbs: ["get", "list", "watch"]
```

Exemple de RoleBinding pour lier ce rôle à un utilisateur spécifique :
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-only-binding
  namespace: development
subjects:
- kind: User
  name: read-only-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: read-only-role
  apiGroup: rbac.authorization.k8s.io
```

---

### 6. Meilleures Pratiques
[Retour à la Table des Matières](#table-des-matières)

#### Principe de moindre privilège
[Retour à la Table des Matières](#table-des-matières)

Le principe de moindre privilège consiste à donner aux utilisateurs uniquement les permissions minimales nécessaires pour accomplir leurs tâches. Cela réduit les risques de sécurité en limitant l'impact potentiel d'un compte compromis.

Pour implémenter ce principe :
- Créez des rôles spécifiques pour chaque fonction ou tâche.
- Assurez-vous que chaque rôle n'a que les permissions nécessaires.
- Évitez d'utiliser des rôles génériques avec des permissions larges.

#### Auditer les permissions
[Retour à la Table des Matières](#table-des-matières)

L'audit des permissions est une pratique essentielle pour s'assurer que les rôles et les bindings sont toujours appropriés. Cela inclut la vérification régulière des rôles attribués aux utilisateurs et la mise à jour des permissions en fonction des changements de responsabilités.

Pour auditer les permissions :
- Utilisez des outils Kubernetes pour examiner les rôles et les bindings actuels.
- Révisez régulièrement les permissions accordées aux utilisateurs et groupes.
- Mettez à jour ou révoquez les permissions en cas de changement de rôle ou de fonction.

---

### 7. Exercices Pratiques
[Retour à la Table des Matières](#table-des-matières)

#### Créer et tester des rôles dans un cluster de test
[Retour à la Table des Matières](#table-des-matières)

1. **Créer un rôle pour gérer les pods dans un espace de noms :**

   **role.yaml**
   ```yaml
   apiVersion: rbac.authorization.k8s.io/v1
   kind: Role
   metadata:
     namespace: development
     name: pod-manager
   rules:
   - apiGroups: [""]
     resources: ["pods"]
     verbs: ["create", "delete", "get", "list", "patch", "update", "watch"]
   ```

   Appliquez le rôle :
   ```bash
   kubectl apply -f role.yaml
   ```

2. **Assigner ce rôle à un utilisateur spécifique :**

   **rolebinding.yaml**
   ```yaml
   apiVersion: rbac.authorization.k8s.io/v1
   kind: RoleBinding
   metadata:
     name: pod-manager-binding
     namespace: development
   subjects:
   - kind: User
     name: test-user
     apiGroup: rbac.authorization.k8s.io
   roleRef:
     kind: Role
     name: pod-manager
     apiGroup: rbac.authorization.k8s.io
   ```

   Appliquez le RoleBinding :
   ```bash
   kubectl apply -f rolebinding.yaml
   ```

3. **Tester les permissions en se connectant avec l'utilisateur et en essayant de gérer les pods :**

   Utilisez le contexte de l'utilisateur pour vérifier les permissions :
   ```bash
   kubectl get pods --context=test-user-context
   ```

---

### 8. Cas Pratiques
[Retour à la Table des Matières](#table-des-matières)

#### Gérer l'accès des développeurs, utilisateurs finaux et applications/bots
[Retour à la Table des Matières](#table-des-matières)

**1. Développeurs/Admins**

Les développeurs et administrateurs sont responsables des tâches administratives et de développement sur le cluster. Cela inclut des opérations comme la mise à jour du cluster ou la création de ressources/workloads sur le cluster.

**role.yaml**
```yaml
apiVersion: rbac.authorization.k

8s.io/v1
kind: Role
metadata:
  namespace: development
  name: admin-role
rules:
- apiGroups: [""]
  resources: ["pods", "deployments", "services"]
  verbs: ["create", "delete", "get", "list", "patch", "update", "watch"]
```

**rolebinding.yaml**
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: admin-binding
  namespace: development
subjects:
- kind: User
  name: admin-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: admin-role
  apiGroup: rbac.authorization.k8s.io
```

**2. Utilisateurs Finaux**

Les utilisateurs finaux accèdent aux applications déployées sur notre cluster Kubernetes. Les restrictions d'accès pour ces utilisateurs sont gérées par les applications elles-mêmes.

**3. Applications/Bots**

Certaines applications peuvent avoir besoin d'accéder au cluster Kubernetes pour interagir avec des ressources ou workloads à l'intérieur du cluster. Kubernetes facilite cela en utilisant des comptes de service (Service Accounts).

**serviceaccount.yaml**
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: app-service-account
  namespace: development
```

**Créer un Namespace**

Pour commencer, nous allons créer un namespace nommé `development` :

```bash
$ kubectl create namespace development

namespace/development created
```

**Créer un Certificat Client pour l'Authentification**

Les clients peuvent accéder au cluster Kubernetes en s'authentifiant au kube-apiserver en utilisant un mécanisme d'authentification basé sur SSL. Nous devons générer une clé privée et un certificat client X-509 pour authentifier un utilisateur nommé `DevUser` au kube-apiserver. Cet utilisateur travaillera sur le namespace `development`.

1. **Générer une clé privée pour `DevUser` :**

```bash
$ cd ${HOME}/.kube

$ openssl genrsa -out DevUser.key 2048

Generating RSA private key, 2048 bit long modulus (2 primes)
```

2. **Créer une demande de signature de certificat (CSR) pour `DevUser` :**

```bash
$ openssl req -new -key DevUser.key -out DevUser.csr -subj "/CN=DevUser/O=development"
```

3. **Signer la CSR pour générer le certificat :**

Nous devons fournir les clés CA du cluster Kubernetes pour générer le certificat :
```bash
$ openssl x509 -req -in DevUser.csr -CA ${HOME}/.minikube/ca.crt -CAkey ${HOME}/.minikube/ca.key  -CAcreateserial -out DevUser.crt -days 45

Signature ok

subject=CN = DevUser, O = development

Getting CA Private Key
```

**Configuration de kubectl**

1. **Ajouter une entrée dans la section des utilisateurs :**

```bash
$ kubectl config set-credentials DevUser --client-certificate=${HOME}/.kube/DevUser.crt --client-key=${HOME}/.kube/DevUser.key

User "DevUser" set.
```

2. **Ajouter une entrée dans la section des contextes :**

```bash
$ kubectl config set-context DevUser-context --cluster=minikube --namespace=development --user=DevUser

Context "DevUser-context" created.
```

**Ajouter des Permissions à l'Utilisateur**

Pour permettre à cet utilisateur d'accéder uniquement aux pods dans le namespace `development`, nous devons créer un rôle et l'associer à DevUser à l'aide de la ressource RoleBinding.

1. **Créer le rôle :**

**role.yaml**
```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: Role
metadata:
  name: dev-role
  namespace: development
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "update", "list"]
```

Appliquer le rôle :
```bash
$ kubectl apply -f role.yaml
```

2. **Créer le RoleBinding :**

**rolebinding.yaml**
```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: RoleBinding
metadata:
  name: dev-DevUser
  namespace: development
subjects:
- kind: User
  name: DevUser
  apiGroup: ""
roleRef:
  kind: Role
  name: dev-role
  apiGroup: ""
```

Appliquer le RoleBinding :
```bash
$ kubectl apply -f rolebinding.yaml
```

### Vérification des Permissions

1. **Essayer de lister les pods avec le nouveau contexte :**

```bash
$ kubectl get pods --context=DevUser-context

No resources found.
```

Cela indique que nous n'avons pas de pods en cours d'exécution dans le namespace `development`.

### Conclusion

Ce cours complet sur RBAC devrait vous fournir une base solide pour comprendre et mettre en œuvre RBAC dans Kubernetes. Nous avons couvert les concepts de base, la configuration, et des cas pratiques détaillés pour différents types d'utilisateurs. Si vous avez besoin de détails supplémentaires ou de précisions sur certains aspects, n'hésitez pas à demander.
