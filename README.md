# Docker OXID 6 Setup

Docker setup for a OXID v6 installation. OXID v6 is currently in beta mode, I wanted to test the OXID installation inside Docker containers. This repository is a work in progress to find a best practice for OXID inside Docker.

Any feedback and pullrequests are welcome! :)


The current setup includes:

- OXID 6
- MySQL 5.7
- PHP 7.0
- PHPMyAdmin


## Setup

0. [Install Docker](https://docs.docker.com/engine/installation/) on your system.
1. Make sure you clone the repository to your home directory. So we don't clash with macOS permission.
2. Run `$ docker-compose up -d` in the root to build and create all Docker containers.
3. Start the containers with `$ docker-compose start` and use `$ docker-compose stop` to stop it.
4. Install Composer dependencies with `$ docker-compose run composer install`
5. Add to your local `/etc/hosts` file: `127.0.0.1 local.oxid.tld`

### Fresh OXID installation

Go to [local.oxid.tld](http://local.oxid.tld) and run through the OXID installer. MySQL Database credentials are:

    Host:     mysql
    User:     oxid
    Database: oxid
    Password: oxid

The credentials can be altered inside the `docker-compose.yml`.

After installing and configuring your OXID you can export the MySQL database (without the `oxv_*` tables) and store the dump inside `docker/assets/database/`. Also add ignored files like `config.inc.php` and images to the `docker/files/` directory. By doing so you can have minimal working OXID inside your git repository.

### Existing OXID installation

1. Copy the assets from `docker/assets/files` to `src/oxid/soruce/`, replacing and merging with existing files. Please make sure hidden (`.htaccess` i.e.) are copied as well.
2. Import the database from `docker/assets/database`, you can find a PHPMyAdmin at `local.oxid.tld:8080`. Make sure to select the existing (but empty) database named `oxid`
6. Go to `local.oxid.tld` for Frontend or `local.oxid.tld/admin/` for Backend


### PHP configuration

- You can edit the `php.ini` inside `docker/conf/php.ini`, you need to restart the containers with `$ docker-compose restart`
- All logs of the webserver container are exposed in the automatically created `log/` directory, PHP/Apache logs are inside `log/s/apache2/`

### PHP Debugging with Xdebug

Copy the `.env.example` file to `.env` and change the settings to your values.

Inside PHPStorm https://medium.com/@pablofmorales/xdebug-with-docker-and-phpstorm-786da0d0fad2

## File structure

    .
    ├── README.md
    ├── docker
    │   ├── assets               # files needed to run the project: database dump, config file, images, …
    │   │   ├── database
    │   │   └── files
    │   └── conf                 # PHP/Apache container settings
    │       ├── 000-default.conf
    │       ├── Dockerfile
    │       ├── modules          # PHP modules to be installed with php.ini
    │       └── php.ini
    ├── docker-compose.yml
    ├── log                      # contains Apache/PHP Logs of the container (git ignored )
    └── src                      # src contains oxid and other project relevant stugg, i.e. flow-sass
        └── oxid
            ├── composer.json
            ├── composer.lock
            ├── source # oxid src
            └── vendor

Inside `src/` you can locate other project relevant code. For example we use a frontend build porcess with Grunt and create `src/pattern` from which we will copy compiled CSS and JavaScript resources into `src/oxid/out/<theme>`.

## Questions / todos

- [  ] What should be excluded in the `.gitignore`? OXID modules installed with composer will be copied to `oxid/source/modules/`. Should/can we ignore those? Also Application, themes, etc.?
- [  ] Best practice for deployments ([Deployer](https://deployer.org/))
- [  ] the Docker setup can be painfully slow on macOS
- [  ] When developing a new extension which includes only stuff for the actual project, should I place inside the modules directory or still install via a new repo and composer?
