# Docker-compose

A `docker-compose.yml` file is used to define and manage multi-container Docker applications. For a Spring Boot application, it can help orchestrate the application container along with any other necessary services (like a database).

### Example `docker-compose.yml` for a Spring Boot Application

```yaml
version: '3.8'

services:
  app:
    image: my-spring-boot-app
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "8080:8080"
    environment:
      - SPRING_DATASOURCE_URL=jdbc:mysql://db:3306/mydatabase
      - SPRING_DATASOURCE_USERNAME=root
      - SPRING_DATASOURCE_PASSWORD=rootpassword
    depends_on:
      - db

  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: mydatabase
    ports:
      - "3306:3306"
    volumes:
      - db_data:/var/lib/mysql

volumes:
  db_data:
```

### Detailed Explanation of Each Command

#### `version: '3.8'`
- **Purpose:** 
  - Specifies the version of the Docker Compose file format. Version `3.8` is a commonly used and current version that supports most features.
- **Why it's used:** 
  - Different versions may support different features or syntax, so it's important to specify the correct version.

#### `services:`
- **Purpose:** 
  - Defines the list of services (containers) that will be managed by Docker Compose. Each service corresponds to a container.
- **Why it's used:** 
  - Allows you to describe multiple containers and how they interact with each other in a single YAML file.

#### `app:`
- **Purpose:** 
  - Defines the service for the Spring Boot application.
- **Why it's used:** 
  - This is where you define how the Spring Boot application will run, including which image to use, how to build it, and other configurations.

#### `image: my-spring-boot-app`
- **Purpose:** 
  - Specifies the Docker image to use for this service. If the image doesn't exist locally, Docker Compose will try to pull it from a registry.
- **Why it's used:** 
  - Naming the image allows you to reuse it across different environments or deployments.

#### `build:`
- **Purpose:** 
  - Provides instructions on how to build the Docker image for the Spring Boot application.
- **Why it's used:** 
  - Useful when you want Docker Compose to build the image from a `Dockerfile` located in the current directory.

  - **`context: .`**: Sets the build context to the current directory (`.`). The build context is the directory from which the Dockerfile and other necessary files will be sourced.
  
  - **`dockerfile: Dockerfile`**: Specifies the name of the Dockerfile to use. By default, it’s named `Dockerfile`, but you can customize this if your Dockerfile has a different name.

#### `ports:`
- **Purpose:** 
  - Maps ports on the host machine to ports on the container. In this case, port `8080` on the host is mapped to port `8080` on the container.
- **Why it's used:** 
  - This makes the Spring Boot application accessible via `http://localhost:8080` on your host machine.

#### `environment:`
- **Purpose:** 
  - Defines environment variables that will be passed to the container.
- **Why it's used:** 
  - Environment variables are often used to configure application settings without hardcoding them into the codebase. Here, they configure the Spring Boot application's database connection.
  
  - **`SPRING_DATASOURCE_URL`**: Sets the database URL for the Spring Boot application, pointing to the `db` service defined in this `docker-compose.yml` file.
  
  - **`SPRING_DATASOURCE_USERNAME`** and **`SPRING_DATASOURCE_PASSWORD`**: Set the database username and password.

#### `depends_on:`
- **Purpose:** 
  - Specifies dependencies between services. In this case, the `app` service depends on the `db` service.
- **Why it's used:** 
  - Docker Compose will start the `db` service before starting the `app` service, ensuring the database is ready when the Spring Boot application tries to connect.

#### `db:`
- **Purpose:** 
  - Defines the service for the MySQL database.
- **Why it's used:** 
  - Provides the configuration for running a MySQL database container that the Spring Boot application can connect to.

#### `image: mysql:8.0`
- **Purpose:** 
  - Specifies the MySQL image to use. Here, `mysql:8.0` is an official Docker image for MySQL 8.0.
- **Why it's used:** 
  - Ensures that a specific version of MySQL is used for the database service.

#### `environment:`
- **Purpose:** 
  - Configures environment variables for the MySQL container.
- **Why it's used:** 
  - These variables set up the root password and create a new database on startup.
  
  - **`MYSQL_ROOT_PASSWORD`**: Sets the root password for MySQL.
  
  - **`MYSQL_DATABASE`**: Creates a database with the specified name on startup.

#### `volumes:`
- **Purpose:** 
  - Maps a volume from the host machine to the container, ensuring that the MySQL data persists even if the container is stopped or removed.
- **Why it's used:** 
  - Prevents data loss by storing the MySQL data in a persistent volume on the host machine.

  - **`db_data:/var/lib/mysql`**: Maps the `db_data` volume to the MySQL data directory inside the container.

#### `volumes:`
- **Purpose:** 
  - Defines named volumes that are managed by Docker Compose.
- **Why it's used:** 
  - Named volumes allow you to persist data outside of the container's lifecycle.

  - **`db_data:`**: Creates a named volume `db_data` that will store the MySQL data.

### Running the Docker Compose Application

1. **Start the Application:**
   ```bash
   docker-compose up
   ```
   - This command will build the Docker images (if needed), create the containers, and start the services defined in the `docker-compose.yml` file.

2. **Stop the Application:**
   ```bash
   docker-compose down
   ```
   - This command stops and removes the containers, but the data in the `db_data` volume will persist.

### Summary

- **`services:`** defines the different containers in your application.
- **`app:`** is the service for your Spring Boot application, specifying how to build and run it.
- **`db:`** is the service for the MySQL database that the Spring Boot application will connect to.
- **`volumes:`** ensures that the database data persists between container restarts.

This `docker-compose.yml` file helps to easily manage and deploy a Spring Boot application along with its required database, simplifying the setup process in different environments.
