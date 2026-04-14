---
name: aws-ec2-operations
description: |
  AWS EC2 インスタンスに対する操作（一覧・起動・停止・状態確認）を行うスキル。
  provider: aws
---

# AWS EC2 操作スキル

## provider

aws

## 認証方法

- 環境変数 `AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY` / `AWS_DEFAULT_REGION` を使用する
- Credentials はコード・ファイルに直書きしない
- IAM ロールによるインスタンスプロファイル認証も使用可能
- 認証情報は外部（Secrets Manager / 環境変数）で管理する

## 実行条件

- `AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY` / `AWS_DEFAULT_REGION` がすべて設定されていること
- 操作対象のインスタンス ID が明示されていること
- 実行ユーザー／ロールが対象インスタンスへの必要権限（`ec2:DescribeInstances` / `ec2:StartInstances` / `ec2:StopInstances`）を保持していること
- 権限は最小限に限定すること（権限過剰禁止）

## 副作用

| 操作 | 副作用 |
| --- | --- |
| 起動（StartInstances） | インスタンスを起動する。課金が発生する |
| 停止（StopInstances） | インスタンスを停止する。EBS 費用は継続する |
| 一覧（DescribeInstances） | 副作用なし（読み取り専用） |
| 状態確認（DescribeInstanceStatus） | 副作用なし（読み取り専用） |

## 失敗時の挙動

- 認証エラー（401 / 403）: 即時失敗。エラーメッセージを出力して停止する
- インスタンス不在（InvalidInstanceID.NotFound）: 即時失敗。エラーメッセージを出力して停止する
- インスタンス状態不正（IncorrectInstanceState）: 即時失敗。現在の状態を出力して停止する
- ネットワークエラー: 即時失敗。自動リトライは行わない
- すべての失敗は明示的にエラーとして返す

## 禁止事項

- Credentials のログ出力・コメント・ファイル埋め込みを禁止する
- 自動リトライを禁止する
- インスタンスの強制終了（TerminateInstances）はこのスキルに含めない
- 汎用ロジックをこのスキルに混入することを禁止する（汎用処理は skills-core へ）
- ドメインロジックをこのスキルに混入することを禁止する（ドメイン処理は skills-domain へ）
