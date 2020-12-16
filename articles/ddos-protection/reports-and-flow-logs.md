---
title: Azure DDoS Protection Standard のレポートとフロー ログ
description: レポートとフロー ログを構成する方法について説明します。
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
ms.openlocfilehash: 5c3bfbdf133777f0bc219d1306f80bd4d38b56ea
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746269"
---
# <a name="configure-ddos-attack-mitigation-reports-and-flow-logs"></a>DDoS 攻撃の軽減策レポートとフロー ログを構成する 

Azure DDoS Protection Standard では、DDoS 攻撃の分析情報により、攻撃の詳細な情報および視覚化が提供されます。 DDoS 攻撃から仮想ネットワークを保護している場合、攻撃の軽減策レポートと軽減策フロー ログによって、攻撃のトラフィック、および攻撃を緩和するために行われたアクションの詳細を視覚的に確認できます。 豊富なテレメトリは、DDoS 攻撃の間に、詳細なメトリックを含む Azure Monitor を通じて公開されます。 DDoS Protection によって公開される Azure Monitor の任意のメトリックに対して、アラートを構成することができます。 Azure Monitor 診断インターフェイスを介した高度な分析用に、ログを [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md)、Splunk (Azure Event Hubs)、OMS Log Analytics、Azure Storage とさらに統合できます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * DDoS 攻撃の軽減策レポートを表示および構成する
> * DDoS 攻撃の軽減フロー ログを表示および構成する

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
- このチュートリアルの手順を実行する前に、まず [Azure の DDoS 標準保護プラン](manage-ddos-protection.md)を作成する必要があります。

## <a name="view-and-configure-ddos-attack-mitigation-reports"></a>DDoS 攻撃の軽減策レポートを表示および構成する

攻撃の軽減策レポートでは、リソースへの攻撃に関する詳細情報を提供するために集計される、Netflow プロトコル データが使用されます。 パブリック IP リソースが攻撃を受けるたびに、軽減策が開始されるとすぐにレポートの生成が開始されます。 5 分ごとに増分レポートが生成され、軽減策の期間全体に対して軽減策後のレポートが生成されます。 これにより、DDoS 攻撃が長時間継続する場合に、確実に 5 分ごとに軽減策レポートの最新のスナップショットを表示し、攻撃の軽減策が完了したら完全な概要を表示することができます。 

1. ポータルの左上にある **[すべてのサービス]** を選択します。
2. **[フィルター]** ボックスに「*Monitor*」と入力します。 結果に **[Monitor]** が表示されたら、それを選択します。
3. **[設定]** で **[診断設定]** を選択します。
4. ログに記録するパブリック IP アドレスを含む **サブスクリプション** と **リソース グループ** を選択します。
5. **[リソースの種類]** に **[パブリック IP アドレス]** を選択し、メトリックをログに記録する特定のパブリック IP アドレスを選択します。
6. **[Turn on diagnostics to collect the DDoSMitigationReports log]\(診断を有効にして DDoSMitigationReports ログを収集する\)** を選択し、必要に応じて次のオプションを任意の数だけ選択します。

    - **[ストレージ アカウントへのアーカイブ]** :データは Azure Storage アカウントに書き込まれます。 このオプションについて詳しくは、[リソース ログのアーカイブ](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)に関する記事をご覧ください。
    - **[イベント ハブへのストリーム]** :ログの受信者が Azure Event Hub を使用してログを取得できるようにします。 イベント ハブにより、Splunk やその他の SIEM システムとの統合が可能になります。 このオプションについて詳しくは、[イベント ハブへのリソース ログのストリーミング](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)に関する記事をご覧ください。
    - **[Log Analytics への送信]** :Azure Monitor サービスにログを書き込みます。 このオプションについて詳しくは、[Azure Monitor ログで使用するログの収集](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace)に関する記事をご覧ください。

増分レポートと攻撃の軽減策後のレポートには、両方とも次のフィールドが含まれます。
- 攻撃ベクトル
- トラフィック統計情報
- パケットが削除された理由
- 関与するプロトコル
- 上位 10 の攻撃元の国またはリージョン
- 上位 10 の攻撃元の ASN

## <a name="view-and-configure-ddos-attack-mitigation-flow-logs"></a>DDoS 攻撃の軽減フロー ログを表示および構成する
攻撃の軽減フロー ログを使用すると、アクティブな DDoS 攻撃中に、削除されたトラフィック、転送されたトラフィック、およびその他の有用なデータ ポイントをほぼリアルタイムで確認できます。 この一定のデータ ストリームをほぼリアルタイムに監視するために、イベント ハブを使用して Azure Sentinel またはサードパーティの SIEM システム内に取り込み、可能なアクションを実行して、防御操作の必要性に対処することができます。

1. ポータルの左上にある **[すべてのサービス]** を選択します。
2. **[フィルター]** ボックスに「*Monitor*」と入力します。 結果に **[Monitor]** が表示されたら、それを選択します。
3. **[設定]** で **[診断設定]** を選択します。
4. ログに記録するパブリック IP アドレスを含む **サブスクリプション** と **リソース グループ** を選択します。
5. **[リソースの種類]** に **[パブリック IP アドレス]** を選択し、メトリックをログに記録する特定のパブリック IP アドレスを選択します。
6. **[Turn on diagnostics to collect the DDoSMitigationFlowLogs log]\(診断を有効にして DDoSMitigationFlowLogs ログを収集する\)** を選択し、必要に応じて次のオプションを任意の数だけ選択します。

    - **[ストレージ アカウントへのアーカイブ]** :データは Azure Storage アカウントに書き込まれます。 このオプションについて詳しくは、[リソース ログのアーカイブ](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)に関する記事をご覧ください。
    - **[イベント ハブへのストリーム]** :ログの受信者が Azure Event Hub を使用してログを取得できるようにします。 イベント ハブにより、Splunk やその他の SIEM システムとの統合が可能になります。 このオプションについて詳しくは、[イベント ハブへのリソース ログのストリーミング](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)に関する記事をご覧ください。
    - **[Log Analytics への送信]** :Azure Monitor サービスにログを書き込みます。 このオプションについて詳しくは、[Azure Monitor ログで使用するログの収集](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace)に関する記事をご覧ください。

この[テンプレート](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Enable%20Diagnostic%20Logging/Azure%20Policy)では、診断ログを有効にするための Azure Policy 定義が作成されます。

### <a name="azure-sentinel-data-connector"></a>Azure Sentinel のデータ コネクタ

攻撃の軽減フロー ログを Azure Sentinel に接続し、ブック内のデータを表示および分析して、カスタム アラートを作成し、それを調査プロセスに組み込むことができます。 Azure Sentinel に接続するには、[Azure Sentinel への接続に関するページ](../sentinel/connect-azure-ddos-protection.md)を参照してください。 

![Azure Sentinel の DDoS コネクタ](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Azure DDoS Protection のブック

Azure analytics ダッシュボードでフロー ログ データを表示するには、 https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20DDoS%20Protection%20Workbook からサンプル ダッシュボードをインポートします。

フロー ログには次のフィールドがあります。 
- 発信元 IP
- 宛先 IP
- 発信元ポート 
- 宛先ポート 
- プロトコルの種類 
- 軽減時に実行されたアクション

![DDoS Protection のブック](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

攻撃分析は、パブリック IP アドレスの仮想ネットワークで DDoS Protection Standard が有効になっている場合にのみ機能します。 

## <a name="sample-log-outputs"></a>サンプル ログ出力

次のスクリーンショットは、ログ出力の例です。

### <a name="ddosmitigationflowlogs"></a>DDoSMitigationFlowLogs

![DDoS Protection DDoSMitigationFlowLogs](./media/ddos-attack-telemetry/ddos-mitigation-flow-logs.png)

### <a name="ddosprotectionnotifications"></a>DDoSProtectionNotifications

![DDoS Protection DDoSProtectionNotifications](./media/ddos-attack-telemetry/ddos-protection-notifications.png)

### <a name="ddosmitigationreports"></a>DDoSMitigationReports

![DDoS Protection DDoSMitigationReports](./media/ddos-attack-telemetry/ddos-mitigation-reports.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

- DDoS 攻撃の軽減策レポートを表示および構成する
- DDoS 攻撃の軽減フロー ログを表示および構成する

DDoS 攻撃をテストおよびシミュレートする方法については、以下のシミュレーション テスト ガイドを参照してください。

> [!div class="nextstepaction"]
> [シミュレーションを通じたテスト](test-through-simulations.md)