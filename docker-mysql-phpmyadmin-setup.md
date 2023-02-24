---
title: "Setting Up Docker for MySQL and phpMyAdmin"
summary : "This file provides instructions for setting up a local docker environment with MySQL and phpMyAdmin."
isDraft: true
date: "24/02/2023"
---

## Prerequisites
Before we begin, ensure that you have the following installed:
- Docker
- Docker Compose
If you don't have them already, you can download them from [Docker Website](https://www.docker.com/) 

# Setting up Docker for MySQL and phpMyAdmin
First, we'll set up Docker for MySQL. Open up your terminal and enter the following command:
```bash
docker run -d --name mysql-example --network example  --restart always -e MYSQL_ROOT_PASSWORD=yourpassword -p 3306:3306 arm64v8/mysql:oracle
```
Let's break down this command:
- `docker run` runs a container based on an image.
- `-d` runs the container in the background.
- `--name` gives the container a name so we can refer to it later.
- `--network` connects the container to a network.
- `--restart` sets the container to automatically restart if it fails.
- `-e` sets an environment variable, in this case, the root password for MySQL.
- `-p` maps the container's port to the host's port, allowing us to connect to MySQL from outside the container.
- `arm64v8/mysql:oracle` is the Docker image we're using for MySQL(ARM or MAC M1/M2) if you are windows user change it to `mysql`

Now, let's set up Docker for phpMyAdmin. Enter the following command in your terminal:
```bash
docker run -d  --name phpmyadmin-example --network example -e PMA_HOST=mysql-example  -p 8080:80  phpmyadmin/phpmyadmin
```
This command is similar to the one for MySQL, with a few differences:
- We're using the `phpmyadmin/phpmyadmin` Docker image instead of arm64v8/mysql:oracle.
- We're setting an environment variable, `PMA_HOST`, which tells phpMyAdmin where to find the MySQL server.
- We're mapping port 80 inside the container to port 8080 on the host.

This will start the containers in the background. You can now access phpMyAdmin by going to http://localhost:8080 in your browser.

Finally, let's create a Docker Compose file to manage our containers. Create a new file called docker-compose.yml and enter the following:
```yml
version: '3'

services:
  mysql-service:
    image: arm64v8/mysql:oracle
    container_name: mysql-example
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: yourpassword
    ports:
      - 3306:3306
    networks:
      - example

  phpmyadmin-service:
    image: phpmyadmin/phpmyadmin
    container_name: phpmyadmin-example
    restart: always
    environment:
      PMA_HOST: mysql-example
    ports:
      - 8080:80
    networks:
      - example

networks:
  example:
    driver: bridge
```

This file defines two services, mysql-service and phpmyadmin-service, which are the same as the containers we created earlier. It also defines a network called `example` and sets it to use the bridge driver.

Now, we can start our containers using Docker Compose. Enter the following command in your terminal:
```bash
docker-compose up -d
```

Now that we have set up Docker for MySQL and phpMyAdmin, we may want to stop and remove the containers. To do this, we can use the `docker-compose down` command.

## Conclusion
In this article, we've gone over how
