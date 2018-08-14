---
title: Azure Event Hubs のスループット単位を自動的にスケールアップする | Microsoft Docs
description: 名前空間の自動インフレを有効にしてスループット単位を自動的にスケールアップします
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2018
ms.author: shvija
ms.openlocfilehash: 32f99b43a37277e70d209f1f315dcb398c2b5931
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40004794"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Azure Event Hubs のスループット単位を自動的にスケールアップする

Azure Event Hubs は、拡張性の高いデータ ストリーミング プラットフォームです。 そのため、Event Hubs の使用は、多くの場合、サービスの利用開始後に増えます。 このような使用では、Event Hubs のスケーリングとより大きな転送率の処理を行うために、事前に定義された[スループット単位](event-hubs-features.md#throughput-units)を増やす必要があります。 Event Hubs の**自動インフレ**機能は、使用量のニーズに合わせてスループット単位の数を増やすことで、自動的にスケールアップします。 スループット単位を増やすことで、以下の状況で必要になる調整シナリオを防ぐことができます。

* データの受信レートが、設定されたスループット単位を超えている。
* データの送信要求レートが、設定されたスループット単位を超えている。

## <a name="how-auto-inflate-works"></a>自動インフレのしくみ

Event Hubs のトラフィックは [スループット単位](event-hubs-features.md#throughput-units)で制御されます。 受信の場合、1 スループット単位は 1 MB/秒となり、送信の場合はその倍となります。 標準的なイベント ハブを構成するときのスループット単位は 1 ～ 20 です。 自動インフレを使用すると、自分で選択した最低限必要なスループット単位から小さく始めることができます。 その後、この機能は、トラフィックの増加に応じて、必要なスループット単位の上限を自動的にスケーリングします。 自動インフレには、次の利点があります。

- 小さく始めて、成長と共にスケールアップする効率的なスケーリング メカニズムです。
- 調整問題なしで、指定された上限に自動的にスケーリングします。
- タイミングと量を制御できるため、スケーリングを細かく制御できます。

## <a name="enable-auto-inflate-on-a-namespace"></a>名前空間で自動インフレを有効にする

Event Hubs 名前空間の自動インフレを有効または無効にするには、次のいずれかの方法を使用します。

- [Azure ポータル](https://portal.azure.com)。
- [Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate)。

### <a name="enable-auto-inflate-through-the-portal"></a>ポータルで自動インフレを有効にする

自動インフレ機能は、Event Hubs 名前空間を作成するときに有効にできます。
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

このオプションを有効にすると、小さなスループット単位で開始し、使用量のニーズの増加に合わせてスケールアップできます。 インフレの上限はすぐには料金に影響しません。料金は 1 時間ごとに使用されるスループット単位の数によって決まります。

自動インフレは、ポータルの設定ウィンドウにある **[スケール]** オプションを使用して有効にすることもできます。
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate2.png)

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して自動インフレを有効にする

Azure Resource Manager テンプレートのデプロイ時に自動インフレを有効にできます。 たとえば、`isAutoInflateEnabled` プロパティを **true** に設定し、`maximumThroughputUnits` を 10 に設定します。 例: 

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

完全なテンプレートについては、GitHub の[イベント ハブ名前空間とインフレの有効化](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate)のテンプレートを参照してください。

## <a name="next-steps"></a>次の手順

Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs の概要](event-hubs-what-is-event-hubs.md)

