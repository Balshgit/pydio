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

## storing data

setup directory with env `MINIO_STORE_DATA` in .env file

if directory created by user
```bash
sudo chown $USER:$USER /path/to/data/directory -R
sudo chmod +ugo+rw
```

By default it in current directory named `cells_data`
 

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

```bash
docker exec -i pydio_mysql mysql -u pydiouser -pcellspasswrd cells < clean-jwt.sql
```

- if on `docker-compose down` and next `docker-compose up` getting error with jwt token
```sql
SET FOREIGN_KEY_CHECKS = 0;
TRUNCATE hydra_oauth2_trusted_jwt_bearer_issuer;
TRUNCATE hydra_jwk;
SET FOREIGN_KEY_CHECKS = 0;

```

