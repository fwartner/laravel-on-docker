# laravel-on-docker
Laravel running on 🐳 

## How to install
Running any laravel-application using the `fwartner/php-7.3` image is pretty simple.

Just create a `docker-compose.yml` within your project and edit it like the sample below:

```
version: '2'
services:
  app:
    image: 'fwartner/php-7.3'
    ports:
      - '80:80'
    volumes:
      - './supervisor:/etc/supervisor/conf.d'
      - '.:/var/www/html/'
    networks: 
      - appnet
  mysql:
    image: mysql
    networks: 
      - appnet
    ports:
      - '3306:3306'
    environment:
      - MYSQL_ROOT_PASSWORD=changeme123
      - MYSQL_DATABASE=app_db
      - MYSQL_USER=app_db_user
      - MYSQL_PASSWORD=changeme123
    volumes:
      - dbdata:/var/lib/mysql
  redis:
    image: 'redis:latest'
    networks: 
      - appnet
    ports:
      - '6379:6379'
    volumes:
      - cachedata:/data
networks: 
  appnet:
    driver: bridge
volumes:
  dbdata:
    driver: local
  cachedata:
    driver: local
```

Run `docker-compose up -d` to run the application in the background.

To install the composer dependencies run `docker run -t --rm -v $(pwd):/var/www/html fwartner/php-7.3 composer install`

Access your application on `http://YOUR-HOST.TLD` (The application is exposed on port 80)

## Migrating the database
Run `docker run -t --rm -v $(pwd):/var/www/html fwartner/php-7.3 php artisan migrate --force` in order to migrate the database. But before that you´ll need to make sure that your `.env` has the correct database-connection values.

Edit your .env like this:
```
DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=app_db
DB_USERNAME=app_db_user
DB_PASSWORD=changeme123
```

(According to your `docker-compose.yml` mysql configuration)