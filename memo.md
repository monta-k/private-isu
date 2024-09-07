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

## nginx のログ出力設定

docker-compose.yml の nginx の volume に以下を追記

```
- ./logs/nginx:/var/log/nginx
```

./webapp/etc/nginx/default.conf に以下に修正

```
log_format ltsv "time:$time_local"
  "\thost:$remote_addr"
  "\tforwardedfor:$http_x_forwarded_for"
  "\treq:$request"
  "\tmethod:$request_method"
  "\turi:$request_uri"
  "\tstatus:$status"
  "\tsize:$body_bytes_sent"
  "\treferer:$http_referer"
  "\tua:$http_user_agent"
  "\treqtime:$request_time"
  "\truntime:$upstream_http_x_runtime"
  "\tapptime:$upstream_response_time"
  "\tcache:$upstream_http_x_cache"
  "\tvhost:$host";

server {
  listen 80;

  client_max_body_size 10m;
  root /public/;

  location / {
    proxy_set_header Host $host;
    proxy_pass http://app:8080;
  }

  access_log /var/log/nginx/access.log ltsv;
}

```

## アクセスログ解析

```
brew install alp

# -mに正規表現を指定することでエンドポイントのパスにIDなどの値が入っている場合でも同一のものとみなす
cat logs/nginx/access.log | alp ltsv -m="^/posts/[0-9]+","^/image/[0-9]+\.(jpg|png|gif)","^/@[a-z]*" --sort=sum --reverse
```
