# Fast simple cloud based on GO language

- with Redis cache
- data storage on S3 Minio
- queue broker nats

Simple docker-compose deployment to experiment with Cells v4.
It uses `pydio/cells` docker image, use whatever image by editing the docker-compose.yml file.

## Starting Cells

```bash
cp .env.template .env
docker-compose up -d
```

on web browser: http://localhost:8080

login: admin
password: admin

## Storing data

- User data
setup directory with env `MINIO_STORE_DATA` in .env file

if directory created by user
```bash
sudo chown $USER:$USER /path/to/data/directory -R
sudo chmod +ugo+rw
```

By default it in current directory named `cells_data`

- cells settings

cells settings stored in `cells_dir` volume
 

## Caddy LoadBalancer Access

Access https://caddy:8080/ to access Cells. Enjoy!

## Stopping cluster

```bash
docker-compose down -v
```

```sh
# To clean everything
docker-compose down -v --remove-orphan
sudo rm -rf ./cells_data or user your `MINIO_STORE_DATA` storage path
```

## Clean jwt table

if on `docker-compose down` and next `docker-compose up` getting error "Could not ensure that signing keys are correct!"

```bash
docker exec -i pydio_mysql mysql -u pydiouser -pcellspasswrd cells < scripts/clean-jwt.sql
```

## Backup

- Database

backup:

```bash
docker-compose up
docker container exec -i pydio_mysql sh -c "mysqldump -u pydiouser -pcellspasswrd cells --no-tablespaces > /pydio_database_backup.sql"
docker cp pydio_mysql:/pydio_database_backup.sql /path/to/backup/for/database/pydio_database_backup.sql
```

restore:

```bash
docker-compose down
docker volume rm pydio_mysql_data
docker-compose run mysql
docker cp /path/to/backup/for/database/pydio_database_backup.sql $(docker container ls -aq --filter "name=pydio_mysql"):/tmp/pydio_database_backup.sql
docker exec -i --workdir /var/lib/mysql $(docker container ls -aq --filter "name=pydio_mysql") sh -c 'mysql -u pydiouser -pcellspasswrd cells < /tmp/pydio_database_backup.sql'
docker-compose down
docker-compose up
```

- Data

```bash
sudo rsync -urv --delete ./cells_data/ user@server:/path/backup/folder/
```



