##
Regles :
Un container Docker doit être build avant d’être lancé.
C’est pendant le build que vous pourrez obtenir des infos sur des potentielles erreurs que vous auriez fait dans le Dockerfile.
Chaque commande Docker commence par le mot clef docker
Préciser un nom au build, avec le flag -t
exemple : docker build -t nginx
##

===================================================================================================

##
Commandes pour build un container (dans le dossier contenant le dockerfile) :

- build le container (-t = preciser le nom)									->	docker build -t "nom container" . || docker build .
- connaitre image actuelle (apres build reussi)								->	docker image ls
- demarrer une image 														->	docker run "image_name"
- demarrer une image (acces console container)								->	docker run -it "image_name"
- connaitre les containers lancer											->	docker ps
- connaitre les containers stopper											->	docker ps -a
- stopper un container lancer												->	docker stop "ID_CONTAINER"
- supprimer tous les containers arreter										->	sudo docker rm $(sudo docker ps -aq)

Dans le terminal du container :
- quitter le terminal d un container										->	exit
##

===================================================================================================

##
1. NGINX :
- sert de point d'entree pour les requetes

- Mise en place du fichier dockerfile pour NGINX (pour inception) :

FROM	debian:bookworm
- Lance la version anterieur de debian, qui sera l'OS

RUN		
- apt update				-> met a jour les paquets au lancement
- apt install nginx -y		-> installe les paquets pour nginx (-y permet de creer un projet sans demander l avis a l utilisateur)
- apt install vim -y		-> installation de vim
- apt install curl -y		-> installation de curl
- mkdir -p /etc/nginx/ssl	-> créer un dossier, qui permettra de stocker le certificat et la clef pour une connexion sécurisée
- apt install OpenSSL -y	-> securise les communication internet et implemente les protocole de secutie TLS

- openssl req				-> generer un certificat ssl (req créer des certificats auto-signés)
- openssl req -x509			-> precise le type de certificat (creer un certificat auto-signer)
- openssl req -x509 -nodes	-> permet de lancer sans mot de passe
- openssl req -x509 -nodes -out /etc/nginx/ssl/inception.crt -keyout /etc/nginx/ssl/inception.key	-> stocker le certificat
- openssl req -x509 -nodes -out /etc/nginx/ssl/inception.crt -keyout /etc/nginx/ssl/inception.key -subj	-> -subj permet de preremplir les informations du certificat
- openssl req -x509 -nodes -out /etc/nginx/ssl/inception.crt -keyout /etc/nginx/ssl/inception.key -subj "/C=FR/ST=IDF/L=Renens/O=42/OU=42/CN=dle-fur.42.fr/UID=login"		-> commande complete
- mkdir -p /var/run/nginx	->	permet creere un dossier et de stocker les fichier de config NGINX
- chmod 755 /var/www/html	-> les droits pour l'acces au root principale
- chown -R www-data:www-data /var/www/html	-> les droits sur l'acces de l'utilisateur principale
- m -rf /var/lib/apt/lists/*    -> Une fois les paquets installés, ils ne servent plus dans l’image finale.

COPY
- conf/nginx.conf /etc/nginx/nginx.conf	->	copie notre dossier conf dans le dossier de l'image NGINX

EXPOSE
- 443 port d'entree

CMD
- [ "nginx", "-g", "daemon off;"]		-> lance le container NGINX en premier plan pour que le container ne se stop pas

=========

- Parametrer le fichier config de NGINX (pour le ssl) :

server
{
	listen 443 ssl;											-> preciser le ssl a utiliser
	ssl_protocols TLSv1.3;									-> protocole utiliser
	ssl_certificate /etc/nginx/ssl/inception.crt;			-> indique le certificat
	ssl_certificate_key /etc/nginx/ssl/inception.key;		-> indique la clef
	root /var/www/wordpress;								-> dossier ou se trouve WordPress (première page à afficher)
	server_name dle-fur.42.fr;								-> IP de connexion
	index index.php index.html index.htm;					-> prend le premier fichier index trouver et utilise comme page d’accueil

	location {
	try_files $uri $uri/ =404;								-> si le fichier a ouvrir n'existe pas, renvoie l'erreur 404 (WordPress)
	}

	location ~ \.php$ {										-> installe PHP pour gerer les requetes PHP de WordPress
	include snippets/fastcgi-php.conf;
	fastcgi_pass wordpress:9000;							-> indique ou NGINX doit renvoyer le code PHP (9000 : port interne du conteneur PHP-FPM)
	}
}
##

===================================================================================================

2. MariaDB :
- Gestion de base de donnee

- Mise en place du fichier dockerfile pour MariaDB (pour inception) :


=========


- Configuer le dossier conf de Mariadb (dans fichier 50-server.cnf) :

[mysqld]							->	categorie de configuration
datadir=/var/lib/mysql				->	dossier qui va stocker notre base de donnee
socket = /run/mysqld/mysqld.sock	-> dossier socket pour communiquer
bind_address=*						-> toutes les IP du reseau peuvent se connecter (bind_address=XXX.XXX.XX.XX)
port = 3306							-> port standard pour ectouer les connexion server-client
user=mysql							-> ajout de l'utilisateur

=========

- Creer une database et un utilisateur associer (en creant un script .sh) :

service mysql start;			->  La commande service permet de démarrer MySQL avec la commande associée.
mysql -e "CREATE DATABASE IF NOT EXISTS \ '${SQL_DATABASE}\';"	-> créer une table si elle n’existe pas déjà, du nom de la variable d’environnement SQL_DATABASE, indiqué dans mon fichier .env qui sera envoyé par le docker-compose.yml
mysql -e "CREATE USER IF NOT EXISTS \'${SQL_USER}\'@'localhost' IDENTIFIED BY '${SQL_PASSWORD}';"	-> crée l’utilisateur SQL_USER s’il n’existe pas, avec le mot de passe SQL_PASSWORD
mysql -e "GRANT ALL PRIVILEGES ON \`${SQL_DATABASE}\`.* TO \`${SQL_USER}\`@'%' IDENTIFIED BY '${SQL_PASSWORD}';" -> donne les droits à l’utilisateur SQL_USER avec le mot de passe SQL_PASSWORD pour la table SQL_DATABASE
mysql -e "ALTER USER 'root'@'localhost' IDENTIFIED BY '${SQL_ROOT_PASSWORD}';"	-> Je change les droits root par localhost, avec le mot de passe root SQL_ROOT_PASSWORD
mysql -e "FLUSH PRIVILEGES;"	-> rafraichir pour que MySQL le prenne en compte.
mysqladmin -u root -p$SQL_ROOT_PASSWORD shutdown	-> éteindre MySQL
exec mysqld_safe	-> Ici je lance la fameuse commande que MySQL recommande sans arrêt à son démarrage. 

