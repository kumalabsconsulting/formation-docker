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

Par exemple, pour connaitre la version de docker:
```bash
docker version
```