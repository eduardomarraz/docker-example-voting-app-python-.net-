# Aplicación de Votación de Ejemplo

Una aplicación distribuida simple que se ejecuta en múltiples contenedores Docker.

## Empezando

Descarga [Docker Desktop](https://www.docker.com/products/docker-desktop) para Mac o Windows. [Docker Compose](https://docs.docker.com/compose) se instalará automáticamente. En Linux, asegúrate de tener la última versión de [Compose](https://docs.docker.com/compose/install/).

Esta solución utiliza Python, Node.js, .NET, con Redis para mensajería y Postgres para almacenamiento.

Ejecuta en este directorio para construir y ejecutar la aplicación:

```shell
docker compose up
```

La aplicación vote estará corriendo en http://localhost:5000, y results estará en http://localhost:5001.

Alternativamente, si deseas ejecutarlo en un Docker Swarm, primero asegúrate de tener un swarm. Si no lo tienes, ejecuta:

```shell
docker swarm init
```

Una vez que tengas tu swarm, en este directorio ejecuta:

```shell
docker stack deploy --compose-file docker-stack.yml vote
```

## Ejecutar la aplicación en Kubernetes

La carpeta k8s-specifications contiene las especificaciones YAML de los servicios de la Aplicación de Votación.

Ejecuta el siguiente comando para crear los despliegues y servicios. Nota que esto creará estos recursos en tu namespace actual (default si no lo has cambiado).

```shell
kubectl create -f k8s-specifications/
```

La aplicación web vote estará disponible en el puerto 31000 en cada host del clúster, la aplicación web result estará disponible en el puerto 31001.

Para eliminarlos, ejecuta:

```shell
kubectl delete -f k8s-specifications/
```

## Arquitectura

![Diagrama de arquitectura](architecture.excalidraw.png)

* Una aplicación web front-end en [Python](/vote) que te permite votar entre dos opciones.
* Un [Redis](https://hub.docker.com/_/redis/) que recopila nuevos votos.
* Un trabajador [.NET](/worker/) que consume votos y los almacena en…
* Una base de datos [Postgres](https://hub.docker.com/_/postgres/) respaldada por un volumen Docker
* Una aplicación web [Node.js](/result) que muestra los resultados de la votación en tiempo real

## Notas

La aplicación de votación solo acepta un voto por navegador cliente. No registra votos adicionales si ya se ha enviado un voto desde un cliente.

Este no es un ejemplo de una aplicación distribuida perfectamente diseñada... es solo un ejemplo simple de los diversos tipos de piezas y lenguajes que podrías ver (colas, datos persistentes, etc.), y cómo manejarlos en Docker a un nivel básico.

----------------------------------------------------------

#English
# Example Voting App

A simple distributed application running across multiple Docker containers.

## Getting started

Download [Docker Desktop](https://www.docker.com/products/docker-desktop) for Mac or Windows. [Docker Compose](https://docs.docker.com/compose) will be automatically installed. On Linux, make sure you have the latest version of [Compose](https://docs.docker.com/compose/install/).

This solution uses Python, Node.js, .NET, with Redis for messaging and Postgres for storage.

Run in this directory to build and run the app:

```shell
docker compose up
```

The `vote` app will be running at [http://localhost:5000](http://localhost:5000), and the `results` will be at [http://localhost:5001](http://localhost:5001).

Alternately, if you want to run it on a [Docker Swarm](https://docs.docker.com/engine/swarm/), first make sure you have a swarm. If you don't, run:

```shell
docker swarm init
```

Once you have your swarm, in this directory run:

```shell
docker stack deploy --compose-file docker-stack.yml vote
```

## Run the app in Kubernetes

The folder k8s-specifications contains the YAML specifications of the Voting App's services.

Run the following command to create the deployments and services. Note it will create these resources in your current namespace (`default` if you haven't changed it.)

```shell
kubectl create -f k8s-specifications/
```

The `vote` web app is then available on port 31000 on each host of the cluster, the `result` web app is available on port 31001.

To remove them, run:

```shell
kubectl delete -f k8s-specifications/
```

## Architecture

![Architecture diagram](architecture.excalidraw.png)

* A front-end web app in [Python](/vote) which lets you vote between two options
* A [Redis](https://hub.docker.com/_/redis/) which collects new votes
* A [.NET](/worker/) worker which consumes votes and stores them in…
* A [Postgres](https://hub.docker.com/_/postgres/) database backed by a Docker volume
* A [Node.js](/result) web app which shows the results of the voting in real time

## Notes

The voting application only accepts one vote per client browser. It does not register additional votes if a vote has already been submitted from a client.

This isn't an example of a properly architected perfectly designed distributed app... it's just a simple
example of the various types of pieces and languages you might see (queues, persistent data, etc), and how to
deal with them in Docker at a basic level.
