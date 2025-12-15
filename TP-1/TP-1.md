# TP1 : Devs like Docker

## 1. Une ptite db MySQL

### Lancez un conteneur mysql en version 8.4

```docker run --name mysql mysql:8.4```

### Vérifier que le conteneur est actif

```PS C:\Users\garto> docker ps -a
CONTAINER ID   IMAGE       COMMAND                  CREATED         STATUS                   PORTS             NAMES
bbd3b52a366f   mysql:8.4   "docker-entrypoint.s…"   2 minutes ago   Exited (1) 2 minutes ago                   mysql```