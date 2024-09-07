## comments テーブルにインデックスを追加

```
ALTER TABLE `comments` ADD INDEX `post_id_idx` (`post_id`,`created_at`);
```

## 画像の配信元を変更

画像のリクエストのたびに DB から画像データを取得して配信していたので nginx のエイリアスを用いて存在する画像ファイルの場合はファイルを直接配信する。
一度取得した画像データは直接ファイルに保存するようにする。
画像を保存する際に直接ファイルに保存するようにする。
https://github.com/monta-k/private-isu/commit/9fe100938a9c555bf07aa4e715c4b786826a19c0
