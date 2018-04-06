---
title: Azure Network Watcher との接続を監視する - Azure Portal | Microsoft Docs
description: Azure Portal を使用して、Azure Network Watcher とのネットワーク接続を監視する方法について説明します。
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2018
ms.author: jdial
ms.openlocfilehash: c7d98350dc8f66ebd4097f22b44dcbbe2653b25d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>Azure Portal を使用して Azure Network Watcher とのネットワーク接続を監視する

接続モニターを使用して、Azure 仮想マシンと IP アドレス間のネットワーク接続を監視する方法について説明します。 接続モニターは、接続レベルでの監視を提供します。 接続は、ソースと、宛先の IP アドレスとポートの組み合わせとして定義されます。 接続モニターにより、仮想ネットワーク内の仮想マシンから、ポート 1433 を経由して、同一または別の仮想ネットワーク内で SQL サーバーを実行している仮想マシンへの接続を監視するといったシナリオを実現できます。 接続モニターでは、Azure Monitor メトリックとして、60 秒ごとに記録される接続の待機時間が提供されます。 また、ホップバイホップ トポロジを提供し、接続に影響を与える構成の問題を識別します。


## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次の前提条件を満たす必要があります。

* 接続を監視するリージョンの Network Watcher のインスタンス。 まだ Azure Network Watcher インスタンスを作成していない場合は、「[Azure Network Watcher のインスタンスの作成](network-watcher-create.md)」の手順を実行して作成できます。
* 監視する仮想マシン。
* 接続を監視する仮想マシンに `AzureNetworkWatcherExtension` をインストールしておきます。 Windows 仮想マシンに拡張機能をインストールする方法については、「[Windows 用 Network Watcher Agent 仮想マシン拡張機能](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)」を、Linux 仮想マシンに拡張機能をインストールする方法については、「[Linux 用 Network Watcher Agent 仮想マシン拡張機能](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)」を参照してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン 

[Azure ポータル](http://portal.azure.com)にサインインします。

## <a name="create-a-connection-monitor"></a>接続モニターを作成する

次の手順では、ポート 80 および 1433 経由での接続先仮想マシンへの接続監視を有効にします。

1. ポータルの左側にある **[その他のサービス]** を選択します。
2. 「*network watcher*」の入力を始めます。 検索結果に **[Network Watcher]** が表示されたら、それを選択します。
3. **[監視]** の **[接続モニター (プレビュー)]** を選択します。 プレビュー リリース段階にある機能には、一般リリースの機能と同じレベルの信頼性とリージョンの可用性はありません。
4. **[+ 追加]**を選択します。
5. 監視する接続の情報を入力するか選択して、**[追加]** を選択します。 次の図に示した例では、*MultiTierApp0* 仮想マシンと *Database0* 仮想マシンの間が、監視される接続です。

    ![接続モニターを追加する](./media/connection-monitor/add-connection-monitor.png)

    監視が開始されます。 接続モニターは 60 秒間隔でプローブを実行します。

## <a name="view-connection-monitoring"></a>接続監視を表示する

1. [接続モニターを作成する](#create-a-connection-monitor)の手順 1 ~ 3 を完了して、接続監視を表示します。
2. 次の図は、AppToDB(80) 接続の詳細を示しています。 **[ステータス]** は到達可能となっています。 **[グラフ ビュー]** には、**平均ラウンド トリップ時間**と**失敗したプローブの割合**が示されています。 グラフではホップバイホップ情報が提供され、接続先への到達可能性に影響している問題はないことが示されています。

    ![接続モニターを表示する](./media/connection-monitor/view-connection-monitor.png)

3. 次の図に示した *AppToDB(1433)* モニターを表示すると、同じソースおよび接続先の仮想マシンについて、ポート 1433 経由ではステータスが到達不能になっていることがわかります。 このシナリオの**グリッド ビュー**では、ホップバイホップ情報と、到達可能性に影響を与えている問題が示されます。 この場合、ある NSG ルールが、2 つ目のホップでポート 1433 上のすべてのトラフィックをブロックしています。

    ![接続モニターを表示する](./media/connection-monitor/view-connection-monitor-2.png)

## <a name="next-steps"></a>次の手順

- [アラートがトリガーするパケット キャプチャの作成](network-watcher-alert-triggered-packet-capture.md)に関するページを参照して、仮想マシンのアラートを使用してパケット キャプチャを自動化する方法を確認します。
- [IP フローの検証](network-watcher-check-ip-flow-verify-portal.md)に関する記事を参照して、仮想マシンで送受信される特定のトラフィックが許可されているかどうかを判断します。