# RSuite Docker Setup

This project provides Dockerfiles and docker-compose files that allow you to run RSuite and its dependencies in Docker containers.

You are resposible for getting the RSuite and MarkLogic installers.

## Setup Process

1. Install RSuite under the directory for the RSuite version you're running using the normal RSuite installer.
1. Get the MarkLogic installer for the MarkLogic version you want to run and put it in the corresponding `ml`n directory. 
1. Set up the mapping from local directories to the writable volumes needed by RSuite by creating a `.env` file to set the environment variables used in the `docker-compose.yml` file.
1. Modify the RSuite `conf/rsuite.properties` file as needed to reflect the MarkLogic setup you plan to create (e.g., the ports of your XDBC servers, authentication details, etc.)
1. Modify the RSuite `conf/rsuite.properties` file as needed to reflect the MySQL set up you plan to create. The default setup uses a database named "rsuite".
1. Update the `docker-compose.yml` file as needed to set the host-side ports you will use to access RSuite, MarkLogic, and MySQL. You only need to change the ports if the provided ports conflict with something you're already running.
1. Run `docker-compose up` to start the containers
1. Connect to the MarkLogic server's admin app (Docker port 8001, default host port 9101) and finish the installation process by providing your MarkLogic license.
1. Use the MarkLogic admin app to set up the database, forest, and XDBC app for use by RSuite.
1. Connect to the MySQL server using a normal MySQL client and create the database for RSuite's use, e.g. "rsuite".
1. Restart the Docker containers so RSuite can connect to the now-configured MySQL and MarkLogic servers. RSuite should work.

## Setting up Volumes

There are a number of volumes that you will likely need to be able to write to, including the RSuite modules directory (if you have custom XQuery), the RSuite plugins directory (so your deployed plugins are persistent and so you can deploy them by simply copying to the directory rather than using the remote API or the Admin web app), and any hot folders you set up.

The directories are specified using the following environment variables:

* RSUITE_MODULES
* RSUITE_PLUGINS
* RSUITE_HOTFOLDERS

You can set these variables statically in a file named `.env` in the same directory you run the `docker-compose` command from (e.g., the root of this project). The file `dotEnv.txt` provides a default set of settings. Simply copy it to `.env` and then run `docker-compose` as you normally would. 

As shown in the `dotEnv.txt` file, you can use relative paths for the host-side directories.

## Setting up RSuite

Because the RSuite installers don't provide for easy fully-automatic install, this project uses the technique of simply installing RSuite under the Dockerfile and then using Docker COPY to put the pre-configured RSuite into the Docker container. This is simple to set up but not as convenient to replicate as simply providing an installer.

The RSuite configuration needs to reflect the MarkLogic and MySQL servers as they are known inside docker, meaning that the server names are the service names defined in the docker-compose.yml file (e.g., "marklogic" and "mysql") and the ports are the inside-Docker ports, not the ports mapped from the host machine to the Docker container ports.

Once you have RSuite installed and configured, build the container:

```
docker build -t rsd/rsuite:3.6 .
```

Where the tag matches the `image:` value specified in the `docker-compose.yml` file.

## Setting up MarkLogic

Note that you will do the install using an RPM file even though the container runs Ubuntu.

### Build the container

1. Put the RPM file for the MarkLogic version you are running into the appropriate `ml`x directory.
2. Edit the `Dockerfile` to set the `INSTALLER` argument to the filename of the MarkLogic RPM file.
3. Build the container:

   ```
   docker build -t rsd/ml:4.2 .
   ```
   
   Where the tag matches the `image:` value specified in the `docker-compose.yml` file.

### Configure the MarkLogic server

If you want to be able to access the MarkLogic XDBC app server (or any other app server you configure) from outside Docker you need to add port mappings to the `docker-compose.yml` file.

Otherwise, there is no other configuration required to run MarkLogic. You will need to configure MarkLogic using the MarkLogic administration web app once you start the container. The MarkLogic data is persisted to a named volume as configured in the `docker-compose.yml` file.

## Setting up MySQL

The MySQL image is a public image pulled from hub.docker.com.

The defaul host port for MySQL is 9306 (as defined in the `docker-compose.yml` file). You can connect to this using a normal MySQL client like so:

```
mysql -P 9306 -u root -p  --host=127.0.0.1 --protocol=TCP
```

The default password is "admin"

To create the RSuite database do:

```
create database rsuite;
```

The MySQL data is persisted to a named volume defined in the `docker-compose.yml` file.

