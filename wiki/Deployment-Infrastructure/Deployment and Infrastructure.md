<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [.env.example](https://github.com/aanickode/access-control-service/blob/main/.env.example)
- [package.json](https://github.com/aanickode/access-control-service/blob/main/package.json)
</details>

# Deployment and Infrastructure

## Introduction

The "Deployment and Infrastructure" aspect of this project focuses on the configuration and setup required to run the Access Control Service application. This service is built using Node.js and the Express.js framework, and it relies on environment variables for configuring the server port. The deployment process involves installing the necessary dependencies and running the application using the provided scripts.
Sources: [package.json](https://github.com/aanickode/access-control-service/blob/main/package.json), [.env.example](https://github.com/aanickode/access-control-service/blob/main/.env.example)

## Environment Configuration

The application uses the `dotenv` package to load environment variables from a `.env` file. The `.env.example` file serves as a template for the actual `.env` file, which should be created during the deployment process.

### Environment Variables

The following environment variable is defined in the `.env.example` file:

| Variable | Description                      | Default Value |
|----------|----------------------------------|---------------|
| `PORT`   | The port number for the server   | `8080`        |

Sources: [.env.example](https://github.com/aanickode/access-control-service/blob/main/.env.example)

## Application Dependencies

The project's dependencies are listed in the `package.json` file, which is used by the Node.js package manager (npm) to install the required packages.

```json
"dependencies": {
  "dotenv": "^16.0.3",
  "express": "^4.18.2"
}
```

- `dotenv`: This package is used for loading environment variables from the `.env` file.
- `express`: This is the web application framework used for building the Access Control Service.

Sources: [package.json](https://github.com/aanickode/access-control-service/blob/main/package.json)

## Application Startup

The `package.json` file defines a script for starting the application:

```json
"scripts": {
  "start": "node src/index.js"
}
```

To start the application, run the following command:

```
npm start
```

This command will execute the `node src/index.js` script, which is responsible for initializing the Express.js server and starting the application.

Sources: [package.json](https://github.com/aanickode/access-control-service/blob/main/package.json)

## Deployment Process

The deployment process for the Access Control Service involves the following steps:

1. Clone the project repository or obtain the source code.
2. Create a `.env` file based on the `.env.example` template and configure the desired environment variables, such as the server port.
3. Install the project dependencies by running `npm install`.
4. Start the application using the `npm start` command.

After completing these steps, the Access Control Service will be running and accessible on the configured port.

Sources: [package.json](https://github.com/aanickode/access-control-service/blob/main/package.json), [.env.example](https://github.com/aanickode/access-control-service/blob/main/.env.example)