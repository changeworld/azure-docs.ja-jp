---
title: az arcdata dc リファレンス
titleSuffix: Azure Arc-enabled data services
description: az arcdata dc コマンドのリファレンス記事。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 23cbe9c98e3c0ad86704061dadfad0e83f627ac3
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852962"
---
# <a name="az-arcdata-dc"></a>az arcdata dc
## <a name="commands"></a>コマンド
| コマンド | 説明|
| --- | --- |
[az arcdata dc create](#az-arcdata-dc-create) | データ コントローラーを作成します。
[az arcdata dc upgrade](#az-arcdata-dc-upgrade) | データ コントローラーをアップグレードします。
[az arcdata dc list-upgrades](#az-arcdata-dc-list-upgrades) | 使用可能なアップグレード バージョンを一覧表示します。
[az arcdata dc delete](#az-arcdata-dc-delete) | データ コントローラーを削除します。
[az arcdata dc endpoint](reference-az-arcdata-dc-endpoint.md) | エンドポイント コマンド。
[az arcdata dc status](reference-az-arcdata-dc-status.md) | 状態コマンド。
[az arcdata dc config](reference-az-arcdata-dc-config.md) | 構成コマンド。
[az arcdata dc debug](reference-az-arcdata-dc-debug.md) | データ コントローラーをデバッグします。
[az arcdata dc export](#az-arcdata-dc-export) | メトリック、ログ、使用状況をエクスポートします。
[az arcdata dc upload](#az-arcdata-dc-upload) | エクスポートされたデータ ファイルをアップロードします。
## <a name="az-arcdata-dc-create"></a>az arcdata dc create
データ コントローラーを作成します - 次の環境変数によって指定される監視ダッシュボードの資格情報と共に、システムで kube 構成が必要です - ログ ダッシュボード用の AZDATA_LOGSUI_USERNAME と AZDATA_LOGSUI_PASSWORD、およびメトリック ダッシュボード用の AZDATA_METRICSUI_USERNAME と AZDATA_METRICSUI_PASSWORD。 また、いずれかの環境変数のセットが欠落している場合は、フォールバックとして AZDATA_USERNAME と AZDATA_PASSWORD が使用されます。
```bash
az arcdata dc create 
```
### <a name="examples"></a>例
間接接続データ コントローラーをデプロイします。
```bash
az arcdata dc create --name name --k8s-namespace namespace --connectivity-mode indirect --resource-group group  --location location --subscription subscription --use-k8s
```
直接接続データ コントローラーをデプロイします。
```bash
az arcdata dc create --name name  --connectivity-mode direct --resource-group group  --location location --subscription subscription  --custom-location custom-location         
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
## <a name="az-arcdata-dc-upgrade"></a>az arcdata dc upgrade
指定された目的のバージョンにデータ コントローラーをアップグレードします。  目的のバージョンが指定されていない場合、最新バージョンへのアップグレードが試行されます。 目的のバージョンが不明な場合は、list-upgrades コマンドを使用して、使用可能なバージョンを表示できます。また、--dry-run 引数を使用して、使用されるバージョンを表示できます
```bash
az arcdata dc upgrade 
```
### <a name="examples"></a>例
データ コントローラーのアップグレード。
```bash
az arcdata dc upgrade --k8s-namespace namespace --use-k8s
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
## <a name="az-arcdata-dc-list-upgrades"></a>az arcdata dc list-upgrades
Docker イメージ レジストリでアップグレードに使用できるバージョンを一覧表示します。 - 次の環境変数と共にシステムで kube 構成が必要です: ['AZDATA_USERNAME', 'AZDATA_PASSWORD']。
```bash
az arcdata dc list-upgrades 
```
### <a name="examples"></a>例
データ コントローラーのアップグレード。
```bash
az arcdata dc list-upgrades --k8s-namespace namespace --use-k8s            
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
## <a name="az-arcdata-dc-delete"></a>az arcdata dc delete
データ コントローラーを削除します。Kube 構成がシステムに必要です。
```bash
az arcdata dc delete 
```
### <a name="examples"></a>使用例
間接接続データ コントローラーを削除します。
```bash
az arcdata dc delete --name name --k8s-namespace namespace --use-k8s
```
直接接続データ コントローラーを削除します。
```bash
az arcdata dc delete --name name --resource-group resource-group            
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
## <a name="az-arcdata-dc-export"></a>az arcdata dc export
メトリック、ログ、または使用状況をファイルにエクスポートします。
```bash
az arcdata dc export 
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
## <a name="az-arcdata-dc-upload"></a>az arcdata dc upload
データ コントローラーからエクスポートされたデータ ファイルを Azure にアップロードします。
```bash
az arcdata dc upload 
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
