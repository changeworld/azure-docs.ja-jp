---
title: az sql mi-arc dag リファレンス
titleSuffix: Azure Arc-enabled data services
description: az sql mi-arc dag コマンドのリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 4ebd6e71c14c9a319e99e68eca15f19a21d52033
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781242"
---
# <a name="az-sql-mi-arc-dag"></a>az sql mi-arc dag
## <a name="commands"></a>コマンド
| command | 説明|
| --- | --- |
[az sql mi-arc dag create](#az-sql-mi-arc-dag-create) | 分散型可用性グループのカスタム リソースを作成します
[az sql mi-arc dag delete](#az-sql-mi-arc-dag-delete) | sqlmi インスタンス上の分散可用性グループのカスタム リソースを削除します。
[az sql mi-arc dag show](#az-sql-mi-arc-dag-show) | 分散可用性グループのカスタム リソースを表示します。
## <a name="az-sql-mi-arc-dag-create"></a>az sql mi-arc dag create
分散型可用性グループを作成するための分散型可用性グループのカスタム リソースを作成します
```bash
az sql mi-arc dag create --name -n 
                         --dag-name -d  
                         
--local-instance-name -l  
                         
--local-primary -p  
                         
--remote-instance-name -r  
                         
--remote-mirroring-url -u  
                         
--remote-mirroring-cert-file -f  
                         
[--k8s-namespace -k]  
                         
[--path]  
                         
[--use-k8s]
```
### <a name="examples"></a>例
例 1 - 分散可用性グループのカスタム リソース dagCr1 を作成して、ローカル sqlmi インスタンス sqlmi1 とリモート sqlmi インスタンス sqlmi2 の間に分散可用性グループ dagName1 を作成します。 それには、リモート sqlmi プライマリ ミラー remotePrimary:5022 とリモート sqlmi ミラー エンドポイント証明書ファイル ./sqlmi2.cer が必要です。
```bash
az sql mi-arc dag create --name=dagCr1 --dag-name=dagName1  --local-instance-name=sqlmi1 --local-primary=true --remote-instance-name=sqlmi2  --remote-mirroring-url==remotePrimary:5022 --remote-mirroing-cert-file="./sqlmi2.cer"
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--name -n`
分散型可用性グループ リソースの名前。
#### `--dag-name -d`
この SQL マネージド インスタンスの分散可用性グループの名前です。 ローカルとリモートの両方で、同じ名前を使用する必要があります。
#### `--local-instance-name -l`
ローカル SQL マネージド インスタンスの名前
#### `--local-primary -p`
True は、ローカルの SQL マネージド インスタンスが Geo プライマリであることを示します。 False は、ローカルの SQL マネージド インスタンスが Geo セカンダリであることを示します
#### `--remote-instance-name -r`
リモート SQL マネージド インスタンスまたはリモート SQL 可用性グループの名前
#### `--remote-mirroring-url -u`
リモート SQL マネージド インスタンスまたはリモート SQL 可用性グループのミラーリング エンドポイント URL
#### `--remote-mirroring-cert-file -f`
リモート SQL マネージド インスタンスまたはリモート SQL 可用性グループのミラーリング エンドポイントの公開証明書のファイル名。 サポートされているのは PEM 形式のみです
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--k8s-namespace -k`
SQL マネージド インスタンスが存在する名前空間。 名前空間が指定されていない場合は、kubeconfig で定義されている名前空間が使用されます。
#### `--path`
カスタム リソース仕様へのパス (つまり、custom/spec.json)
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
## <a name="az-sql-mi-arc-dag-delete"></a>az sql mi-arc dag delete
sqlmi インスタンス上の分散可用性グループのカスタム リソースを削除して、分散可用性グループを削除します。 カスタム リソース名が必要です
```bash
az sql mi-arc dag delete --name 
                         [--k8s-namespace -k]  
                         
[--use-k8s]
```
### <a name="examples"></a>例
例 1 - dagCr1 という名前の分散型可用性グループ リソースを削除します。
```bash
az sql mi-arc dag delete --name=dagCr1
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--name`
分散型可用性グループ リソースの名前。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--k8s-namespace -k`
SQL マネージド インスタンスが存在する名前空間。 名前空間が指定されていない場合は、kubeconfig で定義されている名前空間が使用されます。
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
## <a name="az-sql-mi-arc-dag-show"></a>az sql mi-arc dag show
分散可用性グループのカスタム リソースを表示します。 カスタム リソース名が必要です
```bash
az sql mi-arc dag show --name 
                       [--k8s-namespace -k]  
                       
[--use-k8s]
```
### <a name="examples"></a>例
例 1 - dagCr1 という名前の分散型可用性グループ リソースを表示します。
```bash
az sql mi-arc dag show --name=dagCr1
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--name`
分散型可用性グループ リソースの名前。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--k8s-namespace -k`
SQL マネージド インスタンスが存在する名前空間。 名前空間が指定されていない場合は、kubeconfig で定義されている名前空間が使用されます。
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
