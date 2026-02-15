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
Mise en place du fichier dockerfile (pour inception)

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

COPY
- conf/nginx.conf /etc/nginx/nginx.conf	->	copie le fichier dans l’image au moment du build dans un fichier.conf (acces avec vim)
##

===================================================================================================

##
Parametrer le fichier config de NGINX







##

