Ce projet a été créé dans le cadre du cursus 42 par dle-fur

##
1. Description :
- Inception est un projet d’administration système dont l’objectif est de mettre en place, dans une machine virtuelle, une petite infrastructure web gérée avec Docker Compose
- Il consiste à relier plusieurs images Docker et à pouvoir les lancer ensemble, sans pour autant qu’elles perdent leur indépendance (grâce à Docker Compose)

Le projet repose sur plusieurs conteneurs séparés :
- NGINX sert de point d’entrée unique en HTTPS (TLS 1.2/1.3) sur le port 443

- WordPress fonctionne avec PHP-FPM (sans NGINX)

- MariaDB gère la base de données.
Les services communiquent via un réseau Docker dédié, et les données persistantes (base de données et fichiers WordPress) sont stockées dans des volumes nommés, sauvegardés sur l’hôte dans /home/<login>/data

=========

But du projet :

Les différents conteneurs à mettre en place :
- NGINX (avec TLS v1.2 ou v1.3)
- WordPress (avec PHP-FPM configuré)
- MariaDB (sans NGINX)

Volumes à mettre en place :
- Un volume contenant votre base de données WordPress
- Un volume contenant les fichiers de votre site WordPress

Mise en place d’un réseau de conteneurs :
- Il fera le lien entre les conteneurs

Utilisateurs à créer dans la base de données WordPress :
- Un utilisateur admin (ne doit pas s’appeler « admin »)
- Un utilisateur standard

- Configurer un nom de domaine afin qu’il pointe vers l' adresse IP locale. Le nom de domaine sera : <login>.42.fr

Le tag latest est interdit :
- Il faut préciser quelle version est installée

- Aucun mot de passe ne doit être présent dans vos Dockerfiles.

- L’utilisation des variables d’environnement est obligatoire.

- La mise en place d’un fichier .env afin de stocker vos variables d’environnement est fortement conseillée.

- Le conteneur NGINX doit être le seul point d’entrée de votre infrastructure, via le port 443 uniquement, en utilisant le protocole TLSv1.2 ou TLSv1.3.
(Le port 443 permet l’accès via https://, et le port 80 via http://.)

=========

OS différents :

Alpine Linux :
- C’est une distribution Linux légère, orientée sécurité. Elle contient le moins de fichiers et d’outils possible afin de laisser au développeur la possibilité de les installer lui-même si besoin.

Debian :
- C’est un système d’exploitation universel. Les systèmes Debian utilisent actuellement le noyau Linux (et, dans certains cas, le noyau FreeBSD).

=========

Docker :
- Docker est un outil qui permet d’empaqueter une application et ses dépendances dans un conteneur isolé.
- Docker utilise des conteneurs (à partir d’images). Par exemple, si l’on veut héberger un site internet, Docker peut utiliser une image (conteneur) NGINX.

- Le grand avantage de Docker est la possibilité de modéliser chaque conteneur sous la forme d’une image que l’on peut stocker localement.
- Un conteneur est une sorte de machine virtuelle sans noyau.
- Le noyau est l’ensemble des composants du système permettant au système d’exploitation de fonctionner (gestion du matériel, réseau, processus, etc.).
- Un conteneur ne contient que l’application et ses dépendances : il utilise le noyau du système hôte.

Exemple image NGINX (dockerfile) :
<img width="754" height="527" alt="Image" src="https://github.com/user-attachments/assets/4df1d55d-6471-471d-9051-f14807da8fea" />

- Ce fichier est un Dockerfile : c’est le nom du fichier principal qui sert à construire vos images Docker.

FROM :
- Permet d’indiquer à Docker sur quel OS doit tourner votre conteneur.
- C’est le premier mot-clé de votre Dockerfile et il est obligatoire.
- Les plus courants sont debian:buster pour Debian ou alpine:x.xx pour Alpine Linux.

RUN :
- Permet d’exécuter une commande dans l’image lors de la construction (l’équivalent de se connecter en SSH puis de taper une commande, par exemple : echo "Hello World!").
- En général, les premiers RUN du Dockerfile servent à mettre à jour les paquets (avec apk, apt, etc.) ou à installer des utilitaires basiques comme vim, curl ou sudo.

COPY :
- Permet de copier un fichier (ou un dossier).
- Il faut indiquer où se trouve le fichier à copier (depuis le répertoire où se trouve le Dockerfile), puis où le placer dans le conteneur.

EXPOSE :
- L’instruction EXPOSE informe Docker que le conteneur écoute sur les ports réseau spécifiés au moment de l’exécution.
- EXPOSE ne rend pas automatiquement les ports du conteneur accessibles depuis l’hôte.

ENTRYPOINT :
- Commande qui permet au conteneur de démarrer (définit le programme principal lancé au démarrage).

=========

Docker-Compose
- C'est un outil qui a été développé pour aider à définir et à partager des applications multi-conteneurs.
- Avec Compose, nous pouvons créer un fichier YAML pour définir les services et, à l'aide d'une seule commande, tout mettre en route ou tout démonter.
- Compose permet de gérer des applications qui utilisent plusieurs containers et de communiquer entre eux.

Exemple de fichier .yml :
<img width="654" height="614" alt="Image" src="https://github.com/user-attachments/assets/d7be77f3-3e33-48ff-bd94-737efb9ecf57" />

Service :
- Précise les différents services (images) à utiliser.

env_file: .env :
- Indique le fichier optionnel contenant les variables d’environnement.

container_name: website :
- Le nom du conteneur (doit porter le même nom que le service, d’après le sujet).

"80:80" :
- Le mapping de ports

restart: always :
- Permet de redémarrer automatiquement le conteneur en cas de crash.

- Ce fichier .yml permet de donner les instructions à Docker Compose pour gérer 3 images : MariaDB, NGINX et WordPress.
- Le fichier YAML doit commencer par la version de Compose : référez-vous aux versions actuelles.

=========

TLS :
- C’est un protocole qui sécurise les échanges sur un réseau informatique, notamment sur Internet.

TLS permet :
- L’authentification du serveur
- La confidentialité des données échangées (session chiffrée)
- L’intégrité des données échangées
- L’authentification du client

===================================================================================================

##
Instructions :

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

Container NGINX :
- NGINX permet de mettre en place un serveur Web.
##

===================================================================================================

##
Ressources :
https://tuto.grademe.fr/inception/
https://www.youtube.com/
##

===================================================================================================
