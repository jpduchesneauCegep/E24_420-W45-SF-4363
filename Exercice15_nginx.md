#  Exercice 15 :  Nginx

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

-	Quelle est l’utilité de la directive worker_process ? 

-	À quel endroit se trouvent les logs et sont-ils gérés par docker ? 

- Pourquoi ne voit-on pas de contexte server dans le contexte http? 

- Suite à la réponse de la question précédente, comment pouvons-nous implanter des hôtes virtuels avec cette version de nginx ?

-	Récupérer les fichiers nginx.conf et  /etc/nginx/conf.d/default.conf.

-	Arrêter votre conteneur.


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

