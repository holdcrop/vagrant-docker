Local Development Environment
====================================

This repository contains the relevant *Vagrant* and *Docker* files to build a working local environment.

### Dependencies
* [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
* [Vagrant](https://www.vagrantup.com/downloads.html)

### Plugins
* vagrant plugin install dotenv
* vagrant plugin install vagrant-docker-compose
* vagrant plugin install vagrant-vbguest

### Directory Setup
* Create a directory e.g. `C:\tmp` or `/tmp/logs`. This is where the various logs will be written to from the application e.g. Nginx, PHP-FPM. In most cases, a sub-directory will be created automatically for each log type.

### Installation Guide
1. Clone the repository to your desired local directory
2. Make a copy of the `config.dist.yml` as `config.yml` and edit the new file with your preferred resources and directory paths
3. Add the following entry to your hosts file
    ```
    192.168.10.100 api.main.local
    ```
4. Ensure the environment file is ready in the `main` directory

### Commands
* Run default environment

        $ vagrant up

* Run environment with redis

        $ vagrant --with-redis up

* Rebuild containers

        $ vagrant --force-recreate up

* Rebuild images

        $ vagrant --no-cache up

* Rebuild images and containers

        $ vagrant --no-cache --force-recreate up

* *NOTE*: Rebuilding images and/or containers will take the VM longer to boot


### Accessing Containers
Each container can be accessed through the command line on the VM. This might be neccessary to run some diagnostics or tasks like composer updates.

        $ vagrant ssh
        Last login: Wed Jan  2 14:11:00 2019 from 10.0.2.2
        [vagrant@vagrant-docker ~]$ docker exec -ti nginx bash

Some of the containers have aliases for the commands

        [vagrant@vagrant-docker ~]$ cron
        [vagrant@vagrant-docker ~]$ db

### PHPStorm XDebug Setup
1. Create new marklets [here](https://www.jetbrains.com/phpstorm/marklets/) with the *IDE Key* `PHPSTORM`. Add the marklets to your bookmarks toolbar
2. In PHPStorm navigate to *File -> Settings -> Languages & Frameworks -> PHP -> Debug*
3. Change the *Debug Port* in the XDebug section to `9100` and ensure `Can accept external connections` is ticked
4. Expand the *Debug* menu and select the *DBGp Proxy* section
5. Set *IDE key* to `PHPSTORM`, the *Host* to `192.168.10.100` and the *Port* to `9100`
6. Navigate to *File -> Settings -> Languages & Frameworks -> PHP -> Servers* and add a new server
7. Use `api.main.local` as the *Name* and *Host*, `80` as the *Port* and `Xdebug` as the *Debugger*
8. Tick *Use path mappings* and in the section that expands, set the root of your local repository to map to `/var/www/main` as the *Absolute path on the server* e.g. `C:\Code\main -> /var/www/main`
9. Apply all changes and turn on the tool to listen for incoming debug requests
