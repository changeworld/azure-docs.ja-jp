---
title: Azure Event Hubs でスループット ユニットを自動的にスケールアップする
description: 名前空間の自動インフレを有効にしてスループット ユニットを自動的にスケールアップします (Standard レベル)。
ms.topic: article
ms.date: 05/26/2021
ms.openlocfilehash: 05ea9a76ec5ece9bf522679c85f3671d600d41e8
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444099"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units-standard-tier"></a>Azure Event Hubs のスループット ユニットを自動的にスケールアップする (Standard レベル) 
Azure Event Hubs は、拡張性の高いデータ ストリーミング プラットフォームです。 そのため、Event Hubs の使用は、多くの場合、サービスの利用開始後に増えます。 このような使用では、Event Hubs のスケーリングとより大きな転送率の処理を行うために、事前に定義された[スループット ユニット (TU)](event-hubs-scalability.md#throughput-units) を増やす必要があります。 Event Hubs の **自動インフレ** 機能は、使用量のニーズに合わせて TU の数を増やすことで、自動的にスケールアップします。 TU を増やすことで、以下の状況で必要になる調整シナリオを防ぐことができます。

* データのイングレス レートが TU を超えている 
* データのエグレス要求が設定された TU を超えている

負荷が最小しきい値を超えていて、ServerBusy エラーなどで失敗した要求がない場合、Event Hubs サービスでスループットが増えます。

> [!NOTE]
> **Premium** レベルの詳細については、[Event Hubs Premium](event-hubs-premium-overview.md) に関するページを参照してください。

## <a name="how-auto-inflate-works-in-standard-tier"></a>Standard レベルでの自動インフレのしくみ
Event Hubs トラフィックは TU によって制御されます (standard レベル)。 TU あたりのイングレス レートやエグレス レートなどの制限については、[Event Hubs のクォータと制限](event-hubs-quotas.md)に関するページを参照してください。 自動インフレを使用すると、自分で選択した、最低限必要な TU から小さく始めることができます。 その後、この機能は、トラフィックの増加に応じて、必要な TU の上限を自動的にスケーリングします。 自動インフレには、次の利点があります。

- 小さく始めて、成長と共にスケールアップする効率的なスケーリング メカニズムです。
- 調整問題なしで、指定された上限に自動的にスケーリングします。
- タイミングと量を制御できるため、スケーリングを細かく制御できます。

> [!NOTE]
> 自動インフレでは、イングレスまたはエグレス レートが制限を下回った場合でも TU 数が "*自動的に*" 縮小されません。 

 ## <a name="enable-auto-inflate-on-a-namespace"></a>名前空間で自動インフレを有効にする
[Azure portal](https://portal.azure.com) または [Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventhub/eventhubs-create-namespace-and-enable-inflate)のいずれかを使用して、Standard レベルの Event Hubs 名前空間で自動インフレを有効または無効にすることができます。

Premium の Event Hubs 名前空間では、この機能は自動的に有効になります。 無効にすることはできません。 

> [!NOTE]
> Basic レベルの Event Hubs 名前空間では、自動インフレはサポートされていません。

## <a name="use-azure-portal"></a>Azure Portal の使用
Azure portal では、Standard の Event Hubs 名前空間を作成するとき、または名前空間が作成された後に、この機能を有効にすることができます。 また、名前空間の TU を設定し、TU の上限を指定することもできます。 

自動インフレ機能は、**Event Hubs 名前空間の作成時** に有効にできます。 次の図は、Standard レベルの名前空間に対して自動インフレ機能を有効にする方法と、開始時の TU 数と TU の最大数を構成する方法を示しています。 

:::image type="content" source="./media/event-hubs-auto-inflate/event-hubs-auto-inflate.png" alt-text="Standard レベルの名前空間のイベント ハブの作成時に自動インフレを有効にしているスクリーンショット":::

このオプションを有効にすると、小さな TU で開始し、使用量のニーズの増加に合わせてスケールアップできます。 インフレの上限は料金にすぐには影響しません。料金は 1 時間ごとに使用される TU の数によって決まります。

自動インフレ機能を有効にし、既存の設定を変更するには、次の手順を実行します。

1. **[Event Hubs 名前空間]** ページの左側にあるメニューの **[設定]** の下にある **[スケール]** を選択します。
2. **[スケールの設定**] ページで、**[有効]** のチェック ボックスをオンにします (自動スケール機能が有効になっていなかった場合)。

    :::image type="content" source="./media/event-hubs-auto-inflate/scale-settings.png" alt-text="既存の Standard 名前空間の自動インフレを有効にするスクリーンショット":::
3. スループット ユニットの **最大** 数を入力するか、スクロール バーを使用して値を設定します。
4. (省略可能) このページの上部でスループット ユニットの **最小** 数を更新します。

> [!NOTE]
> 自動インフレの構成を適用してスループット ユニットを増やした場合、Event Hubs サービスにより、スループットがいつ何故増加したかに関する情報を提供する診断ログが出力されます。 イベント ハブの診断ログを有効にするには、Azure portal のイベント ハブ ページの左のメニューで **[診断設定]** を選択します。 詳しくは、「[Azure イベント ハブの診断ログを設定する](monitor-event-hubs-reference.md#resource-logs)」をご覧ください。


## <a name="use-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用

Azure Resource Manager テンプレートのデプロイ時に自動インフレを有効にできます。 たとえば、`isAutoInflateEnabled` プロパティを **true** に設定し、`maximumThroughputUnits` を 10 に設定します。 次に例を示します。

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

完全なテンプレートについては、GitHub の[イベント ハブ名前空間とインフレの有効化](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventhub/eventhubs-create-namespace-and-enable-inflate)のテンプレートを参照してください。


## <a name="next-steps"></a>次のステップ

Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs の概要](./event-hubs-about.md)
