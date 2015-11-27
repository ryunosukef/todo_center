# TODO Center

# THIS PROJECT IS A SAMPLE PROTOTYPE TO BUILD MICROSERVICES

TODO Center is a central repository for a complete TODO app which is composed of distributed microservices.

## Installation

### Clone Repositories

Clone this repository and enter the directory.

```
git clone https://github.com/scubism/todo_center.git
cd todo_center
```

Then, clone each repository which provide a respective microservice.

- [Go TODO API](https://github.com/scubism/go_todo_api): A TODO API server written in Go.

```
git clone https://github.com/scubism/go_todo_api.git
```

- [React TODO Web](https://github.com/scubism/react_todo_web): A TODO Web client based on react.js framework.

```
git clone https://github.com/scubism/react_todo_web.git
```

### Setup A Host VM

The following will build a VM based on Centos7 with Docker Engin and Docker Compose installed.

```
vagrant up
# Use "vagrant suspend" and "vagrant resume" on your machine restart.
```

Login to the VM.

```
vagrant ssh
# Now you are in /vagrant directory (not in /home/vagrant directory).
```

For the first time or environment changed, execute a init file.

```
./init.sh
# This will create a docker-compose.yml file from the environment.
```

### Setup Containers For Production

We use Docker Compose to build docker containers by a setting file "docker-compose.yml".

```
# Build a mongodb server which is used by the following go_todo_api server
docker-compose --x-networking up -d mongo

# Build each microservice respectively
docker-compose --x-networking up -d go_todo_api
docker-compose --x-networking up -d react_todo_web
```

Notice that "--x-networking" option enables [Docker Networking](http://docs.docker.com/engine/userguide/networking/dockernetworks/).

Check the container statuses.

```
docker ps -a
```

You can access contents via public endpoints for microservices as follows.

```
curl http://$LOCALIP:$GO_TODO_API_PORT
curl http://$LOCALIP:$REACT_TODO_WEB_PORT
# Here, the variables such as $LOCALIP are defined in default.env
```

### Setup Containers For Development

The above containers are immutable for production.
Thus for development, we should run a mutable container beside the original.

```
# For each service, stop the production container to use same port and run another container for development

# For go_todo_api
docker-compose stop go_todo_api
docker-compose --x-networking -f docker-compose.yml -f docker-compose.dev.yml run -p $GO_TODO_API_PORT:3000 --rm go_todo_api

# For react_todo_web
docker-compose stop react_todo_web
docker-compose --x-networking -f docker-compose.yml -f docker-compose.dev.yml run -p $REACT_TODO_WEB_PORT:3000 --rm react_todo_web
```

Notice that "-f" option specify docker compose setting files which can be overwritten, and "--rm" option will destroy the built container after exit.

You can access mongodb directory from a mongo container.

```
# Run a mongo container to inspect
docker-compose --x-networking -f docker-compose.yml -f docker-compose.dev.yml run --rm mongo
# Invoke the mongodb client
mongo
```

## Roadmap

- Create an oauth API with Redis backend
- Create an API gateway with Nginx
- Create a user service
- Create a report service using Elasticsearch and Kibana
- Create a notification service


## License

Released under the MIT License.