## Running docker
```
docker-compose up
```

## Database setup
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

#### Remove a connecton
```shell
curl -X DELETE http://localhost:8083/connectors/mysql-connector
```

## Run a console consumer
```shell
docker-compose exec kafka /kafka/bin/kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic dbserver1.test_db.users --from-beginning
```

## Push data to stream

```sql
INSERT INTO users (name, email)
VALUES (CONCAT('User', ROUND(RAND() * 100)), CONCAT('user', ROUND(RAND() * 100), '@example.com'));
```