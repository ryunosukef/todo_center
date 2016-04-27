# TODO Center

This project is a sample prototype to build microservices.

TODO center itself is a central repository for a TODO app which is composed of multiple microservices as follows:

- [Todo API Gateway](https://github.com/scubism/todo_api_gateway.git): an API Gateway which proxy frontend requests to individual microservices.
- [PHP TODO API](https://github.com/scubism/php_todo_api): A TODO API service written in PHP.
- [Go TODO API](https://github.com/scubism/go_todo_api): An alternative option for TODO API service written in Go.
- [React TODO Web](https://github.com/scubism/react_todo_web): A TODO Web client based on react.js framework.

TODO center build these services based on [Docker](https://www.docker.com/), a container management tool by which you can easily setup services and their relations.

## Installation

Clone this repository and enter the directory.

```
git clone https://github.com/scubism/todo_center.git
cd todo_center
```

Install [Docker Engine](https://www.docker.com/products/docker-engine) and [Docker Compose](https://docs.docker.com/compose/) on your host machine. For local, you can build a ready-made virtual machine using [VirtualBox](https://www.virtualbox.org/) and [Vagrant](https://www.vagrantup.com/) in the following way.

```
vagrant up

# For the first time, you should also type the following commands for share folder problem.
vagrant plugin install vagrant-vbguest
vagrant vbguest

# Login to the VM.
vagrant ssh
# Now you are in /vagrant directory (not in /home/vagrant directory).
```

Edit the local configuration if needed.

```
vi config/config-local.yml
```

Execute a init file for the first time or config changed.

```
./init.sh
# This will clone and setup dependent microservice repositories
# and create a docker-compose.yml file from config/config.yml and config/config-local.yml.
```

Build containers based on docker-compose.yml generated by init.sh

```
docker-compose up -d
# This take several minutes for the first time
```

Check the container statuses.

```
docker ps -a
```

You can access contents via public endpoints for microservices as follows.

```
# Load config environment
eval $(./init.sh | grep config_)

# For TODO API
curl -k https://$config_host:$config_todo_api_gateway_port
# -k option allows connections to SSL sites without certs

# For Web
curl -k https://$config_host:$config_todo_api_gateway_web_port
# Check the url on your browser too.
```

See [docs/docker-tips.md](https://github.com/scubism/todo_center/blob/master/docs/docker-tips.md) for detail docker operations.


## Development

By default, it is not sync for a target source folder between the host and a container.
So, in development, you should recreate the container in a development mode which will sync the folder.

```
# Set container
CONTAINER=php_todo_api

# Remove the previous container for mount change
docker rm -f $CONTAINER

# Start the container with development settings
# the "-f" option specify docker compose setting files which can be overwritten
docker-compose -f docker-compose.yml -f docker-compose-dev.yml up -d $CONTAINER

# Enter the container
docker exec -it $CONTAINER bash

# === Execute any commands ===
...
# Typically you will run your app in background process
./docker-entrypoint.sh &
# kill the process if needed
```

## Benchmarking
Please read [docs/benchmark-with-ab.md](https://github.com/scubism/todo_center/blob/master/docs/benchmark-with-ab.md) for detail.

## API Documentation with Swagger
This project use Swagger for documenting backend API.
To use it, create your own `config-local.yml` from `config-local.example.yml`:
```
cp ./config/config-local{.example,}.yml
```

Change `compose_mode` in `config-local.yml` to __full__
```
# Compose Mode: min|full
#   - min: without swagger_ui & swagger_editor
#   - full: with swagger_ui & swagger_editor
compose_mode: full
```

Run `init.sh` to re-generate the `docker-compose.yml` file:
```
sh ./init.sh
```

Start __swagger_ui__ & __swagger_editor__ containers:
```
docker-compose scale swagger_ui=1 swagger_editor=1
```

Open these url on your browser:
```
eval $(./init.sh | grep config_)
echo https://$config_host:$config_swagger_ui_port
echo https://$config_host:$config_swagger_editor_port
```

* Notice: 
  - Make sure to expose swagger.yml somewhere in your api for using __swagger_ui__, for example "/v1/swagger.yml".
  - Use [OpenAPI Specification v2.0](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md) for you swagger.yml file.

## License

Released under the MIT License.
