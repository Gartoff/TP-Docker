# TP1 : Devs like Docker

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

