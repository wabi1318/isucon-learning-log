# Sakaguchi練習環境

このファイルは、学習で使う練習環境の正本です。教材を作る前と、実環境を使う課題の前に確認します。

## 練習リポジトリの特定

- リポジトリ名: `isucon2026-team-sakaguchi`
- 公開URL: [topi-log/isucon2026-team-sakaguchi](https://github.com/topi-log/isucon2026-team-sakaguchi)
- 標準配置: 学習ログと同じ親ディレクトリに置く
- 学習ログから見た候補: `../isucon2026-team-sakaguchi`

Codexは最初に候補が存在するかだけを確認します。見つからない場合は、利用者へ練習リポジトリの場所を一度だけ聞き、そのセッション内で使います。解決した絶対パスは、教材、日次ログ、学習状況、実験記録へ保存しません。ファイルシステム全体を探索しません。

教材と記録では、`src/server/index.ts`のような練習リポジトリ内の相対パスだけを使います。コマンドは「練習リポジトリのルートで実行」と案内します。

## 実際の構成

```text
ブラウザまたは負荷試験
          ↓ HTTP
        nginx
    ├── React画面
    └── /apiをHonoへ転送
                 ↓
              Hono API
              ├── PostgreSQL
              └── kumo
```

- nginx: React画面の配信、`/api`の転送、アクセス時間の記録
- Hono: Node.js上で動くJSON API
- PostgreSQL: users、posts、comments、likesの保存と検索
- kumo: 学習用のAWS代替環境
- autocannon: HTTP負荷試験

Compose環境はPostgreSQL 17、Lima VMはPostgreSQL 16を使います。異なる環境の結果を直接比較しません。

## 環境の使い分け

| 目的 | 使う環境 | 理由 |
|---|---|---|
| コード、SQL、nginxの小さな実験 | Docker Compose | 起動と初期化を短時間で行える |
| Linux、systemd、プロセス、ポート、journal | Lima VM | ISUCON本番に近いサーバー操作を練習できる |
| 変更前後の比較 | 同じ環境を継続使用 | マシンやDBの違いによる揺れを混ぜない |

環境を切り替えた場合は、同じ実験の続きとして比較せず、新しいベースラインを取ります。

## 最初に読むファイル

練習リポジトリで次を確認します。

| 知りたいこと | ファイル |
|---|---|
| 起動方法と全体構成 | `README.md` |
| 利用できる操作 | `Taskfile.yml` |
| APIとSQLの流れ | `src/server/index.ts` |
| DB接続設定 | `src/server/db.ts` |
| テーブルとインデックス | `database/001-schema.sql` |
| VMのnginx設定 | `vm/nginx/sakaguchi.conf` |
| systemdのservice | `vm/systemd/` |
| VMへの反映方法 | `vm/provision.sh` |

`.env`、認証情報、鍵、履歴は読みません。

## 主なAPI

- `GET /api/health`: DBを含む動作確認
- `GET /api/posts`: 投稿一覧
- `GET /api/posts/:id`: 投稿とコメント
- `POST /api/posts`: 投稿作成
- `GET /api/storage`: kumoの確認
- `POST /api/initialize`: 学習データの初期化

状態を変えない学習では、原則としてGETだけを使います。POSTはデータが変わることを先に説明します。

## 操作の安全区分

### 状態確認

対象と目的が明らかな場合に使えます。

```sh
task --list
task ps
task ports
task stats
task vm:status
```

`task stats`はCompose環境の`pg_stat_statements`を読みます。VM用のコマンドではありません。

### 対話的な観察

利用者へ終了方法を伝えてから案内します。

```sh
task logs
task vm:ssh
task vm:logs
```

ログ追跡は`Ctrl-C`で終了します。SSH接続後も、状態を変えるコマンドは別に判断します。

### 短時間の負荷試験

対象URL、接続数、実行時間、pipeliningを先に示します。最初はGETと小さい値を使います。

```sh
task bench
task vm:bench
```

独自オプションでPOSTを送る場合は状態変更として扱います。共有環境や本番環境へ向けません。

### 状態を変更する操作

目的、対象、影響、確認方法、戻し方を説明し、利用者の確認を待ちます。

```sh
task up
task down
task reset
task tools
task vm:setup
task vm:up
task vm:sync
task vm:deploy
task vm:down
```

特に`task vm:deploy`はソースを同期し、buildとservice再起動を行います。

### データを削除する操作

通常の教材では使いません。必要な場合は、削除対象と復旧方法を示して個別の明示確認を取ります。

```sh
docker compose down -v
task vm:delete CONFIRM=sakaguchi-practice
```

## 計測の基本

1. 環境と対象APIを固定する
2. 必要なら学習データの状態をそろえる
3. 変更前を同じ条件で計測する
4. 仮説に対応する変更を一つだけ行う
5. 正しさとservice状態を確認する
6. 同じ条件で再計測する
7. 採用または不採用を決める
8. 戻し方を確認する

結果は`templates/benchmark-experiment.md`を使い、`experiments/`へ保存します。

## 公開リポジトリへ書かないもの

- 利用者名を含む絶対パス
- マシン名、SSH接続情報、private IP
- `.env`の内容、トークン、Cookie、鍵
- 個人を識別できるコマンド出力
- 大量の生ログ

必要な結果だけを要約し、ファイルはリポジトリ相対パスで記録します。
