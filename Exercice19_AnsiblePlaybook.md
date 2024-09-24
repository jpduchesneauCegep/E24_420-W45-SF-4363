# Exercice 19 - Ansible  - Playbook

### Informations
- Évaluation : formative.
- Durée estimée : 3 heures.
- Système d'exploitation : Linux.
- Environnement : Virtuel. 

### Objectifs  

- Déterminer les différents types de tests à effectuer.
- Appliquer la séquence d’exécution des tests selon le service visé.
- Déterminer la pertinence des correctifs proposés.
- Gérer les correctifs de logiciels, du système d’exploitation et des micrologiciels (Firmware).
- Appliquer les correctifs.
- Déterminer les indices de performance des serveurs.
- Paramétrer les indices de performance.
- Mettre en place des mécanismes d’agrégation des serveurs.
- Gérer le stockage.

### Description

Dans cet exercice, vous allez utiliser Ansible sur des machines distantes avec des playbook.

Voici les tâches à réaliser dans cet exercice :
 
  - Créer un dossier *webapp* qui va contenir tous les fichiers du projet.
  - Créer un dépôt git pour le projet et le placer sur GitHub.
  - Créer un fichier d'inventaire pour le projet. 
  - Créer un groupe *prod* dans votre fichier d'inventaire. 
  - Créer un fichier group_vars qui va contenir un fichier nommé *prod* qui contiendra les informations de connexion à utiliser par Ansible (Login et mot de passe).  
  - Créez un playbook nommé deploy.yaml permettant de déployer apache à l'aide de Docker sur le client (l'image à utiliser est httpd et le port à exposer à l'extérieur est le 80).  
  - Vous devez installer tous les prérequis à l'aide du module apt.
  - Vérifier la syntaxe du playbook avec la commande *ansible-lint*.  
  - Vérifier qu'après l'exécution de votre playbook le site par défaut d’apache est bien disponible sur le port 80.  
  - Extraire le mot de passe.
  - Explorez les options de debug d’Ansible.  
  - Afin de conserver votre travail, poussez sur votre Github en mode privé. 
  - Ajouter le professeur à votre dépôt github.

## Section 1 : Projet Ansible

### 1- Créer un dossier webapp qui va contenir tous les fichiers du projet.

Sur le poste de contrôle, créez le dossier sous l'utilisateur deploy. Vous devez également y copier le fichier <code>ansible.cfg</code>.

```bash
su deploy
cd
mkdir webapp
cp ansible.cfg webapp
cd webapp/
```

### 2- Suivre son code source sur GitHub

```bash
echo "# Webapp-ansible-apache" >>README.md
git config --global user.email "your@exemple.com" #si pas déjà définit.
git config --global user.name "Votre nom" #si pas déjà définit.
git init
git add *
git commit -m "Intialisation de mon dépôt"
git branch -M main
# Créez le projet sur Github.com avec votre navigateur
git remote add origin git@github.com:[votrecompte git hub]/webapp-ansible-apache.git
git push -u origin main
```

Créez le projet sur Github.com, en ne mettant PAS de Readme. Il vaut mieux l’ajouter après, une fois que les fichiers ont été téléchargés, pour éviter tout conflit.

### 3- Créer un fichier inventaire.yaml 

Créer un fichier <code>inventaire.yaml</code> pour le projet webapp.yaml avec un groupe prod : 

```yaml
---
all:
  vars:
    ansible_ssh_common_args: '-o StrictHostKeyChecking=no'
  hosts:
    control:
      ansible_connection: local
prod:
  hosts:
    srv-apache-1:
       ansible_host: 10.0.1.4 # A remplacer par l'IP de votre client
```

Modifiez votre fichier <code>ansible.cfg</code> pour qu'il tienne compte du nouveau fichier d'inventaire.

### 4- Créer un playbook nommé deploy.yaml

Un playbook Ansible est un fichier YAML contenant un ou plusieurs plays. Chaque play est un ensemble de tâches.  

- Un **play** est un ensemble de tâches correspondant à un appareil ou un groupe d'appareils.
- Une **tâche** est une action unique qui fait référence à un **module** à exécuter avec tous les arguments et actions en entrée. Ces tâches peuvent être simples ou complexes selon le besoin d'autorisations, l'ordre d'exécution des tâches, etc.

Un playbook peut également contenir des **rôles**. Un rôle est un mécanisme permettant de diviser un playbook en plusieurs composants ou fichiers, de simplifier le playbook et de le rendre plus facile à réutiliser. Par exemple, le rôle **commun** est utilisé pour stocker les tâches qui peuvent être utilisées dans tous vos playbooks.

Le playbook Ansible YAML comprend des **objets**, des **listes** et des **modules**.
 
- Un objet YAML est une ou plusieurs paires de valeurs clés. Les paires de valeurs clés sont séparées par un deux-points sans l'utilisation de guillemets, par exemple  <code>hosts: srv-apache-1</code>.
-  Un objet peut contenir d'autres objets tels qu'une liste. YAML utilise des listes ou des tableaux. Un tirait "-" est utilisé pour chaque élément de la liste.
-  Ansible est livré avec un certain nombre de modules (appelés bibliothèque de modules) qui peuvent être exécutés directement sur des hôtes distants ou via des playbooks. Par exemple, le module <code>ping</code> utilisé pour vérifier la connectivité. Chaque tâche se compose généralement d'un ou de plusieurs modules Ansible.

Vous exécutez un playbook Ansible à l'aide de la commande <code>ansible-playbook</code>, par exemple :

```bash
ansible-playbook mon_playbook.yaml -i inventaire.yaml
```

La commande <code>ansible-playbook</code> utilise des paramètres pour spécifier : 
 
- Le playbook que vous voulez exécuter (mon_playbook.yaml).
- Le fichier d'inventaire et son emplacement (-i hosts). Ce paramètre est nécessaire si vous n'avez pas de fichier <code>ansible.cfg</code> qui change son emplacement par défaut.


Créez un playbook nommé <code>deploy.yaml</code> permettant de déployer Apache à l'aide de Docker sur le client (l'image à utiliser est httpd et le port à exposer à l'extérieur est le 80).  

```Bash
vim deploy.yaml
```

```yaml
---
- name: "Apache installation avec Docker"
  hosts: prod
  tasks:
    - name: CREATE APACHE CONTAINER
      community.docker.docker_container:
        name: webapp
        image: httpd
        ports:
          - "80:80"
```

### 5- Vérifier la syntaxe du playbook
Pour avoir l'outil de vérification de la syntaxe, nous aurons besoin d'ansible-lint qui s'installe avec l'installateur de package pour Python PIP. Voici les étapes :

#### Voir ce lien si erreur python - DG
https://stackoverflow.com/questions/75608323/how-do-i-solve-error-externally-managed-environment-every-time-i-use-pip-3

```bash
sudo apt update && sudo apt install python3-pip -y
sudo pip install ansible-lint --break-system-packages
# Finalement, nous pouvons vérifier le fichier: 
ansible-lint deploy.yaml
```

Lorsqu'il n'y a pas d'erreur, exécutez le playbook.  

>[Attention]
  Il se peut que vous ailliez une erreur si vous avez installé Ansible avec un package. Vous devrez faire une nouvelle installation d’Ansible en allant chercher la version chez Ansible plutôt que celle d'Ubuntu. Voici les commandes :

```
sudo apt update
sudo apt install software-properties-common
sudo apt-add-repository --yes --update ppa:Ansible/Ansible
sudo apt install Ansible
```


### 6- Lancer le playbook

```
ansible-playbook deploy.yaml
```

Il devrait avoir une erreur d'absence du module Docker (l'erreur peut être différente):

![Absence module Docker](images/ErrModDocker.png)

On va ajouter le module manquant directement dans le playbook, c'est sa raison d'être après tout.

```yaml
---
- name: "Apache installation avec Docker"
  hosts: prod
  pre_tasks:
    - name: INSTALL DOCKER
      apt:
        name: docker.io
        state: present
        update_cache: yes
  tasks:
    - name : Create Apache container
      community.docker.docker_container:
        name: webapp
        image: httpd
        ports:
            - "80:80"
```

Exécution du playbook.  

```
ansible-playbook deploy.yaml
```

Nouvelle erreur: <code>Permision  denied</code>

Nous n'avons pas les droits. Le compte deploy n'est pas suffisant. Il faut une élévation de privilège. Avec l'ajout de <code>become: true</code>.  

```yaml
---
- name: Apache installation avec Docker
  hosts: prod
  become: true  
  pre_task:
   - name: Install Docker
      apt:
        name: docker.io
        state: present
        update_cache: yes
  tasks:
    - name : Create Apache container
      docker_container:
        name: webapp
        image: httpd
        ports:
            - "80:80"
```

Exécution de playbook.  

```
ansible-playbook deploy.yaml
```

Cette fois, "sudo: il est nécessaire de saisir un mot de passe". 
Nous allons y aller pour la façon la plus  simple bien sure, la moins sécuritaire :

```yaml
---
- name: "Apache installation avec Docker"
  hosts: prod
  become: true
  vars:
    ansible_sudo_pass: MotDePasse # Mettre votre mot de passe de deploy
  pre_task:
   - name: Install Docker
      apt:
        name: docker.io
        state: present
        update_cache: yes
  tasks:
    - name : Create Apache container
      community.docker.docker_container:
        name: webapp
        image: httpd
        ports:
            - "80:80"
```

Exécution de playbook.  

```
ansible-playbook deploy.yaml
```

Résultat attendu : 

![Le playbook fonctionne](images/fonctionne.png)

Vérifions dans le navigateur :

![La page est là!](images/ItWork.jpg)


Et aussi sur la machine srv-apache-1 : 

![Cmd Docker ps](images/dockerps.jpg)


### 7- Sortir le mot de passe du playbook
Enlevez les objets (entrées) <code>vars</code> et <code>ansible\_sudo\_pass</code> de votre fichier <code>deploy.yaml</code>.

Nous allons utiliser un fichier de variables de groupes.

Bien que vous puissiez stocker des variables dans le fichier d'inventaire principal, le stockage de fichiers de variables d'hôte et de groupe distincts peut vous aider à organiser plus facilement les valeurs de vos variables. Vous pouvez également utiliser des listes et des données de hachage dans les fichiers de variables d'hôte et de groupe, ce que vous ne pouvez pas faire dans votre fichier d'inventaire principal.

Les fichiers de variables d'hôte et de groupe doivent utiliser la syntaxe YAML. Les extensions de fichier valides incluent « .yml », « .yaml », « .json » ou aucune extension de fichier. Consultez la syntaxe YAML si vous êtes nouveau sur YAML.

Ansible charge les fichiers de variables d'hôte et de groupe en recherchant les chemins relatifs au fichier d'inventaire ou au fichier playbook. Si votre fichier d'inventaire dans <code>/etc/ansible/hosts</code> contient un hôte nommé « foosball » qui appartient à deux groupes, « raleigh » et « webservers », cet hôte utilisera des variables dans les fichiers YAML aux emplacements suivants :

```Bash
/etc/ansible/group_vars/raleigh # peut se terminer avec '.yml', '.yaml', ou '.json'
/etc/ansible/group_vars/webservers
/etc/ansible/host_vars/foosball
```

Vous pouvez également créer des répertoires nommés d'après vos groupes ou hôtes. Ansible lira tous les fichiers de ces répertoires par ordre lexicographique. Un exemple avec le groupe « raleigh » :

```Bash
/etc/ansible/group_vars/raleigh/db_settings
/etc/ansible/group_vars/raleigh/cluster_settings
```

Tous les hôtes du groupe «raleigh» disposeront des variables définies dans ces fichiers. Cela peut être très utile pour garder vos variables organisées lorsqu'un seul fichier devient trop volumineux ou lorsque vous souhaitez utiliser Ansible Vault sur certaines variables de groupe.

Pour <code>ansible-playbook</code>, vous pouvez également ajouter les répertoires <code>group_vars/</code> et <code>host_vars/</code> à votre répertoire playbook. D'autres commandes Ansible (par exemple, <code>ansible</code>, <code>ansible-console</code>, etc.) rechercheront uniquement <code>group_vars/</code> et <code>host_vars/</code> dans le répertoire d'inventaire. Si vous souhaitez que d'autres commandes chargent des variables de groupe et d'hôte à partir d'un répertoire playbook, vous devez fournir l'option <code>--playbook-dir</code> sur la ligne de commande. Si vous chargez des fichiers d'inventaire à partir du répertoire playbook et du répertoire d'inventaire, les variables du répertoire playbook remplaceront les variables définies dans le répertoire d'inventaire.

Créer un répertoire <code>group\_vars</code> qui va contenir un fichier nommé <code>prod.yaml</code> qui contiendra les informations de connexion à utiliser par Ansible (Login et mot de passe).

```Bash
mkdir group_vars
vim group_vars/prod.yaml
```

Contenus du fichier <code>prod.yaml</code>.

```yaml
---
ansible_user: deploy
ansible_sudo_pass: votreMotDePasse
```

Exécution du playbook.

```bash
ansible-playbook deploy.yaml
```

Jusqu'à maintenant, nous avons toujours le mot de passe dans un fichier texte. Donc, nous avons un problème de sécurité.

Enlevez l'objet (entrée) <code>ansible\_sudo\_pass</code> de votre fichier <code>group_vars/prod.yaml</code>.

Modifiez le fichier <code>ansible.cfg</code> comme suit :

```bash
vim ansible.cfg

# Ajoutons le paramètre nécessaire:
[privilege_escalation]
become_ask_pass=true
```

Exécution du playbook.

```bash
ansible-playbook deploy.yaml
```

Le mot de passe est demandé.


### 8- Ansible Vault pour plus de sécurité

Essentiellement, Vault est un moyen pour garder secrètes les informations sensibles de votre configuration Ansible. Il vous permet de chiffrer vos fichiers plutôt que d'avoir du texte brut dans vos playbooks.  

Nous pouvons essentiellement exécuter la commande <code>ansible-vault</code> pour chiffrer n'importe quel fichier. Pour l'instant, la plupart des fichiers que nous allons chiffrer seront des fichiers variables, car ils contiendront peut-être des mots de passe ou des clés sensibles.
Ces fichiers peuvent être partagés via un outil de contrôle de sources comme GIT, en gardant les mots de passe et les clés sensibles hors du contrôle de sources.  

Tous ces fichiers sont protégés par un mot de passe et le chiffrement par défaut est AES.

Ajoutons un répertoire <code>vars</code> :

```bash
mkdir vars
```

Nous allons créer notre fichier de variables sensibles :

```bash
ansible-vault create vars/secret-variables.yaml
```

Entrez un mot de passe pour protéger le fichier. Le fichier va s'ouvrir dans l'éditeur par défaut.

Entrez le mot de passe de l'utilisateur deploy (pour l'utilisation de la commande sudo) :

```yaml
# À ajouter dans le fichier
---
ansible_sudo_pass: votreMotDePasse
```

Pour éditer à nouveau le fichier, vous utilisez la commande <code>ansible-vault edit NomFichier</code>.
Vous pouvez vérifier que le fichier est bien chiffré :

```bash
cat -v vars/secret-variables.yaml
```

Enlevez les lignes pour <code>[privilege_escalation]</code> du fichier <code>ansible.cfg</code>.

Modifiez le fichier <code>deploy.yaml</code> pour ajouter le fichier contenant la variable chiffrer :

```yaml
---
- name: "Apache installation avec Docker"
  hosts: prod
  become: true
  vars_files:
    - ./vars/secret-variables.yaml
  pre_task:
   - name: Install Docker
      apt:
        name: docker.io
        state: present
        update_cache: yes
  tasks:
    - name : Create Apache container
      community.docker.docker_container:
        name: webapp
        image: httpd
        ports:
            - "80:80"
```

Maintenant, on doit ajouter le paramètre <code>--ask-vault-pass</code> au lancement de notre playbook :

```bash
ansible-playbook deploy.yaml --ask-vault-pass
```

Le mot de passe pour ouvrir le fichier <code>secret-variables.yaml</code> est demandé.

![L'exécution avec Vault.](images/Vault.png)

Maintenant, faites un _commit_ (ne pas oublier d'ajouter les nouveaux fichiers) de votre projet et _poussez_ votre projet dans votre dépôt github.

Retrouvez-vous votre fichier de _vault_ (<code>secret-variables.yaml</code>) ?

<details>
	<summary>Réponse</summary/>
	Oui.
</details>

Que retrouvez-vous dans le fichier lorsque vous essayez de le consulter sur github ?

<details>
	<summary>Réponse</summary/>
	Le ficher est chiffrer, donc on ne peut pas lire les informations à l'intérieur.
</details>

## Remise

Placez des captures des commandes suivantes dans un seul fichier et déposez-le sur LÉA dans Travaux. 

>Astuce : utiliser un terminal pour les deux commande `cat` et un autre pour l'exécution de playbook et ce, côte à côte.

```bash
cat inventaire.yaml
cat deploy.yaml
ansible-playbook deploy.yaml --ask-vaut-pass
```

Exemple de remise :

![Exemple de remise.](images/ExempleRemiseExer20.png)

## Références
[Documentation ansible pour group_vars](https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html#organizing-host-and-group-variables)
