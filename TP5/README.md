# Docker - Création d'une stack Wordpress

## Objectif
Mise en ligne d'un site Wordpress.

La stack sera composée des éléments suivants:
* Serveur web nginx
* Php 8.1
* MySQL

## Création de l'environnement de votre projet
<br >
    Les commandes suivantes sont valides uniquement sous Linux/MacOS

<br >

```bash
mkdir wordpress
cd wordpress
mkdir nginx-conf
```

Créer le fichier suivants avec le contenu de la config nginx
```bash
vi nginx-conf/nginx.conf
```

Contenu de la config wordpress/nginx-conf/nginx.conf
```nginx
server {
        listen 80;
        listen [::]:80;

        server_name localhost your_domain www.your_domain;

        index index.php index.html index.htm;

        root /var/www/html;

        location ~ /.well-known/acme-challenge {
                allow all;
                root /var/www/html;
        }

        location / {
                try_files $uri $uri/ /index.php$is_args$args;
        }

        location ~ \.php$ {
                try_files $uri =404;
                fastcgi_split_path_info ^(.+\.php)(/.+)$;
                fastcgi_pass wordpress:9000;
                fastcgi_index index.php;
                include fastcgi_params;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                fastcgi_param PATH_INFO $fastcgi_path_info;
        }

        location ~ /\.ht {
                deny all;
        }
        
        location = /favicon.ico { 
                log_not_found off; access_log off; 
        }
        location = /robots.txt { 
                log_not_found off; access_log off; allow all; 
        }
        location ~* \.(css|gif|ico|jpeg|jpg|js|png)$ {
                expires max;
                log_not_found off;
        }
}
```

### Définition des variable d'environnements

```bash
vi ../.env
```

Contenu de la config wordpress/.env
```env
MYSQL_ROOT_PASSWORD=your_root_password
MYSQL_USER=your_wordpress_database_user
MYSQL_PASSWORD=your_wordpress_database_password

```

Ajoutez le fichier suivant pour éviter de se retrouver avec une image contenant le ficheir .env:
```bash
vi wordpress/.dockerignore
```

Ajouter dans ce fichier 
```bash
.env
.git
docker-compose.yml
.dockerignore
```

## On passe à la mise en place du fichier docker-compose

```bash
vi docker-compose.yml
```


Contenu de la config wordpress/docker-compose.yml

```yaml
version: '3'

services:
  db:
    image: mysql:latest
    container_name: db
    restart: unless-stopped
    env_file: .env
    environment:
      - MYSQL_DATABASE=wordpress
    volumes: 
      - dbdata:/var/lib/mysql
    command: '--default-authentication-plugin=mysql_native_password'
    networks:
      - app-network
```

Le service nommé db contien les informations suivantes:

* image:  Indique le nom de l'image à utiliser.Ici latest veut dire la denière version.
* container_name: This specifies a name for the container.
* restart: Redémarre le container jusqu'à forcé l'arrêt manuellement.
* env_file: Permet de préciser le chemin+nom du fichier environnement (contient les variables sensibles aussi).
* environment: Permet de surcharger des variable d'environnements qui seront présentes dans votee conteneur. On y fait passer les variables sensibles.
* volumes: Ici on monte un volume appelé dbdata dans le conteneur vers /var/lib/mysql. C'est le répertoire par défaut pour mysql.
* command: PErmet de remplacer la commande définie dans CMD (=> Dockerfile). Dans ce cas particulier, vous ajouterez une option à la commande mysqld standard de l'image Docker, qui démarre le serveur MySQL sur le conteneur. 
Étant donné que PHP et donc votre image WordPress ne prendront pas en charge la nouvelle authentification par défaut de MySQL, vous devez effectuer cet ajustement afin d'authentifier l'utilisateur de la base de données de votre application.
* networks: Ceci précise que votre service applicatif rejoindra le réseau app-network, que vous définirez en bas du fichier.


Suite du fichier wordpress/docker-compose.yml
```yaml
wordpress:
    depends_on: 
      - db
    image: wordpress:php8.1-fpm
    container_name: wordpress
    restart: unless-stopped
    env_file: .env
    environment:
      - WORDPRESS_DB_HOST=db:3306
      - WORDPRESS_DB_USER=$MYSQL_USER
      - WORDPRESS_DB_PASSWORD=$MYSQL_PASSWORD
      - WORDPRESS_DB_NAME=wordpress
    volumes:
      - wordpress:/var/www/html
    networks:
      - app-network

```

Explications:
* depends_on: Cette option garantit que vos conteneurs démarreront dans l'ordre de dépendance, le conteneur **wordpress** commençant après le conteneur **db**. Votre application WordPress repose sur l'existence de votre base de données et de votre utilisateur, donc exprimer cet ordre de dépendance permettra à votre application de démarrer correctement.
* image: Nous utilisons l'image Officiel fourni par wordpress.
* env_file: Comme pour précédement. On précise le fichier contenant des secrets
* environment: Ici, vous utilisez les valeurs que vous avez définies dans votre fichier **.env**, mais vous les affectez aux noms de variables attendus par l'image WordPress : **WORDPRESS_DB_USER** et **WORDPRESS_DB_PASSWORD**. Vous définissez également un **WORDPRESS_DB_HOST**, qui sera le serveur MySQL exécuté sur le conteneur **db** accessible sur le port par défaut de MySQL, **3306**. Votre **WORDPRESS_DB_NAME** aura la même valeur que celle que vous avez spécifiée dans la définition de service MySQL pour votre **MYSQL_DATABASE** : wordpress.
* volumes: Vous montez un volume nommé appelé wordpress sur le point de montage /var/www/html créé par l'image WordPress. L'utilisation d'un volume nommé de cette manière vous permettra de partager votre code d'application avec d'autres conteneurs.
* networks: Vous ajoutez également le conteneur wordpress au réseau app-network.

<br >

Suite du fichier worpress/docker-compose.yml. On y ajoute la partie webserveur
```yaml
  webserver:
    depends_on:
      - wordpress
    image: nginx:latest
    container_name: webserver
    restart: unless-stopped
    ports:
      - "80:80"
    volumes:
      - wordpress:/var/www/html
      - ./nginx-conf:/etc/nginx/conf.d
      - certbot-etc:/etc/letsencrypt
    networks:
      - app-network
```

Ici, vous nommez votre conteneur et le rendez dépendant du conteneur wordpress dans l'ordre de départ. Vous utilisez également une image nginx

* ports: Ceci expose le port 80 pour activer les options de configuration que vous avez définies dans votre nginx.conf au step [config nginx](README.md#création-de-lenvironnement-de-votre-projet).
* volumes: Ici, vous définissez une combinaison de volumes nommés et de montages liés :
  * **wordpress:/var/www/html**: Cela montera vos page WordPress dans le répertoire **/var/www/html**, le répertoire que vous avez défini comme racine dans votre bloc de serveur Nginx.
  * **./nginx-conf:/etc/nginx/conf.d**: Cela liera le montage du répertoire de configuration Nginx sur l'hôte au répertoire approprié sur le conteneur, garantissant que toutes les modifications que vous apportez aux fichiers sur l'hôte seront reflétées dans le conteneur.
  * **certbot-etc:/etc/letsencrypt**: Cela montera les certificats et les clés Let's Encrypt pertinents pour votre domaine dans le répertoire approprié du conteneur.

Vous avez également ajouté ce conteneur au réseau **app-network**.

  
On ne verra pas ici la mise en place d'un certificat SSL car pour cela il vous faut un nom de domain.


Au final, votre docker-compose.yml ressemblera à ceci:
```yaml
version: '3'

services:
  db:
    image: mysql:latest
    container_name: db
    restart: unless-stopped
    env_file: .env
    environment:
      - MYSQL_DATABASE=wordpress
    volumes: 
      - dbdata:/var/lib/mysql
    command: '--default-authentication-plugin=mysql_native_password'
    networks:
      - app-network

  wordpress:
    depends_on: 
      - db
    image: wordpress:php8.1-fpm
    container_name: wordpress
    restart: unless-stopped
    env_file: .env
    environment:
      - WORDPRESS_DB_HOST=db:3306
      - WORDPRESS_DB_USER=$MYSQL_USER
      - WORDPRESS_DB_PASSWORD=$MYSQL_PASSWORD
      - WORDPRESS_DB_NAME=wordpress
    volumes:
      - wordpress:/var/www/html
    networks:
      - app-network  

  webserver:
    depends_on:
      - wordpress
    image: nginx:latest
    container_name: webserver
    restart: unless-stopped
    ports:
      - "80:80"
    volumes:
      - wordpress:/var/www/html
      - ./nginx-conf:/etc/nginx/conf.d
    networks:
      - app-network

volumes:
  wordpress:
    driver: local
    driver_opts:
      type: 'none'
      o: 'bind'
      device: 'html'
  dbdata:
    driver: local
    driver_opts:
      type: 'none'
      o: 'bind'
      device: 'db'

networks:
  app-network:
    driver: bridge
```



## Sources documentaire
* [Docker Volumes](https://docs.docker.com/storage/volumes/)
* [Digital Ocean](https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-with-docker-compose)
* [Examples Volumes](https://devopscell.com/docker/docker-compose/volumes/2018/01/16/volumes-in-docker-compose.html)