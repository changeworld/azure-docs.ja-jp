---
title: az sql mi-arc リファレンス
titleSuffix: Azure Arc-enabled data services
description: az sql mi-arc コマンドのリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 0a98cd873de2da0daaf793a2e40df5c4d5c38285
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852296"
---
# <a name="az-sql-mi-arc"></a>az sql mi-arc
## <a name="commands"></a>コマンド
| コマンド | 説明|
| --- | --- |
[az sql mi-arc endpoint](reference-az-sql-mi-arc-endpoint.md) | SQL エンドポイントを表示および管理します。
[az sql mi-arc create](#az-sql-mi-arc-create) | SQL マネージド インスタンスを作成します。
[az sql mi-arc update](#az-sql-mi-arc-update) | SQL マネージド インスタンスの構成を更新します。
[az sql mi-arc delete](#az-sql-mi-arc-delete) | SQL マネージド インスタンスを削除します。
[az sql mi-arc show](#az-sql-mi-arc-show) | SQL マネージド インスタンスの詳細を表示します。
[az sql mi-arc get-mirroring-cert](#az-sql-mi-arc-get-mirroring-cert) | sql mi から可用性グループ ミラーリング エンドポイントの証明書を取得し、ファイルに格納します。
[az sql mi-arc upgrade](#az-sql-mi-arc-upgrade) | SQL マネージド インスタンスをアップグレードします。
[az sql mi-arc list](#az-sql-mi-arc-list) | SQL マネージド インスタンスの一覧を表示します。
[az sql mi-arc config](reference-az-sql-mi-arc-config.md) | 構成コマンド。
[az sql mi-arc dag](reference-az-sql-mi-arc-dag.md) | 分散型可用性グループを作成または削除します。
## <a name="az-sql-mi-arc-create"></a>az sql mi-arc create
SQL マネージド インスタンスのパスワードを設定するには、環境変数 AZDATA_PASSWORD を設定します。
```bash
az sql mi-arc create 
```
### <a name="examples"></a>使用例
間接接続 SQL マネージド インスタンスを作成します。
```bash
az sql mi-arc create -n sqlmi1 --k8s-namespace namespace --use-k8s
```
HA シナリオで 3 つのレプリカを含む間接接続 SQL マネージド インスタンスを作成します。
```bash
az sql mi-arc create -n sqlmi2 --replicas 3  --k8s-namespace namespace --use-k8s
```
直接接続 SQL マネージド インスタンスを作成します。
```bash
az sql mi-arc create --name name --resource-group group  --location location --subscription subscription   --custom-location custom-location
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
## <a name="az-sql-mi-arc-update"></a>az sql mi-arc update
SQL マネージド インスタンスの構成を更新します。
```bash
az sql mi-arc update 
```
### <a name="examples"></a>例
SQL マネージド インスタンスの構成を更新します。
```bash
az sql mi-arc update --path ./spec.json -n sqlmi1 --use-k8s
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
## <a name="az-sql-mi-arc-delete"></a>az sql mi-arc delete
SQL マネージド インスタンスを削除します。
```bash
az sql mi-arc delete 
```
### <a name="examples"></a>使用例
指定された名前空間を使用して SQL マネージド インスタンスを削除します。
```bash
az sql mi-arc delete --name sqlmi1 --k8s-namespace namespace --use-k8s
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
## <a name="az-sql-mi-arc-show"></a>az sql mi-arc show
SQL マネージド インスタンスの詳細を表示します。
```bash
az sql mi-arc show 
```
### <a name="examples"></a>使用例
間接接続 SQL マネージド インスタンスの詳細を表示します。
```bash
az sql mi-arc show --name sqlmi1 --k8s-namespace namespace --use-k8s
```
直接接続 SQL マネージド インスタンスの詳細を表示します。
```bash
az sql mi-arc show --name sqlmi1 --resource-group resource-group            
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
## <a name="az-sql-mi-arc-get-mirroring-cert"></a>az sql mi-arc get-mirroring-cert
sql mi から可用性グループ ミラーリング エンドポイントの証明書を取得し、ファイルに格納します。
```bash
az sql mi-arc get-mirroring-cert 
```
### <a name="examples"></a>例
sqlmi1 から可用性グループ ミラーリング エンドポイントの証明書を取得し、ファイル fileName1 に格納します。
```bash
az sql mi-arc get-mirroring-cert -n sqlmi1 --cert-file fileName1
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
## <a name="az-sql-mi-arc-upgrade"></a>az sql mi-arc upgrade
SQL マネージド インスタンスを指定された目的のバージョンにアップグレードします。  目的のバージョンが指定されていない場合は、データ コントローラーのバージョンが使用されます。
```bash
az sql mi-arc upgrade 
```
### <a name="examples"></a>例
SQL マネージド インスタンスをアップグレードします。
```bash
az sql mi-arc upgrade -n sqlmi1 -k arc --desired-version v1.1.0 --use-k8s
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
## <a name="az-sql-mi-arc-list"></a>az sql mi-arc list
SQL マネージド インスタンスの一覧を表示します。
```bash
az sql mi-arc list 
```
### <a name="examples"></a>使用例
SQL マネージド インスタンスの一覧を表示します。
```bash
az sql mi-arc list --use-k8s
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
