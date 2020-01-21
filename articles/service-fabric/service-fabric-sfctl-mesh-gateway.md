---
title: Azure Service Fabric CLI- sfctl mesh gateway
description: Azure Service Fabric のコマンド ライン インターフェイスである sfctl について説明します。 Service Fabric Mesh のゲートウェイ リソースを取得および削除するためのコマンドの一覧が含まれます。
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 86e63f88cf1412b54fad6f9e386a1a5052463917
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646162"
---
# <a name="sfctl-mesh-gateway"></a>sfctl mesh gateway
メッシュ ゲートウェイ リソースを取得および削除します。

## <a name="commands"></a>コマンド

|command|[説明]|
| --- | --- |
| delete | ゲートウェイ リソースを削除します。 |
| list | すべてのゲートウェイ リソースを一覧表示します。 |
| show | 指定した名前のゲートウェイ リソースを取得します。 |

## <a name="sfctl-mesh-gateway-delete"></a>sfctl mesh gateway delete
ゲートウェイ リソースを削除します。

名前で識別されるゲートウェイ リソースを削除します。

### <a name="arguments"></a>引数

|引数|[説明]|
| --- | --- |
| --name -n (必須) | ゲートウェイ リソースの名前。 |

### <a name="global-arguments"></a>グローバル引数

|引数|[説明]|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-mesh-gateway-list"></a>sfctl mesh gateway list
すべてのゲートウェイ リソースを一覧表示します。

特定のリソース グループ内のすべてのゲートウェイ リソースに関する情報を取得します。 この情報には、ゲートウェイの説明とその他のプロパティが含まれます。

### <a name="global-arguments"></a>グローバル引数

|引数|[説明]|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-mesh-gateway-show"></a>sfctl mesh gateway show
指定した名前のゲートウェイ リソースを取得します。

指定した名前のゲートウェイ リソースに関する情報を取得します。 この情報には、ゲートウェイの説明とその他のプロパティが含まれます。

### <a name="arguments"></a>引数

|引数|[説明]|
| --- | --- |
| --name -n (必須) | ゲートウェイ リソースの名前。 |

### <a name="global-arguments"></a>グローバル引数

|引数|[説明]|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |


## <a name="next-steps"></a>次のステップ
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。