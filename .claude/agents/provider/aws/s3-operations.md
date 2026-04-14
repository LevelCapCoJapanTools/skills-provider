---
name: aws-s3-operations
description: |
  AWS S3 バケットに対するオブジェクト操作（アップロード・ダウンロード・一覧・削除）を行うスキル。
  provider: aws
---

# AWS S3 操作スキル

## provider

aws

## 認証方法

- 環境変数 `AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY` / `AWS_DEFAULT_REGION` を使用する
- Credentials はコード・ファイルに直書きしない
- IAM ロールによるインスタンスプロファイル認証も使用可能
- 認証情報は外部（Secrets Manager / 環境変数）で管理する

## 実行条件

- `AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY` / `AWS_DEFAULT_REGION` がすべて設定されていること
- 操作対象の S3 バケットが存在すること
- 実行ユーザー／ロールが対象バケットへの必要権限（`s3:GetObject` / `s3:PutObject` / `s3:DeleteObject` / `s3:ListBucket`）を保持していること
- 権限は最小限に限定すること（権限過剰禁止）

## 副作用

| 操作 | 副作用 |
| --- | --- |
| アップロード（PutObject） | バケット内にオブジェクトを新規作成または上書きする |
| 削除（DeleteObject） | バケットからオブジェクトを削除する（バージョニング無効時は復元不可） |
| ダウンロード（GetObject） | 副作用なし（読み取り専用） |
| 一覧（ListBucket） | 副作用なし（読み取り専用） |

## 失敗時の挙動

- 認証エラー（401 / 403）: 即時失敗。エラーメッセージを出力して停止する
- バケット不在（NoSuchBucket）: 即時失敗。エラーメッセージを出力して停止する
- オブジェクト不在（NoSuchKey）: 即時失敗。エラーメッセージを出力して停止する
- ネットワークエラー: 即時失敗。自動リトライは行わない
- すべての失敗は明示的にエラーとして返す

## 禁止事項

- Credentials のログ出力・コメント・ファイル埋め込みを禁止する
- 自動リトライを禁止する
- 汎用ロジックをこのスキルに混入することを禁止する（汎用処理は skills-core へ）
- ドメインロジックをこのスキルに混入することを禁止する（ドメイン処理は skills-domain へ）
