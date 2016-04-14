<<  [Principal](http://erasmolpa.github.io/dockerLab)
|   [Ejercicios](https://github.com/erasmolpa/dockerLab/blob/master/Ejercicios.md) >>

# Tutorial de Docker

##  Contenido:

 * [Images](#imagenes)
 
 * [Containers](#Contenedores)
       * [Layers](#layers)
       * [Links](#links)
       * [Volumes](#volumes)
       * [Exposing Ports](#exposing-ports)
       * [Red](#red)
 * [Dockerfile](#dockerfile)
       * [Best Practices](#best-practices)
 * [Registry](#registro)
    
## Contenedores

### Ciclo de vida 

* [`docker create`](https://docs.docker.com/reference/commandline/create) -> Crea un contenedor pero no lo arranca
* [`docker run`](https://docs.docker.com/reference/commandline/run)  -> Crea y arranca un contenedor.
* [`docker rm`](https://docs.docker.com/reference/commandline/rm)  -> Elimina un contenedor.
* [`docker update`](https://docs.docker.com/engine/reference/commandline/update/) --> Actualizar recursos de un contenedor. ejm: cambio en memoria size.

Curiosidades....

 `docker run --rm`  .Para Eliminar un contenedor después de parar su ejecución.Contenedor de tránsito.
 `docker run -i -t /bin/bash` .Arrancamos un docker en modo terminal. -i para conectar a STDIN y -t para decir que entramos en modo terminal.
 
Para mapear directorios entre Host y Contenedor -->  `docker run -v $HOSTDIR:$DOCKERDIR`.  Ver [volúmenes en Docker](https://github.com/wsargent/docker-cheat-sheet/#volumes) 

Para eliminar el volumen asociado a un contenedor. `docker rm -v`.

### Interactuar con Contenedores.

* [`docker start`](https://docs.docker.com/reference/commandline/start)  --> Inicia un contenedor que está corriendo.
* [`docker stop`](https://docs.docker.com/reference/commandline/stop) --> Para un contenedor que está corriendo.
* [`docker restart`](https://docs.docker.com/reference/commandline/restart) --> Para y arrranca un container.
* [`docker pause`](https://docs.docker.com/engine/reference/commandline/pause/) --> "congela" un conteneder tal cuál está en ese momento. Pausa todo sus procesos.
* [`docker unpause`](https://docs.docker.com/engine/reference/commandline/unpause/)  --> Descongela el contenedor y lo arranca .
* [`docker kill`](https://docs.docker.com/reference/commandline/kill) --> Mata el proceso con el que correo un contenedor.Para el contenedor.
* [`docker attach`](https://docs.docker.com/reference/commandline/attach) --> Conectamos a un contenedor que esta corriendo.

```
$ sudo docker attach 665b4a1e17b6     #Para entrar usando el ID=665b4a1e17b6
$ sudo docker attach mysql_container  #Para entrar usando en nombre,donde el nombre es mysql_container
```

### Información sobre Contenedores.

* [`docker ps`](https://docs.docker.com/reference/commandline/ps)  --> Muestra los contenedores que está corriendo.
* [`docker logs`](https://docs.docker.com/reference/commandline/logs) --> Obtenemos los logs de un contenedor. Es posible usar drivers de terceros.
* [`docker inspect`](https://docs.docker.com/reference/commandline/inspect) --> Obtener información completa de un contendor.
* [`docker events`](https://docs.docker.com/reference/commandline/events) --> Muestra información en tiempo real de los eventos relativos a los contenedores.
* [`docker port`](https://docs.docker.com/reference/commandline/port) --> Muestra conexión pública de puertos de un contenedor.
* [`docker top`](https://docs.docker.com/reference/commandline/top) --> Muestra los procesos que corren en un contenedor.
* [`docker stats`](https://docs.docker.com/reference/commandline/stats) --> Muestra estadísticas de uso de recursos del contenedor que corre.
* [`docker diff`](https://docs.docker.com/reference/commandline/diff) ---> Muestra todos los cambios que se van realizando dentro del filesystem del contenedor.

`$ docker ps `     Muestra los contenedores que están corriendo.
`$ docker ps -a` Muestra los contenedores que están parados y corriendos.

### Importar / Exportar Entre contenedor  y Host.

* [`docker cp`](https://docs.docker.com/reference/commandline/cp) --> Copia ficheros entre el contenedor y el host.
* [`docker export`](https://docs.docker.com/reference/commandline/export)  --> Exporta el contenido del sistema de archivos de un contenedor a un archivo tar.

`$ docker cp <containerId>:/file/path/within/container /host/path/target `
 `$ docker export "container ID " > dockerBackup.tar ` 


### Ejecutar comandos.

* [`docker exec`](https://docs.docker.com/reference/commandline/exec) --> Para Ejecutar un comando en el contenedor.

Una de las necesidades más habituales es comunicarse con el contenedor.Esto se puede hacer con exec o attach.
```
$ sudo docker exec -i -t 665b4a1e17b6  #Para entrar usando el ID=665b4a1e17b6  
$ sudo docker exec -it mysql_container /bin/bash #Para entrar usando en nombre,donde el nombre es mysql_container
```

## Layers
El sistema de ficheros de Docker esta basado en Capas.Ya se explicó este concepto en la presentación de [Arquitectura](http://es.slideshare.net/ErasmoDominguezJimen/devops-episodio-1-devopstnf?qid=a3de014b-9e2f-41fb-8d52-cb05aef9f9ac&v=&b=&from_search=1) , diapositivas 29 -33 donde claramente se vé que internamente funciona como un Git.

Docker crea una capa superior de escritura, donde se almacenan todos todos los cambios que hacemos sobre una imagen Padre. La imagen Padre es de sólo lectura.

Para más detalles sobre la estrategia de gestión de imágenes ver la [docu](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) de Docker.

## Links

Los [Links](https://docs.docker.com/userguide/dockerlinks/) , es el mecanismo que ofrece Docker para establecer diálogo entre contenedores.

Para hacer links entre contenedores , existen dos roles:

 *  Recipient container es el que tiene acceso al Source (ejem: servidor Web).
 *  Source Container, es el que se expone como recurso (ejem: base de datos).
 
```
$ docker run --name CONTAINER
$ docker run -d --link CONTAINER:ALIAS --name LINKED user/wordpress
```

Para elimiar el link creado.

 `docker rm --link `.

## Volumes

Un Volumen es asignar un directorio a un container para almacenar datos.

Son de utilidad para compartir información entre contenedores, por ejemplo ficheros, bases de datos o usarse en contenedores de tránsito, contenedores que generan estos volúmenes de información para que otros contenedores lo utilicen posteriormente.

Los [Volumes](https://docs.docker.com/engine/userguide/containers/dockervolumes/) de Docker son  [free-floating filesystems](https://docs.docker.com/userguide/dockervolumes/),es decir , no necesitan ser conectados por un contenedor en concreto. 

* Los cambios son excluidos cuando se actualiza una imagen.
* Se persiste cuando un contenedor se borra.
* Se puede mapear a una carpeta del host.
* Pueden ser compartidos entre contenedores.

### Ciclo de vida

* [`docker volume create`](https://docs.docker.com/engine/reference/commandline/volume_create/)
* [`docker volume rm`](https://docs.docker.com/engine/reference/commandline/volume_rm/)

### Interactuar

* [`docker volume ls`](https://docs.docker.com/engine/reference/commandline/volume_ls/)
* [`docker volume inspect`](https://docs.docker.com/engine/reference/commandline/volume_inspect/)

```
docker run -v /Users/wsargent/myapp/src:/src
```
En el siguiente [artículo](http://crosbymichael.com/advanced-docker-volumes.html) se expone como configurar de forma avanzada los volúmenes.

Leer el [artículo](http://container42.com/2013/12/16/persistent-volumes-with-docker-container-as-volume-pattern/) para configurar un contenedor como proveedor de datos.

## Exposing ports

Es el mecanismo de Docker configurar los puertos por los que escucha el contenedor
especificados en su ejecución. Evidentemente hay que mapear los puertos entre los expuestos por el contenedor y por donde se accede desde el host. Ver la documentación oficial [incomming-ports](https://docs.docker.com/reference/run/#expose-incoming-ports).

Para mapear puertos entre el contenedor y el host usamos  `-p`:

```
docker run -p 127.0.0.1:$HOSTPORT:$CONTAINERPORT --name CONTAINER -t someimage
```
Además podemos decirle a Docker que el contenedor escucha en un puerto especifico, utilizando la directiva 
 [EXPOSE](https://docs.docker.com/reference/builder/#expose) en el Dockerfile.
 
```
EXPOSE <CONTAINERPORT>
```

Para ver los puertos que usa un contenedor:
```
docker port CONTAINER
```

## Images

Como ya hemos comentado muchas veces, las imágenes son  [plantillas para contenedores](https://docs.docker.com/engine/understanding-docker/#how-does-a-docker-image-work).

### Ciclo de vida

* [`docker images`](https://docs.docker.com/reference/commandline/images) -> Muestra todas las imágenes.
* [`docker import`](https://docs.docker.com/reference/commandline/import) --> Crea una imagen de un fichero tgz (local o remoto).
* [`docker build`](https://docs.docker.com/reference/commandline/build) --> Crea imagen de un Dockerfile.
* [`docker commit`](https://docs.docker.com/reference/commandline/commit) -> Guarda los cambios hechos en un contenedor como una nueva imagen.Crea una imagen a partir del contenedor.
* [`docker rmi`](https://docs.docker.com/reference/commandline/rmi) --> Para eliminar una imagen existente.
* [`docker load`](https://docs.docker.com/reference/commandline/load) -->Carga una imagen desde un fichero tar.
* [`docker save`](https://docs.docker.com/reference/commandline/save)  --> Guarda una imagen en un fichero tar. Almacena todos los Layers, Tags y versiones de esa imagen.
*
### Interactuar

* [`docker history`](https://docs.docker.com/reference/commandline/history)  --> Muestra un histórico de una imagen.
* [`docker tag`](https://docs.docker.com/reference/commandline/tag) --> Crea un Tag de una imagen con el nombre facilitado.


## Red

Internamente, Docker utiliza los mecanismos de linux para dar conectividad a los contenedores.En la instalación, docker crea una interfaz virtual ``docker0` en el Host a la que se asigna una Ip , por ejemplo 192.168.99.100.

Podemos deshabilitar esto por completo pasando la opción --net none . En estos casos las conexiones de entrada y salida se hacen por archivos y los streams estándar.

Para más detalles [networking docker](https://docs.docker.com/engine/userguide/networking/work-with-networks/).

### Ciclo de vida.

* [`docker network create`](https://docs.docker.com/engine/reference/commandline/network_create/)
* [`docker network rm`](https://docs.docker.com/engine/reference/commandline/network_rm/)

### Interactuar.
* [`docker network ls`](https://docs.docker.com/engine/reference/commandline/network_ls/)
* [`docker network inspect`](https://docs.docker.com/engine/reference/commandline/network_inspect/)

### Connection

* [`docker network connect`](https://docs.docker.com/engine/reference/commandline/network_connect/)
* [`docker network disconnect`](https://docs.docker.com/engine/reference/commandline/network_disconnect/)

Ejemplo [especificando IP address container](https://blog.jessfraz.com/post/ips-for-all-the-things/):

```
# Servidor Nginx en un contenedor con una ip específica
$ docker run --rm -it --net iptastic --ip 203.0.113.2 nginx
```

##Registro

Como crear, versionar y publicar nuestras propias imágenes? en el siguiente [enlace](https://docs.docker.com/mac/step_six/) se explica como .

### Imágenes en repositorios. Docker Hub.

Un repositorio es un conjunto de imágenes, relacionadas a tags.

Un registro es un host que almacena repositorios.

Ir a [Docker Hub](https://hub.docker.com/) .

Ver la guía de usuario para [manejo de imágenes](https://docs.docker.com/userguide/dockerrepos/).

* [`docker login`](https://docs.docker.com/reference/commandline/login)  Login en un registro
* [`docker search`](https://docs.docker.com/reference/commandline/search) Buscar registros de imágenes.
* [`docker pull`](https://docs.docker.com/reference/commandline/pull) -> "pull" de una imagen desde un registro a mi máquina local.
* [`docker push`](https://docs.docker.com/reference/commandline/push) --> "push" de una imagen a un registro desde mi máquina.

### Como tener un local registry

Se puede tener un registro de imágenes propios usando la imagen de docker hub [docker-registry](https://github.com/docker/distribution)  y hacer [local deploy](https://github.com/docker/distribution/blob/master/docs/deploying.md) .


## Dockerfile

El Dockerfile es el [fichero de configuracion](https://docs.docker.com/reference/builder/) donde definimos todas las intrucciones que conforman una imagen Docker. Para crear una imagen a partir de un Dockerfile utilizaremos el comando `docker build` .

Docker build le manda todo el contexto del directorio actual al Docker daemon,por lo que es muy importante aislar el  Dockerfile, creando un directorio solo para dicha configuración , un "workspace" y agregar en eses workspace todo lo que necesitemos para construir la imagen por medio del Dockerfile.

Docker Daemon corre las instrucciones del Dockerfile linea por linea y va lanzando los resultados en pantalla y va creando imágenes intermedias durante la creación de la imágen final.

El Daemon irá haciendo una limpieza automáticamente de las imágenes intermedias.

Si por alguna razón la creación de la imágen falla, el Daemon hará uso de las imágenes intermedias, y continuará la creación en el punto donde falló.

### Instrucciones y Etiquetas.

* [.dockerignore](https://docs.docker.com/reference/builder/#dockerignore-file) es el .gitignore de docker.

* [FROM](https://docs.docker.com/reference/builder/#from) -->Establece la imagen base de la que partimos. Indica la imágen base que va a utilizar para seguir futuras instrucciones. Buscará si la imagen se encuentra localmente, en caso de que no, la descargará.

```
Sintaxis
FROM <imagen>
FROM <imagen>:<tag>

Ejemplos
FROM ubuntu:latest
```
* [MAINTAINER](https://docs.docker.com/reference/builder/#maintainer) -->Datos del Autor de la imagen.
```
Sintaxis
MAINTAINER <nombre> <Correo>

Ejemplo
MAINTAINER Pepe Grillo “pepegrillo@gmail.com”
```
* [RUN](https://docs.docker.com/reference/builder/#run) -->Esta instrucción ejecuta cualquier comando en una capa nueva encima de una imágen y hace un commit de los resultados. Esa nueva imágen intermedia es usada para el siguiente paso en el Dockerfile.
```
RUN tiene 2 formatos:
RUN <comando>, esta es la forma shell, /bin/sh -c
RUN [“ejecutable”, “parámetro1”. “parámetro2], este es el modo ejecución
```
* [CMD](https://docs.docker.com/reference/builder/#cmd) --> Es la ejecución del contenedor. El objetivo de esta instrucción es proveer valores por defecto a un contenedor. Sólo puede existir una instrucción CMD en un DockerFile, si colocamos más de uno, solo el último tendrá efecto. 
```
CMD tiene tres formatos:
CMD [“ejecutable”, “parámetro1”, “parámetro2”] , este es el formato de ejecución
CMD [“parámetro1”, “parámetro2”] , parámetro por defecto para punto de entrada
CMD comando parámetro1 parámetro2, modo shell
```

Ejemplos:
```
Modo Shell
CMD echo “Esto es una prueba”
```
Si queremos ejecutar un comando sin un shell, debemos expresar el comando en formato JSON y dar la ruta del ejecutable. Es el formato recomendado.
```
CMD [“/usr/bin/service”, “httpd”, “start”]
```

* [EXPOSE](https://docs.docker.com/reference/builder/#expose) --> Esta instrucción le especifica a docker que el contenedor escucha en los puertos especificados en su ejecución. EXPOSE no hace que los puertos puedan ser accedidos desde el host, para esto debemos mapear los puertos usando la opción -p en docker run.

Ejemplo:
```
EXPOSE 80 443

# La escucha....
$ docker run centos:centos7 -p 8080:80
```
* [ENV](https://docs.docker.com/reference/builder/#env) sets environment variable.

* [ADD](https://docs.docker.com/reference/builder/#add)  --> copies new files, directories or remote file to container.  Invalidates caches. Avoid `ADD` and use `COPY` instead.

* [COPY](https://docs.docker.com/reference/builder/#copy)  --> Permite copiar ficheros y directorios en el contenedor.

* [ENTRYPOINT](https://docs.docker.com/reference/builder/#entrypoint) --> Configura un contenedor para comportarse como un ejecutable.

```
ENTRYPOINT tiene 2 formas:
ENTRYPOINT [“ejecutable”, “parámetro1”, “parámetro2”], forma de ejecución
ENTRYPOINT comando parámetro1 parámetro2, forma shell
```
Veamos un ejemplo de uso ENTRYPOINT en un DockerFile

```
FROM ubuntu:14.04
ENTRYPOINT [“http”, “-v ]”
CMD [“-p”, “80”]

```
En este ejemplo corremos httpd, en modo verbose, en el ENTRYPOINT, y los argumentos que entendamos puedan cambiar con CMD, puerto 80. Si quisiera correr el contenedor con httpd corriendo en el puerto 8080, solo tendría que ejecutar :
```
docker run ubuntu:14.04 -p 8080.
```

* [VOLUME](https://docs.docker.com/reference/builder/#volume) --> Crear un punto de montaje , tanto en el host como interno del contenedor. Útil para compartir datos entre contenedores, o para definir donde almacenar los datos de contenedores para base de datos.Si ejecutamos docker run pasándole un VOLUME, iniciará el contenedor con los datos que existan en la ubicación.
```
VOLUME [“/var/tmp”] Es un json
VOLUME /var/tmp o un argumento "plano"
```

* [USER](https://docs.docker.com/reference/builder/#user) sets the user name for following RUN / CMD / ENTRYPOINT commands.
* [WORKDIR](https://docs.docker.com/reference/builder/#workdir) sets the working directory.
* [ARG](https://docs.docker.com/engine/reference/builder/#arg) defines a build-time variable.
* [ONBUILD](https://docs.docker.com/reference/builder/#onbuild) adds a trigger instruction when the image is used as the base for another build.
* [STOPSIGNAL](https://docs.docker.com/engine/reference/builder/#stopsignal) sets the system call signal that will be sent to the container to exit.
* [LABEL](https://docs.docker.com/engine/userguide/labels-custom-metadata/) apply key/value metadata to your images, containers, or daemons.  


### Sitios recomendables

* [Ejemplos](https://docs.docker.com/reference/builder/#dockerfile-examples)
* [Best practices](https://docs.docker.com/articles/dockerfile_best-practices/)
* [Dockerfiles best practices](http://crosbymichael.com/dockerfile-best-practices.html) / [take 2](http://crosbymichael.com/dockerfile-best-practices-take-2.html)


## Best Practices

This is where general Docker best practices and war stories go:

* [The Rabbit Hole of Using Docker in Automated Tests](http://gregoryszorc.com/blog/2014/10/16/the-rabbit-hole-of-using-docker-in-automated-tests/)
* [Bridget Kromhout](https://twitter.com/bridgetkromhout) has a useful blog post on [running Docker in production](http://sysadvent.blogspot.co.uk/2014/12/day-1-docker-in-production-reality-not.html) at Dramafever.  
* There's also a best practices [blog post](http://developers.lyst.com/devops/2014/12/08/docker/) from Lyst.
* [A Docker Dev Environment in 24 Hours!](https://engineering.salesforceiq.com/2013/11/05/a-docker-dev-environment-in-24-hours-part-2-of-2.html)
* [Building a Development Environment With Docker](https://tersesystems.com/2013/11/20/building-a-development-environment-with-docker/)
* [Discourse in a Docker Container](https://samsaffron.com/archive/2013/11/07/discourse-in-a-docker-container)


Turn off interprocess communication with:

```
docker -d --icc=false --iptables
```

Set the container to be read-only:

```
docker run --read-only
```

Verify images with a hashsum:

```
docker pull debian@sha256:a25306f3850e1bd44541976aa7b5fd0a29be
```

Set volumes to be read only:

```
docker run -v $(pwd)/secrets:/secrets:ro debian
```

Set memory and CPU sharing:

```
docker -c 512 -mem 512m
```

Define and run a user in your Dockerfile so you don't run as root inside the container:

```
RUN groupadd -r user && useradd -r -g user user
USER user
```

### User Namespaces

There's also work on [user namespaces](https://s3hh.wordpress.com/2013/07/19/creating-and-using-containers-without-privilege/) -- it is in 1.10 but is not enabled by default.

To enable user namespaces ("remap the userns") in Ubuntu 15.10, [follow the blog example](https://raesene.github.io/blog/2016/02/04/Docker-User-Namespaces/).
(https://raesene.github.io/blog/2016/02/04/Docker-User-Namespaces/).
