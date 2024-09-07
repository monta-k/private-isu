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

## スロークエリの統計情報

pt-query-digest を使って遅いクエリを確認する

```
brew install percona-toolkit
sudo pt-query-digest webapp/logs/mysql/slow-query.log
```

## クエリの実行計画確認

```
docker exec -it private-isu-mysql-1 bash

mysql -uroot -proot
use isuconp

# example
EXPLAIN SELECT * FROM `comments` WHERE `post_id` = 9983 ORDER BY `created_at` DESC LIMIT 3\G
```
