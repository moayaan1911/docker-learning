<!-- @format -->

# React-Docker

This Dockerfile sets up a Docker container for a React application with a focus on security by running the app as a non-root user.

## Docker Commands

### `FROM node:20-alpine`

- 📦 Establishes the base image for the container, using a lightweight version of Node.js (version 20) based on Alpine Linux.
- 🚀 Alpine Linux is chosen for its small size and reduced attack surface, which is ideal for creating secure and efficient container images.

### `RUN addgroup app && adduser -S -G app app`

- 👤 Creates a new group named `app` and a system user `app` that is added to the `app` group.
- 🔒 This step is crucial for security; it ensures that the application does not run with root privileges, which can prevent potential exploitation of the host system.

### `USER app`

- 🔑 Sets the user context to the `app` user for all subsequent commands.
- 🛡️ This command is used to enforce the principle of least privilege, ensuring that the app runs with only the permissions it needs.

### `WORKDIR /app`

- 📂 Specifies `/app` as the working directory for any RUN, CMD, ENTRYPOINT, COPY, and ADD instructions that follow in the Dockerfile.
- 🏠 This command sets the base directory for the application, making it the default location for all subsequent actions.

### `COPY package*.json ./`

- 📋 Copies both `package.json` and `package-lock.json` (if available) to the working directory in the container.
- 🔄 By copying these files separately from the rest of the code, Docker can cache installed dependencies unless these files change, which speeds up the build process.

### `USER root`

- 🔓 Temporarily switches the user context back to `root` to perform actions that require elevated privileges.
- 🛠️ This is necessary to change ownership of files and directories within the container that the `app` user will need to access.

### `RUN chown -R app:app .`

- 📑 Changes the ownership of all files and directories in the current working directory (`/app`) to the `app` user and group.
- 🛡️ Ensures that the `app` user has the correct permissions to access and modify the files, avoiding potential permission errors.

### `USER app`

- 🔑 Switches the user context back to the `app` user after performing the necessary root-level operations.
- 🛡️ Reinforces the security practice of running the application as a non-root user.

### `RUN npm install`

- 📦 Installs the Node.js dependencies defined in `package.json` within the container.
- 🚀 Ensures that your application has all the necessary libraries and modules to run correctly.

### `COPY . .`

- 🔄 Copies the rest of the application source code into the working directory of the container.
- 📈 This step is performed after installing dependencies to ensure that source code changes do not invalidate the Docker cache for the dependency installation layer.

### `EXPOSE 5173`

- 🌐 Informs Docker that the container listens on port 5173 at runtime.
- 📡 This is the default port for Vite development server, which is commonly used in React applications.

### `CMD npm run dev`

- 🚀 Specifies the default command to run when the container starts, which in this case is the development server for the React application.
- 🛠️ This command can be overridden when the container is run, allowing for flexibility in how the container is used.

## Docker Run Command for React-Docker

`docker run -p 5173:5173 -v "$(pwd):/app" -v /app/node_modules react-docker`

### Explanation of the Command

- 🚀 **`docker run`**: Starts a new container from the `react-docker` image.

- 🌍 **`-p 5173:5173`**:

  - Maps port 5173 on the host to port 5173 on the container.
  - Necessary for accessing the app from a browser on the host machine.

- 📁 **`-v "$(pwd):/app"`**:

  - Mounts the current directory from the host to `/app` in the container.
  - Allows for real-time reflection of code changes in the container.

- 📦 **`-v /app/node_modules`**:

  - Creates a persistent volume for `node_modules`.
  - Ensures that the dependencies remain intact and are not overwritten by the host's volume.

- 🖼️ **`react-docker`**:
  - Specifies the image to use for creating the container.
  - The image should contain the necessary environment to run a React application.
