# ロードマップ

このディレクトリは、チーム用ロードマップのローカルスナップショットです。Codexが毎日の学習内容を決めるときに使います。

- 取得日: 2026-08-17
- 取得元: [topi-log/isucon2026-team-sakaguchi](https://github.com/topi-log/isucon2026-team-sakaguchi/tree/main/plans)
- メイン文書の取得元SHA: `d730db1c455b4420cbeee188ab46469cecd954fa`

収録した文書:

- [Sakaguchi練習環境への読み替え](local-adaptation.md)
- [全体ロードマップ](isucon-learning-roadmap-2026.md)
- [準備週: Webシステムの全体像](catch-up/week-00-web-overview.md)
- [第1週: Linuxとサーバーの基礎](catch-up/week-01-linux-server.md)
- [第2週: SQLとインデックス](catch-up/week-02-sql-index.md)
- [第3週: Node.js / TypeScriptと非同期処理](catch-up/week-03-nodejs-typescript.md)
- [第4週: 計測、nginx、キャッシュ](catch-up/week-04-measurement-nginx-cache.md)

元の計画が更新されても、このリポジトリの学習順は自動では変わりません。更新が必要な場合は、差分を確認してから反映します。

毎日の学習では、取得元の固定版を先に書き換えず、`local-adaptation.md`を使ってPostgreSQLとSakaguchi練習環境へ読み替えます。

日別メニューに明記されたコマンドと成果物は、一項目ずつ実行状況を追跡します。目的が似た別コマンドを使っても、元の項目は自動的に完了しません。環境差で置き換える場合は`local-adaptation.md`を根拠に、元項目、代替内容、理由、実行証拠を日次ログへ残します。

教材のPhaseは理解を段階分けするための仕組みであり、ロードマップの日付や実行項目とは別の軸です。Phaseが完了しても、その日に指定された全項目の実行証拠がなければ、日別メニューは未完了のままです。
