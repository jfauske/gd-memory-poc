# gd-memory-poc

## DDEV / Debian based

ddev start
ddev drush si --existing-config -y
ddev drush @self.ddev uli

## Docker compose / Alpine based

docker compose up
docker exec -it php vendor/bin/drush si --existing-config -y
docker exec -it php vendor/bin/drush @self.dcp uli
