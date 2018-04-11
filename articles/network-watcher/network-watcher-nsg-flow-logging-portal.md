---
title: Azure Network Watcher を使用したネットワーク セキュリティ グループのフロー ログの管理 | Microsoft Docs
description: このページでは、Azure Network Watcher でネットワーク セキュリティ グループのフローのログを管理する方法を説明します。
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: cb41781c5ac8fb759cecea01402c08dd716bf7d7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
---
# <a name="manage-network-security-group-flow-logs-in-the-azure-portal"></a>Azure Portal におけるネットワーク セキュリティ グループのフロー ログの管理

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [CLI 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

ネットワーク セキュリティ グループのフロー ログは、ネットワーク セキュリティ グループを使用したイングレスおよびエグレス IP トラフィックに関する情報を表示できる Network Watcher の機能です。 これらのフロー ログは JSON 形式で作成され、重要な情報が記録されています。その例を次に示します。 

- ルールごとの送信フローと受信フロー。
- フローの適用先の NIC。
- フローに関する 5 タプル情報 (送信元/宛先 IP、送信元/宛先ポート、プロトコル)。
- トラフィックが許可 (または拒否) されたかどうかの情報。

## <a name="before-you-begin"></a>開始する前に

この記事の手順を行うには、次のリソースが既にある必要があります。

- 既存の Network Watcher。 Network Watcher を作成するには、「[Azure Network Watcher のインスタンスの作成](network-watcher-create.md)」をご覧ください。
- 有効な仮想マシンを含む既存のリソース グループ。 仮想マシンがない場合は、[Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) または [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) での仮想マシンの作成方法をご覧ください。

## <a name="register-insights-provider"></a>Insights プロバイダーの登録

フロー ログが正常に機能するためには、**Microsoft.Insights** プロバイダーが登録されている必要があります。 このプロバイダーを登録するには、次の手順を実行します。 

1. **[サブスクリプション]** に移動し、フロー ログを有効にするサブスクリプションを選択します。 
2. **[サブスクリプション]** ブレードで、**[リソース プロバイダー]** を選択します。 
3. プロバイダーの一覧を見て、**microsoft.insights** プロバイダーが登録されていることを確認します。 登録されていなければ、**[登録]** を選択します。

![プロバイダーを表示する][providers]

## <a name="enable-flow-logs"></a>フローのログを有効にする

ネットワーク セキュリティ グループのフロー ログを有効にするには、次の手順に従います。

### <a name="step-1"></a>手順 1

Network Watcher インスタンスに移動し、**[NSG フロー ログ]** を選択します。

![フロー ログの概要][1]

### <a name="step-2"></a>手順 2.

一覧からネットワーク セキュリティ グループを選択します。

![フロー ログの概要][2]

### <a name="step-3"></a>手順 3. 

**[フローのログ設定]** ブレードで、状態を **[オン]** に設定し、ストレージ アカウントを構成します。 ストレージ アカウントの **[設定]** の **[ファイアウォールと仮想ネットワーク]** で **[すべてのネットワーク]** (既定値) が選ばれている既存のストレージ アカウントを選びます。 ストレージ アカウントを選んだ後、**[OK]** を選び、**[保存]** を選びます。

![フロー ログの概要][3]

## <a name="download-flow-logs"></a>フロー ログをダウンロードする

フローのログはストレージ アカウントに保存されます。 フロー ログを表示するには、それらをダウンロードしてください。

### <a name="step-1"></a>手順 1

フロー ログをダウンロードするには、**[構成済みのストレージ アカウントからフローのログをダウンロードできました。]** を選択します。 ストレージ アカウント ビューが表示され、ダウンロードするログを選択できます。

![フロー ログ設定][4]

### <a name="step-2"></a>手順 2.

適切なストレージ アカウントに移動します。 次に、**[コンテナー]** > **[insights-log-networksecuritygroupflowevent]** の順にクリックします。

![フロー ログ設定][5]

### <a name="step-3"></a>手順 3.

フロー ログの場所に移動し、フロー ログを選択して **[ダウンロード]** を選択します。

![フロー ログ設定][6]

ログの構造については、[ネットワーク セキュリティ グループのフロー ログの概要](network-watcher-nsg-flow-logging-overview.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順

[Power BI を使用して、NSG フロー ログを視覚化する](network-watcher-visualize-nsg-flow-logs-power-bi.md)方法を確認します。

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-portal/figure1.png
[2]: ./media/network-watcher-nsg-flow-logging-portal/figure2.png
[3]: ./media/network-watcher-nsg-flow-logging-portal/figure3.png
[4]: ./media/network-watcher-nsg-flow-logging-portal/figure4.png
[5]: ./media/network-watcher-nsg-flow-logging-portal/figure5.png
[6]: ./media/network-watcher-nsg-flow-logging-portal/figure6.png
[providers]: ./media/network-watcher-nsg-flow-logging-portal/providers.png
