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
## NGINX
To operate with only one server, simply eliminate the second one. Initially, both servers were utilized as Yii2 Advanced framework comprises separate backend and frontend components. By consolidating them into a single server, you can adapt the configuration according to your specific requirements.
```bash
server {
    listen 80;
    server_name admin.localhost;

    root /var/www/php-project/backend/web/;

    index index.php index.html
    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }

    location ~ ^/upload {
        root /var/www/php-project/frontend/web/;
        try_files $uri $uri/ =404;
    }

    location ~ \.(js|css|png|jpg|gif|swf|ico|pdf|mov|fla|zip|rar)$ {
        try_files $uri =404;
    }

    location ~ ^/(assets|upload)/.*\.php$ {
        deny all;
    }

    location ~* /\. {
        deny all;
    }

    location ~ \.php$ {
        try_files $uri =404;

        fastcgi_index index.php;

        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;

        fastcgi_pass php_project:9000;
    }
}
```
## PHP extensions
You can add php extensions through the Dockerfile in images/php82fpm/Dockerfile:
```bash
RUN docker-php-ext-install pdo pdo_mysql php_extension_name
```
## License

[MIT](https://choosealicense.com/licenses/mit/)