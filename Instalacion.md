<  [Principal](http://erasmolpa.github.io/dockerLab)

## Opción 1. Instalación Host. Dependerá del SO.

### Linux

La distro ha de ser compatible al menos con la versión 3.10.x del kernel [link](https://docs.docker.com/installation/binaries/#check-kernel-dependencies).

```
curl -sSL https://get.docker.com/ | sh
```

### Mac OS X y WINDOWS 

Simplemente hay que descargar el [Docker Toolbox](https://www.docker.com/products/docker-toolbox). Las instrucciones de instalacion están en: 

* [MAC](https://docs.docker.com/installation/mac/)

* [WINDOWS](https://docs.docker.com/engine/installation/windows/)


Docker Toolbox es una herramienta que nos instalará :

* Docker Engine.
* Docker Compose.
* Docker Machine.
* Kitematic.
* Además, permite descargar e instalar git y Virtualbox.

Si tienes dudas sobre la instalacion.... [installation](https://docs.docker.com/installation/)

## Opción 2. Instalación Virtualizada.

### VAGRANT Y VIRTUAL BOX

   Instalación de máquina virtual Ubuntu con Vagrant.Sólo hay que instalar virtualBox y Vagrant.
   
   Los comandos a ejecutar para instalar son:
    
    ```
    $ vagrant init erasmolpa/UbuntuDocker15GB;
    $ vagrant up --provider virtualbox;
    ```
   El box está en la siguiente [enlace](https://atlas.hashicorp.com/erasmolpa/boxes/UbuntuDocker15GB)
   
   Una vez instalada la máquina virtual se puede ajustar la RAM etc..
   
   Esto inicia la máquina virtual en virtual Box.
     * IMPORTANTE el usuario y password son IGUALES :) 
