---
title: "Log Analytics における Azure Storage データの収集について | Microsoft Docs"
description: "Azure リソースは、Azure 診断を使用して、Azure Storage アカウントにログとメトリックを書き込むことができます。 Log Analytics は、このデータのインデックスを作成し、検索可能な状態にすることができます。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2a160030ab51799199fc6df08133f811d4987feb


---
# <a name="collecting-azure-storage-data-in-log-analytics-overview"></a>Log Analytics における Azure Storage データの収集について
多くの Azure リソースは、ログとメトリックを Azure Storage アカウントに書き込むことができます。 そのデータを Log Analytics から利用することによって、Azure リソースを容易に監視することができます。

Azure Storage にデータを書き込むリソースは、Azure 診断を使用するか、データを書き込むための独自の手段を有している必要があります。 このデータは、さまざまな形式で次のいずれかの場所に書き込まれます。

* Azure テーブル
* Azure BLOB
* EventHub

Log Analytics は、[Azure 診断ログ](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)を使用してデータを書き込む Azure サービスに対応しています。 その他、さまざまな場所に各種形式のログとメトリックを出力するサービスにも対応しています。  

> [!NOTE]
> ストレージ アカウントに診断を送信する際、および Log Analytics がストレージ アカウントからデータを読み取る際のデータの格納と転送には、通常の Azure 料金が課金されます。
> 
> 

![Azure Storage の図](media/log-analytics-azure-storage/azure-storage-diagram.png)

## <a name="supported-azure-resources"></a>サポートされる Azure リソース
Log Analytics は、以下の Azure リソースのデータを収集できます。

| リソースの種類 | ログ (診断カテゴリ) | Log Analytics ソリューション |
| --- | --- | --- |
| Application Insights |可用性 <br> カスタム イベント <br> 例外 <br> 要求数 <br> |Application Insights (プレビュー) |
| Automation <br> Microsoft.Automation/AutomationAccounts |JobLogs <br> JobStreams |Azure Automation (プレビュー) |
| Key Vault <br> Microsoft.KeyVault/Vaults |AuditEvent |KeyVault (プレビュー) |
| Application Gateway <br> Microsoft.Network/ApplicationGateways |ApplicationGatewayAccessLog <br> ApplicationGatewayPerformanceLog |Azure Networking (プレビュー) |
| ネットワーク セキュリティ グループ <br> Microsoft.Network/NetworkSecurityGroups |NetworkSecurityGroupEvent <br> NetworkSecurityGroupRuleCounter |Azure Networking (プレビュー) |
| Service Fabric |ETWEvent <br> 操作イベント <br> Reliable Actor イベント <br> Reliable Service イベント |ServiceFabric (プレビュー) |
| Virtual Machines |Linux Syslog <br> Windows イベント <br> IIS ログ <br> Windows ETWEvent |"*なし*" |
| Web ロール <br> worker ロール |Linux Syslog <br> Windows イベント <br> IIS ログ <br> Windows ETWEvent |"*なし*" |

> [!NOTE]
> Azure 仮想マシン (Linux と Windows の両方) を監視する場合は、[Log Analytics VM 拡張機能](log-analytics-azure-vm-extension.md)のインストールをお勧めします。 このエージェントを使用すると、ストレージに書き込まれた診断情報を使用した場合よりもさらに深く、仮想マシンの状態を把握することができます。
> 
> 

OMS で分析できるログを追加する順番を決定するために、 [フィードバック ページ](http://feedback.azure.com/forums/267889-azure-log-analytics/category/88086-log-management-and-log-collection-policy)で投票にご協力ください。

* 「[Log Analytics を使用した Azure 診断ログの分析](log-analytics-azure-storage-json.md)」を参照し、[Azure 診断ログ](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)に対応した Azure サービスから Log Analytics がログを読み取る方法について詳しく確認します。
  * Azure Key Vault
  * Azure Automation
  * Application Gateway
  * ネットワーク セキュリティ グループ
* [IIS の Blob Storage とイベントの Table Storage の使用](log-analytics-azure-storage-iis-table.md)に関するページを参照し、診断情報を Table Storage に出力する Azure サービスのログや Blob Storage に出力された IIS ログを Log Analytics がどのようにして読み取っているかを詳しく確認します。
  * Service Fabric
  * Web ロール
  * worker ロール
  * Virtual Machines

## <a name="next-steps"></a>次のステップ
* [Log Analytics を使用して Azure 診断ログを分析](log-analytics-azure-storage-json.md)します。ログは、診断情報を JSON 形式で Blob Storage に出力する Azure サービスから読み取ります。
* [Blob Storage (IIS の場合) と Table Storage (イベントの場合) を使用](log-analytics-azure-storage-iis-table.md)して、診断情報を Table Storage に出力する Azure サービスのログや、Blob Storage に出力された IIS ログを読み取ります。
* [ソリューションを有効](log-analytics-add-solutions.md) にして、データに対する洞察を得ます。
* [検索クエリを使用](log-analytics-log-searches.md) して、データを分析します。




<!--HONumber=Nov16_HO3-->


