---
title: az arcdata dc debug リファレンス
titleSuffix: Azure Arc-enabled data services
description: az arcdata dc debug コマンドのリファレンス記事。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 219471cf793394c024061f39319a98cc89477c76
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779176"
---
# <a name="az-arcdata-dc-debug"></a>az arcdata dc debug
## <a name="commands"></a>コマンド
| command | 説明|
| --- | --- |
[az arcdata dc debug copy-logs](#az-arcdata-dc-debug-copy-logs) | ログをコピーします。
[az arcdata dc debug dump](#az-arcdata-dc-debug-dump) | メモリ ダンプをトリガーします。
## <a name="az-arcdata-dc-debug-copy-logs"></a>az arcdata dc debug copy-logs
データ コントローラーからデバッグ ログをコピーします。お使いのシステムには、Kubernetes を構成する必要があります。
```bash
az arcdata dc debug copy-logs --k8s-namespace -k 
                              [--container -c]  
                              
[--target-folder -d]  
                              
[--pod]  
                              
[--resource-kind]  
                              
[--resource-name]  
                              
[--timeout -t]  
                              
[--skip-compress]  
                              
[--exclude-dumps]  
                              
[--exclude-system-logs ]  
                              
[--use-k8s]
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--k8s-namespace -k`
データ コントローラーの Kubernetes 名前空間。
### <a name="optional-parameters"></a>省略可能なパラメーター
#### `--container -c`
類似した名前のコンテナーのログをコピーします (省略可能)。既定では、すべてのコンテナーのログがコピーされます。 複数回指定することはできません。 複数回指定した場合は、最後のものが使用されます
#### `--target-folder -d`
ログのコピー先となるターゲット フォルダーのパス。 省略可能。既定では、ローカル フォルダー内に結果が作成されます。  複数回指定することはできません。 複数回指定した場合は、最後のものが使用されます
#### `--pod`
類似した名前のポッドのログをコピーします。 省略可能。既定では、すべてのポッドのログがコピーされます。 複数回指定することはできません。 複数回指定した場合は、最後のものが使用されます
#### `--resource-kind`
特定の種類のリソースのログをコピーします。 複数回指定することはできません。 複数回指定した場合は、最後のものが使用されます。 指定する場合は、リソースを識別する --resource-name も指定する必要があります。
#### `--resource-name`
指定した名前のリソースのログをコピーします。 複数回指定することはできません。 複数回指定した場合は、最後のものが使用されます。 指定する場合は、リソースを識別する --resource-kind も指定する必要があります。
#### `--timeout -t`
コマンドが完了するまで待機する秒数。 既定値は 0 (無制限) です
#### `--skip-compress`
結果フォルダーの圧縮をスキップするかどうかを指定します。 既定値は False で、結果フォルダーを圧縮します。
#### `--exclude-dumps`
結果フォルダーからダンプを除外するかどうかを指定します。 既定値は False で、ダンプを含めます。
#### `--exclude-system-logs `
システム ログをコレクションから除外するかどうかを指定します。 既定値は False で、システム ログを含めます。
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
## <a name="az-arcdata-dc-debug-dump"></a>az arcdata dc debug dump
メモリ ダンプをトリガーし、それをコンテナーからコピーします。システム上に Kubernetes の構成が必要です。
```bash
az arcdata dc debug dump --k8s-namespace -k 
                         [--container -c]  
                         
[--target-folder -d]  
                         
[--use-k8s]
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--k8s-namespace -k`
データ コントローラーの Kubernetes 名前空間。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--container -c`
実行中のプロセスをダンプするためにトリガーされるターゲット コンテナー。
`controller`
#### `--target-folder -d`
ダンプをコピーするターゲット フォルダー。`./output/dump`
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
