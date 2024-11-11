1. Запускаем команду docker-compose up -d

2. Подключаемся к любой ноде и выполняем команду для создания кластера:

docker exec -it redis_1
echo "yes" | redis-cli --cluster create   173.17.0.2:6379   173.17.0.3:6379   173.17.0.4:6379   173.17.0.5:6379   173.17.0.6:6379   173.17.0.7:6379   --cluster-replicas 1 



Ссылка на схему: https://drive.google.com/file/d/1D6Kdm-bqJS-S95UtiwcDr5MhQ9NKgxH4/view?usp=sharing
