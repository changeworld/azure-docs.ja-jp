---
title: Azure Service Fabric CLI- sfctl store | Microsoft Docs
description: Service Fabric CLI sfctl store のコマンドについて説明します。
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
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 2f2da5b38ab39e3636ff0ac09ac2c5515e02b0c1
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39492337"
---
# <a name="sfctl-store"></a>sfctl store
クラスター イメージ ストアで基本的なファイル レベルの操作を実行します。

## <a name="commands"></a>コマンド

|コマンド|説明|
| --- | --- |
| delete | 既存のイメージ ストアのコンテンツを削除します。 |
| root-info | イメージ ストアのルートにあるコンテンツ情報を取得します。 |
| stat | イメージ ストアのコンテンツ情報を取得します。 |

## <a name="sfctl-store-delete"></a>sfctl store delete
既存のイメージ ストアのコンテンツを削除します。

指定したイメージ ストアの相対パス内で見つかった既存のイメージ ストアのコンテンツを削除します。 これを使用すると、アップロードされたアプリケーション パッケージを、それらがプロビジョニングされた後に削除することができます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --content-path [必須] | イメージ ストア内のファイルまたはフォルダーへのルートからの相対パス。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-store-root-info"></a>sfctl store root-info
イメージ ストアのルートにあるコンテンツ情報を取得します。

イメージ ストアのルートにあるイメージ ストアのコンテンツに関する情報を返します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-store-stat"></a>sfctl store stat
イメージ ストアのコンテンツ情報を取得します。

指定した contentPath にあるイメージ ストアのコンテンツに関する情報を返します。 contentPath の基準はイメージ ストアのルートです。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --content-path [必須] | イメージ ストア内のファイルまたはフォルダーへのルートからの相対パス。 |
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