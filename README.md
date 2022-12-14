# Docker WordPress Local

This repository contains a local WordPress environment Docker setup based on:

- Linux Alpine
- PHP FPM
- nginx
- WP CLI
- Mailhog
- xdebug

## Prerequisites

To use this local environment, you need:

1. Install [Docker Desktop](https://www.docker.com/products/docker-desktop/)
1. Install [mkcert](https://github.com/FiloSottile/mkcert#macos): `brew install
mkcert`

## Usage

To use this image in an existing WordPress project:

1. Copy the following to your project root directory:
   1. The `docker-compose.yml` file.
   1. The `.docker` folder.
   1. The `.env.example` file. Rename it to `.env`.
1. Edit the `.env` environment constants with your project details.
1. Go to your project root directory and move into the `.docker` folder:
   1. Create a certs folder and move into it: `mkdir certs && cd certs`
   1. Run `mkcert your-project.test localhost`
   1. Edit the `nginx/nginx.config` file and change these two lines to use the
      certificate you created in the previous point:

      ```text
        ssl_certificate     /etc/ssl/your-project.test+1.pem;
        ssl_certificate_key /etc/ssl/your-project.test+1-key.pem;
      ```

1. Adjust your project `wp-config.php` file to use the database. Ensure the value for
constant `DB_HOST` is `db:3306`.
1. Ensure your `/etc/hosts` file contains `127.0.0.1 your-project.test`
1. Run `docker-compose up` from the project root directory.

## How to Use WP CLI

To use WP CLI, run `docker-compose run --rm cli` followed by the WP CLI command.
For example: `docker-compose run --rm cli plugin list`.

Tip: You can create a shell alias: `alias dwp='docker-compose run --rm cli'`.

## How to Switch PHP Version

To switch the PHP version, change the related PHP_VERSION variable in the `.env`
file. Available versions are `8.1`, `8.0` and `7.4`.

## How to Enable HTTPS

By default, HTTPS is enabled. To make it work, you need to install `mkcert`
library (see Prerequisite section) and generate SSL certificates (see step 3 of
the Usage section).

## How to Use xdebug

`xdebug` is enabled by default. To use it with Visual Studio Code, you have to
include `?XDEBUG_SESSION=TRUE` in the URL you are browsing, and you have to use
this `launch.json` settings:

```JSON
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Listen for Xdebug",
      "type": "php",
      "request": "launch",
      "port": 9003,
      "pathMappings": {
        "/var/www/html": "${workspaceFolder}"
      }
    }
  ]
}
```

## How to Use Mailhog

To use Mailhog, you have to install and activate the mailhog-plugin.
Then you can simply visit http://your-project.test:8025.
