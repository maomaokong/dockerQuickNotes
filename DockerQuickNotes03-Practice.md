# Index
1. [What is Docker](./DockerQuickNotes01.md)
2. [Docker Commands](./DockerQuickNotes02-Commands.md)
3. [Docker Practice](./DockerQuickNotes03-Practice.md)


# 3 Docker in Practice

## 3.1 Workflow with Docker / Docker in Practice
![alt text](https://drive.google.com/uc?export=view&id=1qDYQE6q2E4ArdKWaUq--0AFBSvpShEw2 "Using Docker In Practice")
1. Stage 1 - Development
    1. Developing a *JavaScript Application* on your local machine/environment.
    2. This *JavaScript Application* using *MongoDB* database. Instead of installing it on your local machine/environment, you can download the *MongoDB* docker image from the Docker Hub and run it on your local machine/environment.
    3. Connect the *JavaScript Application* with the *MongoDB Container* for development.
    4. For actual Docker example for developer, please refer [here](https://github.com/maomaokong/developing-with-docker-example).
2. Stage 2 - **Continuous Integration (CI)**
    1. First version of the *JavaScript Application* was developed on local machine/environment.
    2. For testing or deploy it on the *Development Environment* where testers in project team is able to test it.
        1. Developer will commit the *JavaScript Application* in *source code version control and repository system (Git or Bitbucket)*.
        2. Backend will trigger a **Continuous Integration (CI)**, example like *Jenkins* or others.
        3. Automated Build example like *Jenkins* will compile and create an *artifact/application program file*.
        4. Create a *Docker Image* from artifact/application program file.
        5. The *JavaScript Application Docker Image* is ready.
    3. The *JavaScript Application Docker Image* will pushed to a private *Docker Repository*.
3. Stage 3 - **Continous Delivery (CD)**
    1. The backend agent will pull *JavaScript Application Docker Image* from private *Docker Repository* and *MongoDB Docker Image* from public *Docker Hub* and deploy 2 containers using scripts on Development Server for testing.


### 3.1.1 How to package a Docker Container for Developed Application?
1. Dockerfile is a **blueprint** for building the Docker image. The file must save exactly naming and starting with Uppercase D and no extension, or else Docker Build will fail. Template for Dockerfile:
    ```Dockerfile
    FROM {image, always start by base it on the another image}
    ENV {container environment variables, optionally define environmental variables}
    RUN {execute any Linux command}
    WORKDIR {set container working directory}
    COPY {from source to destination, this command will executes on the host!}
    CMD {execute an entry point Linux command}
    ```
2. Dockerfile for packaging the Developed JavaScript with MongoDB, this Dockerfile is build according to the development folder structure below:
    ```
    .\
    |-src\
    |   |-images\
    |   |   |-.
    |   |-index.html
    |   |-server.js
    |-Dockerfile
    |-mongodb.yaml
    |-package-lock.json
    |-package.json
    |-README.md
    ```
    ```Dockerfile
    FROM node:13-alpine
    ENV MONGO_DB_USERNAME=admin \
        MONGO_DB_PWD=P@ssword
    RUN mkdir -p /home/app
    WORKDIR /home/app
    # copy from dev root folder to working directory
    COPY package.json .
    # install the dependencies
    RUN npm install
    # copy fom dev src folder to working directory
    COPY ./src/ .
    CMD ["node", "server.js"]
    ```
3. From Dockerfile above, the following steps will apply to the machine/environment after run it with Docker command:
    1. Install Node image from DockerHub.
    2. Set MONGO_DB_USERNAME Container Environment Variable.
    3. Set MONGO_DB_PASSWORD Container Environment Variable.
    4. Create /home/app folder in the Container.
    5. Copy all files from current folder to Container folder /home/app.
    6. Start the JavaScript application with Linux command `$ node server.js`
4. What is different between *RUN* and *CMD* command? The Dockerfile can have multiple *RUN* command but only 1 *CMD* command. *CMD* is serve as execute an entry point for Docker Container.
5. Start building Docker image with Dockerfile:
    * Docker Build syntax needs 2 parameters, tag the image with *application name* and the location of the Dockerfile.
    ```bash
    $ docker build --tag my-app:1.0 --file Dockerfile . # Build the image with application name and version from local directory with Dockerfile in root folder.
    $
    $ docker build -t my-app:1.0 -f Dockerfile .
    $
    ```
6. Test the new built Docker image.
    ```bash
    $ docker run my-app:1.0
    $
    $ docker ps # getting container ID for JavaScript Application.
    $
    $ docker logs {123456789} # checking logs for JavaScript Application, 123456789 is the Container ID.
    $
    $ docker exec -it {123456789} /bin/sh # can access the container using `docker exec` command, if `/bin.sh` not working then switch to `/bin/bash`. 123456789 is the Container ID.
    / ~ env # checking the container env
    ```
7. When Dockerfile was amended, *MUST* rebuild the Docker Image.
    ```bash
    $ docker build -t my-app:1.1 . # Building new image with newest version number.
    $
    $ docker image ls # Checking the Docker image list
    $
    $ docker run my-app:1.0 # test with Docker image again
    $
    $ # If want to delete the oldest image, refer below:
    $ docker image rm {123456789} # remove the image before rebuild, 123456789 is the Image ID.
    $
    $ # if not able to delete the image, need to manually delete the existing container.
    $ docker ps -a # looking for container that running the image.
    $
    $ docker rm 123456789 # remove the container, 123456789 is the Container ID.
    $
    $ docker image rm {123456789} # remove the image again
    $
    ```


### 3.2.2 Create a Private Docker Registry with AWS Elastic Container Registry(ECR)
1. In this example, a Private Docker Registry will be create in [AWS ECR](https://aws.amazon.com/ecr/). Other Private Docker Registry option also available like [DigitalOcean](https://www.digitalocean.com/products/container-registry/) and [Nexus](https://www.sonatype.com/nexus/repository-oss).
2. AWS ECR will create repository per image. Same image will be save different tags or version under the same repository.
3. Refer this AWS article [Getting started with Amazon ECR using the AWS CLI](https://docs.aws.amazon.com/AmazonECR/latest/userguide/getting-started-cli.html)
4. Using Docker Container to host the AWS CLI Terminal, refer this [AWS article](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2-docker.html).
    ```bash
    $ docker run \
    > --rm \ # Automatically remove the container when it exits
    > --interactive \ # Keep STDIN open even if not attached
    > --tty \ # Allocate a pseudo-TTY
    > amazon/aws-cli:latest \ # Docker Image
    > --version # command after container run
    $
    $ docker run -it --rm amazon/aws-cli:latest --version
    ```
5. Create an *Access Key* for IAM user to push the Docker Image from local machine/environment to AWS ECR.
    1. Log into AWS Console using (a)Root account or (b)Admin IAM account
    2. => Access "My Security Credentials"
    3. => Under "Access Management"
    4. => Under "Users", select an "User name" for Access Key.
        * Example select user "AFM1", make sure this user has the permission to access ECR.
    5. => Under "Security Credentials"
    6. => Under "Access Key", press "Create access key"
        * Remember to copy down the "Access Key ID" and "Secret Access Key". Once close the window no longer able to retrieve the "Secret Access Key" anymore.
6. Create AWS Configuration and Credential at local machine/environment using AWS-CLI docker image
    ```bash
    $ docker run \
    > --rm \ # Automatically remove the container when it exits
    > --interactive \ # Keep STDIN open even if not attached
    > --tty \ # Allocate a pseudo-TTY
    > --volume ~/.aws:/root/.aws \ # Bind mount a volume, local path:container path
    > amazon/aws-cli:latest \ # Docker Image
    > --version # command after container run
    ## or a shorter format
    $ docker run -it --rm --volume ~/.aws:/root/.aws amazon/aws-cli:latest configure
    ```
7. After entered the necessary credential and configuration setting, the files will created under local machine hidden folder (~/.aws/). 
8. Retrieve a AWS authentication token and authenticate Docker client to AWS ECR registry.
    ```bash
    $ docker run \
    > --rm \ # Automatically remove the container when it exits
    > --interactive \ # Keep STDIN open even if not attached
    > --tty \ # Allocate a pseudo-TTY
    > --volume ~/.aws:/root/.aws \ # Bind mount a volume, local path:container path
    > amazon/aws-cli:latest \ # Docker Image
    > ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/w4j0e6a3 # command after container run
    $
    $ docker run -it --rm --volume ~/.aws:/root/.aws amazon/aws-cli:latest ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/w4j0e6a3
    ```
9. Tag a new built Docker Image so can push the Docker Image to AWS ECR properly. (This command will copy the existing image and rename a new image name)
    ```bash
    $ docker image tag \
    > my-app:1.0 \ # existing image name
    > public.ecr.aws/w4j0e6a3/my-app:1.0 # rename to new image name
    $
    $ docker image tag my-app:1.0 public.ecr.aws/w4j0e6a3/my-app:1.0
    ```
10. Push the Docker image from local machine to AWS ECR.
    ```bash
    $ docker push \ # Push an image or a repository to a registry
    > public.ecr.aws/w4j0e6a3/my-app:1.0
    $
    $ docker push public.ecr.aws/w4j0e6a3/my-app:1.0
    $
    ```

## 3.3 Automate *Docker Run* with **Docker Compose**
1. Comparison between *Docker Run* and *Docker Compose* to start the **MongoDB** Container.
    1. Docker Run command
        ```bash
        $ docker run \
        > -d \
        > --name mongodb \ # assign a name for this container
        > --network mongo-network \ # connect this container to the Docker Network
        > -p 27017:27017 \  # MongoDB default port {Localhost:Container}
        > -e MONGO_INITDB_ROOT_USERNAME=admin \ # Set Environment Variable
        > -e MONGO_INITDB_ROOT_PASSWORD=P@ssw0rd \ # Set Environment Variable
        > mongo # image name from $ docker image ls
        $
        ```
    2. Docker Compose file
        ```yaml
        version: '3'
        services:
            mongo:
                image: mongo
                ports:
                    - 27017:27017
                environment:
                    - MONGO_INITDB_ROOT_USERNAME=admin
                    - MONGO_INITDB_ROOT_PASSWORD=P@ssw0rd
        ```
        * **mongodb** after **services** tag is name for the container.
2. Comparison between *Docker Run* and *Docker Compose* to start the **Mongo-Express** Container.
    1. Docker Run command
        ```bash
        $ docker run \
        > -d \
        > --name mongo-express \ # assign a name for this container
        > --network mongo-network \ # connect this container to the Docker Network
        > -p 8081:8081 \ # Mongo-Express default port {Localhost:Container}
        > -e ME_CONFIG_MONGODB_SERVER=mongodb \ # this is MongoDB container name
        > -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin \ # this is MongoDB Admin username
        > -e ME_CONFIG_MONGODB_ADMINPASSWORD=P@ssw0rd \ # this is MongoDB Admin password
        > mongo-express # image name from $ docker image ls
        $
        ```
    2. Docker Compose file
        ```yaml
        version: '3'
        services:
            mongo-express:
                image: mongo-express
                ports:
                    - 8081:8081
                environment:
                    - ME_CONFIG_MONGODB_SERVER=mongodb
                    - ME_CONFIG_MONGODB_ADMINUSERNAME=admin
                    - ME_CONFIG_MONGODB_ADMINPASSWORD=P@ssw0rd
        ```
        * **mongo-express** after **services** tag is name for the container.
3. **Docker Compose** allows multiple containers run on one command instead of multiple **Docker Run** commands. However the **Docker Compose** yaml file must include these containers. Example as follows to run on the new environment that pull the images from *Docker Hub* and own private "Container Images Repository":
    ```yaml
    version: '3'
    services:
        my-app:
            image: public.ecr.aws/w4j0e6a3/my-app:1.0 # this is from AWS ECR Repository
            ports:
                - 3000:3000
            restart: always
            depends_on:
                - mongodb
        mongodb:
            image: mongo
            ports:
                - 27017:27017
            environment:
                - MONGO_INITDB_ROOT_USERNAME=admin
                - MONGO_INITDB_ROOT_PASSWORD=P@ssw0rd
            restart: always
            volumes:
                - mongodb-data:/data/db
        mongo-express:
            image: mongo-express
            ports:
                - 8081:8081
            environment:
                - ME_CONFIG_MONGODB_SERVER=mongodb
                - ME_CONFIG_MONGODB_ADMINUSERNAME=admin
                - ME_CONFIG_MONGODB_ADMINPASSWORD=P@ssw0rd
            restart: always
            depends_on:
                - mongodb
    volumes:
        mongodb-data:
            driver: local            
    ```
    * **Docker Compose** will takes care of creating a common Network for container under the same yaml file by default.
4. Authenticate Docker client to the AWS ECR public registry. Authentication tokens are valid for 12 hours. Refer to this [AWS article](https://docs.aws.amazon.com/AmazonECR/latest/public/public-registries.html#public-registry-auth).
    ```bash
    $ docker run \
    > --rm \ # Automatically remove the container when it exits
    > --interactive \ # Keep STDIN open even if not attached
    > --tty \ # Allocate a pseudo-TTY
    > --volume ~/.aws:/root/.aws \ # Bind mount a volume, local path:container path
    > amazon/aws-cli:latest \ # Docker Image
    > ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/w4j0e6a3 # command after container run
    $
    $ docker run -it --rm --volume ~/.aws:/root/.aws amazon/aws-cli:latest ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws
    ```
    * *On 8 December 2020, managed to login successfully with region us-east-1 but failed using region ap-southeast-1*
5. Execute **Docker Compose up** as follows to start the containers:
    ```bash
    $ docker-compose \
    > --project-name my-app \ # Specify an alternate project name
    > --file mongodb.yaml \ # Specify an alternate compose file
    > up # Create and start containers
    $
    $ docker-compose -p my-app -f mongodb.yaml up
    $
    ```
5. Execute **Docker Compose Down** as follow to stop the containers:
    ```bash
    $ docker-compose \
    > --project-name my-app \ # Specify an alternate project name
    > --file mongodb.yaml \ # Specify an alternate compose file
    > down # Stop containers
    $
    $ docker-compose -p my-app -f mongodb.yaml down # Stop containers
    $
    ```


[Back to top](#)
