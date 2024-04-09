# Docker Project: MariaDB, MySQL, and PHP Stack

This project comes from https://github.com/sprintcube/docker-compose-lamp for the base part but is heavily customised to build native php modules for the php version used at meta video (8.2) therefore using a new php version may require some changes to the various configuration files used.

## Overview

This Docker project provides a flexible environment for running MariaDB, MySQL, and PHP applications. It includes various versions of these services and is structured as follows:

## Project Structure

- **`bin`**: Contains Dockerfiles for different versions of MariaDB (103 to 106), MySQL (57 & 8), and PHP (54 to 82). Avoid editing those files as much as possible unless you know what you're doing.
- **`config`**: Holds configuration files for Apache2, database initialization, PHP, SSL, and virtual hosts.
- **`data`**: Reserved for data storage.
- **`.env`**: Environment variables file template.
For more informations about the setup core see [Lamp.md](Lamp.md), this base may seem complex but it enable deep customisation and will be able to follow potential changes in the meta video stack.

## Setup & Installation

1. Clone this repository to your local machine.
2. Navigate into the project directory .
3. Clone the projects you want to run in the `www` directory (`backoffice` for example).

## Docker compose and `.env` file.

The `docker-compose.yml` file uses many environment variables that can be set in the `.env` file at the root of the project. Most of the informations are already pre filled. You just need to add the database credentials, those will be used automaticaly by php my admin to log to the database. 

**Note**: The `.env` file is ignored by git, use the `sample.env` at the root of the project to create your `.env` file and add database credentials (`MY_SQL_ROOT_PASSWORD`, `MYSQL_USER`, `MYSQL_PASSWORD`, `MYSQL_DATABASE`)

## Environment variables and other configuration

This part is specific to the meta video setup and will cover all configuration files that may require an edit in order to make the setup work properly. These files are located in various subdirectories in the `config` directory.

### Apache2 variables

If you want to use varibles inside your php code edit the `varEnv.conf` file located at `config/apache2/varEnv.conf`. Don't touch other files, they're used in other parts of our setup and don't need modifications for deveopment usage.

### Php configuration

If you want to add something or edit the php configuration it can be done by editing the `php.ini` file at `config/php/php.ini`. The current configuration is based on what was used on other meta video setups previously with some tweaks to make it work with this new setup based on docker.

### Vhosts

Vhosts configuration is available at `config/vhosts/default.conf`, it's currently based on what was used on other setups. Once your setup is complete you'll be able to reach the backoffice with `tagging.localhost` without additional changes.

### Needed extensions

This part is important to run the commands in the next section, it contains needed and useful extensions to deal with docker containers. Copy IDs into the VSCode extensions search bar.

- ms-vscode-remote.remote-containers
- ms-azuretools.vscode-docker
- ms-vscode-remote.vscode-remote-extensionpack
- ms-vscode.remote-explorer

### Running the `composer install` command

Once your setup is done you still need to run the `composer install` command to get all modules you need. `composer` is installed on container build but the `install` command won't be run on container start. To run it manually you'll have to atach a window to the container in order to have a terminal open on the container that's where the extensions listed previously come handy.

- From the `Remote Explorer` extension window look for the `lamp-php82` container.
- Right click on it
- Select `Attach in new window`, this should open a new window attached to the php server container
- Open a terminal
- Navigate to the `composer.json` file location (For the case of `backoffice` it's located at : `/var/www/html/backoffice/resources/lib/server` because our local `www` directory is bound to the `/var/www/html` of our container as specified in the `docker-compose.yml` file)
- Run the `composer install` command
- You should be goot to go !

## Building containers and start development

Now that everything is correctly set up you can hover the `docker-compose.yml` file in VSCode and right click on it, select the `Compose Up` option, a terminal should open with the container logs. The first build is quite long as there's a lot of stuff happening in the background, mostly php plugins building from sources (you'll have to be connected to internet for the first build).
Once this step is complete you should see the containers running on the `Remote Explorer` or `Docker` extensions windows.

## Debugging

The setup also includes a debugger (that currently only work with VSCode), to start debugging just open the debugger window, place a breakpoint and click the `Play` button with the `Listen for XDebug` profile.