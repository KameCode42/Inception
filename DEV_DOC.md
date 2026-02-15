Commandes essentielles d’un container Docker :

- build le container 							->	docker build -t "nom container" . || docker build .
- connaitre image								->	docker image ls
- demarrer une image 							->	docker run "image_name"
- demarrer une image (acces console container)	->	docker run -it "image_name"
- connaitre les containers lancer				->	docker ps
- connaitre les containers stopper				->	docker ps -a
- stopper un container lancer					->	docker stop "ID_CONTAINER"
- quitter le terminal d un container			->	exit



Un container Docker doit être build avant d’être lancé.
C’est pendant le build que vous pourrez obtenir des infos sur des potentielles erreurs que vous auriez fait dans le Dockerfile.
Chaque commande Docker commence par le mot clef docker
Préciser un nom au build, avec le flag -t
exemple : docker build -t nginx