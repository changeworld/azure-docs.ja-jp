---
title: az postgres arc-server リファレンス
titleSuffix: Azure Arc-enabled data services
description: az postgres arc-server コマンドのリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 9a5746f4a18cf0df2045c22c58cf26a9aa44fc74
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845705"
---
# <a name="az-postgres-arc-server"></a>az postgres arc-server
## <a name="commands"></a>コマンド
| コマンド | 説明|
| --- | --- |
[az postgres arc-server create](#az-postgres-arc-server-create) | Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを作成します。
[az postgres arc-server edit](#az-postgres-arc-server-edit) | Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの構成を編集します。
[az postgres arc-server delete](#az-postgres-arc-server-delete) | Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを削除します。
[az postgres arc-server show](#az-postgres-arc-server-show) | Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの詳細を表示します。
[az postgres arc-server list](#az-postgres-arc-server-list) | Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを一覧表示します。
[az postgres arc-server endpoint](reference-az-postgres-arc-server-endpoint.md) | Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのエンドポイントを管理します。
## <a name="az-postgres-arc-server-create"></a>az postgres arc-server create
サーバー グループのパスワードを設定するには、環境変数 AZDATA_PASSWORD を設定してください
```bash
az postgres arc-server create 
```
### <a name="examples"></a>使用例
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを作成します。
```bash
az postgres arc-server create -n pg1 --k8s-namespace namespace --use-k8s
```
エンジン設定を使用して Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを作成します。 次の例はいずれも有効です。
```bash
az postgres arc-server create -n pg1 --engine-settings "key1=val1" --k8s-namespace namespace 
az postgres arc-server create -n pg1 --engine-settings "key2=val2" --k8s-namespace namespace --use-k8s
```
ボリューム要求マウントを使用して PostgreSQL サーバー グループを作成します。
```bash
az postgres arc-server create -n pg1 --volume-claim-mounts backup-pvc:backup 
```
さまざまなノード ロールに対して特定のメモリ制限を使用して PostgreSQL サーバー グループを作成します。
```bash
az postgres arc-server create -n pg1 --memory-limit "coordinator=2Gi,w=1Gi" --workers 1 --k8s-namespace namespace --use-k8s
```
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  使用できる値: json、jsonc、table、tsv。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org/](http://jmespath.org) を参照してください。
#### `--verbose`
ログの詳細レベルを上げます。 完全なデバッグ ログを表示するには `--debug` を使用します。
## <a name="az-postgres-arc-server-edit"></a>az postgres arc-server edit
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの構成を編集します。
```bash
az postgres arc-server edit 
```
### <a name="examples"></a>例
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの構成を編集します。
```bash
az postgres arc-server edit --path ./spec.json -n pg1 --k8s-namespace namespace --use-k8s
```
コーディネーター ノードのエンジン設定を使用して Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを編集します。
```bash
az postgres arc-server edit -n pg1 --coordinator-settings "key2=val2" --k8s-namespace namespace
```
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを編集し、既存のエンジン設定を新しい設定の key1=val1 に置き換えます。
```bash
az postgres arc-server edit -n pg1 --engine-settings "key1=val1" --replace-settings --k8s-namespace namespace
```
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  使用できる値: json、jsonc、table、tsv。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org/](http://jmespath.org) を参照してください。
#### `--verbose`
ログの詳細レベルを上げます。 完全なデバッグ ログを表示するには `--debug` を使用します。
## <a name="az-postgres-arc-server-delete"></a>az postgres arc-server delete
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを削除します。
```bash
az postgres arc-server delete 
```
### <a name="examples"></a>例
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを削除します。
```bash
az postgres arc-server delete -n pg1 --k8s-namespace namespace --use-k8s
```
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  使用できる値: json、jsonc、table、tsv。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org/](http://jmespath.org) を参照してください。
#### `--verbose`
ログの詳細レベルを上げます。 完全なデバッグ ログを表示するには `--debug` を使用します。
## <a name="az-postgres-arc-server-show"></a>az postgres arc-server show
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの詳細を表示します。
```bash
az postgres arc-server show 
```
### <a name="examples"></a>例
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの詳細を表示します。
```bash
az postgres arc-server show -n pg1 --k8s-namespace namespace --use-k8s
```
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  使用できる値: json、jsonc、table、tsv。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org/](http://jmespath.org) を参照してください。
#### `--verbose`
ログの詳細レベルを上げます。 完全なデバッグ ログを表示するには `--debug` を使用します。
## <a name="az-postgres-arc-server-list"></a>az postgres arc-server list
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを一覧表示します。
```bash
az postgres arc-server list 
```
### <a name="examples"></a>例
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを一覧表示します。
```bash
az postgres arc-server list --k8s-namespace namespace --use-k8s
```
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  使用できる値: json、jsonc、table、tsv。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org/](http://jmespath.org) を参照してください。
#### `--verbose`
ログの詳細レベルを上げます。 完全なデバッグ ログを表示するには `--debug` を使用します。
