# A proof of concept for GD memory issues with Drupal on Alpine vs Debian

## UPDATE: 
The issue described here is resolved with configuring/building the PHP GD extension with the --with-external-gd flag
... so got nothing to do with Alpine vs Debian.

For alpine based, something like this would do:
```sh
apk add --no-cache ... gd-dev ... \
&& docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp --with-avif --with-external-gd
```

For debian based, something like this will do:
```sh
apt-get install ... libgd-dev ... \
&& docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp --with-avif --with-external-gd
```

difference in distro packages?
difference in libs/binaries built from source?
glibc vs musl?

For all of the above: I Don't know yet.


Possibly related to https://www.drupal.org/project/drupal/issues/3339075 and https://github.com/php/php-src/issues/13877

## DDEV / Debian based

DDEV set memory limit to 1 GB, for this PoC we set it to 128MB

### How to test with DDEV's debian based docker-image for php

Run the following
```sh
ddev start
ddev composer install
ddev drush si --existing-config -y
ddev drush @self.ddev uli
```

Log in to the site by visiting the URL from the last command above
Then, create a new article and upload the `large-testimage.jpg` for the image field.
Should work without any issues and you should not see any memory exhaustion errors when running `ddev logs`.

## Docker compose / Alpine based

Wodby set memory limit to 512 MB, can be overridden here by setting the `PHP_MEMORY_LIMIT` env var.

### How to test with Wodby's alpine based docker-image for php

Run the following
```sh
docker compose up -d
docker exec -it php composer install
docker exec -it php vendor/bin/drush si --existing-config -y
docker exec -it php vendor/bin/drush @self.dcp uli
```
Log in to the site by visiting the URL from the last command above
Then, create a new article and upload the `large-testimage.jpg` for the image field.
You should see memory exhaustion errors when running `docker logs php`.

## Observations

In the DDEV environment we don't see any issues with GD and memory use even with a memory limit of 128 MB. (Not tested lower yet).
With the alpine based Wodby php image, we need to set memory limit higher than the default 512 MB to avoid fatal error.
