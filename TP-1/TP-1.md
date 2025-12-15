# TP1 Part 1: Devs like Docker

## 1. Une ptite db MySQL

### Lancez un conteneur mysql en version 8.4

```bash
docker run --name mysql mysql:8.4
```

### Vérifier que le conteneur est actif

```bash
PS C:\Users\garto> docker ps -a
CONTAINER ID   IMAGE       COMMAND                  CREATED         STATUS                   PORTS             NAMES
bbd3b52a366f   mysql:8.4   "docker-entrypoint.s…"   2 minutes ago   Exited (1) 2 minutes ago                   mysql
```

### Consulter les logs du conteneur


```bash
PS C:\Users\garto> docker logs mysql -f
2025-12-15 10:25:57+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.4.7-1.el9 started.
2025-12-15 10:25:57+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
2025-12-15 10:25:57+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.4.7-1.el9 started.
2025-12-15 10:25:58+00:00 [ERROR] [Entrypoint]: Database is uninitialized and password option is not specified
    You need to specify one of the following as an environment variable:
    - MYSQL_ROOT_PASSWORD
    - MYSQL_ALLOW_EMPTY_PASSWORD
    - MYSQL_RANDOM_ROOT_PASSWORD
```

### Supprimer le conteneur inactif 

```bash
docker rm bbd3b52a366f
```


### Lancer un nouveau conteneur MySQL

```bash
PS C:\Users\garto> docker run --name mysql -e MYSQL_ROOT_PASSWORD=07Momo05 -d mysql:8.4
01f4e77367d64450f586e2d6004eff7b3db6bf68c3af99e080025cb77504591d
PS C:\Users\garto> docker ps
CONTAINER ID   IMAGE        COMMAND                  CREATED         STATUS          PORTS                    NAMES
01f4e77367d6   mysql:8.4    "docker-entrypoint.s…"   4 seconds ago   Up 3 seconds    3306/tcp, 33060/tcp      mysql
```

## 2. Un PMA pour accompagner la DB

### Lancer un conteneur PHPMyAdmin

```bash
PS C:\Users\garto> docker run --name phpmyadmin -d --link mysql:db -p 8080:80 phpmyadmin
Unable to find image 'phpmyadmin:latest' locally
latest: Pulling from library/phpmyadmin
b1c5c71ad843: Pull complete
0c3a8464b740: Pull complete
a4482e26b5c3: Pull complete
9ab8f1d104c5: Pull complete
e16d68226cdd: Pull complete
d5ce62661451: Pull complete
a923251b43fa: Pull complete
60df91c45d0a: Pull complete
b1f8710b0f16: Pull complete
da081f25a1b3: Pull complete
5e4542c43815: Pull complete
c5e0ba6f6712: Pull complete
543828ab9fd4: Pull complete
745a60025a92: Pull complete
99cb708df8f0: Pull complete
4f4fb700ef54: Pull complete
a72b699530ff: Pull complete
34d0d313218a: Pull complete
b33e61c85737: Pull complete
3ce7470ee145: Pull complete
1733a4cd5954: Pull complete
Digest: sha256:86701cba0ac0397e5d0f39f398192117cbfa8ba11f2c66b9b8556ee04776b6d3
Status: Downloaded newer image for phpmyadmin:latest
490cda4aea2b8bf62c2d04fabc1d0733f13ad8702e69e8f07143dac233f7f427
```

### Visitez l'interface web de PHPMyAdmin

```bash
PS C:\Users\garto> curl http://localhost:8080
<!doctype html>
<html lang="en" dir="ltr">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <meta name="referrer" content="same-origin">
  <meta name="robots" content="noindex,nofollow,notranslate">
  <meta name="google" content="notranslate">
  <style id="cfs-style">html{display: none;}</style>
```

## 3. Compose

###  Créer un fichier compose.yml qui a le contenu suivant / Remplacer par les bonnes valeurs :

```yml
services:
  db:
    image: mysql:8.4
    environment:
      - MYSQL_ROOT_PASSWORD=07Momo05

  pma:
    image: phpmyadmin
    ports:
      - "8080:80"
```

### Lancer la stack compose 

```bash
PS C:\Users\garto\docker> docker compose up
[+] Running 3/3
 ✔ Network docker_default  Created                                                                                 0.0s
 ✔ Container docker-db-1   Created                                                                                 0.1s
 ✔ Container docker-pma-1  Created    
```

### Visitez l'interface Web de PHPMyAdmin

```bash
PS C:\Users\garto\docker> curl http://localhost:8080
<!doctype html>r Desktop   o View Config   w Enable Watch
<html lang="en" dir="ltr">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <meta name="referrer" content="same-origin">
```

## 4. Donnée persistantes

### Ajouter la gestion d'un volume à votre compose.yml

```yml
services:
  db:
    image: mysql:8.4
    environment:
      - MYSQL_ROOT_PASSWORD=07Momo05
    volumes:
      - db_data:/var/lib/mysql


  pma:
    image: phpmyadmin
    ports:
      - "8080:80"

volumes:
  db_data:
```

### Prouver que le volume est bien utilisé

```bash
PS C:\Users\garto\docker> docker volume ls
DRIVER    VOLUME NAME
local     docker_db_data
```

## 5. Changing database easily

### Proposer un nouveau compose.yml

```yml
services:
  db:
    image: "postgres:16"
    environment:
      - POSTGRES_PASSWORD=07Momo05
    volumes:
      - db_data:/var/lib/postgresql/data

  adminer:
    image: adminer
    ports:
      - "8080:8080"


volumes:
  db_data:
```

###  Prouver que ça run !

```bash
PS C:\Users\garto\docker> docker ps
CONTAINER ID   IMAGE         COMMAND                  CREATED         STATUS         PORTS                                         NAMES
2538e42bb9b9   adminer       "entrypoint.sh docke…"   3 minutes ago   Up 3 minutes   0.0.0.0:8080->8080/tcp, [::]:8080->8080/tcp   docker-adminer-1
fe769c008ebc   postgres:16   "docker-entrypoint.s…"   3 minutes ago   Up 3 minutes   5432/tcp                                      docker-db-1
```


# TP1 Part 2 : Dev environment

## 2. Dockerfile

### Construire l'image shitty_app à partir de ce Dockerfile :

```bash
PS C:\Users\garto\OneDrive\Dokumenter\Documents\TP-Docker\TP-1\part2\1> docker build -t shitty_app:1.0 .
[+] Building 56.8s (12/12) FINISHED                                                                                                        docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                       0.1s
 => => transferring dockerfile: 918B     
 ...                         
 ```

### Lancer l'application avec une commande docker run, cette commande :

```bash
PS C:\Users\garto\OneDrive\Dokumenter\Documents\TP-Docker\TP-1\part2\1> docker run -p 3000:3000 -d shitty_app:1.0
20da6e1cac4035a9c801dd4eacebebc99c2345b3fff761c50f9cd72c9fe9cf79
```


```bash
PS C:\Users\garto\OneDrive\Dokumenter\Documents\TP-Docker\TP-1\part2\1> docker ps
CONTAINER ID   IMAGE            COMMAND                  CREATED              STATUS              PORTS                                         NAMES
20da6e1cac40   shitty_app:1.0   "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:3000->3000/tcp, [::]:3000->3000/tcp   elastic_bardeen
PS C:\Users\garto\OneDrive\Dokumenter\Documents\TP-Docker\TP-1\part2\1> docker logs elastic_bardeen -f

> Shitty webapp for B3 Dev TP1@1.0.0 dev
> nodemon -L src/app.js

[nodemon] 3.1.11
[nodemon] to restart at any time, enter `rs`
[nodemon] watching path(s): *.*
[nodemon] watching extensions: js,mjs,cjs,json
[nodemon] starting `node src/app.js`
Server running at http://localhost:3000
```

curl: 

```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>HTTP Cat</title>
        <style>
          body {
            margin: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            background: #f0f0f0;
          }
          img {
            max-width: 90vw;
            max-height: 90vh;
            border-radius: 10px;
            box-shadow: 0 4px 20px rgba(0,0,0,0.2);
          }
        </style>
      </head>
      <body>
        <img src="https://http.cat/images/200.jpg" alt="HTTP Cat 200 - OK">
      </body>
    </html>
```


## 3. Hot reload pleaaaase

### Lancer un nouveau conteneur à partir de l'image shitty_app

```bash
PS C:\Users\garto\OneDrive\Dokumenter\Documents\TP-Docker\TP-1\part2\1> docker run -p 3000:3000 -d -v "src:/app/src" shitty_app:1.0
```

### Vérifier que ça fonctionne

```bash
PS C:\Users\garto\OneDrive\Dokumenter\Documents\TP-Docker\TP-1\part2\1> docker logs -f pensive_wilson

> Shitty webapp for B3 Dev TP1@1.0.0 dev
> nodemon -L src/app.js

[nodemon] 3.1.11
[nodemon] to restart at any time, enter `rs`
[nodemon] watching path(s): *.*
[nodemon] watching extensions: js,mjs,cjs,json
[nodemon] starting `node src/app.js`
Server running at http://localhost:3000
```

## 4. Compose please

###  Transformer ce docker run en compose.yml

```yml
services:
  app:
    image: shitty_app:1.0
    ports:
      - 3000:3000
    volumes:
      - "./src:/app/src"
```

## 5. DB please

### Créer un compose.yml

```yml
services:
  db:
    image: "mysql:8.4"
    environment:
      - MYSQL_ROOT_PASSWORD=07Momo05
    volumes:
      - db_data:/var/lib/mysql
  pma:
    image: phpmyadmin
    ports:
      - "8080:80"
  app:
    image: shitty_app_with_db:1.0
    build: .
    ports:
      - "3000:3000"
    volumes:
      - "./src:/app/src"
    environment:
      - DB_HOST=db
      - DB_USER=root
      - DB_PASSWORD=07Momo05
      - DB_NAME=db
      - DB_PORT=3306
      - PORT=3000
volumes:
  db_data:
```

### Allumer la stack et prouver que ça fonctionne

```bash
PS C:\Users\garto\OneDrive\Dokumenter\Documents\TP-Docker\TP-1\part2\5> docker ps
CONTAINER ID   IMAGE                    COMMAND                  CREATED         STATUS         PORTS                                         NAMES
8b4d33ef8ef3   shitty_app_with_db:1.0   "docker-entrypoint.s…"   4 seconds ago   Up 3 seconds   0.0.0.0:3000->3000/tcp, [::]:3000->3000/tcp   5-app-1
6cf44a1d547f   phpmyadmin               "/docker-entrypoint.…"   2 minutes ago   Up 2 minutes   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp       5-pma-1
e4eaf4a3546f   mysql:8.4                "docker-entrypoint.s…"   2 minutes ago   Up 2 minutes   3306/tcp, 33060/tcp                           5-db-1
PS C:\Users\garto\OneDrive\Dokumenter\Documents\TP-Docker\TP-1\part2\5> docker compose logs app -f
pma-1  | [Mon Dec 15 14:47:48.023619 2025] [mpm_prefork:notice] [pid 1:tid 1] AH00163: Apache/2.4.65 (Debian) PHP/8.3.28 configured -- resuming normal operations
db-1   | 2025-12-15T14:47:48.519125Z 0 [System] [MY-015015] [Server] MySQL Server - start.
app-1  | [nodemon] 3.1.11
app-1  | [nodemon] to restart at any time, enter `rs`
app-1  | [nodemon] watching path(s): *.*
app-1  | [nodemon] watching extensions: js,mjs,cjs,json
app-1  | [nodemon] starting `node src/app.js`
app-1  | Database ready
```

#### Curl App 

```html
curl http://localhost:3000

    <h1>Simple DB App</h1>
      <img src="https://http.cat/images/200.jpg" alt="HTTP Cat 200 - OK">
    <h2>Add User</h2>
    <form method="POST" action="/add">
      <input type="text" name="pseudo" placeholder="Enter pseudo" required>
      <button>Add</button>
    </form>

    <h2>Users (0)</h2>
    <ul><p>No users yet</p></ul>

    <style>
      body { font-family: Arial; padding: 20px; }
      input { padding: 8px; margin-right: 10px; }
      button { padding: 8px 15px; }
      ul { list-style: none; padding: 0; }
      li { padding: 5px; background: #f0f0f0; margin: 5px 0; }
    </style>
```

#### Curl PMA

```html
<!doctype html>
<html lang="en" dir="ltr">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <meta name="referrer" content="same-origin">
  <meta name="robots" content="noindex,nofollow,notranslate">
  <meta name="google" content="notranslate">
  <style id="cfs-style">html{display: none;}</style>
  <link rel="icon" href="favicon.ico" type="image/x-icon">
  <link rel="shortcut icon" href="favicon.ico" type="image/x-icon">
  <link rel="stylesheet" type="text/css" href="./themes/pmahomme/jquery/jquery-ui.css">
  <link rel="stylesheet" type="text/css" href="js/vendor/codemirror/lib/codemirror.css?v=5.2.3">
  <link rel="stylesheet" type="text/css" href="js/vendor/codemirror/addon/hint/show-hint.css?v=5.2.3">
  <link rel="stylesheet" type="text/css" href="js/vendor/codemirror/addon/lint/lint.css?v=5.2.3">
  <link rel="stylesheet" type="text/css" href="./themes/pmahomme/css/theme.css?v=5.2.3">
  <title>phpMyAdmin</title>
  ...
```