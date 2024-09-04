#  Exercice 15 :  Nginx ­
````diff
+ Corrigé
````
### Informations
- Évaluation : formative.
- Type de travail : individuel
- Durée estimée : 3 heures.
- Système d'exploitation : Ubuntu client.
- Environnement : Docker.  

### Objectifs  

- Installer le serveur Web Nginx.
- Faire une configuration de base et avec des hôtes virtuelle.


Dans cet exercice, vous allez installer un environnement de serveur Web (HTTP) nginx, regarder à sa configuration de base et configurer des hôtes virtuels.


## Section 1 : Installation de nginx
Dans cette partie, vous allez installer le serveur Web Apache selon les spécifications données.

### Étape 1 : Installation

- Pour débuter, allez à l’image officielle sur le site de docker hub et prenez en note la commande pour lancer le conteneur.

-	Avant de lancer notre serveur, on va se créer un répertoire pour nos fichiers HTML de ce projet. À partir du répertoire Dev, créer un répertoire SiteNginx/html. Vous devriez avoir la structure suivante :

```bash
/home/$user/Dev/SiteNginx/html
```
- 	Copiez le fichier index.html, que vous avez créé précédemment, dans html. Adapter le contenu au nouveau serveur.

-	Entrez dans le répertoire SiteNginx et exécutez la commande recommandée pour lancer l’image nginx. Ajuster la commande pour que le répertoire html soit monté sur le répertoire de documents du conteneur, en lecteur-écriture, et que le port 80 soit redirigé vers le port 8080.

<details>

```bash
docker run --name some-nginx -p 8080:80 -v "$PWD"/html/:/usr/share/nginx/html/ -d nginx

```

</details>

-	Vérifiez que votre image s’exécute bien.

```bash
    docker container ls
    ss -tnap | grep 8080
```
- Ouvrez un navigateur et allez au site Web http://www.monsite.com:8080.
Avez-vous une page Web? Sinon, dépannez.

## Section 2 : Configuration du serveur Nginx

Dans cette partie, vous allez regarder la configuration par défaut et récupérer le fichier de configuration par défaut.


### Étape 1 : Consultation des configurations par défaut

-	Connectez-vous sur votre serveur en ligne de commande.

```bash
docker container exec -it some-nginx bash
```

-	Déplacez-vous dans le répertoire de configuration et récupérez le fichier de configuration par défaut. Vous pouvez juste le copier dans le répertoire monté. Ouvrez le fichier de configuration et répondez aux questions suivantes :

Consulter les sites de documentations de nginx pour vous aider :

https://nginx.org/en/docs/ 

https://docs.nginx.com/nginx/admin-guide/ 


-	Quelle est l’utilité de la directive user ?  

   ````diff
- L’utilisateur sous lequel s’exécute nginx. ````
````
-	Quelle est l’utilité de la directive worker_process ? 

````diff
-  The number of worker processes is defined by the worker_processes directive in the nginx.conf configuration file and can either be set to a fixed number or configured to adjust automatically to the number of available CPU cores.
````

-	À quel endroit se trouvent les logs et sont-ils gérés par docker ? 
````diff
- error_log  /var/log/nginx/error.log notice;
- access_log  /var/log/nginx/access.log  main;

- Oui, ils sont gérés quand même par docker, si l'on fait la commande docker container logs some-nginx, on les voit.
````

- Pourquoi ne voit-on pas de contexte server dans le contexte http? 

````diff
- Car nous avons include /etc/nginx/conf.d/*.conf;. Dans conf.d/ nous avons le fichier default.conf qui contient le contexte server.
````

- Suite à la réponse de la question précédente, comment pouvons-nous implanter des hôtes virtuels avec cette version de nginx ?

````diff
- En créant un fichier hote.conf par hôte virtuel dans conf.d/.
````

- Récupérer les fichiers nginx.conf et  /etc/nginx/conf.d/default.conf.

- Arrêter votre conteneur.


### Étape 2 : Hôtes virtuels

À cette étape, avec ce que vous avez appris à l’étape précédente, vous allez refaire votre site avec des hôtes virtuels avec nginx. 

Votre structure de répertoire devrait ressembler à ça :


```bash
  /home/$user/Dev/SitesNginx/
                  |--docker-compose.yml
                  |--Dockerfile
                  |--conf/
                      |--ngnix.conf
                      |--default.conf
                      |--site02.conf
                  |--site01/
                    |--html/
                      |--index.html
                  |--site02/
                    |--html/
                      |--index.html
```

- Créer les fichiers nécessaires dans SitesNginx pour créer une image et un conteneur avec les spécifications suivantes (vos fichiers index.html doivent indiquer le nom du site et que le serveur est nginx) :

**Image à utiliser** : nginx:alpine. 

**Dans l’image ** : copier les fichiers de configurations nécessaires et exposer les ports 80 et 443. L’hôte par défaut est site01.com et on veut 2 processus worker pour le serveur.

**Répertoires à créer dans l’image** : /
```bash
usr/share/nginx/html/site01.com 
# et
/usr/share/nginx/html/site02.com.
```

**Nom du conteneur**: nginx.

**Ports à exposer**: le port 80 au port 80 du conteneur.

**Point de montage**: 
```bash
site01/html <-> /usr/share/nginx/html/site01.com 
# et
site02/html <-> /usr/share/nginx/html/site02.com. #En lecture seulement.
```

**Nom de domaine** : Les sites doivent être accessibles sur votre VM avec site01.com, www.site01.com site02.com et www.site02.com.

- Lancer votre conteneur et vérifier que les deux sites fonctionnent.
- Arrêter et effacer votre conteneur nginx.



## Pour vérification

Remettre une capture d’écran de votre Linux avec Firefox ouvert sur les deux sites de votre serveur Web.

[images](images/VerificationEx14.png)

## Compétences développées

00SJ – Effectuer le déploiement de serveurs intranet. 	
- 00SJ # 1 – Analyser le projet de déploiement.
- 00SJ # 2 – Monter les serveurs intranet.
- 00SJ # 3 – Installer les services intranet.
- 00SJ # 5 – Participer à la mise en service des serveurs intranet.


Note : les compétences sont développées en partie.

## Références

https://nginx.org/en/docs/ 

https://docs.nginx.com/nginx/admin-guide/ 

https://hub.docker.com/_/nginx 

­<details>
<summary>Solution partie 2</summary>

### docker-compose.yml

```bash
version: "3.2"

services:
    nginx:
        build: 
            context: .
        ports:
            - "80:80"
        volumes:
                - ./site01/html:/usr/share/nginx/html/site01.com
                - ./site02/html:/usr/share/nginx/html/site02.com
```
### Dockerfile

```bash
# Creer une image pour notre serveur nginx

FROM nginx:alpine
# Utilise la derniere image

RUN mkdir -p /usr/share/nginx/html/site01.com
# Ajoute le repertoire pour site01

RUN mkdir -p /usr/share/nginx/html/site02.com
# Ajoute le repertoire pour site02

EXPOSE 80 443
# Exposer les ports 80 et 443

WORKDIR /etc/nginx/
# Change dans le repertoire de configuration

COPY conf/nginx.conf nginx.conf
# Remplacer nginx.conf par le mien

COPY conf/default.conf conf.d/default.conf
# Remplacer default.conf par le mien, pour site01.com

COPY conf/site02.conf conf.d/site02.conf
# Copier le fichier de configuration pour site02.com
```

### nginx.conf
```bash
user  nginx;
worker_processes  2;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}

```

### default.conf
```bash
server {
    listen       80;
    listen  [::]:80;
    server_name site01.com www.site01.com;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html/site01.com;
        index  index.html index.htm;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}
```

### site02.conf
```bash
server {
    listen       80;
    listen  [::]:80;
    server_name  site02.com www.site02.com;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html/site02.com;
        index  index.html index.htm;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}
```

</details>