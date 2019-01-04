---
title: Azure Monitor でのメトリック アラートの機能
description: メトリック アラートの用途と、Azure Monitor での機能の概要を理解します。
author: snehithm
ms.author: snmuvva
ms.date: 9/18/2018
ms.topic: conceptual
ms.service: azure-monitor
ms.component: alerts
ms.openlocfilehash: 24d0965fa2a88bc844a81c7430d46c071a9b874b
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53580044"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Azure Monitor でのメトリック アラートの機能

Azure Monitor でメトリック アラートは、複数ディメンションのメトリック上で動作します。 これらのメトリックには、プラットフォーム メトリック、[カスタム メトリック](../../azure-monitor/platform/metrics-custom-overview.md)、[Log Analytics からの一般的なログのメトリックへの変換](../../azure-monitor/platform/alerts-metric-logs.md)、Application Insights の標準的なメトリックが含まれます。 メトリック アラートは 1 つまたは複数のメトリック タイム シリーズの条件が true かどうかを定期的に評価し、評価が満たされたときに通知します。 メトリック アラートはステートフルです。つまり、状態が変更されたときにのみ通知を送信します。

## <a name="how-do-metric-alerts-work"></a>メトリック アラートの機能

メトリックのアラート ルールを定義するには、監視対象のターゲット リソース、メトリック名、および条件 (演算子としきい値) と、アラート ルールが発生したときにトリガーされるアクション グループを指定します。
次のような単純なメトリック アラート ルールを作成したとします:

- ターゲット リソース (監視対象の Azure リソース): myVM
- メトリック: Percentage CPU
- 累計時間 (生のメトリックの値に対して実行される統計。 サポートされる累計時間は Min, Max, Avg, Total です): 平均
- 期間 (メトリック値がチェックされるルック バック ウィンドウ):     直近 5 分間
- 頻度 (メトリック アラートで条件が満たされているかどうかをチェックする頻度): 1 分
- 演算子:    より大きい
- しきい値:     70

アラート ルールの作成時から、モニターは 1 分ごとに実行し、直近 5 分間のメトリック値を調べ、それらの値の平均値が 70 を超えるかどうかをチェックします。 条件が満たされている場合、つまり直近 5 分間の平均 CPU が 70 を超えている場合、アラート ルールによりアクティブ通知が生成されます。 アラート ルールに関連付けられているアクション グループで、電子メールまたは web フック アクションを構成した場合、両方でアクティブ通知を受け取ります。

このアラート ルール実行のインスタンスは、Azure portal のすべてのアラート ブレードでも表示されます。

たとえば、"myVM" の使用量が後続のチェックでもしきい値を超えつづけた場合に、この条件が解決されるまでこのアラート ルールが繰り返し生成されることはありません。

一定時間後に "myVM" の使用量が正常に戻った場合、つまり指定されたしきい値以下になります。 アラート ルールでは、その条件をさらに 2 回監視してから、解決済みの通知を送信します。 アラート ルールは、条件が変動している場合のノイズを減らすため、3 つの連続する期間にアラート条件が検出されない場合に、解決済み/非アクティブ化済みのメッセージを送信します。

解決済みの通知がwebhook または電子メールで送信された時、Azure portal でのアラート インスタンスの状態 (「モニター状態」と呼ばれます)も「解決済み」に設定されます。

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Azure Monitor のメトリック アラートによるスケールの監視

### <a name="using-dimensions"></a>ディメンションの使用

Azure Monitor のメトリック アラートでは、1 つのルールによる複数のディメンション値の組み合わせの監視もサポートします。 複数のディメンションの組み合わせを使用する利点を、例を使って説明します。

たとえば、Web サイトに App Service プランがあるとします。 Web サイト/アプリを実行している複数のインスタンスの CPU 使用率を監視できると便利です。 メトリック アラート ルールを以下のように使用すればそれが可能になります。

- ターゲット リソース: myAppServicePlan
- メトリック: Percentage CPU
- ディメンション
  - Instance = InstanceName1, InstanceName2
- 累計時間: 平均
- 期間: 直近 5 分間
- 頻度: 1 分
- 演算子: GreaterThan
- しきい値: 70

前に示したように、このルールは、直近 5 分間の平均 CPU 使用率が 70% を超えているかどうかを監視します。 ただし、同じルールによって web サイトを実行している 2 つのインスタンスを監視できます。 各インスタンスは個別に監視され、個別に通知が受領されます。

たとえば、ある web アプリに大量の要求があり、インスタンスを追加する必要があるとします。 その場合でも上記のルールは 2 つのインスタンスだけを監視します。 ただし、次のようにルールを作成できます。

- ターゲット リソース: myAppServicePlan
- メトリック: Percentage CPU
- ディメンション
  - インスタンス = *
- 累計時間: 平均
- 期間: 直近 5 分間
- 頻度: 1 分
- 演算子: GreaterThan
- しきい値: 70

このルールは、このインスタンスのすべての値を自動的に監視します。つまり、 インスタンスが生成されるたびにメトリックのアラート ルールを変更しなくても、それを監視できます。

### <a name="monitoring-multiple-resources-using-metric-alerts"></a>メトリック アラートによる複数のリソースの監視

前のセクションで説明したようには、1 つのメトリック アラート ルールでそれぞれのディメンションの組み合わせ (つまり、 メトリック タイム シリーズ) を監視することができます。 ただし、以前はこれは一度に 1 つのリソースに限定されていました。 また、Azure Monitor はプレビューで 1 つのメトリック アラート ルールによる複数リソースの監視をサポートするようになりました。 この機能は現在はプレビューで、仮想マシンでのみサポートされています。 また、1 つのメトリック アラートが 1 つの Azure リージョン内のリソースを監視することもできます。

1 つのメトリック アラートでの監視範囲は、次の 3 つのいずれかの方法で指定することができます。

- サブスクリプション内の 1 つの Azure リージョン内の仮想マシンのリストとして
- サブスクリプション内の 1 つまたは複数のリソース グループ内の (1 つの Azure リージョン内の) すべての仮想マシン
- 1 つのサブスクリプション内の (1 つの Azure リージョン内の) すべての仮想マシン

Azure portal では現在、複数のリソースを監視するメトリック アラート ルールの作成はサポートされていません。 そのようなルールは [Azure Resource Manager テンプレート](../../azure-monitor/platform/alerts-metric-create-templates.md#resource-manager-template-for-metric-alert-that-monitors-multiple-resources)を使って作成できます。 仮想マシンごとに個別の通知が届きます。 

## <a name="typical-latency"></a>一般的な待機時間

メトリック アラートでは通常、アラート ルールの頻度を 1 分に設定している場合は 5 分以内に通知を受領します。通知システムが高負荷の場合は、待機時間が長くなることがあります。

## <a name="supported-resource-types-for-metric-alerts"></a>メトリック アラートでサポートされているリソースの種類

サポートされているリソースの種類の完全な一覧については、こちらの[記事](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported)をご覧ください。

現在クラシック メトリック アラートを使用していて、使用しているすべてのリソースの種類がメトリック アラートでサポートされるかどうかを確認する場合のために、次の表に、クラシック メトリック アラートでサポートされているリソースの種類と、これらが現在のメトリック アラートでサポートされているかどうかを示します。

|クラシック メトリック アラートでサポートされているリソースの種類 | メトリック アラートでサポートされている |
|-------------------------------------------------|----------------------------|
| Microsoft.ApiManagement/service | はい |
| Microsoft.Batch/batchAccounts| はい|
|Microsoft.Cache/redis| はい
|Microsoft.ClassicCompute/virtualMachines | いいえ  |
|Microsoft.ClassicCompute/domainNames/slots/roles | いいえ |
|Microsoft.CognitiveServices/accounts | いいえ  |
|Microsoft.Compute/virtualMachines | はい|
|Microsoft.Compute/virtualMachineScaleSets| はい|
|Microsoft.ClassicStorage/storageAccounts| いいえ  |
|Microsoft.DataFactory/datafactories | はい|
|Microsoft.DBforMySQL/servers| はい|
|Microsoft.DBforPostgreSQL/servers| はい|
|Microsoft.Devices/IotHubs | いいえ |
|Microsoft.DocumentDB/databaseAccounts| いいえ |
|Microsoft.EventHub/namespaces | はい|
|Microsoft.Logic/workflows | はい|
|Microsoft.Network/loadBalancers |はい|
|Microsoft.Network/publicIPAddresses| はい|
|Microsoft.Network/applicationGateways| はい|
|Microsoft.Network/expressRouteCircuits| はい|
|Microsoft.Network/trafficManagerProfiles | はい|
|Microsoft.Search/searchServices | いいえ |
|Microsoft.ServiceBus/namespaces| いいえ |
|Microsoft.Storage/storageAccounts | はい|
|Microsoft.StreamAnalytics/streamingjobs| はい|
|Microsoft.TimeSeriesInsights/environments | はい|
|Microsoft. Web/serverfarms | はい |
|Microsoft. Web/sites (関数を除く) | はい|
|Microsoft. Web/hostingEnvironments/multiRolePools | いいえ |
|Microsoft. Web/hostingEnvironments/workerPools| いいえ 
|Microsoft.SQL/Servers | いいえ |

## <a name="next-steps"></a>次の手順

- [Azure でメトリック アラートを作成、表示、管理する方法を学習する](alerts-metric.md)
- [Azure Resource Manager のテンプレートを使ってメトリック アラートを配置する方法を学習する](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [アクション グループの詳細について学習する](action-groups.md)
