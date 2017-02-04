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

## Security Notes

### Port security

By default, the `docker-compose*-example` files will open a port which any device can access if it has
access to your workstation or server and you don't have firewall rules turned on. To avoid this, you can
append `127.0.0.1:` to the ports entries in `docker-compose*-example` (see the `docker-compose.yml` 
example in the workstations directory).

### UFW and Docker

If you are using UFW as your firewall on, say, Ubuntu, be aware that Docker overwrites iptables rules
in a way that UFW remains unaware of. Thus even if you block a port with UFW, Docker will burst open a
hole and not tell you about it unless you append `127.0.0.1:` to the port entry as per the above note.

This solution has been tested for Ubuntu 16.04: You can override this Docker behavior by creating the
file `/etc/docker/daemon.json` with content:

```
{
    "iptables": false
}
```

This will, of course, have consequences with Docker, but in testing I have not seen it interfere with 
this repo's Docker containers. Now, to access your Docker containers securely, you can SSH tunnel:

```
ssh -L 9999:localhost:9999 -L 18888:localhost:18888 username@yourserver
```

and then access the Cloud 9 IDE and the Jupyter Notebook at, respectively:

http://localhost:9999/

http://localhost:18888/


### Including ssh keys

Don't! You can mount your .ssh folder as a volume mount (see docker-compose examples in this repo) which keeps you from throwing your keys around in containers/images.
