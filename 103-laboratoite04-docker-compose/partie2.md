### Partie 2: Ajouter des Utilisateurs via `curl` et Vérifier dans la Base de Données

Dans cette partie, nous allons apprendre à ajouter des utilisateurs à notre application Flask en utilisant `curl`. Ensuite, nous vérifierons dans la base de données PostgreSQL que les utilisateurs ont bien été ajoutés.

### Étape 1: Initialiser les Variables d'Environnement

Tout d'abord, nous allons définir les variables d'environnement pour faciliter l'utilisation de `curl`.

```sh
export NODE_IP=<your-server-ip>
export NODE_PORT=5000
```


# Exemple

```sh
export NODE_IP=184.72.89.59
export NODE_PORT=5000
curl http://$NODE_IP:$NODE_PORT/init
```



### Étape 2: Initialiser la Base de Données

Utilisez `curl` pour initialiser la base de données.

```sh
curl http://$NODE_IP:$NODE_PORT/init
```

Vous devriez voir une réponse indiquant que l'initialisation de la base de données est terminée.

### Étape 3: Ajouter des Utilisateurs via `curl`

Utilisez `curl` pour ajouter des utilisateurs à la base de données.

```sh
curl -i -H "Content-Type: application/json" -X POST -d '{"user":"REHOUMA Haythem 😊"}' http://$NODE_IP:$NODE_PORT/users/add
curl -i -H "Content-Type: application/json" -X POST -d '{"user":"Alice 👩"}' http://$NODE_IP:$NODE_PORT/users/add
curl -i -H "Content-Type: application/json" -X POST -d '{"user":"Bob 👨"}' http://$NODE_IP:$NODE_PORT/users/add
```

Vous devriez voir une réponse indiquant que les utilisateurs ont été ajoutés avec succès.

### Étape 4: Vérifier les Utilisateurs dans la Base de Données

1. **Accéder au conteneur PostgreSQL :**

   ```sh
   docker exec -it postgres bash
   ```

2. **Accéder à l'interface `psql` en tant qu'utilisateur `eleve` :**

   ```sh
   psql -U eleve -d userdb
   ```

3. **Lister les utilisateurs dans la table `users` :**

   ```sql
   SELECT * FROM users;
   ```

###

### Partie 2: Ajouter des Utilisateurs via `curl` et Vérifier dans la Base de Données

Dans cette partie, nous allons apprendre à ajouter des utilisateurs à notre application Flask en utilisant `curl`. Ensuite, nous vérifierons dans la base de données PostgreSQL que les utilisateurs ont bien été ajoutés.

### Étape 1: Initialiser les Variables d'Environnement

Tout d'abord, nous allons définir les variables d'environnement pour faciliter l'utilisation de `curl`.

```sh
export NODE_IP=<your-server-ip>
export NODE_PORT=5000
```

### Étape 2: Initialiser la Base de Données

Utilisez `curl` pour initialiser la base de données.

```sh
curl http://$NODE_IP:$NODE_PORT/init
```

Vous devriez voir une réponse indiquant que l'initialisation de la base de données est terminée.

### Étape 3: Ajouter des Utilisateurs via `curl`

Utilisez `curl` pour ajouter des utilisateurs à la base de données avec des émojis.

```sh
curl -i -H "Content-Type: application/json" -X POST -d '{"user":"REHOUMA Haythem 😊"}' http://$NODE_IP:$NODE_PORT/users/add
curl -i -H "Content-Type: application/json" -X POST -d '{"user":"Alice 👩"}' http://$NODE_IP:$NODE_PORT/users/add
curl -i -H "Content-Type: application/json" -X POST -d '{"user":"Bob 👨"}' http://$NODE_IP:$NODE_PORT/users/add
```

Vous devriez voir une réponse indiquant que les utilisateurs ont été ajoutés avec succès.

### Étape 4: Vérifier les Utilisateurs dans la Base de Données

1. **Accéder au conteneur PostgreSQL :**

   ```sh
   docker exec -it postgres bash
   ```

2. **Accéder à l'interface `psql` en tant qu'utilisateur `eleve` :**

   ```sh
   psql -U eleve -d userdb
   ```

3. **Lister les utilisateurs dans la table `users` :**

   ```sql
   SELECT * FROM users;
   ```

### Exemple Complet

Voici un exemple complet de la procédure.

```sh
# Définir les variables d'environnement
export NODE_IP=<your-server-ip>
export NODE_PORT=5000

# Initialiser la base de données
curl http://$NODE_IP:$NODE_PORT/init

# Ajouter des utilisateurs avec des émojis
curl -i -H "Content-Type: application/json" -X POST -d '{"user":"REHOUMA Haythem 😊"}' http://$NODE_IP:$NODE_PORT/users/add
curl -i -H "Content-Type: application/json" -X POST -d '{"user":"Alice 👩"}' http://$NODE_IP:$NODE_PORT/users/add
curl -i -H "Content-Type: application/json" -X POST -d '{"user":"Bob 👨"}' http://$NODE_IP:$NODE_PORT/users/add

# Vérifier les utilisateurs dans la base de données
docker exec -it postgres bash
psql -U eleve -d userdb
SELECT * FROM users;
```

### Conclusion

En suivant cette partie du tutoriel, vous avez appris à ajouter des utilisateurs à votre application Flask en utilisant `curl`. Vous savez maintenant comment vérifier que les utilisateurs ont été ajoutés correctement dans la base de données PostgreSQL.
