# Index
1. [What is Docker](./DockerQuickNotes01.md)
2. [Docker Commands](./DockerQuickNotes02-Commands.md)
3. [Docker Practice](./DockerQuickNotes03-Practice.md)


# 2 Docker Commands

## 2.1 Basic Docker Commands Example
* docker image
    ```bash
    $ docker image ls # list out all local existing images
    $
    ```
* docker pull
    ```bash
    $ docker pull redis # pulled with latest-version
    $
    $ docker pull redis:5.9.0-alpine # pulled with specific version
    $
    ```
* docker run in attach mode/front-end
    ```bash
    # Docker create a new Container with Image
    # If local machine/environment don't have the image, docker will pull from Docker Hub. Refer Docker Pull
    $ docker run redis
    $
    ```
* docker run option - in detach mode/background
    ```bash
    $ docker run --detach redis
    $
    $ docker run -d redis
    $
    ```
*  docker run option - with port binding (host/local machine:16379 to container:6379)
    ```bash
    $ docker run \
    > --publish 16379:6379 \
    > redis:6.0.9-alpine
    $
    $ docker run \
    > -p 16379:6379 \
    > redis:6.0.9-alpine
    $
    ```
* docker run option - with assign a name to the container
    ```bash
    $ docker run --name container_name # container_name is a name assign by you
    $
    ```
* docker run with all options
    ```bash
    $ docker run \
    > --detach \
    > --publish 16379:6379 \
    > --name mmg_redis \
    > redis:6.0.9-alpine
    $
    $ docker run \
    > --d \
    > --p 16379:6379 \
    > --name mmg_redis \
    > redis:6.0.9-alpine
    $
    ```
* docker ps
    ```bash
    $ docker ps
    $
    $ docker ps -a # show all Containers including not running
    $
    ```
* docker stop (need to get Container ID from `docker ps` command)
    **Important: All data in that container will be wipe once the stop command initiated.**
    ```bash
    $ docker ps
    $
    $ docker stop 123456789 # 12345689 is Container ID from $ docker ps
    $
    ```
* docker start
    ```bash
    # Start the Container
    $ docker ps -a # show all Containers including not running
    $
    $ docker start 123456789 # 12345689 is Container ID from $ docker ps -a
    $
    ```
* docker log, this is for troubleshooting
    ```bash
    $ docker ps # to get Container ID or Container Name from this command
    $
    $ docker logs 123456789 # retrive log using Container ID
    $
    $ docker logs mmg_redis # retrieve log using Container Name
    $
    ```
* docker exec
    ```bash
    $ docker ps # to get Container ID or Container Name from this command
    $
    # enter into the container using terminal
    $ docker exec \
    > --interactive \
    > --tty 123456789 \
    > /bin/bash # using Container ID
    $
    $ docker exec --interactive --tty mmg_redis /bin/bash # using Container Name
    $
    $ docker exec \
    > -it \
    > 123456789 \
    > /bin/bash # using Container ID
    # inside the container for troubleshooting
    root@123456789:/data# # inside the container
    root@123456789:/data#
    root@123456789:/data# env # check environmental variables within the container
    root@123456789:/data#
    root@123456789:/data# exit # exit the container
    $
    ```
* docker network
    ```bash
    $ docker network ls
    $
    ```


[Back to top](#)
