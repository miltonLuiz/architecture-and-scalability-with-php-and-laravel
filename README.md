# Application Scalability Improvement Project

In this project, I demonstrate how I implemented scalability improvements for a monolithic application without the need for code refactoring or adopting microservices. Throughout the development process, I explored different stages of enhancing the application's architecture and implemented various improvements.

**Please Note:** It's essential to be aware that this project may not necessarily adhere to the best practices of PHP. Instead, it aims to illustrate a method for enhancing your project's scalability, not necessarily showcasing best coding practices.

## Initial Setup

1. After cloning the project, install its dependencies with:
```shell
composer install
```

2. If you don't have `composer` installed locally:
```shell
docker run --rm -itv $(pwd):/app -w /app -u $(id -u):$(id -g) composer:2.5.8 install
```

3. Once the dependencies are installed, create the configuration file `.env`:
```shell
cp .env.example .env
```

4. Start the _Docker_ environment by running:
```shell
docker compose up -d
```

5. Grant the correct user permissions to write logs in the application:
```shell
docker compose exec app chown -R www-data:www-data /app/storage
```

6. Ensure that the database container is up and running. The logs should display the message _ready for connections_ in the last lines:
```shell
docker compose logs database
``` 
Wait until the above command shows _ready for connections_ as one of the last lines.

7. To create the database, execute:
```shell
docker compose exec app php artisan migrate --seed
```

This last step may take a while, as it will create a substantial amount of data (1000 specialists with 1000 reviews each). While it runs, the API will already be accessible at http://localhost:8123/api. Additionally, http://localhost:8025 provides access to the _Mailpit_ email service.

## Improvement Steps

During the project, I implemented the following improvements:

### 1. Messaging

I began by incorporating messaging, initially based on a local database but with the flexibility to be replaced by services like SQS or RabbitMQ in a production environment.

### 2. Load Balancer

Next, I introduced a load balancer to distribute requests to the API, enabling horizontal scalability through the addition of multiple servers.

### 3. Cache Layer

To reduce the load on the database, I added a cache layer, optimizing the application's performance.

### 4. Storage of Static Files

To handle complex reports that require real-time updates, I explored the strategy of storing static files in external services.

In this final architecture, users access the front-end, which passes through a load balancer before reaching the API. The API stores precomputed data in an in-memory database, reads and writes data to the relational database, and publishes messages to a message broker. A queue worker consumes messages from the message broker, saves reports in storage, and sends emails to an email server.

## Results

Following all the implementations, the results were significant. In the initial test, we were handling approximately 112 requests per second, totaling 1123 requests in 10 seconds. After implementing the configurations, this number increased to an impressive 1504 requests per second in 10 seconds.
