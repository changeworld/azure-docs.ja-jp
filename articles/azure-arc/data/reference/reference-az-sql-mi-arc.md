---
title: az sql mi-arc リファレンス
titleSuffix: Azure Arc-enabled data services
description: az sql mi-arc コマンドのリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 81c8b2dbca45ac25db35fe15c2510b967bab1ae8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779331"
---
# <a name="az-sql-mi-arc"></a>az sql mi-arc
## <a name="commands"></a>コマンド
| command | 説明|
| --- | --- |
[az sql mi-arc endpoint](reference-az-sql-mi-arc-endpoint.md) | SQL エンドポイントを表示および管理します。
[az sql mi-arc create](#az-sql-mi-arc-create) | SQL マネージド インスタンスを作成します。
[az sql mi-arc edit](#az-sql-mi-arc-edit) | SQL マネージド インスタンスの構成を編集します。
[az sql mi-arc delete](#az-sql-mi-arc-delete) | SQL マネージド インスタンスを削除します。
[az sql mi-arc show](#az-sql-mi-arc-show) | SQL マネージド インスタンスの詳細を表示します。
[az sql mi-arc get-mirroring-cert](#az-sql-mi-arc-get-mirroring-cert) | sql mi から可用性グループ ミラーリング エンドポイントの証明書を取得し、ファイルに格納します。
[az sql mi-arc list](#az-sql-mi-arc-list) | SQL マネージド インスタンスの一覧を表示します。
[az sql mi-arc config](reference-az-sql-mi-arc-config.md) | 構成コマンド。
[az sql mi-arc dag](reference-az-sql-mi-arc-dag.md) | 分散型可用性グループを作成または削除します。
## <a name="az-sql-mi-arc-create"></a>az sql mi-arc create
SQL マネージド インスタンスのパスワードを設定するには、環境変数 AZDATA_PASSWORD を設定します。
```bash
az sql mi-arc create --name -n 
                     --k8s-namespace -k  
                     
[--path]  
                     
[--replicas]  
                     
[--cores-limit -c]  
                     
[--cores-request]  
                     
[--memory-limit -m]  
                     
[--memory-request]  
                     
[--storage-class-data -d]  
                     
[--storage-class-logs -g]  
                     
[--storage-class-datalogs]  
                     
[--storage-class-backups]  
                     
[--volume-size-data]  
                     
[--volume-size-logs]  
                     
[--volume-size-datalogs]  
                     
[--volume-size-backups]  
                     
[--no-wait]  
                     
[--no-external-endpoint]  
                     
[--cert-public-key-file]  
                     
[--cert-private-key-file]  
                     
[--service-cert-secret]  
                     
[--admin-login-secret]  
                     
[--license-type -l]  
                     
[--tier -t]  
                     
[--dev]  
                     
[--labels]  
                     
[--annotations]  
                     
[--service-labels]  
                     
[--service-annotations]  
                     
[--storage-labels]  
                     
[--storage-annotations]  
                     
[--use-k8s]  
                     
[--collation]  
                     
[--language]  
                     
[--agent-enabled]  
                     
[--trace-flags]
```
### <a name="examples"></a>使用例
SQL マネージド インスタンスを作成します。
```bash
az sql mi-arc create -n sqlmi1 --k8s-namespace namespace
```
HA シナリオで、3 つのレプリカを持つ SQL マネージド インスタンスを作成します。
```bash
az sql mi-arc create -n sqlmi2 --replicas 3 --k8s-namespace namespace
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--name -n`
SQL マネージド インスタンスの名前。
#### `--k8s-namespace -k`
SQL マネージド インスタンスがデプロイされる名前空間。 名前空間が指定されていない場合は、kubeconfig で定義されている名前空間が使用されます。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--path`
SQL マネージド インスタンスの json ファイルに対する azext_arcdata ファイルへのパス。
#### `--replicas`
このオプションでは、高可用性を目的として、ご利用の Kubernetes クラスターにデプロイする SQL Managed Instance レプリカの数を指定します。 使用できる値は '3' または '1' で、既定値は '1' です。
#### `--cores-limit -c`
マネージド インスタンスのコア数の制限 (整数)。
#### `--cores-request`
マネージド インスタンスのコア数の要求 (整数)。
#### `--memory-limit -m`
マネージド インスタンスの容量の制限を、整数の後に Gi (ギガバイト) を付けて表したもの。 例: 4Gi
#### `--memory-request`
マネージド インスタンスの容量の要求を、整数の後に Gi (ギガバイト) を付けて表したもの。 例: 4Gi
#### `--storage-class-data -d`
データ ファイル (.mdf、.ndf) に使用するストレージ クラス。 値を指定しないと、ストレージ クラスは指定されず、その結果、Kubernetes によって既定のストレージ クラスが使用されます。
#### `--storage-class-logs -g`
ログ (/var/log) に使用するストレージ クラス。 値を指定しないと、ストレージ クラスは指定されず、その結果、Kubernetes によって既定のストレージ クラスが使用されます。
#### `--storage-class-datalogs`
データベース ログ (.ldf) に使用するストレージ クラス。 値を指定しないと、ストレージ クラスは指定されず、その結果、Kubernetes によって既定のストレージ クラスが使用されます。
#### `--storage-class-backups`
バックアップ (/var/opt/mssql/backups) に使用するストレージ クラス。 値を指定しないと、ストレージ クラスは指定されず、その結果、Kubernetes によって既定のストレージ クラスが使用されます。
#### `--volume-size-data`
データに使用するストレージ ボリュームのサイズを示す正の数値と、その後の Ki (キロバイト)、Mi (メガバイト)、または Gi (ギガバイト)。
#### `--volume-size-logs`
ログに使用するストレージ ボリュームのサイズを示す正の数値と、その後の Ki (キロバイト)、Mi (メガバイト)、または Gi (ギガバイト)。
#### `--volume-size-datalogs`
データ ログに使用するストレージ ボリュームのサイズを示す正の数値と、その後の Ki (キロバイト)、Mi (メガバイト)、または Gi (ギガバイト)。
#### `--volume-size-backups`
バックアップに使用するストレージ ボリュームのサイズを示す正の数値と、その後の Ki (キロバイト)、Mi (メガバイト)、または Gi (ギガバイト)。
#### `--no-wait`
指定した場合、コマンドは戻る前に、インスタンスが準備完了状態になるのを待機しません。
#### `--no-external-endpoint`
指定した場合、外部サービスは作成されません。 それ以外の場合、データ コントローラーと同じサービスの種類を使用して外部サービスが作成されます。
#### `--cert-public-key-file`
SQL Server で使用される PEM 形式の証明書公開キーを含むファイルのパス。
#### `--cert-private-key-file`
SQL Server で使用される PEM 形式の証明書秘密キーを含むファイルのパス。
#### `--service-cert-secret`
生成され、SQL サービス証明書をホストする (予定の) Kubernetes シークレットの名前。
#### `--admin-login-secret`
生成され、管理者ユーザーのログイン アカウントの資格情報をホストする (予定の) Kubernetes シークレットの名前。
#### `--license-type -l`
このマネージド インスタンスに適用するライセンスの種類。 使用可能な値: BasePrice、icenseIncluded。 既定値は LicenseIncluded です。 ライセンスの種類は変更できません。
#### `--tier -t`
インスタンスの価格レベル。 使用可能な値: BusinessCritical (短縮形は bc) または GeneralPurpose (短縮形は gp)。 既定値は GeneralPurpose です。 価格レベルは変更できません。
#### `--dev`
これを指定した場合、それは dev インスタンスと見なされ、課金されません。
#### `--labels`
SQL マネージド インスタンスのラベルのコンマ区切りリスト。
#### `--annotations`
SQL マネージド インスタンスの注釈のコンマ区切りリスト。
#### `--service-labels`
すべての外部サービスに適用するラベルのコンマ区切りリスト。
#### `--service-annotations`
すべての外部サービスに適用する注釈のコンマ区切りリスト。
#### `--storage-labels`
すべての PVC に適用するラベルのコンマ区切りリスト。
#### `--storage-annotations`
すべての PVC に適用する注釈のコンマ区切りリスト。
#### `--use-k8s`
ローカル Kubernetes API を使用して SQL マネージド インスタンスを作成します。
#### `--collation`
インスタンスの SQL Server 照合順序。
#### `--language`
インスタンスでサポートされている任意の言語識別子 (LCID) に対する SQL Server のロケール。
#### `--agent-enabled`
インスタンスで SQL Server エージェントを有効にします。 既定では、無効になっています。 使用可能な値は 'true' または 'false' です。
#### `--trace-flags`
トレースフラグのコンマ区切りリスト。 規定では、フラグはありません。
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
## <a name="az-sql-mi-arc-edit"></a>az sql mi-arc edit
SQL マネージド インスタンスの構成を編集します。
```bash
az sql mi-arc edit --name -n 
                   [--k8s-namespace -k]  
                   
[--path]  
                   
[--cores-limit -c]  
                   
[--cores-request]  
                   
[--memory-limit -m]  
                   
[--memory-request]  
                   
[--no-wait]  
                   
[--dev]  
                   
[--labels]  
                   
[--annotations]  
                   
[--service-labels]  
                   
[--service-annotations]  
                   
[--agent-enabled]  
                   
[--trace-flags]  
                   
[--use-k8s]
```
### <a name="examples"></a>使用例
SQL マネージド インスタンスの構成を編集します。
```bash
az sql mi-arc edit --path ./spec.json -n sqlmi1
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--name -n`
編集されている SQL マネージド インスタンスの名前。 インスタンスを配置するときに使用した名前を変更することはできません。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--k8s-namespace -k`
SQL マネージド インスタンスが存在する名前空間。 名前空間が指定されていない場合は、kubeconfig で定義されている名前空間が使用されます。
#### `--path`
SQL マネージド インスタンスの json ファイルに対する azext_arcdata ファイルへのパス。
#### `--cores-limit -c`
マネージド インスタンスのコア数の制限 (整数)。
#### `--cores-request`
マネージド インスタンスのコア数の要求 (整数)。
#### `--memory-limit -m`
マネージド インスタンスの容量の制限を、整数の後に Gi (ギガバイト) を付けて表したもの。 例: 4Gi
#### `--memory-request`
マネージド インスタンスの容量の要求を、整数の後に Gi (ギガバイト) を付けて表したもの。 例: 4Gi
#### `--no-wait`
指定した場合、コマンドは戻る前に、インスタンスが準備完了状態になるのを待機しません。
#### `--dev`
これを指定した場合、それは dev インスタンスと見なされ、課金されません。
#### `--labels`
SQL マネージド インスタンスのラベルのコンマ区切りリスト。
#### `--annotations`
SQL マネージド インスタンスの注釈のコンマ区切りリスト。
#### `--service-labels`
すべての外部サービスに適用するラベルのコンマ区切りリスト。
#### `--service-annotations`
すべての外部サービスに適用する注釈のコンマ区切りリスト。
#### `--agent-enabled`
インスタンスで SQL Server エージェントを有効にします。 既定では、無効になっています。
#### `--trace-flags`
トレースフラグのコンマ区切りリスト。 規定では、フラグはありません。
#### `--use-k8s`
このアクションを実行するには、ローカル Kubernetes API を使用します。
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
## <a name="az-sql-mi-arc-delete"></a>az sql mi-arc delete
SQL マネージド インスタンスを削除します。
```bash
az sql mi-arc delete --name -n 
                     [--k8s-namespace -k]  
                     
[--use-k8s]
```
### <a name="examples"></a>使用例
SQL マネージド インスタンスを削除します。
```bash
az sql mi-arc delete -n sqlmi1
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--name -n`
削除する SQL マネージド インスタンスの名前。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--k8s-namespace -k`
SQL マネージド インスタンスが存在する名前空間。 名前空間が指定されていない場合は、kubeconfig で定義されている名前空間が使用されます。
#### `--use-k8s`
このアクションを実行するには、ローカル Kubernetes API を使用します。
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
## <a name="az-sql-mi-arc-show"></a>az sql mi-arc show
SQL マネージド インスタンスの詳細を表示します。
```bash
az sql mi-arc show --name -n 
                   [--path -p]  
                   
[--k8s-namespace -k]  
                   
[--use-k8s]
```
### <a name="examples"></a>使用例
SQL マネージド インスタンスの詳細を表示します。
```bash
az sql mi-arc show -n sqlmi1
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--name -n`
表示する SQL マネージド インスタンスの名前。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--path -p`
SQL マネージド インスタンスの完全な仕様が書き込まれるパス。 省略した場合、仕様は標準出力に書き込まれます。
#### `--k8s-namespace -k`
SQL マネージド インスタンスが存在する名前空間。 名前空間が指定されていない場合は、kubeconfig で定義されている名前空間が使用されます。
#### `--use-k8s`
このアクションを実行するには、ローカル Kubernetes API を使用します。
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
## <a name="az-sql-mi-arc-get-mirroring-cert"></a>az sql mi-arc get-mirroring-cert
sql mi から可用性グループ ミラーリング エンドポイントの証明書を取得し、ファイルに格納します。
```bash
az sql mi-arc get-mirroring-cert --name -n 
                                 --cert-file  
                                 
[--k8s-namespace -k]  
                                 
[--use-k8s]
```
### <a name="examples"></a>例
sqlmi1 から可用性グループ ミラーリング エンドポイントの証明書を取得し、ファイル fileName1 に格納します。
```bash
az sql mi-arc get-mirroring-cert -n sqlmi1 --cert-file fileName1
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--name -n`
SQL マネージド インスタンスの名前。
#### `--cert-file`
取得した証明書を PEM 形式で保存するためのローカル ファイル名。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--k8s-namespace -k`
SQL マネージド インスタンスが存在する名前空間。 名前空間が指定されていない場合は、kubeconfig で定義されている名前空間が使用されます。
#### `--use-k8s`
このアクションを実行するには、ローカル Kubernetes API を使用します。
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
## <a name="az-sql-mi-arc-list"></a>az sql mi-arc list
SQL マネージド インスタンスの一覧を表示します。
```bash
az sql mi-arc list [--k8s-namespace -k] 
                   [--use-k8s]
```
### <a name="examples"></a>使用例
SQL マネージド インスタンスの一覧を表示します。
```bash
az sql mi-arc list
```
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--k8s-namespace -k`
SQL マネージド インスタンスが存在する名前空間。 名前空間が指定されていない場合は、kubeconfig で定義されている名前空間が使用されます。
#### `--use-k8s`
このアクションを実行するには、ローカル Kubernetes API を使用します。
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
