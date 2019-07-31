# php-dev

PHP-DEV is a small package that includes a web server, PHP and some tools needed to develop a web page.
You can easily decide with a docker-compose.yml which PHP version you want and if you want to start an Apache or a Nginx webserver.
We recommend to use [pluswerk/docker-global](https://github.com/pluswerk/docker-global) as a wrapper for your projects since this Dockerfile has been build by keeping that in mind.

## Setup

Create a docker-compose.yml like the one from down below.  
Change all your settings. Mainly the `VIRTUAL_HOST`, `WEB_DOCUMENT_ROOT` and maybe the Application Context.

Then you can copy the [start.sh](start.sh) into your Project and start it.

# Documentation

The base Docker Images are [webdevops/php-apache-dev] and [webdevops/php-nginx-dev] respectivly. ([github])

[webdevops/php-apache-dev]: https://hub.docker.com/r/webdevops/php-apache-dev
[webdevops/php-nginx-dev]: https://hub.docker.com/r/webdevops/php-nginx-dev
[github]: https://github.com/webdevops/Dockerfile

## Features
* [XDebug](docs/xdebug.md) - how to debug your code
* [Environment Variables](docs/env-varaibles.md) - what can I configure
* [PHP Profiling](docs/profiling.md) - why is my code so slow/memory hungry

## Helpfull
* [Change docker project name](docs/docker-project-name.md) - all my projects are in folders named the same
* [Nginx Reverse Proxy](docs/nginx-reverse-proxy.md) - VIRTUAL_HOST explanation
* [TYPO3 configuration >=8](docs/typo3-configuration.md) - AdditionalConfiguration.php
* [TYPO3 configuration <=7](docs/typo3-configuration-legacy.md) - AdditionalConfiguration.php
* [ImageMagick or GraphicMagick](docs/magick.md) - using php module

## Docker compose

This is an example of a docker-compose.yml file.
It is enough to put this file into the project, configure it and start the Docker Project.
Further information can be found in the [Documentation].

Example file: docker-compose.yml

```yaml
version: '3.5'

services:
  web:
    image: pluswerk/php-dev:nginx-7.3

    volumes:
      - .:/app
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - ~/.ssh:/home/application/.ssh
      - ~/.composer/cache:/home/application/.composer/cache
      - ~/.gitconfig:/home/application/.gitconfig

    environment:
      # Take a look at VIRTUAL_* in the documentation under Nginx Reverse Proxy
      - VIRTUAL_HOST=docker-website.${TLD_DOMAIN:-docker},sub.docker-website.${TLD_DOMAIN:-docker}
#      - HTTPS_METHOD=noredirect

      - WEB_DOCUMENT_ROOT=/app/public
      - XDEBUG_REMOTE_HOST=${XDEBUG_REMOTE_HOST:-}
      - XDEBUG_REMOTE_PORT=${XDEBUG_REMOTE_PORT:-9000}
      - php.xdebug.idekey=${XDEBUG_IDEKEY:-PHPSTORM}

      # Project Env vars (enable what you need)
#      - APP_ENV=development_docker
#      - PIMCORE_ENVIRONMENT=development_docker
#      - TYPO3_CONTEXT=Development/docker

      # Don't forget to connect via ./start.sh
      - APPLICATION_UID=${APPLICATION_UID:-1000}
      - APPLICATION_GID=${APPLICATION_GID:-1000}

  node:
    image: node:lts
    volumes:
      - ./:/app
    working_dir: /app
    command: tail -f /dev/null

networks:
  default:
    external:
      name: global
```

## Testing this Container

- running start.sh test does execute the test-scripts
- running start.sh start does start a php-dev container and logs in automatically
