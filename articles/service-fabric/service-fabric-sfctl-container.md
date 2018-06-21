---
title: Azure Service Fabric CLI- sfctl container | Microsoft Docs
description: Service Fabric CLI sfctl container のコマンドについて説明します。
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: cd3725ac547a1ed1fd9207dc48ba3b6227e85ef1
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763956"
---
# <a name="sfctl-container"></a>sfctl container
クラスター ノード上でコンテナー関連のコマンドを実行します。

## <a name="commands"></a>コマンド

|コマンド|説明|
| --- | --- |
| invoke-api | コンテナー REST API を呼び出します。 |
| ログ | コンテナー ログを取得します。 |

## <a name="sfctl-container-invoke-api"></a>sfctl container invoke-api
コンテナー REST API を呼び出します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --application-id           [必須] | アプリケーション ID。 |
| --code-package-instance-id [必須] | コード パッケージ インスタンス ID。"service code-package-list" を使用して取得できます。 |
| --code-package-name        [必須] | コード パッケージ名。 |
| --container-api-uri-path   [必須] | コンテナー REST API URI のパス。コンテナー名/ID の代わりに "{ID}" を使用します。 |
| --node-name                [必須] | ノード名。 |
| --service-manifest-name    [必須] | サービス マニフェスト名。 |
| --container-api-body | コンテナー REST API の HTTP 要求本文。 |
| --container-api-content-type | コンテナー REST API のコンテンツの種類。既定値は "application/json" です。 |
| --container-api-http-verb | コンテナー REST API の HTTP 動詞。既定値は GET です。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-container-logs"></a>sfctl container logs
コンテナー ログを取得します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --application-id           [必須] | アプリケーション ID。 |
| --code-package-instance-id [必須] | コード パッケージ インスタンス ID。"service code-package-list" を使用して取得できます。 |
| --code-package-name        [必須] | コード パッケージ名。 |
| --node-name                [必須] | ノード名。 |
| --service-manifest-name    [必須] | サービス マニフェスト名。 |
| --tail | ログの末尾から、この行数のログのみを返します。 整数を指定するか、または all を指定するとすべてのログ行が出力されます。 既定値は "all" です。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="next-steps"></a>次の手順
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。