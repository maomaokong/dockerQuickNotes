# Index
1. [What is Docker](./DockerQuickNotes01.md)
2. [Docker Commands](./DockerQuickNotes02-Commands.md)
3. [Docker Practice](./DockerQuickNotes03-Practice.md)


# 1 Docker

## 1.1 What is Docker? Docker container concept explained
* What is a container and what problems does it solve?
    1. A way to package applications with all the necessary dependencies and configurations.
    2. Portable artefact readily shared and moved around.
    3. Makes development and deployment more efficient.

* Container repository
    Where do containers live?
    1. Container Repository
    2. Private vs Public
        * Docker and [Docker Hub](https://hub.docker.com/)

    ![alt text](https://drive.google.com/uc?export=view&id=1jZtmPH42Ladwp4uC7C16xW-xqpGNjH9K "Container")

* `DEVELOP` applications
    * Before containers
        1. Each developer needs to install the application-specific version.
        2. Installation process different on each OS environments.
        3. Many steps where something could go wrong.
    ![alt text](https://drive.google.com/uc?export=view&id=1o0ALnQicXekJY5VW-K4vw2dTq9lgK0b6 "Development Before Containers")

    * After containers
        1. Packaged with all needed configuration.
        2. One command to install the application.
        3. Run the same app with two different versions.
    ![alt text](https://drive.google.com/uc?export=view&id=1qWl4USQI1_EW0nQD7Hlak1HrbKfJ4J7V "Development After Containers")
    ![alt text](https://drive.google.com/uc?export=view&id=1PDTqWNW990TCWRh9IgR5Fl_KdKagIamo "Development After Containers-Command")

* `DEPLOY` applications
    * Before containers
        1. Textual guide of deployment.
        2. Configuration on the server needed.
        3. External dependencies on the server OS.
            * Problem - dependency versions.
    ![alt text](https://drive.google.com/uc?export=view&id=1aMGM8puJ-hpQrEfQ0S_LiOViv6mSuRsy "Deployment Before Containers")

    * After containers
        1. Developer and Operations work together to package the application in a container.
        2. No environmental configuration needed on the server - except for docker runtime.
    ![alt text](https://drive.google.com/uc?export=view&id=1qIax0cgJn-NLBaWiA9B9y2qbtmFYD-zA "Deployment After Containers")



## 1.2 What is Docker Container?
* Layers of Linux base image, application image with configuration.
![alt text](https://drive.google.com/uc?export=view&id=1ztCjmuuvwUiDSChfWxbT5z7IKi-bcQqP "Sample Container Components")
* Sample Demo
    1. Visit https://hub.docker.com
    2. Search for *PostgreSQL* official image with a specific version. Refer to the command provided within the Docker Hub page.
    ![alt text](https://drive.google.com/uc?export=view&id=1KwWvnhzTyf-0gXxXIwsYNvyjlnx4NuxD "Images from Docker Hub")
    3. In the terminal, execute the following command to pull *PostgreSQL* version 9.6 from Docker Hub:
        ```bash
        $ docker run postgres:9.6
        $
        ```
    4. In the terminal, check the container is running with the following command:
        ```bash
        $ docker container ls
        $ ## or
        $ docker ps
        $
        ```
* What is **Image**?
    Image is the actual packaging. The application package will consist of the configurations and the dependencies. This artefact that is movable around is called **Image**.
* What is **Container**?
    When pulling the source's image on the local machine/environment and started the application will create the container environment.
* **Container** is a running environment for **Image**.
    ![alt text](https://drive.google.com/uc?export=view&id=1KgTMUHEBZQdVlAe3ON6cvlMM5iV2109Q "Container is a running environment for Image")
* Container allows two different PostgreSQL version running on the same local machine/environment.


## 1.3 Docker vs Virtual Machine
* Normal Operating Systems(OS) have two Layers: OS Kernal and Applications Layers.
![alt text](https://drive.google.com/uc?export=view&id=1eBhe_JCAusfUMTVe6PiKhcrdFFwwyKRL "Machine and OS Layers")
* Docker run on Applications Layer only.
* Virtual Machine(VM) run on both OS Kernel and Applications Layer. VM does not run local machine own OS Kernel.
![alt text](https://drive.google.com/uc?export=view&id=1UyevDpNq66LUIQN_JSgwLQnQtOkzjyfE "Docker and VM Layers")
* **Size**: Docker image much smaller than VM image.
* **Speed**: Docker containers start and run much faster.
* **Compatibility**: VM of any OS can run on any OS host.
    * Docker is based on Linux Kernel which not compatible with Windows Kernel (Older Windows Kernel).
    * Workaround for Windows OS is to install *Docker Toolbox*, this might not be applicable in the future version.
![alt text](https://drive.google.com/uc?export=view&id=1SGxU2RC00afTStW6W1ZTTH_Ux0ctxjMy "Docker Compatibility")
        

## 1.4 Docker Container and Network/Port Binding
* Two Docker images with different version will run in the same local machine/environment using the same port number, which generally will crash the application.
![alt text](https://drive.google.com/uc?export=view&id=13UXhgGkA1mnI9gQ6EJWub7kKpHfoX66J "Containers running with same port")
* Need to bind with different Host port if port crashed.
    1. Host port 3000 ==> Container port 3000
    2. Host port 3001 ==> Container port 3000
![alt text](https://drive.google.com/uc?export=view&id=1BEW7IopeWsKowX6LzoGYtqC8NW396a56 "Container port vs Host port")
* Running the application with new bound Host port 3001.
![alt text](https://drive.google.com/uc?export=view&id=1oMAI7O6JsOYDoJreSCNbqvoft3Fszs1s "Connect application with new binded host port")
* Docker creates its isolated network where the containers are running.
* When two containers in the same Docker network, these containers can talk to each other just using the container name without localhost port number and etc.
* The applications that run outside of Docker Network only connect to the containers from outside of the Docker Network using localhost and specific port number.
![alt text](https://drive.google.com/uc?export=view&id=14W1sqerch_dbpckgKw4yO6JIXadV2lxK "Docker Network when Development")
![alt text](https://drive.google.com/uc?export=view&id=10boqqeGu8v6UxU0yem_DP9uEKxJ7YelA "Docker Network when Go-Live")


## 1.5 Docker Volumes and Usages
* Docker Volumes is used for Data Persistence. A Docker container restarting or stopped will be caused all data and configurations lost especially like *Databases* or *Other Stateful Applications*.
![alt text](https://drive.google.com/uc?export=view&id=1-yUYB_cdohZ9BtpxoQQSQqg57jlHj-b1 "Docker Volumes")
![alt text](https://drive.google.com/uc?export=view&id=13RLFxIxqxqvuR-3ehkLOTeoS1N9oEwPg "Without Docker Volumes")
* Three Docker Volume types all can be execute using *Docker Run* command:
    1. Host Volumes
        ![alt text](https://drive.google.com/uc?export=view&id=1RGIEfWi4gAvCqZZlZl5FlEO-9t_cEG6I "3 Docker Volumes Types-Host Volumes")
        * Mount local machine/host path to container path.
        ```bash
        # local machine/host path: container path
        $ docker run -v /home/mount/data:/var/lib/mssql/data
        $
        $ docker run --mount source=/home/mount/data, target=/var/lib/mssql/data
        $
        ```
    2. Anonymous Volumes
        ![alt text](https://drive.google.com/uc?export=view&id=1ewDWpbzzi8mzoGNf3OVU5NR8uTs9X2hw "3 Docker Volumes Types-Anonymous Volumes")
        * Not specify a local machine/host path and let Docker automatically create the path at a local machine/host. 
        ```bash
        # container path
        $ docker run -v /var/lib/mssql/data
        $
        ```
    3. Named Volumes
        ![alt text](https://drive.google.com/uc?export=view&id=1CNNoQKfa8-HdUOkbsBQ39WdwyKOHH9Yy "3 Docker Volumes Types-Named Volumes")
        * Reference the volume by using the name. 
        ```bash
        # container path
        $ docker run -v name:/var/lib/mssql/data
        $
        ```
In Docker-Compose, the Volumes can be specified as an example below:
    ![alt text](https://drive.google.com/uc?export=view&id=1KDSE46HfjKmIHEmGIUjpCxMg3_aSacTC "Docker Volumes using Docker-Compose")
    ```yaml
    version: '3'
    services:
        mongodb:
            image:
            ports:
            volumes:
                - mongodb-data:/data/db
        mongo-express:
            image:
            ports:
    volumes:
        mongodb-data:
            driver: local     
    ```
* Use this command to check the path/mount point of the volumes.
    ```bash
    $ docker volume ls # get Volume Name
    $
    $ docker volume inspect {Volume Name}
    $
    ```
* Docker Volumes Locations
![alt text](https://drive.google.com/uc?export=view&id=1uVUEG3CLBA82NdrGF8bd1oGtxPeJ8zMV "Docker Volumes Locations")


[Back to top](#)
