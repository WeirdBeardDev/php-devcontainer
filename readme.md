# PHP/MySQL Devcontainer
A baseline PHP and MySQL dev containter.

## Tech Stack
The basis for the PHP container comes from Microsoft's [mcr.microsoft.com/devcontainers/php:0-8.2](https://hub.docker.com/_/microsoft-devcontainers-php?tab=description) image.  I have not been able to find a feature list for the exact image, however, this [dev](https://github.com/devcontainers/images/blob/main/src/php/history/dev.md) gives a good idea of all the tools installed.  The version numbers for each install might vary from the linked file to the image.

* PHP v8.2.5
* Composer v2.5.5
* Xdebug v3.2.1
* Git v2.40.1
* MySQL v(latest)
* VS Code Devcontainer Extensions
    * Apache Conf (from image)
    * ESLint (form image)
    * GitLens (from `devcontainer.json`)
    * PHP Debug (from image)
    * PHP Intelephense (from image)

# PHP Configuration and Notes
## Updating PHP Server
You can change the image in the `docker-compose.yml` file to `mcr.microsoft.com/devcontainers/php` to use the latest image available.

## Composer
The repo contains a basic `composer.json` file that sets up the PSR-4 autoload to tie the `App` namespace to the `src` folder.  Once you get this running for you application you will want to run `composer dump-autoload` in the container's terminal.  This will create the `/vendor` folder used by Composer.

## Git
Git is intalled from base image and has a basic configuration applied.  By default, the image copies your host's git config, which you can confirm by running `git config --list --global` and seeing that your user name and email are present.  See the article, [Sharing Git credentials with your container](https://code.visualstudio.com/remote/advancedcontainers/sharing-git-credentials) for ways to have git working from inside the container.

## Mounted Folders
By default the devcontainer mounts a few folders and files.  You can mount additional folders and files by updating the `docker-compose.yml` file.  After you run `composer dump-autoload` (see above) you will want to update the volumes to map the `/vendor` folder, i.e., `./vendor:/var/www/vendor` and then Rebuild and Reopen the devcontainer.

* `.:/workspace` - The root folder of the host workspace is mounted to the container's `/workspace` folder.
* `./docker/xdebug/xdebug.ini:/usr/local/etc/php/conf.d/xdebug.ini` - The xdebug config file maps to the container's config file.
* `./public_html:/var/www/html` - The `public_html` folder maps to Apache's `/var/www/html` folder.
* `./src:/var/www/src` - The `src` folder maps to the `/var/www/src/` folder.

## Xdebug
Xdebug is setup and configured to listen on port 9005 and VS Code's `launch.json` is setup for the same port.  You can use whatever browser extension you find convenient, I use [Xdebug Helper](https://chrome.google.com/webstore/detail/xdebug-helper/eadndfjplgieldjbigjakmdgkmoaaaoc) (or from their [repo](https://github.com/mac-cain13/xdebug-helper-for-chrome)).

You can configure Xdebug by updating the `./docker/xdebug/xdebug.ini` file and then rebuilding the container.

# MySQL Configuration and Notes
The default setup uses the latest version of MySQL and creates a named volume `devdata` (aka `phpdev_devdata`).