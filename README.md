# Cassandra

Beginning to learn cassandra by example, starting simple with docker.

Following https://cassandra.apache.org/_/quickstart.html and updating some to get it working with latest version and podman.

## Files

In the Docker directory there are the following files:

- 1.get_image.sh
- 2.start_cassandra.sh
- 3.load_data.sh
- 4.interactive_cqlsh.sh
- 5.clean_up.sh
- data.cql

Following is the file name with contents, you should execute the first one, then wait for a minute or so and after that you can run the others.

After step 4, interactive cqlsh, you can manually run this instructions and verify:

```sql
SELECT * FROM store.shopping_cart;
INSERT INTO store.shopping_cart (userid, item_count) VALUES ('4567', 20);
SELECT * FROM store.shopping_cart;
```

### 1.get_image.sh
 
docker pull docker.io/cassandra:latest
 
### 2.start_cassandra.sh
 
docker network create cassandra

docker run --rm -d --name cassandra --hostname cassandra --network cassandra cassandra
 
### 3.load_data.sh
 
docker run --rm --network cassandra -v "$(pwd)/data.cql:/scripts/data.cql" -e CQLSH_HOST=cassandra -e CQLSH_PORT=9042 -e CQLVERSION=3.4.6 docker.io/nuvo/docker-cqlsh
 
### 4.interactive_cqlsh.sh
 
docker run --rm -it --network cassandra docker.io/nuvo/docker-cqlsh cqlsh cassandra 9042 --cqlversion='3.4.6'
 
### 5.clean_up.sh
 
docker kill cassandra
docker network rm cassandra
 
### data.cql
 
-- Create a keyspace
CREATE KEYSPACE IF NOT EXISTS store WITH REPLICATION = { 'class' : 'SimpleStrategy', 'replication_factor' : '1' };

-- Create a table
CREATE TABLE IF NOT EXISTS store.shopping_cart (
userid text PRIMARY KEY,
item_count int,
last_update_timestamp timestamp
);

-- Insert some data
INSERT INTO store.shopping_cart
(userid, item_count, last_update_timestamp)
VALUES ('9876', 2, toTimeStamp(now()));
INSERT INTO store.shopping_cart
(userid, item_count, last_update_timestamp)
VALUES ('1234', 5, toTimeStamp(now()));
