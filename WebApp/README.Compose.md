# Compose.yaml & `docker compose`

> **NOTES**: This document is an extension content for document [Containerise Web API application](./README.md). It is advisable to go through it before reading this document.

This section take a closer look at the compose.yaml to understand how `docker compose` helps us to simplify and reduce the development effort.

&nbsp;

## Compose.yaml

Compose.yaml provide a configurable approach for you to run docker commands. It is very useful because it does so many things for you with a single command. Of course, you have to tell docker what to do in `compose.yaml` file.

Taking the `WebApp/compose.yaml` as an exmaple, the top section has the following.

```
services:
  server:
    build:
      context: .
      target: final
    ports:
      - 8080:80
```

`services` tell what follows are the container(s) you want to run. The service `server` is mapped to the name of the image and container. `build` tells docker how to build the image. `ports` tells docker which localhost port the request is map and forwared to container's port.

`Compose` becomes so handy when you need to run more than one container at the same time. Let's continue to look at the default template docker provides in `WebApp/compose.xml`.

```
# The commented out section below is an example of how to define a PostgreSQL
# database that your application can use. `depends_on` tells Docker Compose to
# start the database before your application. The `db-data` volume persists the
# database data between container restarts. The `db-password` secret is used
# to set the database password. You must create `db/password.txt` and add
# a password of your choosing to it before running `docker compose up`.
#     depends_on:
#       db:
#         condition: service_healthy
#   db:
#     image: postgres
#     restart: always
#     user: postgres
#     secrets:
#       - db-password
#     volumes:
#       - db-data:/var/lib/postgresql/data
#     environment:
#       - POSTGRES_DB=example
#       - POSTGRES_PASSWORD_FILE=/run/secrets/db-password
#     expose:
#       - 5432
#     healthcheck:
#       test: [ "CMD", "pg_isready" ]
#       interval: 10s
#       timeout: 5s
#       retries: 5
# volumes:
#   db-data:
# secrets:
#   db-password:
#     file: db/password.txt
```

The example above shows a database container and all telements well explained in the comment.

In some other scenarios, you might have frontend web app and backend services. The structure below gives us a sense how these 2 apps can be configured.

```
services:
  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    depends_on:
      - backend

  backend:
    build: ./backend
    ports:
      - "5000:5000"
```

&nbsp;

## `Compose` Commands

Docker provides us a validation tool to validate `compose.yaml` before running it.

```
webapp> docker compose config
name: webapp
services:
  server:
    build:
      context: C:\Users\user\dev\DevOps\webapp
      dockerfile: Dockerfile
      target: final
    networks:
      default: null
    ports:
      - mode: ingress
        target: 80
        published: "8080"
        protocol: tcp
networks:
  default:
    name: webapp_default
```

To run the containerised app, we run command `docker compose up`.

To run the containerised app a file watch, we run command `docker compose up --watch`.

The file watch monitors files status and build the image and run container if there is a change detected. It is useful for development. Developer do not need to build and run the container manually every single time make a change to files.

To verify if all containerised app is running OK, run command `docker compose ps`

While during development, we need to look at the application logs as well. On a separate `terminal`, we run `docker compose logs` to print the logs.

lastly, when we're done development for the day, run `docker compose down` to tear everything down and release the resources.

&nbsp;

## References


* [Compose file reference](https://docs.docker.com/reference/compose-file)
  
  > **TIPS**:The Ask AI chat on docker is very handy and quick to look for the things you need. The search function, however, might not return the right answers on the top of the result list.

