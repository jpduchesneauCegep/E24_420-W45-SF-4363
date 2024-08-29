#  Exercice 12 :  Apache

### Informations
- Évaluation : formative.
- Type de travail : individuel
- Durée estimée : 2 heures.
- Système d'exploitation : Ubuntu client.
- Environnement : Docker.  

### Objectifs  

- Installer le serveur Web Apache
- Faire une configuration de base

Dans cet exercice, vous allez installer un environnement de serveur Web (HTTP) Apache (httpd), regarder sa configuration de base et modifier quelques paramètres.

Note : si vous fermez votre VM, n’oubliez pas d’arrêter vos conteneurs :

	docker container stop nom_conteneur

	docker container start nom_conteneur


## Section 1 : Installation d'Apache
Dans cette partie, vous allez installer le serveur Web Apache selon les spécifications données.

### Étape 1 : Installation

1. Pour débuter, on doit trouver une image qui nous intéresse. Aller sur le site Web hub.docker.com ( docker hub est un dépôt pour mettre nos images, il est aussi possible de récupérer des images faites par d’autres personnes). 
Si ce n’est pas déjà fait, inscrivez-vous. 
Une fois connecté, faites une recherche pour httpd (apache). Vous pouvez voir qu’il y a plus de 10 000 images. L’image officielle est la httpd éditée par la fondation Apache. Ces celle dont nous allons nous servir. Cliquez httpd et vous allez avoir toute l’information pour vous servir de l’image.

2. Avant de lancer notre serveur, on va se créer un espace de travail qui sera utilisé pour les prochains exercices. 

- À partir de votre répertoire (dossier) $HOME, créer un répertoire Dev et à l’intérieur un répertoire Sites. Vous devriez avoir la structure suivante :

```bash
/home/$user/Dev/Sites
# Exemple sur mon poste :
/home/jpduches/Dev/Sites
```
**Sauf si on vous le précise, vous travaillerez toujours à partir de cette espace.**

3.	Entrez dans le répertoire Sites et exécutez la commande recommandée pour lancer l’image httpd.

```bash
docker run -dit --name my-apache-app -p 8080:80 -v $(pwd):/usr/local/apache2/htdocs/ httpd:2.4
```
<details>
<summary>Pour rappel voici ce que fait la commande : </summary>

- Docker lance un conteneur qui exécute un serveur web Apache en arrière-plan, accessible depuis votre navigateur à l'adresse http://localhost:8080. Les fichiers de votre répertoire de travail sont automatiquement partagés avec le serveur web à l'intérieur du conteneur.

- Les options "-dit" dans votre commande servent à définir le mode d'exécution du conteneur Docker que vous lancez. Voyons chacune d'elles :

    "d" (detached) : Cette option indique à Docker d'exécuter le conteneur en arrière-plan. Cela signifie que le conteneur continuera de fonctionner même si vous fermez votre terminal. Il ne sera pas attaché à votre session interactive.

    "i" (interactive) : Bien que présente, cette option n'est pas réellement utilisée dans votre cas spécifique, car le conteneur est en mode détaché. Elle est généralement utilisée pour obtenir une interface interactive avec le conteneur, comme si vous étiez connecté à un terminal à l'intérieur.

    "t" (TTY): Cette option alloue un pseudo-TTY au conteneur. Un TTY est un terminal virtuel qui permet d'interagir avec une application en mode texte. Cependant, comme le conteneur est en mode détaché, cette option n'est pas nécessaire ici. Par contre, elles pourraient être utiles si vous aviez besoin d'interagir avec le conteneur de manière interactive plus tard.


Le reste de la commande :

- docker run: C'est la commande de base pour exécuter un nouveau conteneur Docker.

-   --name my-apache-app: Attribue un nom au conteneur pour faciliter son identification.

-  -p 8080:80: Mappe le port 80 du conteneur (celui utilisé par Apache) au port 8080 de votre machine hôte. Cela vous permet d'accéder au serveur web en utilisant http://localhost:8080.
  
- -v $(pwd):/usr/local/apache2/htdocs/: Monte le répertoire courant de votre machine hôte dans le répertoire /usr/local/apache2/htdocs/ du conteneur. Cela signifie que les fichiers que vous placez dans votre répertoire courant seront accessibles par le serveur web à l'intérieur du conteneur.

-    httpd:2.4: Spécifie l'image Docker à utiliser. Dans ce cas, c'est une image contenant un serveur web Apache version 2.4.
</details>

### Vérification de l'installation

- Vérifiez que votre image s’exécute bien.
```bash
docker container ls
```
Vérifier les ports qui écoutent sur votre poste local :
```bash
ss -tnap | grep 8080
```
- Ouvrez un navigateur et allez au site Web http://localhost:8080.

**ATTENTION : la majorité des navigateurs ajoute par défaut le ***https*** et non le ***http***, donc pour ne pas avoir d'erreur, il est essentiel de le taper ***http//:localhost:8080*** et non ***localhost:8080***.**

Avez-vous une page Web? 

Sinon, pourquoi?
<details>

Non. On n’a pas de fichier index.html.
</details>

- Dans le répertoire Sites ajouter une page Web index.html avec le contenu ci-dessous (vous pouvez l’améliorer à votre goût) et rafraîchir la page de votre navigateur.

```html
<html>
  <title>Apache test</title>
  <body>
    <p>Wow! Une page Web!</p>
  </body>
</html>
```
Avez-vous votre page Web?

Oui. Sinon, dépanner.

## Section 2 : Configurations
Dans cette partie, vous allez regarder la configuration par défaut et faire quelques changements.

### Configuration par défaut

1.	Quel fichier sur votre VM devez-vous modifier pour pouvoir résoudre un nom de domaine local (serveur qui n’a pas accès à Internet et n’est pas présent dans un DNS).

Allez sur le site de la documentation d’Apache, https://httpd.apache.org/docs/2.4/getting-started.html, et répondez aux questions suivantes :

2.	Quel est l’endroit, répertoire, par défaut des fichiers de configuration ?

3.	Quel est le nom du fichier de configuration ? 

4.	Quelle est l’utilité de la directive Include ? 
5.	Comment configure-t-on un serveur Apache ? 
6.	Où dois-je mettre une directive ? 
7.	Où est placé le fichier .htaccess et quelle est son utilité ? 
8.	Quelle directive permet de spécifie où dans votre système de fichiers vous devez placer les fichiers de votre site Web ? 
9.	Quelle directive définit l’emplacement du journal d’erreurs ? 
10.	Est-ce que cette directive est seulement globale ou elle peut être définie par hôte virtuel ? 
### Consultation des configurations par défaut

1.	Connectez-vous sur votre serveur en ligne de commande.
```bash
docker container exec -it my-apache-app bash
```


2.	Déplacez-vous dans le répertoire de configuration et ouvrez le fichier de configuration par défaut. 

Répondez aux questions suivantes :

**Note** : vous devrez installer un éditeur de texte (soit nano ou vim).

```bash
apt update && apt install nano -y
# ou
apt update && apt install vim -y
```

 2.1 Quelle est l’utilité de la directive ServerRoot ? 

 2.2 Quelle est l’utilité de la directive Listen ?
  
 2.3 Sous quel utilisateur et groupe s’exécute httpd ? 

 2.4 Quelle est l’utilité de la directive ServerName ? 

 2.5 Quel est le répertoire pour les fichiers du site Web par défaut ?

2.6	Déplacez-vous dans le répertoire extra et regardez ce qu’il contient. Regarder le contenu des fichiers httpd-default.conf, httpd-manual.conf et httpd-vhosts.conf

### Modification des configurations du serveur Apache

- Modifiez le fichier /etc/hosts de votre VM pour ajouter que 127.0.0.1 pointe à www.monsite.com.
Ouvrez Firefox et entrez votre site Web : http://www.monsite.com:8080.

	Avez-vous votre page Web?

-	Nous allons changer le répertoire du site Web par défaut.

 	- Regarder les droits du répertoire par défaut /usr/local/apache2/htdocs (dans le shell de votre conteneur) et notez les informations  

	- Créer le répertoire /srv/htdocs et si nécessaire changer les droits (chmod 755) du répertoire pour avoir les mêmes que celui par défaut.

  	- Faire une copie du fichier de configuration de httpd.conf.

	 - Modifiez l’entrée dans le fichier httpd.conf pour que le répertoire par défaut devienne /srv/htdocs (vous avez deux entrées à modifier)

	<details>

	 ```bash
		mkdir /srv/htdocs
		cp /usr/local/apache2/conf/httpd.conf /usr/local/apache2/conf/httpd.conf.old
		nano /usr/local/apache2/conf/httpd.conf
	```
	 Modifiez les directives :

	 DocumentRoot "/srv/htdocs"

	 et 

	 <Directory "/srv/htdocs">

	</details>

   

    - Relancer le serveur httpd : apachectl restart. 
	(Le message d’erreur sur le nom de domaine est normal).

    - Déplacez-vous dans le répertoire /srv/htdocs, créer un document toto.txt comme suit :
		 ```bash
		echo "Information personnelle" > toto.txt
		```

	- Rafraîchir la page de Firefox.
	- Que s’est-il passé? 
	- Copier votre fichier et donnez index.html comme non de la copie et rafraîchir la page par défaut.
	- Que s’est-il passé? 

	- Déplacez-vous dans le répertoire de configuration d’Apache. Ouvrez le fichier de configuration httpd.conf 
	- Rechercher la ligne avec une directive pour les journaux : LogFormat.
	- Ajouter la directive sur la ligne en-dessous pour que nos journaux contienne les traces des connexions :

		***CustomLog "logs/access_log" common***

	- Relancez le serveur httpd et consultez les fichiers de journaux avec la commande tail. 
		```bash
		tail /usr/local/apache2/logs/access_log
		```
	- Si vous n’avez pas de log, rafraîchir la page Web.

    - Exécuter la commande tail avec le paramètre -f et rafraîchir votre page Web quelques fois. 
	- Que permet le paramètre -f ? 
    - Dans le fichier httpd.conf,faite en sorte d'inclure le fichier de configuration des configurations par défauts (rechercher httpd-default.conf dans le fichier et le décomenter).
   
    - Quittez le conteneur en tapant exit.

### Copie des fichiers de confirmation

Nous allons récupérer quelques fichiers de configurations dans le but de pouvoir les réutiliser plus tard.

Dans la page de httpd sur le site de docker hub, consulter la section « Configuration ». On vous présente une commande pour récupérer le fichier de configuration :

```bash
$ docker run --rm httpd:2.4 cat /usr/local/apache2/conf/httpd.conf > my-httpd.conf  
```
- Nous devons modifier cette commande, car dans notre cas, le conteneur s’exécute déjà.

```bash
$ docker container exec -it my-apache-app cat /usr/local/apache2/conf/httpd.conf > httpd.conf 

# Modifier la commandes pour les autres fichiers.
```
- Copier, dans le répertoire de votre hôte, les fichiers httpd.conf (prenez également la copie de l’original), httpd-vhosts.conf et httpd-ssl.conf(ceux deux derniers sont dans le répertoire /usr/local/apache2/conf).

Nous allons utiliser ces fichiers lors des prochains exercices Apache.


## Pour vérification

Remettre les questions répondues sur LÉA.
