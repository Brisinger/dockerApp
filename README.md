## demo app - developing with Docker

This demo app shows a simple user profile app set up using 
- index.html with pure js and css styles
- nodejs backend with express module
- mongodb for data storage
- build a docker image for node.js app from docker-compose.
- publish the app image in private docker repository.

All components are docker-based

### With Docker

#### To start the application

Step 1: Create docker network

    docker network create mongo-network 

Step 2: start mongodb 

    docker run -d -p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=password --name mongodb --net mongo-network mongo    

Step 3: start mongo-express
    
    docker run -d -p 8081:8081 -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin -e ME_CONFIG_MONGODB_ADMINPASSWORD=password --net mongo-network --name mongo-express -e ME_CONFIG_MONGODB_SERVER=mongodb mongo-express   

_NOTE: creating docker-network in optional. You can start both containers in a default network. In this case, just emit `--net` flag in `docker run` command_

Step 4: open mongo-express from browser

    http://localhost:8081

Step 5: create `user-account` _db_ and `users` _collection_ in mongo-express

Step 6: Start your nodejs application locally - go to `app` directory of project 

    npm install 
    node server.js
    
Step 7: Access you nodejs application UI from browser

    http://localhost:3000

### With Docker Compose

#### To start the application

Step 1: start mongodb mongo-express and build docker image from application.

    docker-compose -f docker-compose.yaml up
    
_You can access the mongo-express under localhost:8080 from your browser_
    
Step 2: in mongo-express UI - create a new database "my-db"

Step 3: in mongo-express UI - create a new collection "users" in the database "my-db"       
    
Step 4: start node server inside the container instance of my-app:1.0.0

Step 5: Enter into container host shell running my-app:1.0.0 application.
    
    docker exec -it my-app:1.0.0 /bin/sh

    _NOTE: this will open the shell in cointainer with working directory "home/app"
    executing the linux and node commands in current working directory. This is optional.

    npm install
    node server.js

    _this would already be taken care of by docker file built from docker-compose in step 1.
    _fastest way is from step 1 to step 3 and then execute step 5.
    
Step 5: access the nodejs application from browser 

    http://localhost:3000

#### To build a docker image from the application instead of using docker-compose.

    docker build -t my-app:1.0.0 .       

_Note: Any changes made to application requires removing the my-app:1.0.0 image and executing step 1, this ensures rebuilding current application code image. Running containers and building image from application in docker-compose reuses any existing images of application with changes made to application code not being translated into application image.

The dot "." at the end of the command denotes location of the Dockerfile in current working directory.

### From docker image pused to AWS Elastic Container Registry (ECR)
### Start the Application

Step 1: Set up an AWS account with access key configured.

_You can configure access key for a user from IAM in AWS.

Step 2: Under services search from the service ECR to manage container deployment.

Step 3: Create a private container repository in ECR with name "my-app" same as your application.

_In AWS each repository can have multiple images of the same application.

Step 4: Once repository is create go to link Amazon ECR --> Repositories and select "my-app" repository.

Step 5: Select View Push Commands menu which is enable after selecting the repository.

Step 6: Follow the steps in Modal that opens for Push command for "my-app" repository.

Step 7: In ECR use the Domain name of Container Registry and set the environment variable DOCKER_REGISTRY.

    _Setting environment variable in local maching for linus and mac similar to windows.
    Run the following commands in terminal.    
        
    export DOCKER_REGISTRY=<Container-Registry-URL>
    
    Add this in shell configuration file e.g. ~/.zshrc or ~/.bashrc, ~/.bash_profile etc.
    Replace <Container-Registry-URL> with the actual Domain URL or name of your Docker registry.
    Save the shell configuration file and restart the shell in terminal

    source ~/.zshrc

    _Now, the "DOCKER_REGISTRY" environment variable will be available in all new terminal automatically loaded from your shell configuration files e.g. ~/.zshrc or ~/.bashrc, ~/.bash_profile etc.

Step 8: Use the environment variable DOCKER_REGISTRY in your shell or a configuration file to store the registry URL, and then use that variable in your docker-compose.yml for pulling images for services from AWS ECR under services for image as '${DOCKER_REGISTRY}/my-app:1.0.0'.

Step 9: Pull the image for your application from AWS ECR and start all your containers from docker-compose.yaml file.

    docker-compose -f docker-compose.yaml up

_You can access the mongo-express under localhost:8080 from your browser_
    
Step 10: in mongo-express UI - create a new database "my-db"

Step 11: in mongo-express UI - create a new collection "users" in the database "my-db"       
    
Step 12: node server inside the container instance of my-app:1.0.0 will be running on port 3000. Access the nodejs application from browser 

    http://localhost:3000

Happy Coding!