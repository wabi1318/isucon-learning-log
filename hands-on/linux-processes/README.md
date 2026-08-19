# Linuxのプロセス

Linuxサーバーで「何が動いているか」を安全に調べるための教材です。コマンド名を暗記するのではなく、知りたいことに応じて観察方法を選び、出力から根拠を説明できる状態を目指します。

## 全体の到達目標

- プログラム、プロセス、systemdのserviceの違いを説明できる
- PID、実行利用者、CPU使用率、メモリ使用率、状態、実行内容を読める
- `ps`、`top`、`pgrep`を目的に応じて使い分けられる
- Sakaguchi練習環境のservice定義と実行中のプロセスを対応付けられる
- 負荷中の変化から、次に調べるプロセスを根拠付きで選べる

## Phase 0〜5の全体像

| Phase | できるようになること | 使う環境 | 主な実技 | `task`または参照ファイル | 目安時間 | 完了の判断方法 |
|---|---|---|---|---|---:|---|
| 0 | プログラム、プロセス、PID、CPU、メモリを説明する | Codexとの会話と小問題 | `ps`、`top`、`pgrep`の役割を判断する | `roadmap/catch-up/week-01-linux-server.md`、`vm/systemd/sakaguchi-app.service` | 45分 | 各コマンドで何を調べるか説明できる |
| 1 | 実行中のNode.jsプロセスを見つける | Lima VM | 一つのプロセスをPID、利用者、実行内容に分けて読む | `task vm:status`、`task vm:ssh` | 45分 | 資料なしで対象プロセスを見つけられる |
| 2 | プロセスとsystemdのservice定義を結び付ける | 設定ファイルとLima VM | 実行利用者、作業場所、起動コマンドを照合する | `vm/systemd/sakaguchi-app.service`、`task vm:status` | 45分 | service定義と実行中のプロセスの対応を説明できる |
| 3 | 複数serviceのプロセスと資源使用量を観察する | Lima VM | nginx、PostgreSQL、kumo、Hono APIを比較する | `task vm:status`、`task vm:ssh` | 60分 | PID、CPU、メモリ、実行内容を対応付けられる |
| 4 | プロセスの状態から調査対象を絞る | Lima VM | CPU使用率やプロセス不在を根拠に次の確認先を決める | `task vm:status`、`task vm:logs` | 60分 | 推測と確認済みの事実を分けて説明できる |
| 5 | 負荷中に動くプロセスを特定する | Lima VM | 短いGET負荷試験中の変化を観察する | `task vm:bench`、`task vm:ssh` | 90分 | 負荷、プロセス、serviceの関係を根拠付きで説明できる |

## 進め方

[Phase 0](phase-0.md)と[Phase 1](phase-1.md)はテーマ内の完了条件を満たしました。ただし、8月18日の日別メニューにある`ps aux`と`top`の実行は未完了です。古い日別項目を補うまでPhase 2は作成しません。

主要なコマンドは利用者が実行します。実行前に結果を予想し、実行後は見る列を絞って分かったことを説明します。

## 安全上の決まり

- Phase 0ではLima VMを起動せず、教材用の例だけを読む
- `kill`、serviceの停止・再起動、`task vm:deploy`は、目的や影響を確認せず実行しない
- VMが停止している場合、`task vm:up`を勝手に実行しない
- プロセス一覧の生出力をそのまま保存せず、対象行と必要な列だけを要約する
- コマンドの引数に秘密情報が含まれている場合は、表示・記録・共有しない
- 利用者名を含む絶対パス、マシン名、SSH情報、private IPを教材や記録へ残さない
