# Week 1 — App Containerization
## Gitpod and Codespaces Catch-up
I had to first understand how to use Gitpod and Codespaces.
## Containerize the frontend and backend applications using Docker
I was able to use Docker to containerize the backend application first which I used the below script:
```

FROM python:3.10-slim-buster

# Inside Container
# make a new folder inside container
WORKDIR /backend-flask

# Outside Container -> Inside Container
# this contains the libraries want to install to run the app
COPY requirements.txt requirements.txt

# Inside Container
# Install the python libraries used for the app
RUN pip3 install -r requirements.txt

# Outside Container -> Inside Container
# . means everything in the current directory
# first period . - /backend-flask (outside container)
# second period . /backend-flask (inside container)
COPY . .

# Set Enviroment Variables (Env Vars)
# Inside Container and wil remain set when the container is running
ENV FLASK_ENV=development

EXPOSE ${PORT}

# CMD (Command)
# python3 -m flask run --host=0.0.0.0 --port=4567
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]
```

I built the docker container with the below command:
```
docker build -t  backend-flask ./backend-flask
```
## To run the Docker Container, I ran this command:
```
docker run --rm -p 4567:4567 -it -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flask
```
## To containerize the Frontend, I cd into the frontend directory and inputed this command to install the node modules for the frontend application
```
npm install
```
Then, I created the docker-compose.yml file in the root directory
```
version: "3.8"
services:
  backend-flask:
    environment:
      FRONTEND_URL: "https://3000-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
      BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./backend-flask
    ports:
      - "4567:4567"
    volumes:
      - ./backend-flask:/backend-flask
  frontend-react-js:
    environment:
      REACT_APP_BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./frontend-react-js
    ports:
      - "3000:3000"
    volumes:
      - ./frontend-react-js:/frontend-react-js

# the name flag is a hack to change the default prepend folder
# name when outputting the image names
networks: 
  internal-network:
    driver: bridge
    name: cruddur
```
This helped to link the frontend and backend containers together and linked their ports.

![Screenshot of frontend and backend containers working.](https://github.com/ChinweIjy1/aws-bootcamp-cruddur-2023/blob/main/journal/assets/All%20contaners%20working.PNG)

![Screenshot of frontend and backend containers working.](https://github.com/ChinweIjy1/aws-bootcamp-cruddur-2023/blob/main/journal/assets/Frontend%20and%20backend%20connected.PNG)
# Create a Notification Feature and Page
I created a new API endpoint and a new component for notification API endpoint and its page which I addded to the routing

![Screenshot of Notification feature added to the API endpoint.](https://github.com/ChinweIjy1/aws-bootcamp-cruddur-2023/blob/main/journal/assets/Notification%20page%20created.PNG)

# Installed dynamodb and postgres for database

I installed the below script inside the docker-compose.yml file to install postgres and dynamodb

```
version: "3.8"
services:
  backend-flask:
    environment:
      FRONTEND_URL: "https://3000-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
      BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./backend-flask
    ports:
      - "4567:4567"
    volumes:
      - ./backend-flask:/backend-flask
  frontend-react-js:
    environment:
      REACT_APP_BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./frontend-react-js
    ports:
      - "3000:3000"
    volumes:
      - ./frontend-react-js:/frontend-react-js
  dynamodb-local:
    # https://stackoverflow.com/questions/67533058/persist-local-dynamodb-data-in-volumes-lack-permission-unable-to-open-databa
    # We needed to add user:root to get this working.
    user: root
    command: "-jar DynamoDBLocal.jar -sharedDb -dbPath ./data"
    image: "amazon/dynamodb-local:latest"
    container_name: dynamodb-local
    ports:
      - "8000:8000"
    volumes:
      - "./docker/dynamodb:/home/dynamodblocal/data"
    working_dir: /home/dynamodblocal
  db:
    image: postgres:13-alpine
    restart: always
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    ports:
      - '5432:5432'
    volumes: 
      - db:/var/lib/postgresql/data

# the name flag is a hack to change the default prepend folder
# name when outputting the image names
networks: 
  internal-network:
    driver: bridge
    name: cruddur

volumes:
  db:
    driver: local
```

I had to remove all indentation which disturbed me alot before all containers got connected
![Screenshot of all containers working when I docker compose up](https://github.com/ChinweIjy1/aws-bootcamp-cruddur-2023/blob/main/journal/assets/FBPD%20connected.PNG)

I was able to set up Postgres and DynamoDB

![Screenshot of Postgres setup inside the Database Explorer](https://github.com/ChinweIjy1/aws-bootcamp-cruddur-2023/blob/main/journal/assets/add%20database%20explorer.PNG)

# 10 BEST SECURITY PRACTICES FOR DOCKER CONTAINERS
I read about the 10 best Security Practice for Containers which are as follows:
1. One should keep their Host and Docker updated to the latest security practices
2. Docker Daemon and Containers should be run in the non-root user mode which means it should be run by a User and not with the Root account so that the root user would not be compromised.
3. There should be image vunerability scanning which means the container should have applications and images that is required.
4.  
