
<code>
version: 2,
services:
  contenedor_1:
    image: httpd/org:latest
    container_name: httpd
    ports:
      - 100:22
      - 80:80
    links:
      - tomcat:tomcat_alias
  contenedor_2:
    image: tomcat/org:latest 
    container_name: tomcat 
    ports: 
      - 101:22 
      - 8080:8080
 </code>
 
Este ejemplo crearía 2 contenedores con nombre apache y tomcat, interconectados entre sí mediante el alias tomcat_alias. Un pequeño detalle a nivel conceptual, en la documentación la definición de contenedores se da a entender como definición de servicios ya que lxc y docker están orientados de forma implícita a la implementación de microservicios. La verdad es que este detalle no tiene mucha relevancia pero puede dar lugar a confusiones.

Como introducción, no tiene mucho sentido extender este artículo para describir todas las opciones configurables en este fichero. Si se quiere profundizar en este aspecto aconsejamos consultar la guía de referencia oficial del compose file.

 

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
