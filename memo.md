## Docker コンテナのメモリ使用量確認

```
docker stats
```

## MySQL のスロークエリログを出力させる

./webapp/etc/my.cnf を作成

```
[mysqld]
slow_query_log=1
slow_query_log_file=/var/log/mysql/slow-query.log
long_query_time=0
```

docker-compose.yml の mysql の volume に以下を追記

```
- ./logs/mysql:/var/log/mysql
- ./etc/my.cnf:/etc/mysql/conf.d/my.cnf
```
