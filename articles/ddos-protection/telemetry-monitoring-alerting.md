---
title: DDoS 保護テレメトリの表示と構成
description: DDoS 保護テレメトリを表示および構成する方法を学習します。
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 5c80e5e611c275c2a2262963aa0759075fca836b
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989396"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>DDoS 保護テレメトリの表示と構成

Azure DDoS Protection Standard では、DDoS 攻撃の分析情報により、攻撃の詳細な情報および視覚化が提供されます。 DDoS 攻撃から仮想ネットワークを保護している場合、攻撃の軽減策レポートと軽減策フロー ログによって、攻撃のトラフィック、および攻撃を緩和するために行われたアクションの詳細を視覚的に確認できます。 豊富なテレメトリは、DDoS 攻撃の間に、詳細なメトリックを含む Azure Monitor を通じて公開されます。 DDoS Protection によって公開される Azure Monitor の任意のメトリックに対して、アラートを構成することができます。 Azure Monitor 診断インターフェイスを介した高度な分析用に、ログを [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md)、Splunk (Azure Event Hubs)、OMS Log Analytics、Azure Storage とさらに統合できます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * DDoS Protection メトリックのアラートを構成する
> * DDoS Protection テレメトリを使用する
> * DDoS 軽減ポリシーを表示する
> * Azure Security Center の DDoS 保護アラートを表示する

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
- このチュートリアルの手順を実行する前に、まず [Azure の DDoS 標準保護プラン](manage-ddos-protection.md)を作成する必要があります。

## <a name="configure-alerts-for-ddos-protection-metrics"></a>DDoS Protection メトリックのアラートを構成する

Azure Monitor のアラート構成を使用すると、利用可能な DDoS Protection メトリックをどれでも選び、攻撃中に有効な軽減策がある場合にそのメトリックのアラートを受けることができます。 条件が満たされると、指定したアドレスにアラート メールが届きます。

1. ポータルの左上にある **[すべてのサービス]** を選択します。
2. **[フィルター]** ボックスに「*Monitor*」と入力します。 結果に **[Monitor]** が表示されたら、それを選択します。
3. **[共有サービス]** で **[メトリック]** を選択します。
4. 独自の値を入力または選択するか次の例の値を入力し、残りの既定値はそのまま受け入れて、 **[OK]** を選択します。

    |設定                  |[値]                                                                                               |
    |---------                |---------                                                                                           |
    |名前                     | 「_MyDdosAlert_」と入力します。                                                                                |
    |サブスクリプション             | アラートを受信するパブリック IP アドレスを含むサブスクリプションを選択します。        |
    |Resource group           | アラートを受信するパブリック IP アドレスを含むリソース グループを選択します。      |
    |リソース                 | アラートを受信するパブリック IP アドレスを含むパブリック IP アドレスを選択します。 DDoS は、仮想ネットワーク内のリソースに割り当てられているパブリック IP アドレスを監視します。 仮想ネットワーク内のパブリック IP アドレスを持つリソースがない場合は、最初にパブリック IP アドレスを使用してリソースを作成する必要があります。 Azure App Service Environment と Azure VPN Gateway を除き、[Azure サービスの仮想ネットワーク](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)に関するページに一覧表示されている Resource Manager (クラシックではない) を通じてデプロイされているすべてのリソースのパブリック IP アドレスを監視できます。 このチュートリアルを続行するために、[Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 仮想マシンをすばやく作成できます。                   |
    |メトリック                   | **[DDoS 攻撃中かどうか]** を選択します。                                                                |
    |Threshold                | 1 - **1** は、攻撃を受けていることを示します。 **0** は、攻撃を受けていないことを示します。                         |
    |期間                   | 選んだ任意の値を選択します。                                                                   |
    |電子メールによる通知         | チェック ボックスをオンにします。                                                                                 |
    |Additional administrator (追加の管理者) | 電子メールの所有者、共同作成者、またはサブスクリプションの閲覧者でない場合は自分のメール アドレスを入力します。 |

    攻撃の検出から数分以内に、次の図のような電子メールが Azure Monitor メトリックから届きます。

    ![攻撃アラート](./media/manage-ddos-protection/ddos-alert.png)


DDoS 攻撃をシミュレートしてアラートを検証するには、「[DDoS 検出を検証する](test-through-simulations.md)」をご覧ください。

また、[webhook の構成](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json)や、アラートを作成するための[ロジック アプリ](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)の詳細についても学習できます。

## <a name="use-ddos-protection-telemetry"></a>DDoS Protection テレメトリを使用する

攻撃のテレメトリは、Azure Monitor 経由でリアルタイムに提供されます。 このテレメトリは、パブリック IP アドレスが軽減策のもとにある期間中のみ使用できます。 攻撃が軽減される前または軽減された後、テレメトリは表示されません。

1. ポータルの左上にある **[すべてのサービス]** を選択します。
2. **[フィルター]** ボックスに「*Monitor*」と入力します。 結果に **[Monitor]** が表示されたら、それを選択します。
3. **[共有サービス]** で **[メトリック]** を選択します。
4. テレメトリが必要なパブリック IP アドレスを含む **サブスクリプション** と **リソース グループ** を選択します。
5. **[リソースの種類]** に **[パブリック IP アドレス]** を選択し、テレメトリが必要な特定のパブリック IP アドレスを選択します。
6. 一連の **[利用可能なメトリック]** が画面の左側に表示されます。 これらのメトリックは、選択すると、概要画面の **Azure Monitor のメトリック グラフ** に表示されます。
7. **集計** の種類として **[最大]** を選択します

メトリック名は、異なるパケットの種類と、バイト数かパケット数かを表します。各メトリックでのタグ名の基本的な構成は、次のようになっています。

- **Dropped (削除) タグ名** (例: **Inbound Packets Dropped DDoS** (DDoS 受信パケット削除数)):DDoS 保護システムによって削除/除去されたパケットの数。
- **Forwarded (転送) タグ名** (例: **Inbound Packets Forwarded DDoS** (DDoS 受信パケット転送数)):DDoS システムによって宛先 VIP に転送されたパケットの数 (フィルター処理されなかったトラフィック)。
- **タグ名なし** (例: **Inbound Packets DDoS** (DDoS 受信パケット数)):除去システムに入力されたパケットの総数 (削除および転送されたパケットの総数を表します)。

この [Azure Monitor アラート ルール](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20Monitor%20Alert%20-%20DDoS%20Mitigation%20Started)では、アクティブな DDoS 軽減が発生したことを検出するための単純なクエリが実行されます。 DDoS 攻撃をシミュレートしてテレメトリを検証するには、「[DDoS 検出を検証する](test-through-simulations.md)」をご覧ください。 

## <a name="view-ddos-mitigation-policies"></a>DDoS 軽減ポリシーを表示する

DDoS Protection Standard は、DDoS が有効になっている仮想ネットワーク内で、保護されたリソースのパブリック IP アドレスごとに、3 つの自動調整された軽減ポリシー (TCP SYN、TCP、UDP) を適用します。 次の図に示すように、 **[Inbound TCP packets to trigger DDoS mitigation]\(DDoS 軽減をトリガーする受信 TCP パケット数\)** と **[Inbound UDP packets to trigger DDoS mitigation]\(DDoS 軽減をトリガーする受信 UDP パケット数\)** のメトリックを選択し、**集計** の種類を [最大] にして、ポリシーのしきい値を表示することができます。

![軽減ポリシーを表示する](./media/manage-ddos-protection/view-mitigation-policies.png)

ポリシーのしきい値は、Azure Machine Learning ベースのネットワーク トラフィック プロファイルを使用して自動的に構成されます。 ポリシーのしきい値を超過した場合にのみ、攻撃を受けている IP アドレスに対して DDoS 軽減が行われます。

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Azure Security Center の DDoS 保護アラートを表示する

Azure Security Center は、[セキュリティ アラート](../security-center/security-center-managing-and-responding-alerts.md)の一覧と、問題の調査および修復に役立つ情報を提供します。 この機能により、DDoS 攻撃に関連するアラートや、ほぼリアルタイムで攻撃を軽減するために実行されるアクションを含んだ、アラートの統合ビューが得られます。
DDoS 攻撃の検出と軽減に対して表示される 2 つの特定のアラートがあります。

- **パブリック IP に対して検出された DDoS 攻撃**:DDoS Protection サービスにより、パブリック IP アドレスのいずれかが DDoS 攻撃の対象であることが検出されると、このアラートが生成されます。
- **パブリック IP に対して軽減された DDoS 攻撃**:このアラートは、パブリック IP アドレスへの攻撃が軽減された場合に生成されます。
アラートを表示するには、Azure portal で **[Security Center]** を開きます。 **[脅威の防止]** で、 **[セキュリティ アラート]** を選択します。 次のスクリーンショットは、DDoS 攻撃アラートの例を示します。

![Azure Security Center での DDoS アラート](./media/manage-ddos-protection/ddos-alert-asc.png)

アラートには、攻撃、地域および脅威インテリジェンス情報、修復手順の下にあるパブリック IP アドレスの全般的な情報が含まれます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

- DDoS Protection メトリックのアラートを構成する
- DDoS Protection テレメトリを使用する
- DDoS 軽減ポリシーを表示する
- Azure Security Center の DDoS 保護アラートを表示する

攻撃の軽減策レポートとフロー ログの構成方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [DDoS 攻撃の軽減策レポートとフロー ログを構成する](reports-and-flow-logs.md)