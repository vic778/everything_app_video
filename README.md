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

docker build --secret id=POSTGRES_PASSWORD --secret id=POSTGRES_DB --secret id=POSTGRES_HOST --secret id=POSTGRES_USER --secret id=RAILS_MASTER_KEY  --secret id=VIRTUAL_HOST --secret id=VIRTUAL_PORT --secret id=LETSENCRYPT_HOST --secret id=LETSENCRYPT_EMAIL -t app ./video/.

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
