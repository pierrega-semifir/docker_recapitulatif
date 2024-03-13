# Dockerfile

## Creation du docker file pour l'image

````dockerfile
FROM node:12-alpine
RUN apk add --no-cache python3 g++ make
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node","src/index.js"]
````

## Build de l'image ##
````bash
docker build -t docker_demo_image .
````

## Instanciation de l'image créer

````bash
docker run --name=docker_demo_container -dp 3000:3000 --rm docker_demo_image
````

## Vérification du fonctionnement de l'app
http://localhost:3000/

## Arret du container
````bash
docker stop docker_demo_container
````
## Modification de notre app dans src/static/js/app.js 
-     <p className="text-center">You have no todo items yet! Add one above!</p> // update
+     <p className="text-center">You have nothing in the todolist! Add one above!</p> // update

## Re-build de l'image
````bash
docker build -t docker_demo_image .
````
## Instanciation de l'image modif
````bash
docker run --name=docker_demo_container -dp 3000:3000 --rm docker_demo_image
````

## Vérification du fonctionnement de l'app
http://localhost:3000/

## Création d'une image MAJ pour la push sur le HUB => Convention de nommage : NomRepoDocker/NomImage
````bash
docker commit docker_demo_container NomRepoDocker/my_demo_docker
````
## Push de notre image sur Hub Docker:Tag
````bash
docker push NomRepoDocker/my_demo_docker:latest
````
## Arret du container
````bash
docker stop docker_demo_container
````
## Instanciation de l'image depuis le répertoire distant / Hub Docker
````bash
docker run --name=my_docker_demo_container -dp 3000:3000 --rm NomRepoDocker/my_demo_docker:latest
````

## Création d'un volume todo-db
````bash
docker volume create todo-db
````
## Vérification
````bash
docker volume ls
````
# Instanciation de l'image et connection au volume
````bash
docker run --name=my_docker_demo_container --rm -dp 3000:3000 -v todo-db:/etc/todos NomRepoDocker/my_demo_docker:latest
````

## Vérification du fonctionnement de l'app + ajout de données
http://localhost:3000/

## Observer le volume
````bash
docker volume inspect todo-db
````
## Arret du container (suppression auto)
````bash
docker stop my_docker_demo_container
````

# Re-Instanciation de l'image
````bash
docker run --name=my_docker_demo_container --rm -dp 3000:3000 -v todo-db:/etc/todos NomRepoDocker/my_demo_docker:latest
````

# Vérification du fonctionnement de l'app et de la persistance des données
http://localhost:3000/

## Création du réseau
````bash
docker network create todo-db
````
## Création d'un container MySQL
````
$ docker run --name=my_sql_container -d --network todo-db --network-alias mysql -v todo-mysql-data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=secret -e MYSQL_DATABASE=todos mysql:5.7
````

## Connection a la BDD
````bash
docker exec -it my_sql_container mysql -u root -p
````
## Saisir le MDP
````bash
secret
````
## Vérification de la BDD
````bash
SHOW DATABASES;
````

## Résultat
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| todos              |
+--------------------+
5 rows in set (0.04 sec)

## Exit de la BDD
````bash
exit
````

## Instanciation de l'app dans un conteneur en mode dev avec connection au reseau et insertion

````bash
$ docker run --name=todo_app_container --rm -dp 3000:3000 -w /app -v "$(pwd):/app" --network todo-db -e MYSQL_HOST=mysql -e MYSQL_USER=root -e MYSQL_PASSWORD=secret -e MYSQL_DB=todos node:12-alpine sh -c "apk add --no-cache python3 g++ make && yarn install && yarn run dev"
````

## Vérification du fonctionnement de l'app + ajout d'une todo
http://localhost:3000/

## Connection a la BDD
````bash
docker exec -it my_sql_container mysql -u root -p
````
## Saisir le MDP
````bash
$ secret
````
## Affichage de la BDD
````bash
$ SHOW DATABASES;
````
## Choix de la BDD a utiliser
````bash
$ USE todos;
````
## Affichage de la Table
````bash
$ SHOW TABLES;
````
+-----------------+
| Tables_in_todos |
+-----------------+
| todo_items      |
+-----------------+
1 row in set (0.01 sec)

## Affichage du contenue de la table
````bash
$ SELECT * FROM todo_items;
````
+--------------------------------------+--------+-----------+
| id                                   | name   | completed |
+--------------------------------------+--------+-----------+
| 51642c09-3449-4780-983a-254bbc906a1d | Test-1 |         0 |
+--------------------------------------+--------+-----------+
1 row in set (0.01 sec)

## Exit de la BDD
````bash
$ exit
````
######### DOCKER compose ###########

## Création du fichier Docker-Compose
````yaml
version: '3.7'

services:
    mysql:
        image: mysql:5.7
        volumes:
            - todo-mysql-data:/var/lib/mysql
        environment:
            MYSQL_ROOT_PASSWORD: secret
            MYSQL_DATABASE: todos
    app:
        image: node:12-alpine
        command: sh -c "apk add --no-cache python2 g++ make && yarn install && yarn run dev"
        ports:
            - 3000:3000
        working_dir: /app
        volumes:
            - ./:/app
        environment:
            MYSQL_HOST: mysql
            MYSQL_USER: root
            MYSQL_PASSWORD: secret
            MYSQL_DB: todos
volumes:
    todo-mysql-data:
````
## Démarrage de notre app multi container
$ docker compose up -d

## Vérification du fonctionnement
http://localhost:3000/

## Arret de notre app multi container
$ docker compose down