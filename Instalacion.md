<  [volver](https://github.com/erasmolpa/dockerLab/blob/master/dockerLab.md)

### Linux

La distro ha de ser  al menos 3.10.x kernel is [requisitos de distro](https://docs.docker.com/installation/binaries/#check-kernel-dependencies)

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

### Instalación con Máquina virtual Ubuntu...Y VAGRANT!

   Sólo hay que instalar virtualBox y Vagrant.
   
   El box está en la siguiente [enlace](https://atlas.hashicorp.com/erasmolpa/boxes/UbuntuDocker15GB)
   
   Una vez instalada la máquina virtual se puede ajustar la RAM etc..
   
   Los comandos a ejecutar para instlar son:
    * vagrant init erasmolpa/UbuntuDocker15GB;
    * vagrant up --provider virtualbox;
   Esto inicia la máquina virtual en virtual Box.
     * IMPORTANTE el usuario y password son IGUALES :) 