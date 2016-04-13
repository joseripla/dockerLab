<<  [Principal](http://erasmolpa.github.io/dockerLab)

#  Anexo

##  Contenidos:

* Introducción del siguiente laboratorio.
	* Algunos [comandos](#comandos) útiles.      
	* Docker [Compose](#compose). Orquestando Servicios.
      	* [Docker Cloud](https://cloud.docker.com/). Plataforma para desplegar contenedores.
      	* Docker y la IC
      		*  Integración contínua en Docker con [Circleci](https://circleci.com/docs/docker/)
      		*  Artículo pdf de la web de Docker [Descargar](https://www.docker.com/sites/default/files/RA_CI%20with%20Docker_08.25.2015.pdf)
      		*  Primera parte para Demo de CI [jenkis](http://jenkins-58674a6f-1.225b690b.cont.dockerapp.io:32769/) desplegado aplicación con Digital Ocean, Cloud de Docker, Docker Hub, GitHub. 

##Comandos

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




### Compose. Primeros pasos:

Docker [Compose](https://docs.docker.com/compose/) es una herramienta para definir y correr aplicaciones multicontenedores. Mediante un fichero de configuración, docker-compose.yml, orquestamos y "linkamos" nuestros contenedores.
Es una muy buena opción para proyectos con arquitecturas mutliservicios, aplicaciones monolíticas (Servidor aplicacion + servidor web + base de datos) y muy útil para definir ciclos de integración contínua.

#### Instalación. Sólo Para Linux.
```
$ curl -L https://github.com/docker/compose/releases/download/1.6.2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
```
```
$ chmod +x /usr/local/bin/docker-compose

```
Comprobamos la instalación
```
$ docker-compose --version
```
**En máquinas Mac y Windows ya viene instalado como parte de Docker Toolbox.**

#### Comandos.
```
$ docker-compose up
```
##### Si se quiere pasar como argumento el fichero de especificación:

```
$ docker-compose -f  fichero.yml up
```
##### Ejecutar en segundo plano:
```
$ docker-compose -f <fichero> up -d
```
#####Parar un entorno:
```
$ docker-compose -f <fichero> kill
```
#####Para ver los contenedores parados se haría
```
$ docker ps -a
```
#####Eliminar por completo un entorno:
```
$ docker-compose -f <fichero.yml> rm
```
#####Comprobar los logs de los contenedores (lo explicado en el primer punto):
```
$ docker-compose logs
```
#####Comprobar un fichero con la especificación yml del entorno
```
$ docker-compose -f <fichero.yml> config
```

Existe un [repositorio](https://stackfiles.io/) de Stacks files que se pueden reutilizar. Además en Dicker Hub tienes múltiples de instalaciones compose que pueden servir de guía como la que veremos a continuación


#### Usando Compose:

Creando un contenedor wordpress y mariaDb

Si accedemos al hub de  oficial de [wordpress](https://hub.docker.com/_/wordpress/) vemos que proponen dos medios de crear una web.Tanto utilizando links containers como usando compose. El fichero compose es el siguiente :

Example **docker-compose.yml** for wordpress:
 `
 <pre>
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
 <code>
 
 Finalmente lanzamos compose donde tengamos el fichero docker-compose.yml
 ```
  $ docker-compose up 
```
En la url http://192.168.99.100:8080/wp-admin/install.php , es decir en la http://host-ip:8080.
