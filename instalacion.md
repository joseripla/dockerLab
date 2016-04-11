## Prerequisites

I use [Oh My Zsh](https://github.com/robbyrussell/oh-my-zsh) with the [Docker plugin](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins#docker) for autocompletion of docker commands.  YMMV.

### Linux

The 3.10.x kernel is [the minimum requirement](https://docs.docker.com/installation/binaries/#check-kernel-dependencies) for Docker.

### MacOS

 10.8 “Mountain Lion” or newer is required.

## Installation

### Linux

Quick and easy install script provided by Docker:

```
curl -sSL https://get.docker.com/ | sh
```

If you're not willing to run a random shell script, please see the [installation](https://docs.docker.com/installation/) instructions for your distribution.  

If you are a complete Docker newbie, you should follow the [series of tutorials](https://docs.docker.com/linux/started/) now.

### Mac OS X

Download and install [Docker Toolbox](https://www.docker.com/products/docker-toolbox).  If that doesn't work, see the [installation instructions](https://docs.docker.com/installation/mac/).

> **NOTE** If you have an existing docker toolbox, you might think you can upgrade [Docker Machine](https://docs.docker.com/machine/install-machine/) binaries directly (either from URL or `docker-machine upgrade default`) and it will take care of itself.  This is not going to help -- `docker-machine` will be `1.10.3` while `docker` is still `1.8.3` or whatever your previous version is.
>
> You are much better off using Docker Toolbox DMG file to upgrade, which will take care of all the binaries at once.

Once you've installed Docker Toolbox, install a VM with Docker Machine using the VirtualBox provider:
