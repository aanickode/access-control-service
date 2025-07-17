<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [.env.example](https://github.com/aanickode/access-control-service/blob/main/.env.example)
- [package.json](https://github.com/aanickode/access-control-service/blob/main/package.json)
</details>

# Deployment and Infrastructure

## Introduction

The "Deployment and Infrastructure" section covers the setup and configuration required to run the Access Control Service application. This service is built using Node.js and the Express.js framework, and it relies on environment variables for configuring the server port.
Sources: [package.json](https://github.com/aanickode/access-control-service/blob/main/package.json), [.env.example](https://github.com/aanickode/access-control-service/blob/main/.env.example)

## Server Configuration

### Environment Variables

The application uses the `dotenv` package to load environment variables from a `.env` file. The `.env.example` file serves as a template for the required environment variables.

```
PORT=8080
```

This environment variable specifies the port on which the server should listen for incoming requests. The default value is `8080`.
Sources: [.env.example](https://github.com/aanickode/access-control-service/blob/main/.env.example)

## Application Dependencies

The application relies on the following dependencies, which are listed in the `package.json` file:

```json
"dependencies": {
  "dotenv": "^16.0.3",
  "express": "^4.18.2"
}
```

- `dotenv`: This package is used to load environment variables from a `.env` file.
- `express`: This is the core web application framework used to build the Access Control Service.

Sources: [package.json](https://github.com/aanickode/access-control-service/blob/main/package.json)

## Application Entry Point

The application entry point is specified in the `package.json` file:

```json
"scripts": {
  "start": "node src/index.js"
}
```

This script starts the application by executing the `index.js` file located in the `src` directory.
Sources: [package.json](https://github.com/aanickode/access-control-service/blob/main/package.json)

## Deployment Process

To deploy the Access Control Service, follow these steps:

1. Clone the repository or download the source code.
2. Navigate to the project directory.
3. Create a `.env` file based on the `.env.example` template and configure the desired port number.
4. Install the required dependencies by running `npm install`.
5. Start the application by running `npm start`.

The application will start listening on the configured port, and you can access it through the appropriate URL (e.g., `http://localhost:8080`).

## Summary

The "Deployment and Infrastructure" section covers the essential configuration and setup required to run the Access Control Service application. It involves setting up environment variables, installing dependencies, and starting the application using the provided scripts. By following the deployment process outlined in this section, developers can ensure that the application is running and accessible on the desired port.