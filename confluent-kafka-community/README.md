# confluent community

## set

1. download docker-compose.yml

    ```script
    curl --silent --output docker-compose.yml \
      https://raw.githubusercontent.com/confluentinc/cp-all-in-one/6.2.0-post/cp-all-in-one-community/docker-compose.yml
    ```

1. run

    ```script
    docker-compose up -d
    ```

1. check running docker container

    ```script
    docker-compose ps
    ```

## create topic

1. create users topic

    ```script
    docker-compose exec broker kafka-topics \
        --create \
        --bootstrap-server localhost:9092 \
        --replication-factor 1 \
        --partitions 1 \
        --topic users
    ```

1. create pageviews topic

    ```script
    docker-compose exec broker kafka-topics \
        --create \
        --bootstrap-server localhost:9092 \
        --replication-factor 1 \
        --partitions 1 \
        --topic pageviews
    ```

## connector

1. get datagen - pageviews

    ```script
    curl -L -O -H 'Accept: application/vnd.github.v3.raw' \
        https://api.github.com/repos/confluentinc/kafka-connect-datagen/contents/config/connector_pageviews_cos.config
    ```

    ```script
    curl -X POST -H 'Content-Type: application/json' \
        --data @connector_pageviews_cos.config \
        http://localhost:8083/connectors
    ```

1. get datagen - users

    ```script
    curl -L -O -H 'Accept: application/vnd.github.v3.raw' \
        https://api.github.com/repos/confluentinc/kafka-connect-datagen/contents/config/connector_users_cos.config
    ```

    ```script
    curl -X POST -H 'Content-Type: application/json' \
        --data @connector_users_cos.config \
        http://localhost:8083/connectors
    ```

## ksql

1. connect ksql

    ```script
    docker-compose exec ksql-cli ksql http://ksqldb-server:8088
    ```

1. create stream PAGEVIEWS

    ```sql
    CREATE STREAM pageviews WITH (KAFKA_TOPIC='pageviews', VALUE_FORMAT='AVRO');
    ```

1. create table USERS

    ```sql
    CREATE TABLE users (id VARCHAR PRIMARY KEY) WITH (KAFKA_TOPIC='users', VALUE_FORMAT='AVRO');
    ```

- List the streams:

    ```sql
    SHOW STREAMS;
    ```

- List the tables:

    ```sql
    SHOW TABLES;
    ```

- View the details of a stream or a table:

    ```sql
    DESCRIBE <stream-or-table-name> EXTENDED;
    ```

- For example, to view the details of the users table:

    ```sql
    DESCRIBE USERS EXTENDED;
    ```

- List the running queries:

    ```sql
    SHOW QUERIES;
    ```

- Review the query execution plan:

    Get a Query ID from the output of SHOW QUERIES and run EXPLAIN to view the query execution plan for the Query ID:

    ```sql
    EXPLAIN <Query ID>;
    ```

## stop confluent

```bash
docker-compose stop
docker system prune -a --volumes --filter "label=io.confluent.docker"
```

## Reference

1. [confluent-quickstart](https://docs.confluent.io/platform/current/quickstart/cos-docker-quickstart.html)
