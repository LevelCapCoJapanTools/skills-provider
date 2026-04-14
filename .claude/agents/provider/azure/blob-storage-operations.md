---
name: azure-blob-storage-operations
description: |
  Azure Blob Storage コンテナに対するオブジェクト操作（アップロード・ダウンロード・一覧・削除）を行うスキル。
  provider: azure
---

# Azure Blob Storage 操作スキル

## provider

azure

## 認証方法

- 環境変数 `AZURE_STORAGE_ACCOUNT` / `AZURE_STORAGE_KEY` を使用する
- または環境変数 `AZURE_STORAGE_CONNECTION_STRING` を使用する
- Managed Identity による認証も使用可能
- Credentials はコード・ファイルに直書きしない
- 認証情報は外部（Azure Key Vault / 環境変数）で管理する

## 実行条件

- `AZURE_STORAGE_ACCOUNT` と `AZURE_STORAGE_KEY`、または `AZURE_STORAGE_CONNECTION_STRING` が設定されていること
- 操作対象のコンテナ名が明示されていること
- 実行 ID／Managed Identity が対象コンテナへの必要権限（`Storage Blob Data Reader` / `Storage Blob Data Contributor`）を保持していること
- 権限は最小限に限定すること（権限過剰禁止）

## 副作用

| 操作 | 副作用 |
| --- | --- |
| アップロード（Upload Blob） | コンテナ内にブロブを新規作成または上書きする |
| 削除（Delete Blob） | コンテナからブロブを削除する（ソフト削除無効時は復元不可） |
| ダウンロード（Download Blob） | 副作用なし（読み取り専用） |
| 一覧（List Blobs） | 副作用なし（読み取り専用） |

## 失敗時の挙動

- 認証エラー（401 / 403）: 即時失敗。エラーメッセージを出力して停止する
- コンテナ不在（ContainerNotFound）: 即時失敗。エラーメッセージを出力して停止する
- ブロブ不在（BlobNotFound）: 即時失敗。エラーメッセージを出力して停止する
- ネットワークエラー: 即時失敗。自動リトライは行わない
- すべての失敗は明示的にエラーとして返す

## 禁止事項

- Credentials のログ出力・コメント・ファイル埋め込みを禁止する
- 自動リトライを禁止する
- 汎用ロジックをこのスキルに混入することを禁止する（汎用処理は skills-core へ）
- ドメインロジックをこのスキルに混入することを禁止する（ドメイン処理は skills-domain へ）
