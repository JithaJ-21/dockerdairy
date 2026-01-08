### Step 1: Verify Node.js Application Locally
* Command: ***node app.js***
* Purpose:
1) Run the Node.js application locally to ensure it is working.
2) Checked that routes, views, and static files were functioning properly.
* Observation:
1) Application ran successfully at http://localhost:3000.

------

### Step 2: Verify Docker Installation
* Command: ***docker --version***
* Purpose:
1) Verify that Docker is installed and running on the system.
* Observation:
1) Docker CLI responded with the installed version (e.g., Docker version 28.4.0).

----

### Step 3: Create Dockerfile for Backend

File: Dockerfile inside project folder

Content:

    FROM node:20
    WORKDIR /app
    COPY package*.json ./
    RUN npm install
    COPY . .
    EXPOSE 3000
    CMD ["node", "app.js"]

* Purpose:
1) Containerize the Node.js backend application.
2) Include dependencies and set up the working directory.
* Observation:
1) Dockerfile is ready to build the backend image.

-----

### Step 4: Build Backend Docker Image
* Command: ***docker build -t resource-library .***
* Purpose:
1) Build the Docker image named resource-library from the Dockerfile.
* Observation:
1) Image was built successfully.

-----

### Step 5: List All Containers
* Command: ***docker ps*** lists only the running containers on the system; ***docker ps -a*** lists all containers (whether running, stopped or exited)
* Purpose:
1) See all existing containers (running or stopped).
2) Identify any leftover containers from previous runs.
* Observation:
1) Multiple old containers were present (old backend etc.).

-----

### Step 6: Stop and Remove Unwanted Containers
* Commands:
***docker stop <container_name_or_id>***
  
***docker rm <container_name_or_id>***

Or forcibly remove: ***docker rm -f <container_name_or_id>***

* Purpose:
1) Clean up old containers to avoid conflicts with names and ports.
* Observation:
1) Only relevant containers (backend, mongo-db) remain.

------

### Step 7: Install MongoDB Package (Optional for Node.js app)
* Command: ***npm install mongodb***
* Purpose:
1) Ensure Node.js backend can connect to MongoDB if database operations are required.
* Observation:
1) mongodb dependency installed successfully.

-----

### Step 8: Pull MongoDB Docker Image
* Command: ***docker pull mongo***
* Purpose:
1) Download the official MongoDB image from Docker Hub.
* Observation:
1) MongoDB image downloaded successfully.

------

### Step 9: Create Docker Volume for Database
* Command: ***docker volume create dbdata***
* Purpose:
1) Persist MongoDB data in Docker volume so data is not lost if container is removed.
* Observation:
1) Volume dbdata created successfully.

-----

### Step 10: Create a Custom Docker Network
* Command: ***docker network create myapp-network***
* Purpose:
1) Allow backend and database containers to communicate using container names.
* Observation:
1) Network myapp-network created successfully.

-----

### Step 11: Run Backend Container
* Command: ***docker run -d --name backend --network myapp-network -p 3000:3000 resource-library***
* Purpose:
1) Run the backend Node.js app in a container.
2) Map container port 3000 to host port 3000.
* Observation:
1) Backend container running successfully.

-----

### Step 12: Run MongoDB Container
* Command: ***docker run -d --name mongo-db --network myapp-network -v dbdata:/data/db mongo***
* Purpose:
1) Run MongoDB container connected to the same network.
2) Mount dbdata volume for data persistence.
* Observation:
1) MongoDB container running successfully.

-----

### Step 13: Verify Docker Network
* Commands:

***docker network ls***

***docker network inspect myapp-network***

* Purpose:
1) Confirm both containers (backend, mongo-db) are connected to the network.
* Observation:
1) Both containers appear under Containers in network inspect output.

-----

### Step 14: Verify Docker Volume
* Commands:

***docker volume ls***

***docker volume inspect dbdata***

* Purpose:
1) Check that the MongoDB volume exists and is correctly mounted.
* Observation:
1) Volume dbdata confirmed for MongoDB persistent storage.

-------

### Step 15: Check Container Logs
* Commands:

***docker logs backend***

***docker logs mongo-db***

* Purpose:
1) Ensure containers started without errors.
2) Debug if any issues occurred.
* Observation:
1) Backend logs showed Node.js server running.
2) MongoDB logs showed database started successfully.

--------

### Step 16: Access Application
* Open browser: http://localhost:3000
* Tested functionality: Browse resources; Add new resources; View favorites...

-----

### Task Outcome
* Backend Node.js app containerized successfully.
* MongoDB database containerized and attached to same network.
* Learned about: Docker images and containers; Bridge networking; Persistent volumes for database storage; Verified app runs and persists data inside Docker.
