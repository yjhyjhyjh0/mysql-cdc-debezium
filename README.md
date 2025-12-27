# mysql-cdc-debezium
Example of using MySQL CDC debezium with Kafka.  
Setting up local Kafka, Zookeeper, mysql with cdc config and check CDC result via kafka consumer print.

## How to build

```
debezium:2.5
Zookeeper:3.8
Kafka:3.6
mysql:8.0

Step1-
docker compose up -d

Step2-Setup MySQL cdc settings 
docker exec  -it mysql-cdc mysql -uroot -p

USE testdb;

CREATE TABLE user_count (
  id INT PRIMARY KEY,
  name VARCHAR(50)
);

INSERT INTO user_count VALUES (1, 'alice');
UPDATE user_count SET name='bob' WHERE id=1;
DELETE FROM user_count WHERE id=1;

Step3-Create Kafka Connect job
curl -X POST http://localhost:8083/connectors \
  -H "Content-Type: application/json" \
  -d '{
    "name": "mysql-cdc-connector",
    "config": {
      "connector.class": "io.debezium.connector.mysql.MySqlConnector",
      "database.hostname": "mysql-cdc",
      "database.port": "3306",
      "database.user": "debezium",
      "database.password": "dbz",
      "database.server.id": "184054",
      "topic.prefix": "mysqlserver",
      "database.include.list": "testdb",
      "table.include.list": "testdb.user_count",
      "schema.history.internal.kafka.bootstrap.servers": "kafka:9092",
      "schema.history.internal.kafka.topic": "schema-changes.testdb",
      "include.schema.changes": "false"
    }
  }'
  
Step4-Confirm result
docker exec -it kafka \
  kafka-console-consumer \
  --bootstrap-server localhost:9092 \
  --topic mysqlserver.testdb.user_count \
  --from-beginning

```

FAQ

```

```