# Docker WordPress Local

This repository contains a local WordPress environment Docker setup based on:

- [Linux Alpine](https://www.alpinelinux.org/)
- [PHP FPM](https://www.php.net/manual/en/install.fpm.php)
- [nginx](https://www.nginx.com/)
- [WP CLI](https://wp-cli.org/)
- [Mailhog](https://github.com/mailhog/MailHog)
- [Xdebug](https://xdebug.org/)

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
   1. The `.env.sample` file. Rename it to `.env`.
1. Edit the `.env` environment constants with your project details.
1. Go to your project root directory and move into the `.docker` folder:
   1. Create a certs folder and move into it: `mkdir certs && cd certs`
   1. Run `mkcert -key-file cert-key.pem -cert-file cert.pem your-project.test localhost`
1. Adjust your project `wp-config.php` file:

   ```php
     // Database settings.
     define( 'DB_NAME', getenv( 'DB_NAME' ) );
     define( 'DB_USER', getenv( 'DB_USER' ) );
     define( 'DB_PASSWORD', getenv( 'DB_PASSWORD' ) );
     define( 'DB_HOST', getenv( 'DB_HOST' ) );
     define( 'DB_CHARSET', 'utf8' );
     define( 'DB_COLLATE', '' );

     // Site URL settings.
     define( 'WP_SITEURL', getenv( 'WP_SITEURL' ) );
     define( 'WP_HOME', getenv( 'WP_HOME' ) );
   ```

1. Ensure your `/etc/hosts` file contains `127.0.0.1 your-project.test` or use a
   tool such as [Dnsmasq](https://thekelleys.org.uk/dnsmasq/doc.html)
1. Run `docker-compose up` from the project root directory.

## How to Use WP CLI

To use WP CLI, run `docker-compose run --rm cli` followed by the WP CLI command.
For example: `docker-compose run --rm cli plugin list`.

Tip: You can create a shell alias: `alias dwp='docker-compose run --rm cli'`.

## How to Switch PHP Version

To switch the PHP version, change the related PHP_VERSION variable in the `.env`
file. Available versions are `8.3`, `8.2`, `8.0` and `7.4`. See [Docker Hub
wordpress image
page](https://hub.docker.com/_/wordpress/tags?page=&page_size=&ordering=&name=cli-php7)
for more info about available tags.

## How to Enable HTTPS

By default, HTTPS is enabled. To make it work, you need to install `mkcert`
library (see Prerequisite section) and generate SSL certificates (see step 3 of
the Usage section).

## How to Use Xdebug

Xdebug is enabled by default. To use it with Visual Studio Code, you have to
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

You can enable/disable the Xdebug profiler by adding `profile` to the
`XDEBUG_MODE` variable in `.env`, i.e. `XDEBUG_MODE=debug,profile`.

By default, requests end up in the `./profiling` directory. The files begin with
`cachegrind.out.` and are suffixed with the process ID.

## How to Use Mailhog

To use Mailhog, you have to install and activate [our mailhog plugin for
WordPres](https://github.com/somoscuatro/mailhog). Then you can simply visit
http://your-project.test:8025.
