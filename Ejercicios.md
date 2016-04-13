<<  [Principal](http://erasmolpa.github.io/dockerLab)
|   [Tutorial](https://github.com/erasmolpa/dockerLab/blob/master/TutorialDocker.md) >>

#   Ejercicios

##  Contenido:
* [Ejercicios](https://github.com/erasmolpa/dockerLab/blob/master/Ejercicios.md)
   
   * [Ejercicio 0](https://github.com/erasmolpa/dockerLab/blob/master/Ejercicios.md) .Familiarizarnos con Docker,Docker Hub y Kitematic.
   * [Ejercicio 1](https://github.com/erasmolpa/dockerLab/blob/master/Ejercicios.md) .Creando el primer contenedor.
   * [Ejercicio 2](https://github.com/erasmolpa/dockerLab/blob/master/Ejercicios.md) .Encapsulando un contenedor como servidor. Contenedor [tomcat](https://hub.docker.com/_/tomcat/)
   * [Ejercicio 3](https://github.com/erasmolpa/dockerLab/blob/master/Ejercicios.md) .Creando un microservicio con Node.
   * [Ejercicio 4](https://github.com/erasmolpa/dockerLab/blob/master/Ejercicios.md) .Creando un contenedor MongoDb.Básico.
   * [Ejercicio 5](https://github.com/erasmolpa/dockerLab/blob/master/Ejercicios.md) .Creando una base de datos Mysql:
   * [Ejercicio 6](https://github.com/erasmolpa/dockerLab/blob/master/Ejercicios.md) .Comunicando contenedores:
   * [Ejercicio 7](https://github.com/erasmolpa/dockerLab/blob/master/Ejercicios.md#Ejercicio_7) .Docker Compose.Creando un Wordpress con MariaDb:
   * [Ejercicio 8](https://github.com/erasmolpa/dockerLab/blob/master/Ejercicios.md) .Docker Compose. Creando múltiples multiservicios con Node y Nginx.
   * [Ejercicio 9](https://github.com/erasmolpa/dockerLab/blob/master/Ejercicios.md) .Ejecutando un jenkins en local. La base de datos se almacena en un volumen.
                           
#### <i class="icon-pencil"></i>Ejercicio 0. Familiarizarnos con Docker,Docker Hub y Kitematic.

Crear un primer contenedor...
```
$ docker images
 
$ docker search ubuntu

$ docker search -s 10 ubuntu

$ docker run -i -t ubuntu ./bin/bash
```
Otra forma:
```
$ docker run ubuntu:14.04 echo "Hello Devops"

$ docker run ubuntu ps ax

$ docker ps -a
```
**También podemos buscar las imágenes en [Kitematic](https://kitematic.com/) o Docker [Hub](https://hub.docker.com) y hacer un pull and run.**

#### <i class="icon-pencil"></i>Ejercicio 1.Creando el primer contenedor:

```
sudo docker run -i -t --name ubuntudocker ubuntu:latest /bin/bash

# para ver la imagen corriendo

docker ps

#para ver toda la información detallada del contenedor

docker inspect "IMAGE ID"
```

#### <i class="icon-pencil"></i>Ejercicio 2. Encapsulando un contenedor como servidor tomcat.

###### Ejercicio 2.1. Crear el contenedor.
```
docker run -it --rm -p 8888:8080 tomcat:8.0
```
###### Ejercicio 2.2. Dockerizar Docker con [Graylog](https://www.ctl.io/developers/blog/post/docker-tomcat-graylog).

#### <i class="icon-pencil"></i>Ejercicio 3. Creando un microservicio con Node.



###### Ejercicio 3.1. Usando una Api Node publicada.Modificando el contenedor creado.

```
$ docker pull erasmolpa/loopbackend:1.0

$ docker run -it erasmolpa/loopbackend ./bin/bash

$ mkdir microservice

$ cd microservice

$ git clone https://github.com/erasmolpa/loopCar.git

$ cd /loopCar/loopCar

$ npm install

$ node .
```
###### para ver que la hay un contenedor running de la imagen
```
$ docker ps -a

$ docker images 
```
###### Paso opcional si queremos crear una imagen nueva
```
$ docker commit b26d92b24e72 erasmolpa/loopbackend:microservice

$ docker images
```
###### Sobre la misma imagen sería:
```
$ docker tag sha256:b3bdb  erasmolpa/loopbackend:microservice
```
###### Para publicar la imagen, sino nos hemos logeado aun 
```
$ docker login --username=username --email=user@gmail.com
```
###### Publicamos la imagen 
```
$ docker push erasmolpa/loopbackend:microservice

$ docker stop b26d92b24e72 Paramos el contenedor de microservice para arrancarlo otra vez
```
###### Mapeamos el puerto interno 3000 al externo 9000
```
$ docker run -it -p 9000:3000 erasmolpa/loopbackend:microservice ./bin/bash

$ cd microservice/loopCar/looCar

$ node .

 en el navegador abrir http://192.168.99.100:9000/explorer/
```

###### Ejercicio 3.2.Usando una Api Node publicada.La Api se monta en el contenedor como volumen.

```
$ mkdir directorio

$ cd direcrtorio

$ git clone https://github.com/erasmolpa/loopCar.git

$ cd /loopCar/loopCar
  
$ docker run -it -v /directorio/loopCar:/host -p 9000:3000 erasmolpa/loopbackend:latest ./bin/bash
```
###### Entramos a la carpeta host dentro del contenedor, y es ahí donde hemos dicho que monte el código.
```
$ cd host/"directorioCodigo"
$ node . Arranca la app en 192.168.99.100:9000/explorer/
```

###### Ejercicio 3.1. Node. Ejercicio [Adicional](https://nodejs.org/en/docs/guides/nodejs-docker-webapp/). Así es como proponen en Node montar una aplicación Dockerizada.

#### <i class="icon-pencil"></i>Ejercicio 4.Creando un contenedor MongoDb.Básico.

En la documentación oficial de Docker , explican como [Dockerizar](https://docs.docker.com/engine/examples/mongodb/) un mongodb.

Existen varias alternativas:

Crear nuestro propio DockerFile:
```
# Crear el directorio de trabajo
$ mkdir mongodb
$ cd mongodb
$ vim Dockerfile # y copiar el contenido de :(https://github.com/erasmolpa/mongoDocker.git)
$ docker build -t mongodb . #donde mongodb en este caso es el nombre de la imagen
$ docker run -d --name mongodb -p 27017:27017 -p 28017:28017 mongodb  --httpinterface 
```

El contenido de ese DockerFile es idéntico al propietario.Podríamos modificarlo para ponerlo a nuestras necesidades.
```
$ docker search mongodb 
```
Una vez encontrada la imagen que queremos lanzar podemos hacer un pull y un run o directamente hacer un run.

```
$ docker  pull tutum/mongodb # para bajarnos la imagen de tutum/mongodb que es la oficial # Paso alternativo si queremos hacer un pull primero.
```
```
$ docker run -d --name mongoDB -p 27017:27017 -p 28017:28017 erasmolpa/mongo --httpinterface 
```

Esto monta el mongobd en 192.168.99.100:27017 y la consola en 192.168.99.100:28017

#### <i class="icon-pencil"></i>Ejercicio 5.Creando una base de datos Mysql:

https://hub.docker.com/_/postgres/

#### <i class="icon-pencil"></i>Ejercicio 5.Comunicando contenedores:
https://www.docker.com/products/docker-toolbox#/tutorials
#### <i class="icon-pencil"></i>Ejercicio 6. Docker Compose.Creando un Wordpress con MariaDb:

 Accediendo al [hub oficial de wordpress](https://hub.docker.com/r/library/wordpress/) vemos que proponen dos caminos para crear una web wordpress:

Con dos contenedores que se comunican por link container...

```
 docker run --name some-wordpress --link some-mysql:mysql -d wordpress
```

O con docker compose, creando el docker-compose.yml con el siguiente contenido:

```
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 8080:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: example
```

Una vez creado el compose, lanzar:
```
docker-compose up
```
Y ya puedes instalar tu wordpress... 
```
Ojo, he puesto la ip de mi instancia de docker..

http://192.168.99.100:8080/wp-admin/install.php

```

#### <i class="icon-pencil"></i>Ejercicio_7. Docker Compose. Creando múltiples multiservicios con Node y Nginx:


#### <i class="icon-pencil"></i>Ejercicio 8. Ejecutando un jenkins en local. La base de datos se almacena en un volumen .

```
# Sustituir /Users/erasmodominguezjimenez/Public/Dockers/dockerVolume/ por la ruta de carpeta donde queremos almacenar los datos.
$ docker run --name jenkins -p 8080:8080 -p 50000:50000 -v /Users/erasmodominguezjimenez/Public/Dockers/dockerVolume/:/var/jenkins_home erasmolpa/jenkins:latest

Esto monta jenkins en http://192.168.99.100:8080/
```
Que pasaría si modificamos el Jenkins, instalando plugins? . Al parar el contenedor y volver a arrancarlo que pasaría.?
