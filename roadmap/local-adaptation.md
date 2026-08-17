# Sakaguchi練習環境への読み替え

取得元ロードマップは、一般的なISUCON環境としてMySQLを前提にしています。実際の練習環境はPostgreSQLです。このファイルで差を読み替え、取得元の固定版は変更しません。

## 基本の対応

| ロードマップの表現 | 練習環境で使うもの |
|---|---|
| MySQL | PostgreSQL |
| slow query log | `pg_stat_statements`と`task stats` |
| MySQL形式の`EXPLAIN` | PostgreSQL形式の`EXPLAIN` |
| Node.jsアプリ | Hono API |
| AWS | kumo |
| 過去問環境 | 基礎期間はSakaguchi練習環境 |
| ベンチマーカー | autocannonを使う`task bench`または`task vm:bench` |
| MySQLの設定 | PostgreSQLの接続、メモリ、統計、実行計画 |

過去問へ進む週では、Sakaguchi練習環境だけで完結させず、ロードマップどおり未着手の過去問も使います。

## PostgreSQLで見るもの

### 重いSQL

Compose環境では`task stats`を使い、次を確認します。

- `calls`: 実行回数
- `total_ms`: 合計実行時間
- `mean_ms`: 1回あたりの平均実行時間
- `rows`: 返した行数
- `query`: SQLの先頭部分

合計時間が大きいSQLは、1回が遅い場合と、短いSQLが大量に呼ばれる場合を分けて考えます。

### 実行計画

PostgreSQLの`EXPLAIN`では、最初に次を見ます。

- `Seq Scan`: テーブルを順に読んでいる
- `Index Scan`または`Index Only Scan`: インデックスを使っている
- `rows`: 読む行数の見積もり
- `cost`: PostgreSQLが比較に使う見積もり値

`EXPLAIN ANALYZE`では、SQLを実際に実行して`actual time`、`actual rows`、`loops`を確認できます。SELECT以外に使うとデータが変わるため、教材では原則としてSELECTだけを対象にします。I/Oまで見る場合は、理由を説明して`BUFFERS`を追加します。

MySQL固有の`type`や`key`という列を、PostgreSQLの正解として教えません。

## 週ごとの接続先

### 準備週: Webシステムの全体像

- `README.md`で構成を確認する
- `src/frontend/main.tsx`から`GET /api/posts`を探す
- `src/server/index.ts`で同じAPIとSQLを探す
- `vm/nginx/sakaguchi.conf`で`/api`の転送先を確認する
- `GET /api/health`でnginx、Hono、PostgreSQLのつながりを確認する

成果物は、nginx→Hono→PostgreSQLと、Hono→kumoの流れを含む構成図にします。

### 第1週: Linuxとサーバー

- `task vm:status`: nginx、PostgreSQL、kumo、Hono APIの状態
- `task vm:ssh`: Ubuntuへ入り、`pwd`、`ls`、`ps`、`ss`を練習
- `task vm:logs`: systemdのjournalを追跡
- `vm/systemd/`: service定義を読む
- `vm/nginx/sakaguchi.conf`: nginxの設定とアクセスログの場所を読む

最初は状態確認だけを行い、停止や再起動は仕組みと戻し方を理解してから扱います。

### 第2週: SQLとインデックス

- `database/001-schema.sql`: テーブル、主キー、外部キー、既存インデックス
- `src/server/index.ts`: APIから発行されるSQL
- `task bench`: 変更前後のHTTP計測
- `task stats`: 累積実行時間が大きいSQLの確認
- PostgreSQLの`EXPLAIN`または`EXPLAIN (ANALYZE, BUFFERS)`: 読み方の練習

SQLとインデックスの小さな実験はCompose環境を基本にします。VMで行う場合は、Compose用の`task stats`をそのまま使いません。

### 第3週: Node.js、TypeScript、非同期処理

- `src/server/index.ts`: ルーティング、DBアクセス、レスポンス
- `src/server/db.ts`: コネクションプール
- `src/server/request.ts`: 入力値の検証
- `src/server/request.test.ts`: 入力の仕様
- `pnpm test`: テスト
- `pnpm check`: format、lint、型の確認

URL→Honoの処理→SQL→レスポンスという流れを、実在するAPI一つで追います。

### 第4週: 計測、nginx、キャッシュ

- `task bench`または`task vm:bench`: HTTP負荷試験
- `task logs`または`task vm:logs`: アプリとserviceのログ
- `task stats`: Compose環境の重いSQL
- `vm/nginx/sakaguchi.conf`: `request_time`と`upstream_time`
- `src/server/index.ts`: `Server-Timing`とアプリ処理時間

アクセス全体、nginxからHonoまで、Hono内部という三つの時間を混同しません。

## 改善サイクル

Phase 5またはまとまった実践では、次の順番を守ります。

```text
対象と条件を固定
  → 変更前を計測
  → ログとSQLから仮説を一つ選ぶ
  → 小さな変更
  → テストとservice状態を確認
  → 同じ条件で再計測
  → 採用または不採用
  → 戻し方を確認
```

VMへコードを反映する`task vm:deploy`は、同期、build、service再起動を含みます。実行前に変更対象と戻し方を確認します。
