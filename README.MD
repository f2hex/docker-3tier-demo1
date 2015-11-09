## 3 tier application with Docker
This is a sample 3 tier application used to demonstrate some of the benefits of Docker with a deployment confined to a single host (Docker node):
* **re-use of existing artifacts**: in this case is about to use an existing image, on Docker Hub, customize it and run in a composite 3 tier application service
* **ability to move a workload seamlessly across multiple infrastructure**: at this time this is done as cold migration: stop the application, migrate the data and restart it in the new host (the application images are available on the Docker registry so ther is no need to transfer them) - the Containers in this case are fully stateless.
* **use of custom private networks**: (based on the latest SDN capability of Docker - version 1.9), this allow for self-contained multi-host configuration, within the application service

### Scripts
The `start_container` script it is used to test the startup of each Container by hand. It has two parameters, the first it is useful in order to specify the run mode: interactive or detached. The latter is the name of the Container tier module: web, app or db.

The `backup_restore` it is used to perform a backup or a restore of the volumes of the applicaiton service.
The `docker_compose.yml` it is used by docker-compose to control the application service.

## Images
The images are based on public images available on Docker Hub and slightly customized in order to implement networking self-contained multi-host configuration. The Dockefiles are located in the respective subdirectory: app_server, db_server and web_server.

## How to use it
Prerequisites:
* a Ubuntu system wth Docker engine 1.9 or newer service installed and running
* docker-compose, version 1.5 or newer installed
* git package installed
* no running service bound the HTTPS port (443), becaus the SSL web server will need it.

### Starting the application service
1) Login to the Ubuntu system
2) Create a `redmine` subdir
3) Clone this repo:
```
  git clone docker-3tier-demo1
```
4) Create the volume mount point - the script assume to have `/data` directory on the root, if it does not exists it will be created by the `setup` script.
```
  sudo ./setup
```
5) Start the application service using docker-compose:
```
  docker-compose --x-networking up -d
```
6) Verify that the applicaiton service is up and running:
```
  docker ps
```
You should see the three Containers running - like in this output:
```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
670035f395f1        f2hex/redmine       "/docker-entrypoint.s"   2 seconds ago       Up 1 seconds        3000/tcp               redmine-app_server
a0ef6297626b        f2hex/postgres      "/docker-entrypoint.s"   2 seconds ago       Up 1 seconds        5432/tcp               redmine-db_server
b2014967a7f0        f2hex/nginx         "/docker-entrypoint.s"   2 seconds ago       Up 1 seconds        0.0.0.0:443->443/tcp   redmine-web_server
```
Now you can point your browser to the endpoint of the web server in HTTPS - it should be the main network interface or the "natted" public ip address if you are using a Cloud based OS image as the Docker host.
Play with the Redmine appication logging into it (user: admin, password: admin), create a project and put some data.

### Migrating the application to another infrastructure or Cloud provider
It is almost straightforward and these are the steps:
1) First ensure you are on the same directoy where you started the service (i.e. where the `docker-compose.yml` file is located), then stop the application service using `docker-compose`
```
  docker compose --x-networking stop
```
2) Now you should see no more the application Containers running so you can completely remove the used resources still using `docker-compose`:
```
  docker compose --x-networking rm
```
3) You can now, still in the same directory where you run the `docker-compose rm` commandm run the volume backup script - you will need to authenticate for sudo execution:
```
  ./backup_restore backup
```
The result is a tarball containing the volumes tree used by the three Containers that compose the application service.
4) You need now to move to the new host where you like to move/clone the application service - ensuring the you have the same prerequisites fully satisfied like on the ones explained above in this README.
5) create the same directory on the new host and clone the repo:
```
  git clone docker-3tier-demo1
```

5) Then transfer the tarball, with the volumes data, to the new host, execute the restore using the provided script:
```
  ./backup_restore restore
```
6) You can now start the service in the new host using, again, `docker-compose`:
```
  docker-compose --x-networking up -d
```
The application service should be now running on the new host and if you connect to it, using the browser, you should be able to find the same data you inserted in the previous session on the first host.

### Credits
The original docker images, that are used in this demo application service, are available on Docker Hub:
* [ Docker Redmine official image ] (https://hub.docker.com/_/redmine/)
* [ Docker postgres official image ] (https://hub.docker.com/_/postgres/)
* [ Docker nginx official image ] (https://hub.docker.com/_/nginx/)


