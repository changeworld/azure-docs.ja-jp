---
title: Azure Service Fabric CLI- sfctl mesh network | Microsoft Docs
description: Service Fabric CLI sfctl mesh network のコマンドについて説明します。
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: e92fbc3856bba69db11aba6ad5d85e814bf30a81
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035042"
---
# <a name="sfctl-mesh-network"></a>sfctl mesh network
メッシュ ネットワーク リソースを取得および削除します。

## <a name="commands"></a>command

|command|説明|
| --- | --- |
| delete | ネットワーク リソースを削除します。 |
| list | すべてのネットワーク リソースを一覧表示します。 |
| show | 指定した名前のネットワーク リソースを取得します。 |

## <a name="sfctl-mesh-network-delete"></a>sfctl mesh network delete
ネットワーク リソースを削除します。

名前で識別されるネットワーク リソースを削除します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --name -n (必須) | ネットワークの名前。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-mesh-network-list"></a>sfctl mesh network list
すべてのネットワーク リソースを一覧表示します。

特定のリソース グループ内のすべてのネットワーク リソースに関する情報を取得します。 この情報には、ネットワークの説明とその他のプロパティが含まれます。

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-mesh-network-show"></a>sfctl mesh network show
指定した名前のネットワーク リソースを取得します。

指定した名前のネットワーク リソースに関する情報を取得します。 この情報には、ネットワークの説明とその他のプロパティが含まれます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --name -n (必須) | ネットワークの名前。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |


## <a name="next-steps"></a>次の手順
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。