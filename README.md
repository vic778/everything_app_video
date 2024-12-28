# README

# Docker Network
docker network create everything_app

# NGINX+LetsEncrypt
```shell
docker-compose build
docker-compose up -d

docker-compose stop
docker-compose start
```

# Rails
```shell
export POSTGRES_HOST=postgres
export POSTGRES_DB=prod_2
export POSTGRES_USER=postgres
export POSTGRES_PASSWORD=password
export RAILS_MASTER_KEY=168814261ab73267da242073f651c820
export VIRTUAL_HOST=another.tambu-tech.com
export VIRTUAL_PORT=3001
export LETSENCRYPT_HOST=tambu-tech.com
export LETSENCRYPT_EMAIL=victoiremmanuelbarh@gmail.com
export LETSENCRYPT_TEST=false

docker build \
    --build-arg POSTGRES_HOST=${POSTGRES_HOST} \
    --build-arg POSTGRES_DB=${POSTGRES_DB} \
    --build-arg POSTGRES_USER=${POSTGRES_USER} \
    --build-arg POSTGRES_PASSWORD=${POSTGRES_PASSWORD} \
    --build-arg RAILS_MASTER_KEY=${RAILS_MASTER_KEY} \
    --build-arg VIRTUAL_HOST=${VIRTUAL_HOST} \
    --build-arg VIRTUAL_PORT=${VIRTUAL_PORT} \
    --build-arg LETSENCRYPT_HOST=${LETSENCRYPT_HOST} \
    --build-arg LETSENCRYPT_EMAIL=${LETSENCRYPT_EMAIL} \
    t app ./video/.
docker volume create app-storage
docker run -d --rm -it --name video --env-file ./video/.env -v app-storage:/rails/storage --network everything_app app
```

# Another Rails
```shell
docker build -t another_app ./another/.
docker volume create app-storage
docker run -d --rm -it --name another --env-file ./another/.env -v app-storage:/rails/storage --network everything_app another_app
```

# Postgres
```shell
mkdir psql/postgres-data
export POSTGRES_PASSWORD="password"
docker build --secret "id=POSTGRES_PASSWORD" -t psql ./psql/.
docker network create everything_app 
docker run -d --name postgres --env-file ./psql/.env -v postgres-data:/var/lib/postgresql/data --network everything_app psql
