# ProjectM

version: "2"
services:
 
    lb:
      image: dockercloud/haproxy:1.3
      volumes:
        - /var/run/docker.sock:/var/run/docker.sock
      ports:
        - 80:80
      links:
        - nginx
        - php
 
 
    nginx:
      image: nginx
      volumes:
        - ./src:/usr/share/nginx/html
      environment:
        - VIRTUAL_HOST_WEIGHT=2
        - VIRTUAL_HOST=*/assets/*
 
 
    php:
      build: ./build/php
      volumes:
        - ./src:/var/www/html
      links:
        - mysql
      environment:
        - VIRTUAL_HOST_WEIGHT=1
        - VIRTUAL_HOST=*
 
 
    mysql:
      image: mysql:5.7
      container_name: mysql
      volumes:
        - ./build/mysql:/docker-entrypoint-initdb.d
        - ./tmp/mysql:/var/lib/mysql
      environment:
        - MYSQL_DATABASE=world
        - MYSQL_ROOT_PASSWORD=password
 
 
    pma:
      image: phpmyadmin/phpmyadmin
      links:
        - mysql:db
      ports:
        - 8080:80
