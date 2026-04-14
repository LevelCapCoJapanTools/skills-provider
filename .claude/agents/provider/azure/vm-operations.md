---
name: azure-vm-operations
description: |
  Azure Virtual Machine に対する操作（一覧・起動・停止・状態確認）を行うスキル。
  provider: azure
---

# Azure VM 操作スキル

## provider

azure

## 認証方法

- 環境変数 `AZURE_TENANT_ID` / `AZURE_CLIENT_ID` / `AZURE_CLIENT_SECRET` / `AZURE_SUBSCRIPTION_ID` を使用する（サービスプリンシパル認証）
- または Managed Identity による認証を使用する
- Credentials はコード・ファイルに直書きしない
- 認証情報は外部（Azure Key Vault / 環境変数）で管理する

## 実行条件

- サービスプリンシパル認証の場合: `AZURE_TENANT_ID` / `AZURE_CLIENT_ID` / `AZURE_CLIENT_SECRET` / `AZURE_SUBSCRIPTION_ID` がすべて設定されていること
- 操作対象のリソースグループ名と VM 名が明示されていること
- サービスプリンシパル／Managed Identity が対象 VM への必要権限（`Virtual Machine Contributor` または操作別最小権限）を保持していること
- 権限は最小限に限定すること（権限過剰禁止）

## 副作用

| 操作 | 副作用 |
| --- | --- |
| 起動（Start VM） | VM を起動する。コンピューティング費用が発生する |
| 停止（Deallocate VM） | VM を停止・割り当て解除する。コンピューティング費用は停止する |
| 停止（Stop VM / Stopped） | VM を停止するが割り当ては維持する。コンピューティング費用は継続する |
| 一覧（List VMs） | 副作用なし（読み取り専用） |
| 状態確認（Get VM） | 副作用なし（読み取り専用） |

## 失敗時の挙動

- 認証エラー（401 / 403）: 即時失敗。エラーメッセージを出力して停止する
- VM 不在（ResourceNotFound）: 即時失敗。エラーメッセージを出力して停止する
- 操作競合（Conflict）: 即時失敗。現在の状態を出力して停止する
- ネットワークエラー: 即時失敗。自動リトライは行わない
- すべての失敗は明示的にエラーとして返す

## 禁止事項

- Credentials のログ出力・コメント・ファイル埋め込みを禁止する
- 自動リトライを禁止する
- VM の削除（Delete VM）はこのスキルに含めない
- 汎用ロジックをこのスキルに混入することを禁止する（汎用処理は skills-core へ）
- ドメインロジックをこのスキルに混入することを禁止する（ドメイン処理は skills-domain へ）
