## Installation
1. Go the the docker directory and run the command bellow:
```bash
docker-compose up -d
```
Go into the nginx container and install the composer
```bash
docker exec -it docker_you_nginx_service_1 bash
```
```bash
composer install
```
