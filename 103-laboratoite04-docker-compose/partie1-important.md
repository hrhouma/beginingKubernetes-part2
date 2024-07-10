### Tutoriel Complet pour Déployer une Application Flask avec PostgreSQL et Redis dans Docker

Ce tutoriel vous guidera à travers le processus complet de déploiement d'une application Flask utilisant PostgreSQL et Redis, orchestré avec Docker Compose. Nous inclurons des étapes pour créer des utilisateurs, des bases de données et gérer les privilèges dans PostgreSQL. Nous assurerons également la persistance des données avec des volumes Docker.

#### Structure du Projet

Créez la structure de votre projet comme suit :

```plaintext
project/
├── app.py
├── templates/
│   └── add_user.html
├── Dockerfile
├── docker-compose.yml
└── requirements.txt
```

### Étape 1: Configurer la Structure de votre Projet

1. Créez un nouveau dossier pour votre projet :
   ```sh
   mkdir project
   cd project
   ```

2. Créez les fichiers nécessaires :
   ```sh
   touch app.py Dockerfile docker-compose.yml requirements.txt
   ```

3. Créez le dossier `templates` et le fichier `add_user.html` à l'intérieur :
   ```sh
   mkdir templates
   touch templates/add_user.html
   ```

### Étape 2: Créer les Fichiers de l'Application

#### 2.1 Fichier `app.py`

Ce fichier contient le code de l'application Flask :

```python
import time
import psycopg2
from flask import Flask, Response, request, render_template, jsonify
import redis
from datetime import datetime
import hashlib
import os
import json

app = Flask(__name__)
startTime = datetime.now()

REDIS_HOST = os.environ.get('REDIS_HOST', 'redis')
R_SERVER = redis.Redis(host=REDIS_HOST, port=6379)

max_retries = 10
for attempt in range(max_retries):
    try:
        db = psycopg2.connect(
            dbname=os.environ.get('POSTGRES_DB', 'userdb'),
            user=os.environ.get('POSTGRES_USER', 'eleve'),
            password=os.environ.get('POSTGRES_PASSWORD', 'password'),
            host=os.environ.get('POSTGRES_HOST', 'postgres')
        )
        break
    except psycopg2.OperationalError as e:
        print(f"Attempt {attempt + 1} of {max_retries} failed: {e}. Retrying in 5 seconds...")
        time.sleep(5)
else:
    print("All attempts to connect to PostgreSQL failed.")
    exit(1)

cursor = db.cursor()

@app.route('/')
def home():
    return "Welcome to the User Management Service"

@app.route('/add_user')
def add_user_form():
    return render_template('add_user.html')

@app.route('/init')
def init():
    try:
        cursor.execute("DROP TABLE IF EXISTS users")
        cursor.execute("CREATE TABLE users (ID serial PRIMARY KEY, username varchar(30))")
        db.commit()
        return "DB Init done"
    except Exception as e:
        db.rollback()
        return str(e), 500

@app.route("/users/add", methods=['POST'])
def add_users():
    if request.is_json:
        req_json = request.get_json()
        user = req_json.get('user')
    else:
        user = request.form.get('user')

    if user:
        try:
            cursor.execute("INSERT INTO users (username) VALUES (%s)", (user,))
            db.commit()
            return jsonify({"message": "Added"}), 200
        except Exception as e:
            db.rollback()
            return str(e), 500
    else:
        return jsonify({"message": "User is required"}), 400

@app.route('/users/<uid>')
def get_users(uid):
    hash = hashlib.sha224(str(uid).encode('utf-8')).hexdigest()
    key = "sql_cache:" + hash

    if R_SERVER.get(key):
        return R_SERVER.get(key).decode("utf-8") + "(c)"
    else:
        try:
            cursor.execute("SELECT username FROM users WHERE ID = %s", (uid,))
            data = cursor.fetchone()
            if data:
                R_SERVER.set(key, data[0])
                R_SERVER.expire(key, 36)
                return R_SERVER.get(key).decode("utf-8")
            else:
                return "Record not found"
        except Exception as e:
            return str(e), 500

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000, debug=True)
```

#### 2.2 Fichier `add_user.html`

Ce fichier contient le formulaire HTML pour ajouter des utilisateurs :

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
    <title>Add User</title>
</head>
<body>
    <div class="container">
        <h1 class="mt-5">Add User</h1>
        <form action="/users/add" method="post">
            <div class="form-group">
                <label for="user">User Name:</label>
                <input type="text" class="form-control" id="user" name="user" required>
            </div>
            <button type="submit" class="btn btn-primary">Add User</button>
        </form>
    </div>
</body>
</html>
```

#### 2.3 Fichier `requirements.txt`

Ce fichier contient les dépendances Python nécessaires :

```plaintext
Flask
redis
psycopg2-binary
```

#### 2.4 Fichier `Dockerfile`

Ce fichier contient les instructions pour construire l'image Docker de l'application :

```Dockerfile
FROM python:3.9
COPY requirements.txt /app/
RUN pip install -r /app/requirements.txt
COPY . /app
WORKDIR /app
EXPOSE 5000
CMD ["python", "app.py"]
```

#### 2.5 Fichier `docker-compose.yml`

Ce fichier contient la configuration de Docker Compose pour orchestrer les services :

```yaml
version: '3'
services:
  postgres:
    image: postgres:latest
    container_name: postgres
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
      POSTGRES_DB: userdb
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - my-network

  redis:
    image: redis:latest
    container_name: redis
    networks:
      - my-network

  web:
    build: .
    ports:
      - "5000:5000"
    environment:
      REDIS_HOST: redis
      POSTGRES_HOST: postgres
      POSTGRES_USER: eleve
      POSTGRES_PASSWORD: password
      POSTGRES_DB: userdb
    networks:
      - my-network
    depends_on:
      - postgres
      - redis

networks:
  my-network:
    driver: bridge

volumes:
  postgres_data:
```

### Étape 3: Configurer PostgreSQL avec l'Utilisateur `eleve`

#### 3.1 Lancer le conteneur PostgreSQL

```sh
docker-compose up -d postgres
```

#### 3.2 Accéder au conteneur PostgreSQL

```sh
docker exec -it postgres bash
```

#### 3.3 Accéder à l'interface `psql` en tant qu'utilisateur `postgres`

```sh
psql -U postgres
```

#### 3.4 Créer l'utilisateur `eleve` et la base de données `userdb`

Dans l'interface `psql`, exécutez les commandes suivantes :

```sql
CREATE USER eleve WITH SUPERUSER PASSWORD 'password';
CREATE DATABASE userdb;
GRANT ALL PRIVILEGES ON DATABASE userdb TO eleve;
```

#### 3.5 Quitter l'interface `psql` et le conteneur PostgreSQL

```sh
\q
exit
```

### Étape 4: Rebuild et Redémarrer les Conteneurs Docker

#### 4.1 Rebuild et redémarrez les conteneurs Docker

```sh
docker-compose down
docker-compose up -d --build
```

### Étape 5: Initialiser la Base de Données

Accédez à l'URL suivante pour initialiser la base de données :

```plaintext
http://<your-server-ip>:5000/init
```

### Étape 6: Tester l'application

Accédez à `http://<your-server-ip>:5000/add_user` et essayez d'ajouter un utilisateur via le formulaire. Assurez-vous que l'utilisateur est ajouté avec succès sans l'erreur "Unsupported Media Type".

### Conclusion

En suivant ce tutoriel, vous avez configuré une application Flask avec PostgreSQL et Redis dans Docker, en utilisant un volume pour persister les données PostgreSQL. Cela garantit que les utilisateurs et les bases de données ne seront pas effacés à chaque redémarrage du conteneur. Vous pouvez maintenant ajouter des utilisateurs via l'interface web sans problème.
