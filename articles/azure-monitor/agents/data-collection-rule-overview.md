---
title: Azure Monitor のデータ収集ルール (プレビュー)
description: Azure Monitor のデータ収集ルール (DCR) の概要 (その内容と構造、それらを作成して使用する方法など)。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2021
ms.custom: references_region
ms.openlocfilehash: 7dca96e05860bb399435ac95ed81107c268ebc5c
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122662229"
---
# <a name="data-collection-rules-in-azure-monitor"></a>Azure Monitor のデータ収集ルール
データ収集ルール (DCR) では、Azure Monitor に入ってくるデータを定義し、そのデータを送信または格納する場所を指定します。 この記事では、データ収集ルールの概要 (その内容と構造、それらを作成して使用する方法など) について説明します。

## <a name="input-sources"></a>入力ソース
データ収集ルールでは現在、次の入力ソースがサポートされています。

- 仮想マシン、仮想マシン スケール セット、Azure Arc for servers で実行される Azure Monitor エージェント。 [Azure Monitor エージェント用のデータ コレクションの構成 (プレビュー)](../agents/data-collection-rule-azure-monitor-agent.md) に関する記事をご覧ください。



## <a name="components-of-a-data-collection-rule"></a>データ収集ルールのコンポーネント
データ収集ルールに含まれるコンポーネントを次に示します。

| コンポーネント | 説明 |
|:---|:---|
| データ ソース | 独自の形式と方法でデータを公開する、監視データの一意のソース。 データ ソースの例としては、Windows イベント ログ、パフォーマンス カウンター、syslog などがあります。 以下で説明するように、各データ ソースは特定のデータ ソースの種類に適合します。 |
| ストリーム | 1 つの種類として変換およびスキーマ化される 1 組のデータ ソースを記述する一意のハンドル。 各データ ソースにはストリームが 1 つ以上必要であり、1 つのストリームが複数のデータ ソースで使用される場合もあります。 1 つのストリーム内のすべてのデータ ソースで、1 つの共通スキーマが共有されます。 同じ Log Analytics ワークスペース内の複数のテーブルに特定のデータ ソースを送信する場合などは、複数のストリームを使用します。 |
| 変換先 | データの送信先となる 1 組の宛先。 例として、Log Analytics ワークスペースや Azure Monitor メトリックがあります。 | 
| データ フロー | どのストリームをどの宛先に送信するかの定義。 | 

データ収集ルールはリージョナルに格納され、Log Analytics がサポートされるすべてのパブリック リージョンで利用できます。 政府機関向けのリージョンおよびクラウドは現在サポートされていません。

次の図は、データ収集ルールのコンポーネントとその関係を示しています

[![DCR の図](media/data-collection-rule-overview/data-collection-rule-components.png)](media/data-collection-rule-overview/data-collection-rule-components.png#lightbox)

### <a name="data-source-types"></a>データ ソースの種類
各データ ソースには、データソースの種類があります。 各種類は、データ ソースごとに指定する必要がある一意のプロパティ セットを定義します。 次の表に、現在使用できるデータ ソースの種類を示します。

| [データ ソースの種類] | Description | 
|:---|:---|
| 拡張機能 | VM 拡張機能に基づいたデータ ソース |
| performanceCounters | Windows と Linux の両方のパフォーマンス カウンター |
| syslog | Linux の Syslog イベント |
| windowsEventLogs | Windows イベント ログ |


## <a name="limits"></a>制限
各データ収集ルールに適用される制限については、「[Azure Monitor サービスの制限](../service-limits.md#data-collection-rules)」を参照してください。

## <a name="data-resiliency-and-high-availability"></a>データの回復性と高可用性
サービスとしてのデータ収集ルールは、伴いにデプロイされます。 ルールは、指定したリージョンに作成されて格納され、同じ Geo 内の [ペアリージョン](../../best-practices-availability-paired-regions.md#azure-regional-pairs) にバックアップされます。  
さらに、サービスはリージョン内の 3 つの [可用性ゾーン](../../availability-zones/az-overview.md#availability-zones)すべてにデプロイされて、高可用性がさらに向上する **ゾーン冗長サービス** になります。


**単一リージョン データ保存場所**: 現在、顧客データを 1 つのリージョンに格納できるようにするプレビュー機能は、アジア太平洋地域の東南アジア リージョン (シンガポール) と、ブラジル地域のブラジル南部リージョン (サンパウロ州) でのみ使用できます。 単一リージョン保存場所は、これらのリージョンでは既定で有効になっています。

## <a name="create-a-dcr"></a>DCR を作成する
現在、次の方法のいずれかを使用して DCR を作成できます。

- [Azure portal を使用](../agents/data-collection-rule-azure-monitor-agent.md)してデータ収集ルールを作成し、1 つまたは複数の仮想マシンに関連付けます。
- データ収集ルールを JSON で直接編集し、[REST API を使用して送信します](/rest/api/monitor/datacollectionrules)。
- DCR および [Azure CLI](https://github.com/Azure/azure-cli-extensions/blob/master/src/monitor-control-service/README.md) との関連付けを作成します。
- DCR および Azure PowerShell との関連付けを作成します。
  - [Get-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Get-AzDataCollectionRule.md)
  - [New-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/New-AzDataCollectionRule.md)
  - [Set-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Set-AzDataCollectionRule.md)
  - [Update-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Update-AzDataCollectionRule.md)
  - [Remove-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Remove-AzDataCollectionRule.md)
  - [Get-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Get-AzDataCollectionRuleAssociation.md)
  - [New-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/New-AzDataCollectionRuleAssociation.md)
  - [Remove-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Remove-AzDataCollectionRuleAssociation.md)

## <a name="sample-data-collection-rule"></a>データ収集ルールのサンプル
以下のデータ収集ルールのサンプルは Azure Monitor エージェントを使用した仮想マシン用で、次の詳細が含まれています。

- パフォーマンス データ
  - 特定のプロセッサ、メモリ、論理ディスク、および物理ディスクのカウンターを 15 秒ごとに収集し、1 分ごとにアップロードします。
  - 特定のプロセス カウンターを 30 秒ごとに収集し、5 分ごとにアップロードします。
- Windows イベント
  - Windows セキュリティ イベントを収集し、1 分ごとにアップロードします。
  - Windows のアプリケーション イベントとシステム イベントを収集し、5 分ごとにアップロードします。
- syslog
  - cron ファシリティからデバッグ、重大、および緊急イベントを収集します。
  - syslog ファシリティからアラート、重大、および緊急イベントを収集します。
- 変換先
  - すべてのデータを centralWorkspace という名前の Log Analytics ワークスペースに送信します。

> [!NOTE]
> データ収集ルールでイベント コレクションを指定するために使用される Xpath の説明については、「[カスタム XPath クエリによるデータ収集の制限](data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries)」を参照してください。


```json
{
    "location": "eastus",
    "properties": {
      "dataSources": {
        "performanceCounters": [
          {
            "name": "cloudTeamCoreCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT1M",
            "samplingFrequencyInSeconds": 15,
            "counterSpecifiers": [
              "\\Processor(_Total)\\% Processor Time",
              "\\Memory\\Committed Bytes",
              "\\LogicalDisk(_Total)\\Free Megabytes",
              "\\PhysicalDisk(_Total)\\Avg. Disk Queue Length"
            ]
          },
          {
            "name": "appTeamExtraCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT5M",
            "samplingFrequencyInSeconds": 30,
            "counterSpecifiers": [
              "\\Process(_Total)\\Thread Count"
            ]
          }
        ],
        "windowsEventLogs": [
          {
            "name": "cloudSecurityTeamEvents",
            "streams": [
              "Microsoft-Event"
            ],
            "scheduledTransferPeriod": "PT1M",
            "xPathQueries": [
              "Security!*"
            ]
          },
          {
            "name": "appTeam1AppEvents",
            "streams": [
              "Microsoft-Event"
            ],
            "scheduledTransferPeriod": "PT5M",
            "xPathQueries": [
              "System!*[System[(Level = 1 or Level = 2 or Level = 3)]]",
              "Application!*[System[(Level = 1 or Level = 2 or Level = 3)]]"
            ]
          }
        ],
        "syslog": [
          {
            "name": "cronSyslog",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "cron"
            ],
            "logLevels": [
              "Debug",
              "Critical",
              "Emergency"
            ]
          },
          {
            "name": "syslogBase",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "syslog"
            ],
            "logLevels": [
              "Alert",
              "Critical",
              "Emergency"
            ]
          }
        ]
      },
      "destinations": {
        "logAnalytics": [
          {
            "workspaceResourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.OperationalInsights/workspaces/my-workspace",
            "name": "centralWorkspace"
          }
        ]
      },
      "dataFlows": [
        {
          "streams": [
            "Microsoft-Perf",
            "Microsoft-Syslog",
            "Microsoft-Event"
          ],
          "destinations": [
            "centralWorkspace"
          ]
        }
      ]
    }
  }
```


## <a name="next-steps"></a>次の手順

- [データ収集ルールを作成](data-collection-rule-azure-monitor-agent.md)し、Azure Monitor エージェントを使用する仮想マシンからそのデータ収集ルールとの関連付けを作成します。
