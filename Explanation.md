## 1. Choice of Base Image
Container is build using `node:16-alpine3.16` image which is based on the Alpine Linux distribution beacuse it is lightweight. It includes Version 16 of Node.js, which is higher than what the application need.

## 2. Dockerfile Directives
### _Client_
The client docker file implemeted using using multi-stage builds. This is neccessary as it help achieve:
To achieve smaller and efficient images, the client Dockerfile is implemented using multi-stage builds which separats the build dependencies from the runtime dependencies.

##### Build Stage
`WORKDIR` directive sets the working directory for the container to /client.

Then, `COPY` directive copies the package.json and package-lock.json files to the /client directory which are necessary inorder to install application dependencies.

Then,  `RUN` directive is used to execute the a series of commands;
`npm install --only=production` to installs only the production dependencies
`npm cache clean --force` clears the npm cache and
`rm -rf /tmp/*` removes any temporary files

The `COPY` directive is the used to copy the rest of the app code to the working directory.

Lastly in this stage `RUN` directive  helps execute `npm run build` that builds the application by running and `npm prune --production` which removes development dependencies.

#####   Production Stage
`WORKDIR` directive sets the working directory for the container to /client.

Then the `COPY` directive copies only build directory, public directory, src directory, and all the package files which are necessary files from the build stage to the production stage.

The `ENV` directive is used to sets the NODE_ENV environment variable to production.

The `EXPOSE` directive sets the port 3000 to be exposed by the container when it is running.

The `RUN` directive is used to execute the following commands;
`npm prune --production` to remove development dependencies
`npm cache clean --force` clears the npm cache and
`rm -rf /tmp/*` removes any temporary files

`CMD` directive specifies the default command to run when a container is started and in this case `npm start`.

### _Backend_
The backend Dockerfile does not implement multi build

`WORKDIR` directive sets the working directory for the container to /backend.

Then, `COPY` directive copies the package.json and package-lock.json files to the /client directory which are necessary inorder to install application dependencies.

Then,  `RUN` directive is used to execute the a series of commands;
`npm install --only=production` to installs only the production dependencies
`npm cache clean --force` clears the npm cache and
`rm -rf /tmp/*` removes any temporary files

The `COPY` directive is then used to copy the rest of the application code into the working directory.

The `ENV` directive is used to sets the NODE_ENV environment variable to production.

The `EXPOSE` directive sets the port 5000 to be exposed by the container when it is running.

The `RUN` directive is used to execute the following commands;
`npm prune --production` to remove development dependencies
`npm cache clean --force` clears the npm cache and
`rm -rf /tmp/*` removes any temporary files

`CMD` directive specifies the default command to run when a container is started and in this case is `npm start`.

## 3. Docker-compose Networking (Application port allocation and a bridge network implementation) where necessary:
Two networks are defined: `frontend-net` and `backend-net`.

To configure the IP addressing for the containers in each network `ipam` (IP Address Management option) is used.

For containers to communicate with one another over the network using their unique IP addresses and helps prevent IP address conflicts ;
- The `frontend-net` network, uses the subnet `172.51.0.0/16` and `backend-net`  network uses the subnet `172.100.0.0/16`.
- The client container is given an IP of `172.51.0.101` in this `frontend-net` network.
- The backend container is given an IP of `172.100.0.100` on The `backend-net`, and `172.51.0.100` on the `frontend-net` network.
- The database container is given the IP address `172.100.0.101` on the backend-net network.

Ports `3000` of the client container, `5000` of the backend container and `27017` the database container, are mapped to the host machine's ports `3000`, `5000` and `27017`.

## 4. Docker-compose volume
A backend_voulme is been defined. This is used to persist data in the db. The volume is mounted using the syntax backend_volume:/data/db in the database service to the container's /data/db directory

## 5. Git workflow used to achieve the task
Dockerfile and also the yml file were version-controlled using Git alongside other project files, and changes are be committed and pushed to a Git repository.

