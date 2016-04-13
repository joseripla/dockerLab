<<  [Principal](https://github.com/erasmolpa/dockerLab/blob/master/Principal.md)

#  Anexo

##  Contenidos:

* Algunos [comandos](#comandos) útiles.
* Introducción del siguiente laboratorio.
      * Docker Compose. Orquestando Servicios.
      * [Docker Cloud](https://cloud.docker.com/). Plataforma para desplegar contenedores.
      * Docker y la IC. 
                      *  https://circleci.com/docs/docker/
		      *  https://www.docker.com/sites/default/files/RA_CI%20with%20Docker_08.25.2015.pdf
		      *  Ejemplo http://jenkins-58674a6f-1.225b690b.cont.dockerapp.io:32769/
##comandos

### Last Ids

```
alias dl='docker ps -l -q'
docker run ubuntu echo hello world
docker commit `dl` helloworld
```

### Commit with command (needs Dockerfile)

```
docker commit -run='{"Cmd":["postgres", "-too -many -opts"]}' `dl` postgres
```

### Get IP address

```
docker inspect `dl` | grep IPAddress | cut -d '"' -f 4
```

or install [jq](https://stedolan.github.io/jq/):

```
docker inspect `dl` | jq -r '.[0].NetworkSettings.IPAddress'
```

or using a [go template](https://docs.docker.com/reference/commandline/inspect)

```
docker inspect -f '{{ .NetworkSettings.IPAddress }}' <container_name>
```

### Get port mapping

```
docker inspect -f '{{range $p, $conf := .NetworkSettings.Ports}} {{$p}} -> {{(index $conf 0).HostPort}} {{end}}' <containername>
```

### Find containers by regular expression

```
for i in $(docker ps -a | grep "REGEXP_PATTERN" | cut -f1 -d" "); do echo $i; done`
```

### Get Environment Settings

```
docker run --rm ubuntu env
```

### Kill running containers

```
docker kill $(docker ps -q)
```

### Delete old containers

```
docker ps -a | grep 'weeks ago' | awk '{print $1}' | xargs docker rm
```

### Delete stopped containers

```
docker rm -v `docker ps -a -q -f status=exited`
```

### Delete dangling images

```
docker rmi $(docker images -q -f dangling=true)
```

### Delete all images

```
docker rmi $(docker images -q)
```

### Delete dangling volumes

As of Docker 1.9:

```
docker volume rm $(docker volume ls -q -f dangling=true)
```

In 1.9.0, the filter `dangling=false` does _not_ work - it is ignored and will list all volumes.

### Show image dependencies

```
docker images -viz | dot -Tpng -o docker.png
```

### Slimming down Docker containers  [Intercity Blog](http://bit.ly/1Wwo61N)

- Cleaning APT in a RUN layer  
This should be done in the same layer as other apt commands.  
Otherwise, the previous layers still persist the original information and your images will still be fat.  
```
RUN {apt commands} \
 && apt-get clean \  
 && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
```
- Flatten an image
```
ID=$(docker run -d image-name /bin/bash)
docker export $ID | docker import – flat-image-name
```

- For backup
```
ID=$(docker run -d image-name /bin/bash)
(docker export $ID | gzip -c > image.tgz)
gzip -dc image.tgz | docker import - flat-image-name
```

### Monitor system resource utilization for running containers

To check the CPU, memory, and network i/o usage of a single container, you can use:

```
docker stats <container>
```

For all containers listed by id:

```
docker stats $(docker ps -q)
```

For all containers listed by name:

```
docker stats $(docker ps --format '{{.Names}}')
```




### COMANDOS DOCKER COMPOSE
Aquí los comandos más usados de docker-compose, asumiendo que el usuario ha llevado a cabo el ciclo de creación y mantenimiento de contenedores mediante el manager oficial de docker, y que mantiene un determinado repositorio de imágenes (en local o remoto).

Arrancar un entorno en primer plano, es decir, la ejecución del contenedor es un proceso que hereda de la consola. La salida estándar y de error del contenedor se mostrará por pantalla. Como no se le ha pasado ningún argumento más, Docker Compose tomará el fichero con nombre exacto docker-compose.yml en la ruta en la que se está posicionado en ese momento.

<code>
$ docker-compose up

###### Si se quiere pasar como argumento el fichero de especificación:

$ docker-compose -f <fichero> up

###### Ejecutar en segundo plano:

$ docker-compose -f <fichero> up -d

###### Parar un entorno:
$ docker-compose -f <fichero> kill

###### Para ver los contenedores parados se haría

$ docker ps -a
Eliminar por completo un entorno:
$ docker-compose -f <fichero.yml> rm
Comprobar los logs de los contenedores (lo explicado en el primer punto):
$ docker-compose logs
Comprobar un fichero con la especificación yml del entorno
$ docker-compose -f <fichero.yml> config
</code>


Docker Compose

Docker Compose es una herramienta para definir y correr aplicaciones multicontenedores. Hacemos uso de un archivo docker-compose para configurar los servicios que necesita la aplicación. Luego haciendo uso de un simple comando, creamos los contenedores necesarios e iniciamos todos los servicios especificados en la configuración.
Compose es bueno para desarrollo, pruebas y flujos de trabajo de integración continua.
En el caso de los desarrolladores, tienen la habilidad de correr las aplicaciones en un ambiente aislado e interactuar con la aplicación. El archivo de compose provee una forma de documentar y configurar todas las dependencias de la aplicación y al final solo tiene que hacer uso de un comando para subir todo el ambiente.
Otro punto importante en los procesos de Despliegue e integración continua es el banco de pruebas. Este banco de pruebas requiere de un ambiente donde realizar las pruebas. Compose provee una manera de crear y destruir los ambientes de prueba aislados para ese banco de pruebas. Definiendo todo el ambiente en un archivo compose se puede crear y destruir los ambientes con pocos comandos.
Instalando Docker Compose

Antes de proceder con la instalación de compose es bueno consultar la página de lanzamiento https://github.com/docker/compose/releases y ajustar el comando de instalación acorde con la última versión estable.
$ curl -L https://github.com/docker/compose/releases/download/1.6.2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

$ chmod +x /usr/local/bin/docker-compose



Comprobamos la instalación
$ docker-compose --version
Usando Compose

Vamos a ver el uso de Compose en un caso simple, definiendo un ambiente para Wordpress.
Descargamos el CMS de WordPress y lo descomprimimos.
$ curl https://wordpress.org/latest.tar.gz | tar -xvzf –
El directorio resultante es wordpress/, esto lo podemos cambiar a nuestro gusto
$ mv wordpress/ jsitech/
Accedemos al directorio
$ cd jsitech/
El Próximo paso es crear un Dockerfile dentro del directorio, que definirá el contenedor que estará ejecutando la aplicación.
   $ vi Dockerfile
    FROM ubuntu:14.04

    RUN apt-get update && apt-get install php5 php5-mysql -y

    ADD . /codigo
    
    
    Este Dockerfile crea el Contenedor base con los requisitos para ejecutar el cms de WordPress y agrega los archivos correspondiente
El próximo paso es crear un archivo docker-compose.yml que iniciará los servicios y una instancia separada de MySQL.
$ nano docker-compose.yml

 web:
     build: .
     command: php -S 0.0.0.0:8080 -t /jsitech
     ports:
     - "8080:8080"

     links:
     - db

     volumes:
     - .:/jsitech

    db:
     image: mysql
     environment:
     MYSQL_ROOT_PASSWORD: jsitech
     MYSQL_DATABASE: jsitech
     MYSQL_USER: jsitech
     MYSQL_PASSWORD: jsitech
Vamos a explica brevemente los argumentos
web : Nombre que define el servicio
build : crea el contenedor, en este caso pasamos . , para que haga uso del Dockerfile que creamos recientemente.
command : El comando que le pasamos al contenedor al momento de su ejecución.
ports: Mapeo de Puertos
links : Definimos el enlace de los contenedores
volumes : Creamos el volumen que contiene nuestro código
db: Nombre que define nuestro contenedor con la base de datos
image: la imagen donde basará su contenedor
environment: aquí pasamos las variables, en este caso es un contenedor con MySQL y le pasamos la base de datos a crear y las credenciales.
Ya aquí tenemos el ambiente multi-contenedor listo, pero antes de lanzar nuestro proyecto de wordpress debemos configurar wp-config.php con las credenciales que le pasos en las variables

  // ** MySQL settings - You can get this info from your web host ** //

    /** The name of the database for WordPress */
    define('DB_NAME', 'jsitech');

    /** MySQL database username */
    define('DB_USER', 'jsitech');

    /** MySQL database password */
    define('DB_PASSWORD', 'jsitech');

    /** MySQL hostname */
    define('DB_HOST', 'db:3306');
    
    Ya con todo definido en los archivos correspondiente, solo es lanzar el proyecto multi-contenedor. Ejecutamos el comando
# docker-compose up
Esto se encargará de crear las imágenes necesarias y lanzar los contenedores correspondientes a la web y la base de datos.
    
Vamos a ver si todo funciona bien, si recuerdan creamos un mapeo de puertos 8080:8080, lo que quiere decir que debemos poder acceder al contenedor con la IP del host de docker mediante ese puerto.
http://192.168.1.9:8080
