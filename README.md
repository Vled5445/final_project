# final_project


Чтобы проверить работоспособность нашей системы, подключимся к clickhouse01 и создадим таблицу с реплицируемым движком 
CREATE TABLE sharded_replicated_example
(
    ID UInt32,
    Name String
) ENGINE = ReplicatedMergeTree('/clickhouse/tables/sharded_replica/{shard}', '{replica}')
ORDER BY (ID);

Также создадим такую же таблицу на clickhouse02. 

Теперь мы можем создать дистрибутивную таблицу на главном клике(clickhouse05)

CREATE TABLE sharded_distributed_example
(
    ID UInt32,
    Name String
)
ENGINE = Distributed('cluster_demo_ash', 'default', sharded_replicated_example, ID%2);


Вставляем в sharded_distributed_example данные.

insert into sharded_distributed_example
values (10,'123'),(51,'124'),(23,'223'),(3,'324'),(1,'123'),(5,'124'),(2,'223'),(3,'324');

Посмотрим, какие есть данные select * from sharded_distributed_example;

<img width="279" alt="image" src="https://github.com/user-attachments/assets/9f0257bb-0985-46c3-935d-6f0fa2c3a294">

Посмотрим наличие данных в clickhouse01 

<img width="278" alt="image" src="https://github.com/user-attachments/assets/8945fbc7-7cdb-4132-bc67-68af1a031377">


Видим, что тут есть только нечетные ID  
