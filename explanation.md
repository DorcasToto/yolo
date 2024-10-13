
# Project EXPLANATION


## Choice of Base Image

For this project, we use the following base images:

- **Node.js 16 (Alpine)**: Chosen for both the frontend and backend to ensure a lightweight, secure, and efficient environment for running Node.js applications. The Alpine variant minimizes the size of the image, leading to faster builds and less disk space usage.

- **Nginx (Alpine)**: Used as a lightweight web server for serving the built frontend application. Nginx is known for its performance and efficiency in serving static files.

- **MongoDB 4.4**: A widely used NoSQL database, chosen for its scalability and flexibility in handling unstructured data.

## Dockerfile Directives

### Client Dockerfile

The client Dockerfile is responsible for building the React frontend application. Here’s a breakdown of its components:

```dockerfile
FROM node:16-alpine AS build

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

RUN npm run build

FROM nginx:alpine

COPY --from=build /app/build /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

#### Explanation of Directives:

- **FROM node:16-alpine AS build**: This line specifies the base image for the build stage. By using Node.js 16 on an Alpine base, we keep the image lightweight while providing a suitable environment for building our React application.

- **WORKDIR /app**: Sets the working directory inside the container to `/app`. All subsequent commands will be run in this directory.

- **COPY package*.json ./**: Copies the `package.json` and `package-lock.json` files to the container. This step is essential for installing the application dependencies specified in these files.

- **RUN npm install**: Executes the command to install the dependencies defined in `package.json`. This step is crucial to ensure all necessary packages are available for the application to run.

- **COPY . .**: Copies the rest of the application files from the local machine to the container. This includes all source code and assets needed for the React application.

- **RUN npm run build**: Builds the React application for production. This command generates static files optimized for deployment, which are stored in the `/app/build` directory.

- **FROM nginx:alpine**: Starts a new stage in the Dockerfile, using Nginx as the base image. This layer is responsible for serving the built application.

- **COPY --from=build /app/build /usr/share/nginx/html**: This command copies the built static files from the previous build stage into Nginx's default serving directory. This enables Nginx to serve the frontend application when it runs.

- **EXPOSE 80**: Informs Docker that the container will listen on port 80 at runtime, allowing access to the web application.

- **CMD ["nginx", "-g", "daemon off;"]**: Starts Nginx in the foreground, which is necessary for the container to keep running.

### Backend Dockerfile

The backend Dockerfile is responsible for running the Node.js Express application. Here’s a detailed breakdown:

```dockerfile
FROM node:16-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 5000

CMD ["npm", "start"]
```

#### Explanation of Directives:

- **FROM node:16-alpine**: Specifies the base image for the backend container, utilizing the same lightweight Node.js environment as the client to maintain consistency.

- **WORKDIR /app**: Sets the working directory to `/app`, similar to the frontend Dockerfile, ensuring that all commands are executed in the same context.

- **COPY package*.json ./**: Copies the `package.json` and `package-lock.json` files, allowing for dependency installation.

- **RUN npm install**: Installs all the necessary dependencies listed in `package.json`, ensuring the application has everything it needs to run.

- **COPY . .**: Copies the remaining application files from the local directory to the container. This includes all the server code and configuration files.

- **EXPOSE 5000**: Informs Docker that the backend application will be accessible through port 5000.

- **CMD ["npm", "start"]**: Executes the command to start the Express server, launching the backend application.

