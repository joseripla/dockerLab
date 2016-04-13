## Imagenes

Como ya hemos comentado muchas veces, las imágenes son  [plantillas para contenedores](https://docs.docker.com/engine/understanding-docker/#how-does-a-docker-image-work).

### Lifecycle

* [`docker images`](https://docs.docker.com/reference/commandline/images) -> Muestra todas las imágenes.
* [`docker import`](https://docs.docker.com/reference/commandline/import) --> Crea una imagen de un fichero tgz (local o remoto).
* [`docker build`](https://docs.docker.com/reference/commandline/build) --> Crea imagen de un Dockerfile.
* [`docker commit`](https://docs.docker.com/reference/commandline/commit) -> Guarda los cambios hechos en un contenedor como una nueva imagen.Crea una imagen a partir del contenedor.
* [`docker rmi`](https://docs.docker.com/reference/commandline/rmi) --> Para eliminar una imagen existente.
* [`docker load`](https://docs.docker.com/reference/commandline/load) loads an image from a tar archive as STDIN, including images and tags (as of 0.7).
* [`docker save`](https://docs.docker.com/reference/commandline/save) saves an image to a tar archive stream to STDOUT with all parent layers, tags & versions (as of 0.7).

### Info

* [`docker history`](https://docs.docker.com/reference/commandline/history) shows history of image.
* [`docker tag`](https://docs.docker.com/reference/commandline/tag) tags an image to a name (local or registry).

### Cleaning up

While you can use the `docker rmi` command to remove specific images, there's a tool called [docker-gc](https://github.com/spotify/docker-gc) that will clean up images that are no longer used by any containers in a safe manner.

## Red

Internamente, Docker utiliza los mecanismos de linux para dar conectividad a los contenedores.En la instalación, docker crea una interfaz virtual ``docker0` en el Host a la que se asigna una Ip , por ejemplo 192.168.99.100.

Para más detalles [networking docker](https://docs.docker.com/engine/userguide/networking/work-with-networks/).

### Lifecycle

* [`docker network create`](https://docs.docker.com/engine/reference/commandline/network_create/)
* [`docker network rm`](https://docs.docker.com/engine/reference/commandline/network_rm/)

### Info

* [`docker network ls`](https://docs.docker.com/engine/reference/commandline/network_ls/)
* [`docker network inspect`](https://docs.docker.com/engine/reference/commandline/network_inspect/)

### Connection

* [`docker network connect`](https://docs.docker.com/engine/reference/commandline/network_connect/)
* [`docker network disconnect`](https://docs.docker.com/engine/reference/commandline/network_disconnect/)

You can specify a [specific IP address for a container](https://blog.jessfraz.com/post/ips-for-all-the-things/):

```
# create a new bridge network with your subnet and gateway for your ip block
docker network create --subnet 203.0.113.0/24 --gateway 203.0.113.254 iptastic

# run a nginx container with a specific ip in that block
$ docker run --rm -it --net iptastic --ip 203.0.113.2 nginx

# curl the ip from any other place (assuming this is a public ip block duh)
$ curl 203.0.113.2
```
##Registro

Como crear, versionar y publicar nuestras propias imágenes? en el siguiente [enlace](https://docs.docker.com/mac/step_six/) se explica como .

### Imágenes en repositorios. Docker Hub

Un repositorio es un host, donde se almacenan los tags asociados a imágenes creadas.
Un registro es un host que almacena repositorios.Ejemplo [Docker Hub](https://hub.docker.com/)
A registry is a *host* -- a server that stores repositories and provides an HTTP API for [managing the uploading and downloading of repositories](https://docs.docker.com/userguide/dockerrepos/).

Docker.com hosts its own [index](https://hub.docker.com/) to a central registry which contains a large number of repositories.  Having said that, the central docker registry [does not do a good job of verifying images](https://titanous.com/posts/docker-insecurity) and should be avoided if you're worried about security.

* [`docker login`](https://docs.docker.com/reference/commandline/login)  Login en un registro
* [`docker search`](https://docs.docker.com/reference/commandline/search) Buscar registros de imágenes.
* [`docker pull`](https://docs.docker.com/reference/commandline/pull) -> "pull" de una imagen desde un registro a mi máquina local.
* [`docker push`](https://docs.docker.com/reference/commandline/push) --> "push" de una imagen a un registro desde mi máquina.

### Como tener un local registry

Se puede tener un registro de imágenes propios usando la imagen de docker hub [docker-registry](https://github.com/docker/distribution)  y hacer [local deploy](https://github.com/docker/distribution/blob/master/docs/deploying.md) .


## Dockerfile

[The configuration file](https://docs.docker.com/reference/builder/). Sets up a Docker container when you run `docker build` on it.  Vastly preferable to `docker commit`.  If you use [jEdit](http://jedit.org), I've put up a syntax highlighting module for [Dockerfile](https://github.com/wsargent/jedit-docker-mode) you can use.

### Instructions

@TODO --> EXPLICAR EL DOCKERFILE 
https://jsitech1.gitbooks.io/meet-docker/content/dockerfiles.html

* [.dockerignore](https://docs.docker.com/reference/builder/#dockerignore-file)
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

* [EXPOSE](https://docs.docker.com/reference/builder/#expose) informs Docker that the container listens on the specified network ports at runtime.  NOTE: does not actually make ports accessible.
* [ENV](https://docs.docker.com/reference/builder/#env) sets environment variable.
* [ADD](https://docs.docker.com/reference/builder/#add) copies new files, directories or remote file to container.  Invalidates caches. Avoid `ADD` and use `COPY` instead.
* [COPY](https://docs.docker.com/reference/builder/#copy) copies new files or directories to container.
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
