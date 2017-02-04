# docker-sparker
Spark clusters via Docker infrastructure

## Building and running the workstation

### Via pulling the base image

* Install [docker-compose](https://github.com/docker/compose/releases)
* clone this repository
* `cd workstation-container`
* `cp docker-compose.yml-pull-example docker-compose.yml`
* `docker pull doctimjones/docker-sparker-base`
* `docker-compose up`
* For the Cloud 9 Composer, navigate to [http://localhost:9999](http://localhost:9999)
* For the iPython Notebook, navigate to [http://localhost:18888](http://localhost:18888)

### Via building from scratch

* Install [docker-compose](https://github.com/docker/compose/releases)
* clone this repository
* `cd workstation-container`
* `cp docker-compose.yml-build-example docker-compose.yml`
* `docker-compose up`
* For the Cloud 9 Composer, navigate to [http://localhost:9999](http://localhost:9999)
* For the iPython Notebook, navigate to [http://localhost:18888](http://localhost:18888)

## What does it include and how can I change that?

The apt packages and python pips can be found in the packages.txt and requirements.txt respectively.
Simply edit those files before building the container.

## Including ssh keys

Don't! You can mount your .ssh folder as a volume mount (see docker-compose examples in this repo) which keeps you from throwing your keys around in containers/images.
