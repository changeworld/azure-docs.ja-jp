---
title: Azure Service Fabric CLI- sfctl mesh volume
description: Azure Service Fabric のコマンド ライン インターフェイスである sfctl について説明します。 ボリューム リソースを取得および削除するためのコマンドの一覧が含まれています。
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: e77c98bf384278b0bf27bb0f30f425375093ffab
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645312"
---
# <a name="sfctl-mesh-volume"></a>sfctl mesh volume
ボリューム リソースを取得および削除します。

## <a name="commands"></a>コマンド

|command|[説明]|
| --- | --- |
| delete | ボリューム リソースを削除します。 |
| list | すべてのボリューム リソースを一覧表示します。 |
| show | 指定した名前のボリューム リソースを取得します。 |

## <a name="sfctl-mesh-volume-delete"></a>sfctl mesh volume delete
ボリューム リソースを削除します。

名前で識別されるボリューム リソースを削除します。

### <a name="arguments"></a>引数

|引数|[説明]|
| --- | --- |
| --name -n (必須) | ボリュームの名前。 |

### <a name="global-arguments"></a>グローバル引数

|引数|[説明]|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-mesh-volume-list"></a>sfctl mesh volume list
すべてのボリューム リソースを一覧表示します。

特定のリソース グループ内のすべてのボリューム リソースに関する情報を取得します。 この情報には、ボリュームの説明とその他のプロパティが含まれます。

### <a name="global-arguments"></a>グローバル引数

|引数|[説明]|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-mesh-volume-show"></a>sfctl mesh volume show
指定した名前のボリューム リソースを取得します。

指定した名前のボリューム リソースに関する情報を取得します。 この情報には、ボリュームの説明とその他のプロパティが含まれます。

### <a name="arguments"></a>引数

|引数|[説明]|
| --- | --- |
| --name -n (必須) | ボリュームの名前。 |

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