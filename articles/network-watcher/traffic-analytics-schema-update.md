---
title: Azure のトラフィック分析スキーマ更新 - 2020 年 3 月 | Microsoft Docs
description: Traffic Analytics スキーマに新しいフィールドを使用したサンプル クエリ。
services: network-watcher
documentationcenter: na
author: vinigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/06/2020
ms.author: vinigam
ms.openlocfilehash: 0e9d37e3a89473e59b94168f8f8c80e7a6621107
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969070"
---
# <a name="sample-queries-with-new-fields-in-traffic-analytics-schema-august-2019-schema-update"></a>Traffic Analytics スキーマに新しいフィールドを使用したサンプル クエリ (2019 年 8 月のスキーマ更新)

[Traffic Analytics ログ スキーマ](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema)が更新され、次の新しいフィールドが追加されました。**SrcPublicIPs_s**、**DestPublicIPs_s**、**NSGRule_s**。 次の数か月の間、次の古いフィールドが非推奨になります。**VMIP_s**、**Subscription_g**、**Region_s**、**NSGRules_s**、**Subnet_s**、**VM_s**、**NIC_s**、**PublicIPs_s**、**FlowCount_d**。
新しいフィールドからは、送信元 IP と送信先 IP に関する情報が提供され、クエリが簡単になります。

以下の 3 つの例からは、古いフィールドを新しいフィールドで置換する方法を確認できます。

## <a name="example-1---vmip_s-subscription_g-region_s-subnet_s-vm_s-nic_s-publicips_s"></a>例 1 - VMIP_s、Subscription_g、Region_s、Subnet_s、VM_s、NIC_s、PublicIPs_s

FlowDirection_s フィールドから Azure および External のパブリック フロー (それぞれ、AzurePublic フローと ExternalPublic フロー) の送信元ケースと送信先ケースを推測する必要はありません。 NVA (ネットワーク仮想アプライアンス) の場合、FlowDirection_s フィールドも使用に適さないことがあります。

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend isAzureOrExternalPublicFlows = FlowType_s in ("AzurePublic", "ExternalPublic")
| extend SourceAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), VM1_s),
SourceAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), SrcIP_s),
SourceAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subscription_g, "N/A"), Subscription1_g),
SourceAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Region_s, "N/A"), Region1_s),
SourceAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subnet_s, "N/A"), Subnet1_s),
SourceAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', NIC_s, "N/A"), NIC1_s),
DestAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), VM2_s),
DestAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), DestIP_s),
DestAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subscription_g, "N/A"), Subscription2_g),
DestAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Region_s, "N/A"), Region2_s),
DestAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subnet_s, "N/A"), Subnet2_s),
DestAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', NIC_s, "N/A"), NIC2_s),
SourcePublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'I', PublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'O', PublicIPs_s, "N/A")
```


```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend SourceAzureVM = iif(isnotempty(VM1_s), VM1_s, "N/A"),
SourceAzureVMIP = iif(isnotempty(SrcIP_s), SrcIP_s, "N/A"),
SourceAzureVMSubscription = iif(isnotempty(Subscription1_g), Subscription1_g, "N/A"),
SourceAzureRegion = iif(isnotempty(Region1_s), Region1_s, "N/A"),
SourceAzureSubnet = iif(isnotempty(Subnet1_s), Subnet1_s, "N/A"),
SourceAzureNIC = iif(isnotempty(NIC1_s), NIC1_s, "N/A"),
DestAzureVM = iif(isnotempty(VM2_s), VM2_s, "N/A"),
DestAzureVMIP = iif(isnotempty(DestIP_s), DestIP_s, "N/A"),
DestAzureVMSubscription = iif(isnotempty(Subscription2_g), Subscription2_g, "N/A"),
DestAzureRegion = iif(isnotempty(Region2_s), Region2_s, "N/A"),
DestAzureSubnet = iif(isnotempty(Subnet2_s), Subnet2_s, "N/A"),
DestAzureNIC = iif(isnotempty(NIC2_s), NIC2_s, "N/A"),
SourcePublicIPsAggregated = iif(isnotempty(SrcPublicIPs_s), SrcPublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isnotempty(DestPublicIPs_s), DestPublicIPs_s, "N/A")
```


## <a name="example-2---nsgrules_s"></a>例 2 - NSGRules_s

前のフィールドの形式: <インデックス値 0)>|<NSG_RULENAME>|<Flow Direction>|<Flow Status>|<FlowCount ProcessedByRule>

以前は、NSG と NSGRules からデータを集計していました。 今は集計しません。 そのため、NSGList_s には NSG が 1 つだけ含まれます。NSGRules_s もルールが 1 つだけ含まれていました。 そこで、ここでは複雑な書式設定を削除しました。下記のように、他のフィールドで同じものが見つかることがあります。

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| extend NSGName = NSGList_s // remains same
| extend NSGRuleName = NSGRuleComponents[1],
         FlowDirection = NSGRuleComponents[2],
         FlowStatus = NSGRuleComponents[3],
         FlowCountProcessedByRule = NSGRuleComponents[4]
| project NSGName, NSGRuleName, FlowDirection, FlowStatus, FlowCountProcessedByRule
```

```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| project NSGName = NSGList_s,
NSGRuleName = NSGRule_s ,
FlowDirection = FlowDirection_s,
FlowStatus = FlowStatus_s,
FlowCountProcessedByRule = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d
```

## <a name="example-3---flowcount_d"></a>例 3 - FlowCount_d

NSG からデータを集めることがないため、FlowCount_d は単純に AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d になります。
上の 4 つのうち 1 つだけ 0 ではなく、残りの 3 つが 0 になります。 また、フローがキャプチャされた NIC の状態と数を示します。

フローが許可された場合、"Allowed" というプレフィックスが付いたフィールドの 1 つにデータが入力されます。 それ以外の場合、"Denied" というプレフィックスが付いたフィールドの 1 つにデータが入力されます。
入ってくるフローであった場合、"InFlows_d" など、"\_d" というサフィックスが付いたフィールドの 1 つにデータが入力されます。 それ以外の場合、"OutFlows_d" にデータが入力されます。

上の 2 つの条件に基づき、4 つのうちのどれにデータが入力されるかわかります。


## <a name="next-steps"></a>次の手順
よく寄せられる質問への回答を確認するには、[トラフィック分析に関する FAQ](traffic-analytics-faq.md) をご覧ください。機能に関する詳細を確認するには、[Traffic Analytics のドキュメント](traffic-analytics.md)をご覧ください
