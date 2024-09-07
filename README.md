# private-isu

## 起動方法

### 手元で動かす

#### 初期データを用意する

必要になるので、以下の手順を行う前に必ず実行すること。

```sh
make init
```

#### Docker Compose

アプリケーションは以下の手順で実行できる。dump.sql.bz2 を配置しないと MySQL に初期データが import されないので注意。

```sh
cd webapp
docker compose up
```

ベンチマーカーは以下の手順で実行できる。

```sh
cd benchmarker
docker build -t private-isu-benchmarker .
docker run --network host -i private-isu-benchmarker /bin/benchmarker -t http://host.docker.internal -u /opt/userdata
# Linuxの場合
docker run --network host --add-host host.docker.internal:host-gateway -i private-isu-benchmarker /bin/benchmarker -t http://host.docker.internal -u /opt/userdata
```

動かない場合は`ip a`して docker0 のインタフェースでホストの IP アドレスを調べて`host.docker.internal`の代わりに指定する。以下の場合は`172.17.0.1`を指定する。

```
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    link/ether 02:42:ca:63:0c:59 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:caff:fe63:c59/64 scope link
       valid_lft forever preferred_lft forever
```

## [実装メモ](/memo.md)
