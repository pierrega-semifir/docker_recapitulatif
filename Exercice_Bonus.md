# TP Docker Bonus

## Partie 1 : Création de l'image

-Dans le dossier app que l'on vient d'avoir,on va créer une image a partir d'un Dockerfile en utilisant node:12-alpine et installer python3

-On build l'image (et on oublie pas le terminal)

-On va ensuite instancier l'image dans un containers, on lui donne un nom et un port

-Une fois instancier on vérifier qu'il tourne en allant sur le localHost

## Partie 2 : Update du containers

On va tout d'abbord stopper notre container

Puis modifier le fichier dans src/static/js/app.js :

-     <p className="text-center">You have no todo items yet! Add one above!</p> // update

Par :

+     <p className="text-center">You have nothing in the todolist! Add one above!</p> // update

On va re-build l'image maintenant qu'on la update et on l'instancie

On retourne sur notre localHost pour vérifier que la mise à jour a bien eu lieux

## Partie 3 : DockerHub (Bien penser a ce créer un compte sur le DockerHub)

On va préparer l'envoie de notre image mise à jour sur le DockerHub, pour cela on va utiliser la commande

'$ docker commit' et on oublie pas la convention de nommage : NomRepertoireDocker/NomImage

Ensuite on va l'envoyer avec un push sur le Hub communautaire

On arrete notre ancien container et on vérifie que notre image est bien sur le Hub en créant un container à partir de celle ci

## Partie 4 : Le Volume

On créer un volume simple et on vérifie sont existence

On instancie notre image précédament récupéré du Hub avec notre container

On vérifie sont fonctionnement sur le localHost et on va modifier les données

On coupe le container puis on en créer un nouveau

On retourne sur le localHost et on vérifie que la persistance des données a bien eu lieux

## Partie 5 : Multi Container App

On va créer un réseau

Puis un container MySQL que l'on bind sur le network créée

On ce connecte a la BDD (on oublie pas le mdp) et on va voir l'état de la base

On sort de la BDD

On instancie l'app sur le network

On verifie le bon fonctionnement et on ajout une tache

On retourne sur la BDD pour observer les changements

On quitte la BDD et on coupe le container

## Partie 6 : Docker compose

Fait moi un docker compose qui reprend tout !