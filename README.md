1. Запускаем команду docker-compose up -d

2. Подключаемся к серверу конфигурации и выполняем инициализацию командой 

docker exec -it mongodb1 mongosh 

3. Создаём набор реплик в командной оболочке mongosh:

rs.initiate({_id: "rs0", members: [
{_id: 0, host: "mongodb1:27017"},
{_id: 1, host: "shard1:27018"},
{_id: 2, host: "shard2:27019"},
{_id: 3, host: "shard1_replica:27020"},
{_id: 4, host: "shard2_replica:27021"},
]}) 

