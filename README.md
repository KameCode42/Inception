Ce projet a été créé dans le cadre du cursus 42 par <dle-fur>

##
Description :
- Inception est un projet d’administration système dont l’objectif est de mettre en place, dans une machine virtuelle, une petite infrastructure web gerer avec Docker Compose.
- Il consiste à relier plusieurs image Docker, et pouvoir les lancer ensemble, sans pour autant, qu’elles perdent leur indépendance. (Grace a Docker-Compose)
- Le projet repose sur plusieurs conteneurs séparés : NGINX sert de point d’entrée unique en HTTPS (TLS 1.2/1.3) sur le port 443,
													  WordPress fonctionne avec PHP-FPM (sans nginx)
													  MariaDB gère la base de données. Les services communiquent via un réseau Docker dédié et les données persistantes (base de données et fichiers WordPress) sont stockées dans des volumes nommés, sauvegardés sur l’hôte dans /home/<login>/data.
##

##
Instructions : contenant toute information pertinente sur la compilation, l’installation, et/ou l’exécution.
##

##
Ressources : 
##

##
But du projet :

Les differents containers a mettre en place :
- NGINX (avec TLS v1.2 ou 1.3)
- WordPress (avec php-fpm configuré)
- MARIADB (sans NGINX)

Volume a mettre en place :
- Volume contenant votre base de données WordPress
- Volume contenant les fichiers de votre site WordPress

Mise en place d'un container network
- Il fera le lien entre les containers

Uilisateur a crrer dans la base de donnee WordPress :
- Un utilisateur Admin (ne doit pas s’appeler admin)
- Un utilisateur standard

- Il faudra configurer notre nom de domaine afin qu’il pointe vers notre adresse IP locale. Ce nom de domaine sera <login>.42.fr

Le tag latest est interdit :
- Il faut preciser quelle version est installee

- Aucun mot de passe ne doit être présent dans vos Dockerfiles.

- L’utilisation des variables d’environnement est obligatoire.

- Mise en place d’un fichier .env afin de stocker vos variables d’environnement est fortement conseillée.

- Le container NGINX doit être le seul point d’entrée de votre infrastructure par le port 443 uniquement en utilisant le protocole TLSv1.2 ou TLSv1.3
  (443 est le port qui permet l’accès par https:// , et le 80 par http://)
##

##
Arborescence :
- A la racine, le Makefile

Dossier srcs :
.env :
- Fichier contenant l'envrionnement

docker-compose.yml :
- Pour gerer l'ensemble des containers

Dossier srcs/requirements
- Un dossier par containers
- Chaque containers contient son Dockerfile et son dockerignore et un dossier tools
##

##
OS differents :

Alpine Linux :
- C'est une distribution Linux légère, orientée sécurité, elle contient le moins de fichiers et outils possibles
  afin de laisser la possibilité au développeur de les installer par lui même si besoin.

Debian :
- C'est le système d'exploitation universel. Les systèmes Debian utilisent actuellement le noyau Linux ou le noyau FreeBSD.
##


























===================================================================================================

##
Docker :
- Docker est un outil qui peut empaqueter une application et ses dépendances dans un conteneur isolé.
- Docker utilise des container (image), par exemple on veut heberger un site internet l'outil Docker contient l'image(container) NGINX.

Le grand avantage de Docker est la possibilité de modéliser chaque conteneur sous la forme d'une image que l'on peut stocker localement.

- Un conteneur est une machine virtuelle sans noyau.
- Ce que j’appelle noyau est tout l’ensemble du système permettant à la machine virtuelle de fonctionner, l’OS, le coté graphique, réseau, etc…
- En d’autres termes, un conteneur ne contient que l’application et les dépendances de l’application.

Exemple image NGINX (dockerfile)
========================================================
FROM		alpine:3.12

RUN			apk update && apk upgrade && apk add	\
									openssl			\
									nginx			\
									curl			\
									vim				\
									sudo

RUN			rm -f /etc/nginx/nginx.conf

COPY		./config/nginx.conf /etc/nginx/nginx.conf
COPY		scripts/setup_nginx.sh /setup_nginx.sh

RUN			chmod -R +x /setup_nginx.sh

EXPOSE		443

ENTRYPOINT	["sh", "setup_nginx.sh"]
========================================================

- Ce fichier est un Dockerfile, c’est le nom du fichier principal de vos images Docker.

FROM :
- Permet d’indiquer à Docker sous quel OS doit tourner votre machine virtuelle.
- C’est le premier mot clef de votre Dockerfile et celui ci est obligatoire.
- Les plus courants sont debian:buster pour Debian ou alpine:x:xx pour Linux.

RUN :
- Permet de lancer une commande sur votre machine virtuelle (L’équivalent de se connecter en ssh, puis de taper une commande bash, comme : echo “Hello World!”)
- En général, les premiers RUN fournit dans le Dockerfile consistent à mettre à jour les ressources de votre VM, comme apk, ou encore d’ajouter les utilitaires basiques comme vim, curl ou sudo.

COPY :
- Permet de copier un fichier
- Indiquez ou se trouve le fichier à copier à partir du répertoire ou se trouve le Dockerfile, puis la ou il faut le copier dans votre machine virtuelle.

EXPOSE :
- L'instruction EXPOSE informe Docker que le conteneur écoute sur les ports réseaux spécifiés au moment de l'exécution. EXPOSE ne rend pas les ports du conteneur accessibles à l'hôte.

ENTRYPOINT :
- Commande qui permet au container de demarrer
##

===================================================================================================

##
Docker-Compose
- C'est un outil qui a été développé pour aider à définir et à partager des applications multi-conteneurs.
- Avec Compose, nous pouvons créer un fichier YAML pour définir les services et, à l'aide d'une seule commande, tout mettre en route ou tout démonter.
- Compose permet de gérer des applications qui utilisent plusieurs containers et de communiquer entre eux.

Exemple de fichier .yml
========================================================
version: "3"

services:
nginx:
	build: requirements/website/
	env_file: .env
	container_name: website
	ports:
	- "80:80"
	restart: always
nginx:
	build: requirements/intra/
	env_file: .env
	container_name: intra
	ports:
	- "80:80"
	restart: always 
mariadb:
	container_name: badgeuse
	build: mariadb
	env_file: .env
	restart: always
========================================================

Service :
- Précise les différents services (images) à utiliser

env_file: .env : 
- Indique le fichier optionnel contenant l'environnement

container_name: website :
- Le nom du container (doit porter le meme nom que le service d'apres le sujet)

"80:80" :
- Le port, détaillé juste en dessous

restart: always :
- Permet de redémarrer automatiquement le container en cas de crash

Ce fichier .yml permet de donner les instructions à Compose pour gérer 3 images mariadb, NGINX et WordPress.
Le fichier YAML doit commencer par la version de Compose, référerez vous aux versions actuelles.
##

===================================================================================================

##
Explication TLS :
- C'est un protocole de sécurisation des échanges par réseau informatique, notamment par Internet.

TLS permet :
- L'authentification du serveur
- La confidentialité des données échangées (ou session chiffrée)
- L'intégrité des données échangées
- L'authentification du client
##

===================================================================================================

Container NGINX :
- NGINX permet de mettre en place un serveur Web.


















https://tuto.grademe.fr/inception/