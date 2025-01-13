# SQLの操作

## Dockerでの環境構築方法
### コンテナイメージのビルド
Docker HubにMySQLの公式イメージ有り。
```ruby
docker pull mysal:latest

docker run --name mysql-container -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:latest
# --name mysql-container コンテナに mysql-container という名前をつけている
# -e MYSQL_ROOT_PASSWORD=my-secret-pw ：MYSQL_ROOT_PASSWORD 環境変数を設定して、MySQL の root ユーザーのパスワードを my-secret-pw に設定。パスワードは任意のものに変更できる。
# -d mysql:latest ：バックグラウンドでMySQL の Docker イメージを使用
```

### Mysql コンテナに接続する
```ruby
docker exec -it mysql-container mysql -uroot -p
# 前述で設定した my-secret-pw を入力
```

### コンテナ内のシェルを起動
```ruby
docker exec -it mysql-container bash
```

### SQLに接続
```sql
mysql -pデータベース名
```

## SQLの操作
### データベース一覧
```sql
SHOW DATABASES;
```

### データベースの移動
```sql
USE 移動したいデータベース;
```

### テーブル一覧
```sql
SHOW tables;
```

### テーブルの構造の確認
```sql
DESCRIBE テーブル名;
```

### データの確認
```sql
SELECT * EROM テーブル名　LIMIT 10;
```

