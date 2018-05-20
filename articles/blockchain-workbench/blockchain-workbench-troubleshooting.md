---
title: Azure Blockchain Workbench のトラブルシューティング
description: Azure Blockchain Workbench アプリケーションのトラブルシューティング方法
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 8a2715666c4fff490f5184b7b8719b412952b9bf
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2018
---
# <a name="azure-blockchain-workbench-troubleshooting"></a>Azure Blockchain Workbench のトラブルシューティング

PowerShell スクリプトを利用すると、開発時のデバッグやサポートに役立ちます。 トラブルシューティングのために、スクリプトで概要を生成し、詳細なログを収集することができます。 収集されるログの内容は次のとおりです。

* Ethereum などのブロックチェーン ネットワーク
* Blockchain Workbench マイクロサービス
* Application Insights
* Azure Monitoring (OMS)

この情報を利用して、次の手順を決定し、問題の根本原因を特定することができます。 

## <a name="troubleshooting-script"></a>トラブルシューティング スクリプト

PowerShell のトラブルシューティング スクリプトは、GitHub で入手できます。 [ZIP ファイルをダウンロード](https://github.com/Azure-Samples/blockchain/archive/master.zip)するか、GitHub からサンプルを複製します。

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>スクリプトを実行する
[!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install.md)]

`collectBlockchainWorkbenchTroubleshooting.ps1` スクリプトを実行してログを収集し、トラブルシューティング情報のフォルダーを含む ZIP ファイルを作成します。 例: 

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
このスクリプトは、次のパラメーターを受け取ります。

| パラメーター  | [説明] | 必須 |
|---------|---------|----|
| サブスクリプション ID | すべてのリソースを作成または特定する SubscriptionID。 | [はい] |
| ResourceGroupName | Blockchain Workbench がデプロイされている Azure リソース グループの名前。 | [はい] |
| OutputDirectory | 出力 .ZIP ファイルを作成するパス。 指定しない場合、既定は現在のディレクトリになります。 | いいえ 
| OmsSubscriptionId | OMS が展開されているサブスクリプション ID。 ブロックチェーン ネットワークの OMS が Blockchain Workbench のリソース グループ以外に展開されている場合にのみ、このパラメーターを渡します。| いいえ  |
| OmsResourceGroup |OMS が展開されているリソース グループ。 ブロックチェーン ネットワークの OMS が Blockchain Workbench のリソース グループ以外に展開されている場合にのみ、このパラメーターを渡します。| いいえ  |
| OmsWorkspaceName | OMS ワークスペースの名前。 ブロックチェーン ネットワークの OMS が Blockchain Workbench のリソース グループ以外に展開されている場合にのみ、このパラメーターを渡します。 | いいえ  |

## <a name="what-is-collected"></a>収集される内容

出力 ZIP ファイルのフォルダー構造は次のとおりです。

| フォルダー \ ファイル | [説明]  |
|---------|---------|
| \Summary.txt | システムの概要 |
| \metrics\blockchain | ブロックチェーンに関するメトリック |
| \metrics\workbench | ワークベンチに関するメトリック |
| \details\blockchain | ブロックチェーンに関する詳細なログ |
| \details\workbench | ワークベンチに関する詳細なログ |

概要ファイルは、アプリケーションの全体的な状態とアプリケーションの正常性のスナップショットです。 この概要から、推奨されるアクション、主なエラー、実行中のサービスに関するメタデータがわかります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench アーキテクチャ](blockchain-workbench-architecture.md)