# nginx-php-wordpress
[![Build Status](https://img.shields.io/travis/demyxco/nginx-php-wordpress?style=flat)](https://travis-ci.org/demyxco/nginx-php-wordpress)
[![Docker Pulls](https://img.shields.io/docker/pulls/demyx/nginx-php-wordpress?style=flat&color=blue)](https://hub.docker.com/r/demyx/nginx-php-wordpress)
[![Architecture](https://img.shields.io/badge/linux-amd64-important?style=flat&color=blue)](https://hub.docker.com/r/demyx/nginx-php-wordpress)
[![Alpine](https://img.shields.io/badge/alpine-3.10.2-informational?style=flat&color=blue)](https://hub.docker.com/r/demyx/nginx-php-wordpress)
[![NGINX](https://img.shields.io/badge/nginx-1.17.3-informational?style=flat&color=blue)](https://hub.docker.com/r/demyx/nginx-php-wordpress)
[![PHP](https://img.shields.io/badge/php-7.3.9-informational?style=flat&color=blue)](https://hub.docker.com/r/demyx/nginx-php-wordpress)
[![WordPress](https://img.shields.io/badge/wordpress-5.2.3-informational?style=flat&color=blue)](https://hub.docker.com/r/demyx/nginx-php-wordpress)
[![Buy Me A Coffee](https://img.shields.io/badge/buy_me_coffee-$5-informational?style=flat&color=blue)](https://www.buymeacoffee.com/VXqkQK5tb)

Automatically installs wp-config.php using environment variables, configures salts, and enables HTTP_X_FORWARDED_PROTO. Image was built for: [github.com/demyxco](https://github.com/demyxco/demyx). 

TITLE | DESCRIPTION
--- | ---
USER<br />GROUP | www-data (82)<br />www-data (82)
WORKDIR | /var/www/html
PORT | 80
TIMEZONE | America/Los_Angeles
PHP | /etc/php7/php.ini<br />/etc/php7/php-fpm.d/php-fpm.conf
NGINX | /etc/nginx/nginx.conf<br />/etc/nginx/cache<br />/etc/nginx/common<br />/etc/nginx/modules<br />

## Updates & Support
[![Code Size](https://img.shields.io/github/languages/code-size/demyxco/nginx-php-wordpress?style=flat&color=blue)](https://github.com/demyxco/nginx-php-wordpress)
[![Repository Size](https://img.shields.io/github/repo-size/demyxco/nginx-php-wordpress?style=flat&color=blue)](https://github.com/demyxco/nginx-php-wordpress)
[![Watches](https://img.shields.io/github/watchers/demyxco/nginx-php-wordpress?style=flat&color=blue)](https://github.com/demyxco/nginx-php-wordpress)
[![Stars](https://img.shields.io/github/stars/demyxco/nginx-php-wordpress?style=flat&color=blue)](https://github.com/demyxco/nginx-php-wordpress)
[![Forks](https://img.shields.io/github/forks/demyxco/nginx-php-wordpress?style=flat&color=blue)](https://github.com/demyxco/nginx-php-wordpress)

* Auto built weekly on Sundays (America/Los_Angeles)
* Rolling release updates
* For support: [#demyx](https://webchat.freenode.net/?channel=#demyx)

## WordPress Container
ENVIRONMENT | VARIABLE
--- | ---
WORDPRESS_DB_HOST | db
WORDPRESS_DB_NAME | demyx_db
WORDPRESS_DB_USER | demyx_user
WORDPRESS_DB_PASSWORD | demyx_password
WORDPRESS_DOMAIN | domain.tld
WORDPRESS_UPLOAD_LIMIT | 128M
WORDPRESS_PHP_MEMORY | 256M
WORDPRESS_PHP_MAX_EXECUTION_TIME | 300
WORDPRESS_PHP_OPCACHE | "on"
WORDPRESS_NGINX_CACHE | "off"
WORDPRESS_NGINX_RATE_LIMIT | "off"
WORDPRESS_NGINX_BASIC_AUTH | demyx:demyx<br />demyx:$$apr1$$EqJj89Yw$$WLsBIjCILtBGjHppQ76YT1
TZ | America/Los_Angeles

* Install the helper plugin [Nginx Helper](https://wordpress.org/plugins/nginx-helper/) if DEMYX_NGINX_CACHE is "on"
* To generate htpasswd: `docker run -it --rm demyx/utilities "htpasswd -nb demyx demyx"`
* DEMYX_BASIC_AUTH must have double dollar signs ($$)

## MariaDB Container
ENVIRONMENT | VARIABLE
--- | ---
MARIADB_BINLOG_FORMAT | mixed
MARIADB_CHARACTER_SET_SERVER | utf8
MARIADB_COLLATION_SERVER | utf8_general_ci
MARIADB_DATABASE | # Optional
MARIADB_DEFAULT_CHARACTER_SET | utf8
MARIADB_INNODB_BUFFER_POOL_SIZE | 16M
MARIADB_INNODB_DATA_FILE_PATH | ibdata1:10M:autoextend
MARIADB_INNODB_FLUSH_LOG_AT_TRX_COMMIT | 1
MARIADB_INNODB_LOCK_WAIT_TIMEOUT | 50
MARIADB_INNODB_LOG_BUFFER_SIZE | 8M
MARIADB_INNODB_LOG_FILE_SIZE | 5M
MARIADB_INNODB_USE_NATIVE_AIO | 1
MARIADB_KEY_BUFFER_SIZE | 20M
MARIADB_LOG_BIN | mysql-bin
MARIADB_MAX_ALLOWED_PACKET | 16M
MARIADB_MAX_CONNECTIONS | 151
MARIADB_MYISAM_SORT_BUFFER_SIZE | 8M
MARIADB_NET_BUFFER_SIZE | 8K
MARIADB_PASSWORD | # Optional
MARIADB_READ_BUFFER | 2M
MARIADB_READ_BUFFER_SIZE | 256K
MARIADB_READ_RND_BUFFER_SIZE | 512K
MARIADB_ROOT_PASSWORD | # Required
MARIADB_SERVER_ID | 1
MARIADB_SORT_BUFFER_SIZE | 20M
MARIADB_TABLE_OPEN_CACHE | 64
MARIADB_USERNAME | # Optional
MARIADB_WRITE_BUFFER | 2M

## Usage
This config requires no .toml for Traefik and is ready to go when running: `docker-compose up -d`. 

SSL/TLS
* Remove the comments (#)
* `docker run -t --rm -v demyx_traefik:/demyx demyx/utilities "touch /demyx/acme.json; chmod 600 /demyx/acme.json"`

```
version: "3.7"

services:
  traefik:
    image: traefik:v1.7.16
    container_name: demyx_traefik
    restart: unless-stopped
    command: 
      - --api
      - --api.statistics.recenterrors=100
      - --docker
      - --docker.watch=true
      - --docker.exposedbydefault=false
      - "--entrypoints=Name:http Address::80"
      #- "--entrypoints=Name:https Address::443 TLS"
      - --defaultentrypoints=http
      #- --defaultentrypoints=http,https
      #- --acme
      #- --acme.email=info@domain.tld
      #- --acme.storage=/demyx/acme.json
      #- --acme.entrypoint=https
      #- --acme.onhostrule=true
      #- --acme.httpchallenge.entrypoint=http
      - --logLevel=INFO
      - --accessLog.filePath=/demyx/access.log
      - --traefikLog.filePath=/demyx/traefik.log
    networks:
      - demyx
    ports:
      - 80:80
      #- 443:443
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
      #- demyx_traefik:/demyx/acme.json
    labels:
      - "traefik.enable=true"
      - "traefik.port=8080"
      - "traefik.frontend.rule=Host:traefik.domain.tld"
      #- "traefik.frontend.redirect.entryPoint=https"
      #- "traefik.frontend.auth.basic.users=${DEMYX_STACK_AUTH}"
      #- "traefik.frontend.headers.forceSTSHeader=true"
      #- "traefik.frontend.headers.STSSeconds=315360000"
      #- "traefik.frontend.headers.STSIncludeSubdomains=true"
      #- "traefik.frontend.headers.STSPreload=true"
  db:
    container_name: demyx_db
    image: demyx/mariadb
    restart: unless-stopped
    networks:
      - demyx
    volumes:
      - demyx_db:/var/lib/mysql
    environment:
      - MARIADB_DATABASE=demyx_db
      - MARIADB_USERNAME=demyx_user
      - MARIADB_PASSWORD=demyx_password
      - MARIADB_ROOT_PASSWORD=demyx_root_password # mandatory
      - MARIADB_BINLOG_FORMAT=mixed
      - MARIADB_CHARACTER_SET_SERVER=utf8
      - MARIADB_COLLATION_SERVER=utf8_general_ci
      - MARIADB_DEFAULT_CHARACTER_SET=utf8
      - MARIADB_INNODB_BUFFER_POOL_SIZE=16M
      - MARIADB_INNODB_DATA_FILE_PATH=ibdata1:10M:autoextend
      - MARIADB_INNODB_FLUSH_LOG_AT_TRX_COMMIT=1
      - MARIADB_INNODB_LOCK_WAIT_TIMEOUT=50
      - MARIADB_INNODB_LOG_BUFFER_SIZE=8M
      - MARIADB_INNODB_LOG_FILE_SIZE=5M
      - MARIADB_INNODB_USE_NATIVE_AIO=1
      - MARIADB_KEY_BUFFER_SIZE=20M
      - MARIADB_LOG_BIN=mysql-bin
      - MARIADB_MAX_ALLOWED_PACKET=16M
      - MARIADB_MAX_CONNECTIONS=151
      - MARIADB_MYISAM_SORT_BUFFER_SIZE=8M
      - MARIADB_NET_BUFFER_SIZE=8K
      - MARIADB_READ_BUFFER=2M
      - MARIADB_READ_BUFFER_SIZE=256K
      - MARIADB_READ_RND_BUFFER_SIZE=512K
      - MARIADB_SERVER_ID=1
      - MARIADB_SORT_BUFFER_SIZE=20M
      - MARIADB_TABLE_OPEN_CACHE=64
      - MARIADB_WRITE_BUFFER=2M
      - TZ=America/Los_Angeles
  wp:
    container_name: demyx_wp
    image: demyx/nginx-php-wordpress
    restart: unless-stopped
    networks:
      - demyx
    volumes:
      - demyx_wp:/var/www/html
    environment:
      - WORDPRESS_DB_HOST=db
      - WORDPRESS_DB_NAME=demyx_db
      - WORDPRESS_DB_USER=demyx_user
      - WORDPRESS_DB_PASSWORD=demyx_password
      - WORDPRESS_DOMAIN=domain.tld
      - WORDPRESS_UPLOAD_LIMIT=128M
      - WORDPRESS_PHP_MEMORY=256M
      - WORDPRESS_PHP_MAX_EXECUTION_TIME=300
      - WORDPRESS_PHP_OPCACHE=on
      - WORDPRESS_NGINX_CACHE=off
      - WORDPRESS_NGINX_RATE_LIMIT=off
      - WORDPRESS_NGINX_BASIC_AUTH=demyx:$$apr1$$EqJj89Yw$$WLsBIjCILtBGjHppQ76YT1
      - TZ=America/Los_Angeles
    labels:
      - "traefik.enable=true"
      - "traefik.port=80"
      - "traefik.frontend.rule=Host:domain.tld"
      #- "traefik.frontend.redirect.entryPoint=https"
      #- "traefik.frontend.auth.basic.users=${DEMYX_STACK_AUTH}"
      #- "traefik.frontend.headers.forceSTSHeader=true"
      #- "traefik.frontend.headers.STSSeconds=315360000"
      #- "traefik.frontend.headers.STSIncludeSubdomains=true"
      #- "traefik.frontend.headers.STSPreload=true"  
volumes:
  demyx_wp:
    name: demyx_wp
  demyx_db:
    name: demyx_db
  demyx_traefik:
    name: demyx_traefik
networks:
  demyx:
    name: demyx
```
