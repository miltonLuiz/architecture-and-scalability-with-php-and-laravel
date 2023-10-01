# Project for the _Architecture and Scalability in PHP_ Course

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
