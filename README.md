# Docker Boilerplate for Symfony 2x/3x projects

Dockerfiles and docker-compose configuration for Symfony 2.x/3.x PHP applications. Includes PHP7.0/MySQL/nginx/memcached and more.

Thanks to [Maxence POUTORD](https://github.com/maxpou) for his [maxpou/docker-symfony](https://github.com/maxpou/docker-symfony) as my Dockerfiles source.

## Features

### PHP
 * PHP 7.0 version and following extensions included:
   * pdo_mysql
   * memcached
   * opcache
   * xdebug
 * Composer package installed
 * Additional packages which allows you to run assetic with compressors which requires nodejs and/or Java:
   * nodejs
   * node-less
   * openjdk-7-jre

### NGINX
 * configured nginx-proxy container to allow you access your project using custom URL added to your /ect/host file
 * log files exposed in var/logs/nginx directory

### MySQL
 * mapping created to store all database on local machine (var/db/)
 * custom access configuration via .env file

## Installation

1. Copy all files from this repo to existing Symfony project:
    ```bash
    cp .env.dist /path/to/symfony/project/
    cp docker-compose.yml /path/to/symfony/project/
    cp -r docker /path/to/symfony/project/
    ```
    If your project has an existing GIT repository and `.gitignore` file, please concat:

    ```bash
    cat .gitignore >> /path/to/symfony/project/.gitignore
    ```

    Finally, switch to the symfony repository:

    ```bash
    cd /path/to/symfony/project/
    ```

2. Create a `.env` from the `.env.dist` file. Change the `PROJECT_NAME` variable and MySQL configuration if you wish. The name specified in the `PROJECT_NAME` will be used as vhost configuration.

    ```bash
    cp .env.dist .env
    ```

3. Build/run containers 

    ```bash
    docker-compose build
    docker-compose up -d
    ```

    **Note:** If you have running services which takes :80 and :81 ports on local machine, please stop it to prevent docker reporting an error or change configuration in docker-compose.yml.

4. Update your system host file, where PROJECT_NAME is the name you specified in .env file:

    ```bash
    sudo echo "127.0.0.1 PROJECT_NAME" >> /etc/hosts
    ```

5. Prepare Symfony app
    1. Update app/config/parameters.yml

        ```yml
        # /path/to/symfony/project/app/config/parameters.yml
        parameters:
            database_host: db
        ```

    2. Composer install & create database

        ```bash
        $ docker-compose exec php bash
        $ composer install
        # Symfony2
        $ sf doctrine:database:create
        $ sf doctrine:schema:update --force
        $ sf doctrine:fixtures:load --no-interaction
        # Symfony3
        $ sf3 doctrine:database:create
        $ sf3 doctrine:schema:update --force
        $ sf3 doctrine:fixtures:load --no-interaction
        ```

6. Go to http://PROJECT_NAME/, where PROJECT_NAME is the name you specified in .env file.