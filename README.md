# docker-sparker
Spark clusters via Docker infrastructure

## Building and running the workstation

* Install [docker-compose](https://github.com/docker/compose/releases)
* clone this repository
* `cd docker-sparker/workstation-container`
* `docker-compose up`
* For the Cloud 9 Composer, navigate to [http://localhost:9999](http://localhost:9999)
* For the iPython Notebook, navigate to [http://localhost:18888](http://localhost:18888)

## What does it include and how can I change that?

The apt packages and python pips can be found in the packages.txt and requirements.txt respectively.
Simply edit those files before building the container.

## Including ssh keys

We turn this off by default as this is [potentially dangerous](https://github.com/docker/docker/issues/6396), however, 
you can add the following lines to the Docker container just above the final 
`CMD` line:

```
ARG SSH_KEY
RUN mkdir -p /root/.ssh && \
  echo "$SSH_KEY" >/root/.ssh/id_rsa && \
  chmod 0600 /root/.ssh/id_rsa
```

and then either build with 

```
docker build -t workstation-container --build-arg SSH_KEY="$(cat ~/.ssh/id_rsa) .
```

or modify the compose file to provide arguments as above.