
## Steps to Initialize Sharding in MongoDB

1. **Start Docker Compose:**
   Run the following command to start the services:
   ```bash
   docker-compose up -d
  ```
2. **Initialize Config Server Replica Set:**

  Connect to the configsvr container and initiate the replica set:

  ```
    docker compose exec -T configSrv mongosh --port 27017

    >rs.initiate(
      {
        _id : "config_server",
          configsvr: true,
        members: [
          { _id : 0, host : "configSrv:27017" }
        ]
      }
    );exit(); 

  ```
3. **Initialize Shard Replica Sets:**
   For each shard, connect and initiate its replica set:

   ```

   docker compose exec -T shard1 mongosh --port 27018 

   > rs.initiate(
      {
        _id : "shard1",
        members: [
          { _id : 0, host : "shard1:27018" },
        // { _id : 1, host : "shard2:27019" }
        ]
      }
   );exit();

  docker compose exec -T shard2 mongosh --port 27019

    >rs.initiate(
      {
        _id : "shard2",
        members: [
        // { _id : 0, host : "shard1:27018" },
          { _id : 1, host : "shard2:27019" }
        ]
      }
    );exit(); 


   ```

4. **Add Shards to the Cluster:** Connect to the mongos container and add shards:

    ```
    docker compose exec -T mongos_router mongosh --port 27020
    >sh.addShard( "shard1/shard1:27018");
    >sh.addShard( "shard2/shard2:27019");
    >sh.enableSharding("somedb");
    >sh.shardCollection("somedb.helloDoc", { "name" : "hashed" } )
    


    ```

5. **Заполняем mongodb данными**

```shell
./scripts/mongo-init.sh
```

6. **Add replics for shard**

```
rs.initiate({
    _id: "shard1",
    members: [
        { _id: 0, host: "shard1:27018" },
        { _id: 1, host: "shard1_replica1:27018" },
        { _id: 2, host: "shard1_replica2:27018" }
    ]
});


rs.initiate({
    _id: "shard2",
    members: [
        { _id: 0, host: "shard2:27019" },
        { _id: 1, host: "shard2_replica1:27019" },
        { _id: 2, host: "shard2_replica2:27019" }
    ]
});


```

### Если вы запускаете проект на локальной машине

Откройте в браузере http://localhost:8080

## Доступные эндпоинты

Список доступных эндпоинтов, swagger http://<ip виртуальной машины>:8080/docs