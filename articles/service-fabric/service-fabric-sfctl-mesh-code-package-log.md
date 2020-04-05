---
title: Azure Service Fabric CLI- sfctl mesh code-package-log
description: Azure Service Fabric のコマンド ライン インターフェイスである sfctl について説明します。 指定したコード パッケージのログを取得するためのコマンドの一覧が含まれています。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f8665e75e4c921a3305c9965601e2ee0825c8995
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906050"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
特定のサービス レプリカの指定されたコード パッケージのコンテナーのログを取得します。

## <a name="commands"></a>コマンド

|command|説明|
| --- | --- |
| get | コンテナーからログを取得します。 |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh code-package-log get
コンテナーからログを取得します。

サービス レプリカの指定されたコード パッケージのコンテナーのログを取得します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --app-name --application-name [必須] | アプリケーションの名前です。 |
| --code-package-name           [必須] | サービスのコード パッケージの名前。 |
| --replica-name                [必須] | Service Fabric レプリカ名。 |
| --service-name                [必須] | サービスの名前。 |
| --tail | ログの末尾から表示する行数。 既定値は 100 です。 ログ全体を表示する場合は "all" です。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |


## <a name="next-steps"></a>次のステップ
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。