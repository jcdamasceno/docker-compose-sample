# Docker Compose Example

This is the step by step tutorial to understand uses of Docker compose. In this tutorial, I will create two Docker containers using Docker compose. One docker container will have MySQL database instance and another Docker container have Apache web server with our dummy application file.

Let’s follow step by step tutorial and watch the things happening there.

## Step 1 – Create Directory Structure
First of all, create a directory structure. Here webapp is our web application directory. Also, create a index.html in webapp directory for testing.


```
mkdir docker-compose-sample && cd docker-compose-sample
mkdir webapp
echo "<h2>It Works</h2>" > webapp/index.html
```
## Step 2 – Create Dockerfile for Webapp
Now create a Dockerfile in webapp directory to create a customized image for your application including Apache web server.

```
vim  webapp/Dockerfile
```

add following content

```
FROM ubuntu:latest

RUN apt-get update \
   && apt-get install -y apache2

COPY index.html /var/www/html/
WORKDIR /var/www/html
CMD ["apachectl", "-D", "FOREGROUND"]
EXPOSE 80
```

## Step 3 – Create Docker Compose File

Finally create a docker compose configuration file (docker-compose.yml) file in current directory. This will define all the containers will be used in your current setup.

```
vim  docker-compose.yml
```

add following content.

```html
version: '3'
services:
  db:
     image: mysql
     container_name: mysql_db
     restart: always
     environment:
        - MYSQL_ROOT_PASSWORD="123456"
  web:
    image: apache
    build: ./webapp
    depends_on:
       - db
    container_name: apache_web
    restart: always
    ports:
      - "8080:80"
```

Above docker compose file has settings for two containers. The first container is for mysql database server and the second is for web server. The web container will run our application on Apache server. As this is customized we have defined build directory to webapp.

## Step 4 – Build Webapp Image

Now, build an image using the following command. This will create an image named apache using Dockerfile and contents from webapp directory.

```
docker-compose build
```

read the below output of above command. I have skipped some part of output which is not required. The first line of below output shows that it skipped building for db container due to no build defined. For web container it uses webapp/Dockerfile to build an image.


## Step 5 – Launch Docker Containers

Finally launch your containers using docker-compose up command. Use -d switch to run them in daemon mode.

```
docker-compose up -d
```

You can access your web application running on the apache_web container by accessing your docker host on port 8080. For example, http://dockerhost:8080/ where dockerhost is IP or hostname of your Docker host machine. If it's running on localhost can be accessed by http://127.0.0.1:8080

## Step 6 – Update Content in Web Application
Let’s make a change in your web application. I have added some more content to webapp/index.html file as following.

```
echo "Welcome to Docker Compose Tutorial" >> webapp/index.html
```

Now use the following commands to rebuild webapp container and relaunch using docker-compose.

```
docker-compose build
docker-compose up -d
```

Check the output of the command.

You can see that mysql_db container is showing unchanged as nothing changed there. The only apache_web container has been recreated due to new build found for the image used for that.

Again access your web application on port 8080 of docker host machine. You will see the updated content here.

http://dockerhost:8080/ 

Or, if local:

http://127.0.0.1:8080

## Credits
Project based on an origial tutorial by tecadmin.net at  https://tecadmin.net/tutorial/docker/docker-compose-example/
