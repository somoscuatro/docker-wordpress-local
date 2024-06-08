# Docker WordPress Local

This repository contains a local WordPress environment Docker setup based on:

- [Linux Alpine](https://www.alpinelinux.org/)
- [PHP FPM](https://www.php.net/manual/en/install.fpm.php)
- [nginx](https://www.nginx.com/)
- [WP CLI](https://wp-cli.org/)
- [Mailhog](https://github.com/mailhog/MailHog)
- [Xdebug](https://xdebug.org/)

Composer, Node and PNPM are also included as part of the WordPress image.

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

## How to Use Composer

To execute Composer commands within the Docker environment, use the following syntax:

`docker-compose run --rm wp composer [command]`

If you need to run Composer commands in a specific directory, you can utilize
the `--working-dir` option to designate the target directory. This is
particularly useful when you want to manage dependencies located
in a subdirectory of your container.

For example, to install Composer dependencies for the WordPress theme
[sc-starter-theme](https://github.com/somoscuatro/sc-starter-theme), run:

`docker-compose run --rm wp composer install
--working-dir=wp-content/themes/sc-starter-theme`

## How to Use Package Managers

Our WordPress image comes with all the major package managers pre-installed:
[bun](https://bun.com/), [pnpm](https://pnpm.io/), [yarn](https://yarnpkg.com/) and [npm](https://www.npmjs.com/).

To execute a package manager commands within the Docker environment, use the following
syntax:

`docker-compose run --rm wp <package-manager> <command>`

For example:

`docker-composer run --rm wp bun run build`

Some package managers allow commands to be run in a specific directory, you can utilize the
`--dir` option to designate the target directory. This is particularly useful
when you want to manage dependencies located in a subdirectory of your
container.

For example, to build assets for the WordPress theme
[sc-starter-theme](https://github.com/somoscuatro/sc-starter-theme), run:

`docker-compose run --rm wp bun --dir=wp-content/themes/sc-starter-theme run
build`

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

Xdebug comes pre-installed and activated in the wp and cli containers, allowing
debugging of website requests and WP-CLI commands.

To use Xdebug with Visual Studio Code, you have to include `?XDEBUG_SESSION=TRUE`
in the URL you are browsing, and you have to use this `launch.json` settings:

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
As an alternative to the `?XDEBUG_SESSION=TRUE` parameter in the URL, you can use
a browser extension like Xdebug Helper for [Chrome](https://chromewebstore.google.com/detail/xdebug-helper/eadndfjplgieldjbigjakmdgkmoaaaoc),
[Firefox](https://addons.mozilla.org/en-US/firefox/addon/xdebug-helper-for-firefox/),
[Edge](https://microsoftedge.microsoft.com/addons/detail/xdebug-helper/ggnngifabofaddiejjeagbaebkejomen)
or [Xdebug Key for Safari](https://apps.apple.com/bg/app/xdebug-key/id1441712067?mt=12), 

To initiate Xdebug for WP-CLI commands, you need to set environment variable `XDEBUG_SESSION=true` inside
the cli Docker container. E.g.
```shell
docker-compose run -e XDEBUG_SESSION=true --rm cli wp_cli_command_to_debug
```

You can enable/disable the Xdebug profiler by adding `profile` to the
`XDEBUG_MODE` variable in `.env`, i.e. `XDEBUG_MODE=debug,profile`.

Profiling data files are, by default, stored in the ./profiling directory. These files are prefixed
with cachegrind.out. and appended with the respective process ID.

## How to Use Mailhog

To use Mailhog, you have to install and activate [our mailhog plugin for
WordPres](https://github.com/somoscuatro/mailhog). Then you can simply visit
http://your-project.test:8025.
