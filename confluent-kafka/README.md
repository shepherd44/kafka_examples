# confluent docker compose up

## getting started

1. start container

    ```bash
    docker-compose up -d
    ```

1. check container

    ```bash
    docker-compose ps
    ```

1. connect confluent kafka Control Center
    * [http://localhost:9021](http://localhost:9021)

## confluent stop

```bash
docker-compose stop
docker system prune -a --volumes --filter "label=io.confluent.docker"
```
