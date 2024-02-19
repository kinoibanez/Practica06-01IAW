---
layout: post
title:  "Práctica de Wordpress!"
date:   2024-02-19 13:00:00 -0600
categories: Este es el README de Wordpress 
---

# Example12_Wordpress
En este ejemplo tendremos que generar la estructura de Docker para poder descargar Wordpress, tanto por el puerto 80 como haciendo uso del certificado con Let´s Encrypt.


# Primer apartado de la práctica.

- Tendremos que generar un código que nos permita poder instalar wordpress a través de un *_docker_*. Para esto tendremos que hacer uso del siguiente código, pero teniendo en cuenta una serie de `excepciones` bastantes importantes.

    ```

        version: '3'

    services:
    wordpress:
        image: bitnami/wordpress
        ports:
        - 80:8080
        - 443:8443
        environment: 
            #IMPORTANTE BUSCAR EXACTAMENTE EL NOMBRE DE CADA UNA. ES DECIR, EN EL .ENV PUEDEN LLAMARSE COMO QUIERAN
            #PERO AQUÍ TENEMOS QUE BUSCAR EXACTAMENTE COMO SE LLAMAN
            # EN ESTE CASO EN BITNAMI/WORDPRESS
        - WORDPRESS_DATABASE_HOST=mysql
        - WORDPRESS_DATABASE_NAME=${WORDPRESS_DATABASE_NAME}
        - WORDPRESS_DATABASE_USER=${WORDPRESS_DATABASE_USER}
        - WORDPRESS_DATABASE_PASSWORD=${WORDPRESS_DATABASE_PASSWORD}
        - WORDPRESS_DATABASE_PORT_NUMBER=${WORDPRESS_DATABASE_PORT_NUMBER}
        - WORDPRESS_USERNAME=${WORDPRESS_USERNAME}
        - WORDPRESS_PASSWORD=${WORDPRESS_PASSWORD}
        - WORDPRESS_EMAIL=${WORDPRESS_EMAIL}
        - ALLOW_EMPTY_PASSWORD=yes
        # Cada variables tiene que estar bien identificada, el apartado 1 es el nombre de como tiene que llamarse en bitnami
        # El apartado después es el que tenemos que identificar con $ y el nombre de nuestra variables en el .env
        volumes: 
        - wordpress_data:/bitnami/wordpress
        depends_on:
        - mysql
        restart: always
        networks:
        - frontend
        - backend

    mysql:
        image: mysql:8.0
        ports:
        - 3306:3306
        environment:
        #Cogemos las variables que hemos declarado en Wordpress, así podemos tener menos variables y las reciclamos.
        - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
        - MYSQL_DATABASE=${WORDPRESS_DATABASE_NAME}
        - MYSQL_USER=${WORDPRESS_DATABASE_USER}
        - MYSQL_PASSWORD=${WORDPRESS_DATABASE_PASSWORD}
        volumes:
        - mysql_data:/var/lib/mysql
        restart: always
        networks:
        - backend

    phpmyadmin:
        image: phpmyadmin
        ports:
        - 8080:80
        environment: 
        - PMA_ARBITRARY=1
        restart: always
        networks:
        - frontend
        - backend

    volumes: 
    mysql_data:
    wordpress_data:

    networks:
    frontend:
    backend:
    ```


## Apartados a comentar.

- El primer bloque que debemos de comentar es el siguiente: 

    ```
    environment: 
            #IMPORTANTE BUSCAR EXACTAMENTE EL NOMBRE DE CADA UNA. ES DECIR, EN EL .ENV PUEDEN LLAMARSE COMO QUIERAN
            #PERO AQUÍ TENEMOS QUE BUSCAR EXACTAMENTE COMO SE LLAMAN
            # EN ESTE CASO EN BITNAMI/WORDPRESS
        - WORDPRESS_DATABASE_HOST=mysql
        - WORDPRESS_DATABASE_NAME=${WORDPRESS_DB_NAME}
        - WORDPRESS_DATABASE_USER=${WORDPRESS_DB_USER}
        - WORDPRESS_DATABASE_PASSWORD=${WORDPRESS_DB_PASSWORD}
        - WORDPRESS_DATABASE_PORT_NUMBER=${WORDPRESS_DATABASE_PORT_NUMBER}
        - ALLOW_EMPTY_PASSWORD=yes

    ```

- Lo apartados más importantes que encontramos aquí es que las variables que tenemos dentro de nuestro `environment` tienen que llamarse IGUAL que las variables que encontramos en *_DockerHub_* sobre la instalación de `bitnami/wordpress`. 

- Esto se debe a qué esta *_empresa_* desarrolla este prestashop con una serie de variables que tienen que definirse así es la estructura del docker, pero en nuestro *_.env_* podrán llamarse de la manera que queramos.

### Destacamos 3.

1. `WORDPRESS_DATABASE_PORT_NUMBER=${WORDPRESS_DATABASE_PORT_NUMBER}` --> Puerto por el que se va a conectar a nuestro servicio de `MySQL`

2. `ALLOW_EMPTY_PASSWORD=yes` --> Para que no nos proporcione ningun tipo de error de acceso a nuestro servicio.

3. ` ports:
        - 80:8080
        - 443:8443` --> El puerto *_8443_* es el puerto que utiliza *_bitnami_* para poder conectarse.

# Comprobación de funcionamiento de nuestro servicio al puerto *_80_* y *_8080_*

- Antes de enseñar la comprobación, tendremos que tener lo anterior configurado de la manera correcta.

1. `docker compose up -d` --> Lanzamos los contenedores en *_background_* 

    ![]({{ site.rul }}/images/Wordpress-5.2-images/cap1.png)

2. `docker compose ps -a` --> Podemos ver toda la información de los contenedores actualmente en uso.

     ![]({{ site.rul }}/images/Wordpress-5.2-images/cap2.png)

3. `docker compose logs -f` --> Podemos ver los logs del sistema para ver que está bien o mal.

     ![]({{ site.rul }}/images/Wordpress-5.2-images/cap3.png)

### Comprobamos accediendo a la URL.

- Accedemos a la *_ip_* *_elastica_* de nuestra máquina y podremos comprobar que se ha realizado la instalación correctamente.

     ![]({{ site.rul }}/images/Wordpress-5.2-images/cap4.png)


- Conexión con PHP.

    ![]({{ site.rul }}/images/Wordpress-5.2-images/cap5.png)

# Estructura para el funcionamiento con el certificado *_HTTPS_*

- La estructura es la misma que la anterior pero modificando una serie de `apartados` que comento más adelante.

```
    version: '3'

    services:
    wordpress:
        image: bitnami/wordpress
        #Buena idea poner el tag para saber la versión por las variables.
        environment: 
            #IMPORTANTE BUSCAR EXACTAMENTE EL NOMBRE DE CADA UNA. ES DECIR, EN EL .ENV PUEDEN LLAMARSE COMO QUIERAN
            #PERO AQUÍ TENEMOS QUE BUSCAR EXACTAMENTE COMO SE LLAMAN
            # EN ESTE CASO EN BITNAMI/WORDPRESS
        - WORDPRESS_DATABASE_HOST=mysql
        - WORDPRESS_DATABASE_NAME=${WORDPRESS_DATABASE_NAME}
        - WORDPRESS_DATABASE_USER=${WORDPRESS_DATABASE_USER}
        - WORDPRESS_DATABASE_PASSWORD=${WORDPRESS_DATABASE_PASSWORD}
        - WORDPRESS_DATABASE_PORT_NUMBER=${WORDPRESS_DATABASE_PORT_NUMBER}
        - WORDPRESS_BLOG_NAME=${WORDPRESS_BLOG_NAME}
        - WORDPRESS_USERNAME=${WORDPRESS_USERNAME}
        - WORDPRESS_PASSWORD=${WORDPRESS_PASSWORD}
        - WORDPRESS_EMAIL=${WORDPRESS_EMAIL}
        - ALLOW_EMPTY_PASSWORD=yes
        # Cada variables tiene que estar bien identificada, el apartado 1 es el nombre de como tiene que llamarse en bitnami
        # El apartado después es el que tenemos que identificar con $ y el nombre de nuestra variables en el .env
        volumes: 
        - wordpress_data:/bitnami/wordpress
        depends_on:
        - mysql
        restart: always
        networks:
        - frontend
        - backend

    mysql:
        image: mysql:8.0
        ports:
        - 3306:3306
        environment:
        #Cogemos las variables que hemos declarado en Wordpress, así podemos tener menos variables y las reciclamos.
        - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
        - MYSQL_DATABASE=${WORDPRESS_DATABASE_NAME}
        - MYSQL_USER=${WORDPRESS_DATABASE_USER}
        - MYSQL_PASSWORD=${WORDPRESS_DATABASE_PASSWORD}
        volumes:
        - mysql_data:/var/lib/mysql
        restart: always
        networks:
        - backend

    phpmyadmin:
        image: phpmyadmin
        ports:
        - 8080:80
        environment: 
        #Con pma_host indicamos el servicio con el que queremos que se use.
        - PMA_HOST=mysql
        restart: always
        networks:
        - frontend
        - backend

    https-portal:
        image: steveltn/https-portal:1
        ports:
        - 80:80
        - 443:443
        restart: always
        environment:
        DOMAINS: '${DOMINIO} -> http://wordpress:8080'
        STAGE: 'production' # Don't use production until staging works
        # FORCE_RENEW: 'true'
        networks:
        - frontend


    volumes: 
    mysql_data:
    wordpress_data:

    networks:
    frontend:
    backend:
```

# Apartados mas importantes.

- Uno de los errores que nos va a dar es por culpa de mapeo de puertos. *_¿Que es el mapeo de puertos_*? --> El mapeo de puertos es cuando encontramos que en nuestra estructura los puertos se repiten.

- Para poder solucionarlo como primer paso al `servicio de Wordpress` le pondremos los puertos siguientes que son el *8080 y 8443* del contenedor de Wordpress.

- Segun `dockerhub` el contenedor de wordpress de `bitnami`nos pide que el puerto tiene que ser el *8443* por lo tanto lo pondremos.

## Apartado de HTTPS

- Como los puertos 80 y 443 de HTTPS estan libres debido a que hemos cambiado la configuración de nuestro `service`de wordpress podremos añadirlo con la siguiente estructura.


    ```
    https-portal:
            image: steveltn/https-portal:1
            ports:
            - 80:80
            - 443:443
            restart: always
            environment:
            DOMAINS: 'dominio-iawjoaquin.ddns.net -> http://wordpress:8080'
            STAGE: 'production' # Don't use production until staging works
            # FORCE_RENEW: 'true'
    ```

- Donde hemos añadido nuestro nombre de *dominio* para poder tener una conexión segura.

## Comprobación del funcionamiento.

1. `docker compose ps -a`

     ![]({{ site.rul }}/images/Wordpress-5.2-images/cap6.png)

- Aquí podemos ver los puertos nuevos que hemos configurado con su contenedor correspondiente.


2. `Pagina de ejemplo de Wordpress`

     ![]({{ site.rul }}/images/Wordpress-5.2-images/cap7.png)


![imagen]({{ site.url }})