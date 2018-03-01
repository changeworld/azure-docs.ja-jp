---
title: "Azure Network Watcher との接続を監視する - Azure Portal | Microsoft Docs"
description: "Azure Portal を使用して、Azure Network Watcher とのネットワーク接続を監視する方法について説明します。"
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2018
ms.author: jdial
ms.openlocfilehash: beaa458d727a05eccf496933deb3c998828868cd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>Azure Portal を使用して Azure Network Watcher とのネットワーク接続を監視する

接続モニターを使用して、Azure 仮想マシンと IP アドレス間のネットワーク接続を監視する方法について説明します。 IP アドレスは、別の Azure リソース、インターネット リソース、またはオンプレミス リソースに割り当てることができます。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次の前提条件を満たす必要があります。

* 接続を監視するリージョンの Network Watcher のインスタンス。 まだ Azure Network Watcher インスタンスを作成していない場合は、「[Azure Network Watcher のインスタンスの作成](network-watcher-create.md)」の手順を実行して作成できます。
* 監視する仮想マシン。
* 接続を監視する仮想マシンに `AzureNetworkWatcherExtension` をインストールしておきます。 Windows 仮想マシンに拡張機能をインストールする方法については、「[Windows 用 Network Watcher Agent 仮想マシン拡張機能](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)」を、Linux 仮想マシンに拡張機能をインストールする方法については、「[Linux 用 Network Watcher Agent 仮想マシン拡張機能](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)」を参照してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン 

[Azure ポータル](http://portal.azure.com)にサインインします。

## <a name="create-a-connection-monitor"></a>接続モニターを作成する

1. ポータルの左側にある **[その他のサービス]** を選択します。
2. 「*network watcher*」の入力を始めます。 検索結果に **[Network Watcher]** が表示されたら、それを選択します。
3. **[監視]** の **[接続モニター (プレビュー)]** を選択します。 プレビュー リリース段階にある機能には、一般リリースの機能と同じレベルの信頼性とリージョンの可用性はありません。
4. **[+ 追加]**を選択します。
5. 監視する接続の適切な情報を入力するか選択して、**[追加]** を選択します。 この例では、*myVmSource* 仮想マシンと *myVmDestination* 仮想マシン間の接続はポート 80 を介して監視されます。
    
    |  Setting                                 |  値               |
    |  -------------------------------------   |  ------------------- |
    |  Name                                    |  myConnectionMonitor |
    |  ソース仮想マシン                  |  myVmSource          |
    |  発信元ポート                             |                      |
    |  ターゲット (仮想マシンを選択します)   |  myVmDestination     |
    |  宛先ポート                        |  80                  |

6. 監視が開始されます。 接続モニターは 60 秒間隔でプローブを実行します。
7. 接続モニターには、平均ラウンド トリップ時間とプローブの失敗率が表示されます。 モニター データは、グリッドまたはグラフで表示できます。

## <a name="next-steps"></a>次の手順

- [アラートがトリガーするパケット キャプチャの作成](network-watcher-alert-triggered-packet-capture.md)に関するページを参照して、仮想マシンのアラートを使用してパケット キャプチャを自動化する方法を確認します。

- [IP フローの検証](network-watcher-check-ip-flow-verify-portal.md)に関する記事を参照して、仮想マシンで送受信される特定のトラフィックが許可されているかどうかを判断します。