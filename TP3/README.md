# Découverte de Docker

## Objectif
Votre objectif est d'apprivoiser les commandes suivantes:

* pull
* run
* exec


## Descriptions
| CMD     | But                                                                                                             | Commentaires |
|---------|-----------------------------------------------------------------------------------------------------------------|----------------------------------------|
| pull    | Rappatrier une image docker depuis le hub docker                                                                | Pull an image or a repository from a Docker registry server             |
| run     | Lancement d'un conteneur à partir d'une image. Permet aussi de lancer une commande depuis un nouveau conteneur. |       Run a command in a new container       |
| exec    | Permet de lancer une commande depuis un conteneur déjà lancé par une précédente commande run                    |    Run a command in an existing container          |

## docker help

Lancez csimplement la commande suivante

```bash
$ docker
…
Commands:
    attach    Attach to a running container
    build     Build an image from a Dockerfile
    commit    Create a new image from a container's changes
    cp        Copy files/folders from a container's filesystem to the host path
    create    Create a new container
    diff      Inspect changes on a container's filesystem
    events    Get real time events from the server
    exec      Run a command in an existing container
    export    Stream the contents of a container as a tar archive
    history   Show the history of an image
    images    List images
    import    Create a new filesystem image from the contents of a tarball
    info      Display system-wide information
    inspect   Return low-level information on a container
    kill      Kill a running container
    load      Load an image from a tar archive
    login     Register or log in to a Docker registry server
    logout    Log out from a Docker registry server
    logs      Fetch the logs of a container
    port      Lookup the public-facing port that is NAT-ed to PRIVATE_PORT
    pause     Pause all processes within a container
    ps        List containers
    pull      Pull an image or a repository from a Docker registry server
    push      Push an image or a repository to a Docker registry server
    restart   Restart a running container
    rm        Remove one or more containers
    rmi       Remove one or more images
    run       Run a command in a new container
    save      Save an image to a tar archive
    search    Search for an image on the Docker Hub
    start     Start a stopped container
    stop      Stop a running container
    tag       Tag an image into a repository
    top       Lookup the running processes of a container
    unpause   Unpause a paused container
    version   Show the Docker version information
    wait      Block until a container stops, then print its exit code
```

Par exemple, pour connaître la version de docker:
```bash
docker version
```



## Lancez une commande dans un conteneur Alpine

```bash
docker container run alpine hostname
```

Docker continu de faire fonctionner le conteneur tant que le processus qui a démarré à l'interieur continu de fonctionner. Sinon il est en status Exited

```bash
docker container ls --all
```

## Lancez un conteneur interactif

```bash
docker container run --interactive --tty --rm alpine sh
```
 Dans cette exemple, nous demandons à Docker de lancer un conteneur basé sur Alpine dans un shell
 * --interactive indique que nous souhaitons une session interactive
 * --tty allocation d'un pseudo-tty
 * --rm demande à Docker d'éffacer le conteneur une fois celui-ci arrêté

Tapez les commandes suivantes dans le conteneur:
 * ls / pour afficher le contenu de la root directory
 * cat /etc/os-release pour indiquer la version d'Alpine
 * ps aux pour afficher les process en cours
 * exit pour quitter le shell du conteneur

Vous remarquerez que le conteneur n'existe plus:
```bash
docker container ls --all
```


## Recherchez l'image Docker MariaDB OFFICIEL
Pour cela, il faut se diriger sur le site de https://hub.docker.com puis rechercher l'image suivante:

MariaDB (Fundation)

ou bien via la commande docker

```bash
docker search --limit=10 --filter=is-official=true mariadb
```

## Récupérez l'image officiel de MariaDB

```bash
 docker container run \
 --detach \
 --name mydb \
 -e MYSQL_ROOT_PASSWORD=my-secret-pw \
 mysql:latest
```

 * --detach le mode détaché permet de ne pas bloquer votre terminal. Dès que le conteneur est lancé, docker vous rend la main
 * --name permet de lui donner un petit nom personnalisé
 * --e permet de surcharger une variable d'environnement

```bash
docker logs mydb
```
 * logs permet d'afficher les logs du conteneur

```bash
docker exec -it mydb bash
```
 * exec permet d'exécuter la commande => ici bash dans le container déjà lancé

Vérifiez que le mot de passe précédement donné MYSQL_ROOT_PASSWD fonctionne

```bash
mysql -u root -p
```

Sortez du container en tapant exit

Une autre commande interessante:
```bash
docker exec -it mydb mysql --user=root --password=$MYSQL_ROOT_PASSWORD --version
```

Elle vous permet de lancer la commande mysql et d'en demander la version





## Vérifier la disponibilité en local

```bash
docker images
```

## Récupérer l'image Docker ngnix OFFICIEL
A vous de jouer !


* [Haut de page](README.md#menu)
* [Menu Principal](../README.md#menu-des-tp)