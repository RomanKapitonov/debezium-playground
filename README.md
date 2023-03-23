## Running docker
```
docker-compose up
```

## Database setup
#### Connect to the database
```shell
mysql --host=0.0.0.0 --user=debezium --password=dbz
```

```sql
GRANT ALL PRIVILEGES ON *.* TO 'debezium'@'%';

CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  email VARCHAR(255) UNIQUE NOT NULL
);
```

## Debezium connector commands:

#### Register a connector
```shell
curl -i -X POST -H "Accept:application/json" -H "Content-Type:application/json" http://localhost:8083/connectors/ -d @debezium-connector-config/mysql-connector.json
```

#### Remove a connector
```shell
curl -X DELETE http://localhost:8083/connectors/mysql-connector
```

## Run a console consumer
```shell
docker-compose exec kafka /kafka/bin/kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic dbserver1.test_db.users --from-beginning
```

## Set the max message size to enforce worker failure
```shell
docker-compose exec kafka /kafka/bin/kafka-configs.sh --bootstrap-server kafka:9092 --alter --entity-type topics --entity-name dbserver1.test_db.users --add-config max.message.bytes=100
```

## Push data to stream

```sql
INSERT INTO users (name, email)
VALUES (CONCAT('User', ROUND(RAND() * 100)), CONCAT('user', ROUND(RAND() * 100), '@example.com'));
```

## Observe the failed worker status
```shell
curl -X GET http://0.0.0.0:8083/connectors/mysql-connector/status
```
