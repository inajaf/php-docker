## Docker container for PHP 8.2 + NGINX + MYSQL + Attached volumes


Command to run/build container

```bash
docker-compose up -d --build
```

## Usage
After building the containers, run the command to build a project with the composer.
In this example, we will use Yii2.

```bash
docker exec -it php_project bash
```
## Install PHP project inside the container
```bash
composer create-project --prefer-dist yiisoft/yii2-app-advanced php-project
```
## MySQL
SQL params are provided in compose.yaml
```bash
  mysql:
   container_name: php_mysql
   image: mysql:latest
   ports:
     - 3306:3306
   volumes:
     - ./www:/var/www
     - ./mysql-data:/var/lib/mysql
     - ./my.cnf:/etc/mysql/conf.d/my.cnf
   environment:
     - MYSQL_PASSWORD=root
     - MYSQL_ROOT_PASSWORD=root
     - MYSQL_DATABASE=sample
   command: ["--default-authentication-plugin=mysql_native_password"]
   networks:
     - php
```
## License

[MIT](https://choosealicense.com/licenses/mit/)