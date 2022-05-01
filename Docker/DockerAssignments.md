# Docker Assignments

### 1. Dockeriza la aplicación dentro de lemoncode-challenge, la cual está compuesta de 3 partes:

    -Un front-end con Node.js
    -Un backend en .NET que utiliza un MongoDB para almacenar la información.
    -El MongoDB donde se almacena la información en una base de.

### Requisitos del ejercicio: 
### Los tres componentes deben estar en una red llamada lemoncode-challenge.
###   -El backend debe comunicarse con el mongodb a través de esta URL mongodb://some-mongo:27017
###    -El front-end debe comunicarse con la api a través de http://topics-api:5000/api/topics
###    -El front-end debe estar mapeado con el host para ser accesible a través del puerto 8080.
###    -El MongoDB debe almacenar la información que va generando en un volumen, mapeado a la ruta /data/db.
###    -Este debe de tener una base de datos llamada TopicstoreDb con una colección llamada Topics. La colección Topics debe tener esta estructura: 

{"_id":{"$oid":"5fa2ca6abe7a379ec4234883"},"Name":"Contenedores"} ¡Añade varios registros!

---


### Creamos la red lemoncode challenge donde han de estar los tres componentes:
```console
docker network create lemoncode-challenge
```

### Creamos el volumen 'lemoncode-topics' para el contenedor 'some-mongo':
```console
docker run --name some-mongo --network lemoncode-challenge `
    --mount source=topics-data,target=/data/db `
    -p 27017:27017 `
    -d mongo
```
* Si queremos comprobar que el contenedor se haya creado correctamente vinculado a la red 'lemoncode-challenge', podemos usar:
`docker network inspect lemoncode-challenge`

### En DockerHub podemos comprobar que el contenedor se haya creado correctamente, con todos los parámetros necesarios:
![alt](https://eperedo-blog.s3.amazonaws.com/docker/mongo/docker-mongo-hub.PNG)

### Ahora, deberíamos poder conectarnos a nuestra MongoDB, por ejemplo con Mongo Compass a través de este connection string:

 `mongodb://localhost:27017`  _El puerto es el especificado por el enunciado_

### Una vez dentro, debemos crear una colección llamada 'Topics' y una base de datos con nombre 'TopicstoreDb'. Accediendo a la colección, importamos el fichero del bootcamp 'topics.json' en el directorio raíz.
* He añadido una carpeta 'Screenshots' donde podemos ver el resultado en Mongo Compass.

### Para construir el backend, necesitamos construir un Dockerfile teniendo en cuenta que usamos Express, MongoDB, .NET Core >= 3.1, npm y Nodejs. Hecho lo cual, construimos la imagen de 'topics' para el back. 
`docker build -t backend-topics-image`

### A la que añadimos un contenedor 'backend-topics':
```
docker run -d --name backend-topics `
--network lemoncode-challenge `
backend-topics-image
```

### Ahora necesitaríamos una variable de entorno para settear la connection string, pero ajaj no porque ya nos la da Lemoncode en el 'appsettings.json'. Solo hay que modificarlo para que no apunte al localhost sino a:
`"ConnectionString": "mongodb://some-mongo:27017"`

### Volvemos a ejecutar `docker network inspect lemoncode-challenge` para comprobar que se ha conectado correctamente a la red.

### Por último, nos queda construir el front. Comenzamos con el dockerfile, con una imagen de distro a nuestro gusto, para frameworks sencillos y no de bajo requerimiento con alpine nos basta(recuerdo que los Dockerfile tanto de Front como de Back se encuentran en sus respectivas carpetas dentro de esta subsección del repo root).

### Una vez lanzado el dockerfile, podemos comprobar con `docker images` que se ha creado la imagen del frontend tras ejecutar `docker build -t frontend-topics-image`.

* Como nota, al estar manejando múltiples dockerfiles, nos puede aparecer la duda de cuál estamos ejecutando, por defecto se ejecuta el del directorio actual, pero si queremos especificar uno concreto basta con usar el flag -f en nuestro comando:

    `docker build -t myimage -f ./wherever/wherever/Dockerfile ./name`



### Como antes, creamos el contenedor dentro de la imagen, recordando que los puertos son especificados en el enunciado y aclarando que en -p abcd:efgh [puerto externo : puerto interno] y con -e sobreescribimos la API URL.

```
docker run --name frontend-topics `
--network lemoncode-challenge `
-p 8080:3000 `
-e API_URI=http://backend-topics:5000/api/topics `
frontend-topics-image
```

### Ya podríamos ver el contenido de nuestra app accediendo a `http://localhost:8080`

---
---

### 2. Ahora que ya tienes la aplicación del ejercicio 1 dockerizada, utiliza Docker Compose para lanzar todas las piezas a través de este. Debes plasmar todo lo necesario para que esta funcione como se espera: la red que utilizan, el volumen que necesita MongoDB, las variables de entorno, el puerto que expone la web y la API. Además debes indicar qué comandos utilizarías para levantar el entorno, pararlo y eliminarlo.
* Para este ejercicio, se han modificado ligeramente los archivos de backend y frontend, se encuentran bajo el directorio _PreRequisitesej2_


### Creamos en primer lugar el fichero docker-compose que requerirá nuestra app.

```
version: '3.4'

services:
some-mongo:
    image: mongo:latest
    volumes:
    - topics-data:/data/db   
    restart: always
    networks:
        - lemoncode-challenge
topics-api:
    depends_on:
    - some-mongo
    build: 
    context: ./backend
    dockerfile: Dockerfile
    restart: always
    networks: 
    - lemoncode-challenge
frontend-web:
    depends_on:
    - topics-api
    build: 
    context: ./frontend
    dockerfile: Dockerfile
    ports:
    - "8080:3000"
    restart: always
    environment:
    - API_URI=http://topics-api:5000/api/topics
    networks: 
    - lemoncode-challenge
volumes:
    topics-data:
networks:
    lemoncode-challenge:
```


### Por último, simplemente cumplimos el añadido criterio del ejercicio(levantar, detener y parar):

* `docker-compose --project-name lemoncode-challenge-docker up --build -d` Lanza todos los contenedores(--build) y en el background(-d). Es importante lanzar este comando desde la raíz del directorio de proyecto, porque tomará de ahí el docker compose file que haya.

* `docker-compose -p lemoncode-challenge-docker stop` Para los contenedores(-p o --project-name para especificar un nombre de proyecto alternativo). Para más info sobre los flags y abreviaturas en docker-compose(no confundir con docker [ESPACIO] compose): https://docs.docker.com/compose/reference/

* `docker-compose -p lemoncode-challenge-docker rm` Borra los contenedores una vez detenidos con el comando previo.

### Para asegurarnos verdaderamente de que TODO está borrado(TODO == contenedores, volúmenes, imágenes y redes creados):
* `docker-compose -p lemoncode-challenge-docker down --volumes --rmi 'all'`
  
  * 'down' elimina contendedores y redes creados por 'up' al comienzo, eso sí, los especificados en el docker-compose.yml.
  * '--volumes' o '-v' se añaden como flags a 'down' para indicar que además se borren los volúmenes especificados en su sección homónima dentro del docker-compose.yml.
  * '--rmi 'all' ' elimina todas las imágenes base utilizadas por los distintos servicios.