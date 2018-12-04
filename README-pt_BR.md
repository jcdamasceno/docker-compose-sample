# Docker Compose Example

Este é o tutorial passo a passo para entender os usos do Docker. Neste tutorial, serão criados dois contêineres Docker. Um contêiner terá a instância do banco de dados MySQL e outro contêiner será o servidor da Web Apache com com uma aplicação genérica.

Vamos seguir o passo a passo e ver as coisas acontecendo.

## Passo 1 – Criar Estrutura de Diretórios

Primeiramente, crie uma estrutura de diretórios. A pasta webapp é o nosso diretório de aplicativos da web. Além disso, crie um index.html no diretório webapp para teste. O nome da pasta pode ser qualquer nome, aqui foi utilizado "docker-compose-sample", pois foi o nome usado originalmente para criar esse repositório.

```
mkdir docker-compose-sample && cd docker-compose-sample
mkdir webapp
echo "<h2>Docker Compose 2018</h2>" > webapp/index.html
```
## Passo 2 – Criação do Dockerfile para Webapp

Agora, crie um Dockerfile no diretório webapp para criar uma imagem docker customizada para seu aplicativo, incluindo o servidor da web Apache.

```
vim  webapp/Dockerfile
```

Adicione o seguinte conteúdo:

```
FROM ubuntu:latest

RUN apt-get update \
   && apt-get install -y apache2

COPY index.html /var/www/html/
WORKDIR /var/www/html
CMD ["apachectl", "-D", "FOREGROUND"]
EXPOSE 80
```

## Passo 3 – Criação do arquivo Docker Compose

Por fim, crie um arquivo de configuração (docker-compose.yml) no diretório atual. Isso definirá todos os contêineres que serão usados em sua configuração atual.

```
vim  docker-compose.yml
```

Adicione o seguinte conteúdo:

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

O arquivo docker-compose.yml possui as configurações para dois contêineres. O primeiro container é para o servidor de banco de dados mysql e o segundo é para o servidor web. O contêiner da web executará nosso aplicativo no servidor Apache. Como isso é personalizado, definimos o diretório de construção para o webapp.

## Passo 4 – Construir a imagem Web

Construir a imagem usando o seguinte comando. Este comando criará uma imagem chamada "apache" usando o Dockerfile e o conteúdo do diretório webapp.

```
docker-compose build
```

## Passo 5 – Iniciar os Containers

Por fim, inicie seus contêineres usando o comando docker-compose up. Use o parâmetro -d para executá-los no modo daemon.

```
docker-compose up -d
```

Você pode acessar a aplicação web em execução no contêiner apache_web acessando o IP do host na porta 8080. Por exemplo, http://127.0.0.1:8080, se estiver testando localmente.

## Passo 6 – Atualizar o conteúdo da Aplicação Web

Vamos fazer uma alteração no index.html. Foi adicionado uma nova linha no final do arquivo webapp/index.html da seguinte forma:

```
echo "Bem Vindo ao Tutorial de Docker Compose" >> webapp/index.html
```

Agora use os seguintes comandos para reconstruir o contêiner do aplicativo Web e reiniciá-lo usando o docker-compose:

```
docker-compose build
docker-compose up -d
```

Durannte a execução dos comandos é mostrado que mysql_db não será recriado, já que nada mudou lá. Apennas o contêiner apache_web foi recriado devido à nova compilação encontrada para a imagem usada.

Verifique se os seus container estão em execução:

```
docker-compose ps
```

Novamente, acesse seu aplicativo na porta 8080 da máquina docker. Você verá o conteúdo atualizado aqui.

http://127.0.0.1:8080 ou http://dockerhost:8080/

## Credits
Project based on an origial tutorial by tecadmin.net at  https://tecadmin.net/tutorial/docker/docker-compose-example/
