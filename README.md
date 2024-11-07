1. Запускаем команду docker-compose up -d

2. Подключаемся к серверу конфигурации и выполняем инициализацию командой 


docker exec -it mongodb1 mongosh --port 27017

> rs.initiate(
  {
    _id : "config_server",
       configsvr: true,
    members: [
      { _id : 0, host : "mongodb1:27017" }
    ]
  }
);
> exit();


3. Инициализируйте шарды

docker exec -it shard1 mongosh --port 27018

> rs.initiate(
    {
      _id : "shard1",
      members: [
        { _id : 0, host : "shard1:27018" },
       // { _id : 1, host : "shard2:27019" }
      ]
    }
);
> exit();

docker exec -it shard2 mongosh --port 27019

> rs.initiate(
    {
      _id : "shard2",
      members: [
       // { _id : 0, host : "shard1:27018" },
        { _id : 1, host : "shard2:27019" }
      ]
    }
  );
> exit();


4. Инцициализируйте роутер и наполните его тестовыми данными:

docker exec -it mongos_router mongosh --port 27020

> sh.addShard( "shard1/shard1:27018");
> sh.addShard( "shard2/shard2:27019");

> sh.enableSharding("somedb");
> sh.shardCollection("somedb.helloDoc", { "name" : "hashed" } )

> use somedb

> for(var i = 0; i < 1000; i++) db.helloDoc.insert({age:i, name:"ly"+i})

> db.helloDoc.countDocuments() 
> exit();


5. Сделайте проверку на шардах:
 Получится результат — 1000 документов.

 docker exec -it shard1 mongosh --port 27018
 > use somedb;
 > db.helloDoc.countDocuments();
 > exit();


