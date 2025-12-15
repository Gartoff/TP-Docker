# TP1 : Devs like Docker

## 1. Une ptite db MySQL

### Lancez un conteneur mysql en version 8.4

```docker run --name mysql mysql:8.4```

### Vérifier que le conteneur est actif

```
PS C:\Users\garto> docker ps -a
CONTAINER ID   IMAGE       COMMAND                  CREATED         STATUS                   PORTS             NAMES
bbd3b52a366f   mysql:8.4   "docker-entrypoint.s…"   2 minutes ago   Exited (1) 2 minutes ago                   mysql
```

### Consulter les logs du conteneur


```
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

```docker rm bbd3b52a366f```


### Lancer un nouveau conteneur MySQL

```
PS C:\Users\garto> docker run --name mysql -e MYSQL_ROOT_PASSWORD=07Momo05 -d mysql:8.4
01f4e77367d64450f586e2d6004eff7b3db6bf68c3af99e080025cb77504591d
PS C:\Users\garto> docker ps
CONTAINER ID   IMAGE        COMMAND                  CREATED         STATUS          PORTS                    NAMES
01f4e77367d6   mysql:8.4    "docker-entrypoint.s…"   4 seconds ago   Up 3 seconds    3306/tcp, 33060/tcp      mysql
```




