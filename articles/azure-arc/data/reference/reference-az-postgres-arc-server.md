---
title: az postgres arc-server リファレンス
titleSuffix: Azure Arc-enabled data services
description: az postgres arc-server コマンドのリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: c58819707eb5987bddeb0d8983c29e7519b69818
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779458"
---
# <a name="az-postgres-arc-server"></a>az postgres arc-server
## <a name="commands"></a>コマンド
| command | 説明|
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
az postgres arc-server create --name -n 
                              [--path]  
                              
[--k8s-namespace -k]  
                              
[--cores-limit]  
                              
[--cores-request]  
                              
[--memory-limit]  
                              
[--memory-request]  
                              
[--storage-class-data]  
                              
[--storage-class-logs]  
                              
[--storage-class-backups]  
                              
[--volume-claim-mounts]  
                              
[--extensions]  
                              
[--volume-size-data]  
                              
[--volume-size-logs]  
                              
[--volume-size-backups]  
                              
[--workers -w]  
                              
[--engine-version]  
                              
[--no-external-endpoint]  
                              
[--port]  
                              
[--no-wait]  
                              
[--engine-settings]  
                              
[--coordinator-settings]  
                              
[--worker-settings]  
                              
[--use-k8s]
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
### <a name="required-parameters"></a>必須のパラメーター
#### `--name -n`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの名前。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--path`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのソース json ファイルのパス。 これは省略可能です。
#### `--k8s-namespace -k`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループがデプロイされる Kubernetes 名前空間。 名前空間が指定されていない場合は、kubeconfig で定義されている名前空間が使用されます。
#### `--cores-limit`
ノードごとに使用できる Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの CPU コアの最大数。 少数のコアがサポートされています。 必要に応じて、値を持つロールのコンマ区切りリストを <role>= の形式で指定できます。<value>. 有効なロール: "coordinator" または "c"、"worker" または "w"。 ロールが指定されていない場合、設定は PostgreSQL Hyperscale サーバーグ ループのすべてのノードに適用されます。
#### `--cores-request`
サービスをスケジュールするためにノードごとに使用できる必要がある CPU コアの最小数。 少数のコアがサポートされています。 必要に応じて、値を持つロールのコンマ区切りリストを <role>= の形式で指定できます。<value>. 有効なロール: "coordinator" または "c"、"worker" または "w"。 ロールが指定されていない場合、設定は PostgreSQL Hyperscale サーバーグ ループのすべてのノードに適用されます。
#### `--memory-limit`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのメモリ制限。数値の後に Ki (キロバイト)、Mi (メガバイト)、または Gi (ギガバイト) が続きます。 必要に応じて、値を持つロールのコンマ区切りリストを <role>= の形式で指定できます。<value>. 有効なロール: "coordinator" または "c"、"worker" または "w"。 ロールが指定されていない場合、設定は PostgreSQL Hyperscale サーバーグ ループのすべてのノードに適用されます。
#### `--memory-request`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのメモリ要求。数値の後に Ki (キロバイト)、Mi (メガバイト)、または Gi (ギガバイト) が続きます。 必要に応じて、値を持つロールのコンマ区切りリストを <role>= の形式で指定できます。<value>. 有効なロール: "coordinator" または "c"、"worker" または "w"。 ロールが指定されていない場合、設定は PostgreSQL Hyperscale サーバーグ ループのすべてのノードに適用されます。
#### `--storage-class-data`
データ永続ボリュームに使用するストレージ クラス。
#### `--storage-class-logs`
ログ永続ボリュームに使用するストレージ クラス。
#### `--storage-class-backups`
バックアップ永続ボリュームに使用するストレージ クラス。
#### `--volume-claim-mounts`
ボリューム要求マウントのコンマ区切りの一覧。 ボリューム要求マウントは、(同じ名前空間内の) 既存の永続的ボリューム要求と、コロンで区切られたボリュームの種類 (およびボリュームの種類に応じたオプションのメタデータ) のペアです。永続的ボリュームは、PostgreSQL サーバー グループの各ポッドにマウントされます。 マウント パスは、ボリュームの種類によって異なる場合があります。
#### `--extensions`
起動時に読み込む必要のある Postgres 拡張機能のコンマ区切りのリスト。 サポートされている値については、Postgres のドキュメントを参照してください。
#### `--volume-size-data`
データに使用するストレージ ボリュームのサイズを示す正の数値と、その後の Ki (キロバイト)、Mi (メガバイト)、または Gi (ギガバイト)。
#### `--volume-size-logs`
ログに使用するストレージ ボリュームのサイズを示す正の数値と、その後の Ki (キロバイト)、Mi (メガバイト)、または Gi (ギガバイト)。
#### `--volume-size-backups`
バックアップに使用するストレージ ボリュームのサイズを示す正の数値と、その後の Ki (キロバイト)、Mi (メガバイト)、または Gi (ギガバイト)。
#### `--workers -w`
サーバー グループにプロビジョニングするワーカー ノードの数。 プレビューでは、ワーカー ノードの数を減らすことはサポートされていません。 詳細については、ドキュメントを参照してください。
#### `--engine-version`
11 または 12 にする必要があります。 既定値は 12 です。
`12`
#### `--no-external-endpoint`
指定した場合、外部サービスは作成されません。 それ以外の場合、データ コントローラーと同じサービスの種類を使用して外部サービスが作成されます。
#### `--port`
省略可能。
#### `--no-wait`
指定した場合、コマンドは戻る前に、インスタンスが準備完了状態になるのを待機しません。
#### `--engine-settings`
"key1=val1, key2=val2" という形式の、Postgres エンジン設定のコンマ区切りリスト。
#### `--coordinator-settings`
'coordinator' ノード ロールに適用される、'key1=val1,key2=val2' という形式の Postgres エンジン設定のコンマ区切りのリスト。 ノード ロール固有の設定が指定されると、既定の設定は無視され、ここで指定した設定でオーバーライドされます。
#### `--worker-settings`
'worker' ノード ロールに適用される、'key1=val1,key2=val2' という形式の Postgres エンジン設定のコンマ区切りのリスト。 ノード ロール固有の設定が指定されると、既定の設定は無視され、ここで指定した設定でオーバーライドされます。
#### `--use-k8s`
このアクションを実行するには、ローカルの Kubernetes API を使用します。
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  json、jsonc、none、table、tsv、yaml、yamlc のいずれかの値を指定できます。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org](http://jmespath.org) を参照してください。
#### `--subscription`
サブスクリプションの名前または ID。 `az account set -s NAME_OR_ID` を使用して、既定のサブスクリプションを構成できます。
#### `--verbose`
ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。
## <a name="az-postgres-arc-server-edit"></a>az postgres arc-server edit
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの構成を編集します。
```bash
az postgres arc-server edit --name -n 
                            [--k8s-namespace -k]  
                            
[--path]  
                            
[--workers -w]  
                            
[--cores-limit]  
                            
[--cores-request]  
                            
[--memory-limit]  
                            
[--memory-request]  
                            
[--extensions]  
                            
[--port]  
                            
[--no-wait]  
                            
[--engine-settings]  
                            
[--replace-settings]  
                            
[--coordinator-settings]  
                            
[--worker-settings]  
                            
[--admin-password]  
                            
[--use-k8s]
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
### <a name="required-parameters"></a>必須のパラメーター
#### `--name -n`
編集する Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの名前。 インスタンスを配置するときに使用した名前を変更することはできません。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--k8s-namespace -k`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループがデプロイされる Kubernetes 名前空間。 名前空間が指定されていない場合は、kubeconfig で定義されている名前空間が使用されます。
#### `--path`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのソース json ファイルのパス。 これは省略可能です。
#### `--workers -w`
サーバー グループにプロビジョニングするワーカー ノードの数。 プレビューでは、ワーカー ノードの数を減らすことはサポートされていません。 詳細については、ドキュメントを参照してください。
#### `--cores-limit`
ノードごとに使用できる Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの CPU コアの最大数。少数のコアがサポートされています。 cores_limit を削除するには、その値を空の文字列として指定します。 必要に応じて、値を持つロールのコンマ区切りリストを <role>= の形式で指定できます。<value>. 有効なロール: "coordinator" または "c"、"worker" または "w"。 ロールが指定されていない場合、設定は PostgreSQL Hyperscale サーバーグ ループのすべてのノードに適用されます。
#### `--cores-request`
サービスをスケジュールするためにノードごとに使用できる必要がある CPU コアの最小数。少数のコアがサポートされています。 cores_request を削除するには、その値を空の文字列として指定します。 必要に応じて、値を持つロールのコンマ区切りリストを <role>= の形式で指定できます。<value>. 有効なロール: "coordinator" または "c"、"worker" または "w"。 ロールが指定されていない場合、設定は PostgreSQL Hyperscale サーバーグ ループのすべてのノードに適用されます。
#### `--memory-limit`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのメモリ制限。数値の後に Ki (キロバイト)、Mi (メガバイト)、または Gi (ギガバイト) が続きます。 memory_limit を削除するには、その値を空の文字列として指定します。 必要に応じて、値を持つロールのコンマ区切りリストを <role>= の形式で指定できます。<value>. 有効なロール: "coordinator" または "c"、"worker" または "w"。 ロールが指定されていない場合、設定は PostgreSQL Hyperscale サーバーグ ループのすべてのノードに適用されます。
#### `--memory-request`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのメモリ要求。数値の後に Ki (キロバイト)、Mi (メガバイト)、または Gi (ギガバイト) が続きます。 memory_request を削除するには、その値を空の文字列として指定します。 必要に応じて、値を持つロールのコンマ区切りリストを <role>= の形式で指定できます。<value>. 有効なロール: "coordinator" または "c"、"worker" または "w"。 ロールが指定されていない場合、設定は PostgreSQL Hyperscale サーバーグ ループのすべてのノードに適用されます。
#### `--extensions`
起動時に読み込む必要のある Postgres 拡張機能のコンマ区切りのリスト。 サポートされている値については、Postgres のドキュメントを参照してください。
#### `--port`
省略可能。
#### `--no-wait`
指定した場合、コマンドは戻る前に、インスタンスが準備完了状態になるのを待機しません。
#### `--engine-settings`
"key1=val1, key2=val2" という形式の、Postgres エンジン設定のコンマ区切りリスト。 指定した設定は、既存の設定とマージされます。 設定を削除するには、"removedKey=" のように空の値を指定します。 再起動が必要なエンジン設定を変更すると、すぐに設定を適用するためにサービスが再起動されます。
#### `--replace-settings`
--engine-settings と共に指定すると、既存のすべてのカスタム エンジンの設定が設定と値の新しいセットに置き換えられます。
#### `--coordinator-settings`
'coordinator' ノード ロールに適用される、'key1=val1,key2=val2' という形式の Postgres エンジン設定のコンマ区切りのリスト。 ノード ロール固有の設定が指定されると、既定の設定は無視され、ここで指定した設定でオーバーライドされます。
#### `--worker-settings`
'worker' ノード ロールに適用される、'key1=val1,key2=val2' という形式の Postgres エンジン設定のコンマ区切りのリスト。 ノード ロール固有の設定が指定されると、既定の設定は無視され、ここで指定した設定でオーバーライドされます。
#### `--admin-password`
指定すると、Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの管理者パスワードは、AZDATA_PASSWORD 環境変数が存在する場合はその値に設定され、それ以外の場合はプロンプトが表示されます。
#### `--use-k8s`
このアクションを実行するには、ローカルの Kubernetes API を使用します。
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  json、jsonc、none、table、tsv、yaml、yamlc のいずれかの値を指定できます。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org](http://jmespath.org) を参照してください。
#### `--subscription`
サブスクリプションの名前または ID。 `az account set -s NAME_OR_ID` を使用して、既定のサブスクリプションを構成できます。
#### `--verbose`
ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。
## <a name="az-postgres-arc-server-delete"></a>az postgres arc-server delete
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを削除します。
```bash
az postgres arc-server delete --name -n 
                              [--k8s-namespace -k]  
                              
[--force -f]  
                              
[--use-k8s]
```
### <a name="examples"></a>例
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを削除します。
```bash
az postgres arc-server delete -n pg1 --k8s-namespace namespace --use-k8s
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--name -n`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの名前。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--k8s-namespace -k`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループがデプロイされる Kubernetes 名前空間。 名前空間が指定されていない場合は、kubeconfig で定義されている名前空間が使用されます。
#### `--force -f`
確認せずに Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを強制的に削除します。
#### `--use-k8s`
このアクションを実行するには、ローカルの Kubernetes API を使用します。
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  json、jsonc、none、table、tsv、yaml、yamlc のいずれかの値を指定できます。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org](http://jmespath.org) を参照してください。
#### `--subscription`
サブスクリプションの名前または ID。 `az account set -s NAME_OR_ID` を使用して、既定のサブスクリプションを構成できます。
#### `--verbose`
ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。
## <a name="az-postgres-arc-server-show"></a>az postgres arc-server show
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの詳細を表示します。
```bash
az postgres arc-server show --name -n 
                            [--k8s-namespace -k]  
                            
[--path]  
                            
[--use-k8s]
```
### <a name="examples"></a>例
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの詳細を表示します。
```bash
az postgres arc-server show -n pg1 --k8s-namespace namespace --use-k8s
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--name -n`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの名前。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--k8s-namespace -k`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループがデプロイされる Kubernetes 名前空間。 名前空間が指定されていない場合は、kubeconfig で定義されている名前空間が使用されます。
#### `--path`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの完全な仕様を書き込む先のパス。 省略した場合、仕様は標準出力に書き込まれます。
#### `--use-k8s`
このアクションを実行するには、ローカルの Kubernetes API を使用します。
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  json、jsonc、none、table、tsv、yaml、yamlc のいずれかの値を指定できます。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org](http://jmespath.org) を参照してください。
#### `--subscription`
サブスクリプションの名前または ID。 `az account set -s NAME_OR_ID` を使用して、既定のサブスクリプションを構成できます。
#### `--verbose`
ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。
## <a name="az-postgres-arc-server-list"></a>az postgres arc-server list
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを一覧表示します。
```bash
az postgres arc-server list [--k8s-namespace -k] 
                            [--use-k8s]
```
### <a name="examples"></a>例
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを一覧表示します。
```bash
az postgres arc-server list --k8s-namespace namespace --use-k8s
```
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--k8s-namespace -k`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループがデプロイされる Kubernetes 名前空間。 名前空間が指定されていない場合は、kubeconfig で定義されている名前空間が使用されます。
#### `--use-k8s`
このアクションを実行するには、ローカルの Kubernetes API を使用します。
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  json、jsonc、none、table、tsv、yaml、yamlc のいずれかの値を指定できます。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org](http://jmespath.org) を参照してください。
#### `--subscription`
サブスクリプションの名前または ID。 `az account set -s NAME_OR_ID` を使用して、既定のサブスクリプションを構成できます。
#### `--verbose`
ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。
