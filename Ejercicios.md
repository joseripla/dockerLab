#Ejecutamos la imagen de loopCar

1) cd Public/Dockers/loopback/
  
2)  docker run -it -v /Users/erasmodominguezjimenez/Documents/jsBases/strongloopDockerApp/loopCar:/host -p 9000:3000 erasmolpa/loopbackend:latest ./bin/bash

3) en la carpeta host dentro de la imagen cd host

4) node . Arranca la app en 192.168.99.100:9000/explorer/

5) hacer el ejercicio de ejecutar otro microservicio en otros puertos distintos. 8000:2000 p ejemplo.

6) Cambiamos el Dockerfile y dentro del directorio hacemos 

docker build --tag "erasmolpa/loopbackend:1.0" .

#Ejecutamos la imagen de MongoDB

1) docker run -d --name mongoDB -p 27017:27017 -p 28017:28017 erasmolpa/mongo --httpinterface 


Esto monta el mongobd en 192.168.99.100:27017 y la consola en 192.168.99.100:28017


#Ejecutamos la imagen de Jenkins:
docker run --name jenkins -p 8080:8080 -p 50000:50000 -v /Users/erasmodominguezjimenez/Public/Dockers/dockerVolume/:/var/jenkins_home erasmolpa/jenkins:latest

Esto monta jenkins en http://192.168.99.100:8080/
