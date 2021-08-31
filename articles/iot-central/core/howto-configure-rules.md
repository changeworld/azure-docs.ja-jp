---
title: Azure IoT Central でルールとアクションを構成する | Microsoft Docs
description: このハウツー記事では、作成者として Azure IoT Central アプリケーションでテレメトリベースのルールとアクションを構成する方法について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 5d36c303f1ebc23b317ccd5dcb08d65f5ceaf30b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746418"
---
# <a name="configure-rules"></a>ルールを構成する

IoT Central のルールは、接続されているデバイスからアクティブに監視されているイベントでトリガーする、カスタマイズ可能な応答ツールとして機能します。 以下のセクションで、ルールの評価方法について説明します。 ルールがトリガーされたときに発生する 1 つ以上のアクションを定義できます。この記事では、メール、Webhook、Azure Monitor のアクション グループについて説明します。 その他のアクションの種類の詳細については、「[ワークフローを使用して Azure IoT Central アプリケーションを他のクラウド サービスと統合する](howto-configure-rules-advanced.md)」を参照してください。

## <a name="select-target-devices"></a>ターゲット デバイスの選択

このルールが適用されるデバイスの種類を選択するには、[ターゲット デバイス] セクションを使用します。 フィルターを使用すると、対象デバイスをさらに絞り込むことができます。 フィルターは、デバイス テンプレートのプロパティを使用して、デバイスのセットを絞り込みます。 フィルター自体はアクションをトリガーしません。 次のスクリーンショットでは、対象に指定されようとしているデバイスは、デバイス テンプレートの種類が **[冷蔵庫]** であるデバイスです。 フィルターで、 **[Manufactured State]\(製造された州\)** プロパティが **[ワシントン]** と等しい **[冷蔵庫]** のみをルールの対象とするように指定しています。

:::image type="content" source="media/howto-configure-rules/filters.png" alt-text="条件の定義を示すスクリーンショット。":::

## <a name="use-multiple-conditions"></a>複数の条件を使用する

条件とは、ルールがトリガーされる基になるものです。 ルールに複数の条件を追加し、すべての条件が true の場合と、いずれかの条件が true の場合のどちらでルールをトリガーするかを指定できます。  

次のスクリーンショットでは、温度が 70&deg; F より高く、かつ湿度が 10 未満になるタイミングを条件で確認します。 これらのステートメントのいずれかが true である場合、ルールの評価結果は true になり、アクションがトリガーされます。

:::image type="content" source="media/howto-configure-rules/conditions.png" alt-text="温度と湿度の条件が指定された冷蔵庫モニターを示すスクリーンショット。":::

> [!NOTE]
> 現時点では、テレメトリの条件のみがサポートされています。  

### <a name="use-a-cloud-property-in-a-value-field"></a>値フィールドでクラウド プロパティを使用する

クラウド プロパティは、条件の **値** フィールドにあるデバイス テンプレートから参照できます。 クラウド プロパティとテレメトリ値は、類似した型である必要があります。 たとえば、**温度** が double の場合、 **[値]** ドロップダウンには、double 型のクラウド プロパティのみがオプションとして表示されます。

イベントの種類のテレメトリ値を選択する場合、 **[値]** ドロップダウンには、 **[任意]** オプションが表示されます。 **[任意]** オプションは、ペイロードの内容にかかわらず、アプリケーションがその種類のイベントを受信するときに規則が適用されることを意味します。

## <a name="use-aggregate-windowing"></a>集計ウィンドウを使用する

時間枠に基づいてルールをトリガーするための、集計時間を指定できます。 ルール条件は、集計時間枠の利用統計情報を、タンブリング ウィンドウとして評価します。 ルールにプロパティ フィルターがある場合は、時間枠の最後に適用されます。 次のスクリーンショットでは、時間枠は 5 分です。 このルールでは、5 分ごとに、最後の 5 分間の利用統計情報が評価されます。 データは、対応するウィンドウ内で 1 回だけ評価されます。

:::image type="content" source="media/howto-configure-rules/tumbling-window.png" alt-text="タンブリング ウィンドウがどのように定義されているかを示す図。":::

## <a name="create-an-email-action"></a>メール アクションを作成する

メール アクションを作成する場合は、メール アドレスがアプリケーションの **ユーザー ID** であり、そのユーザーがアプリケーションに少なくとも一度はサインインしている必要があります。 メールに含めるメモを指定することもできます。 IoT Central に、ルールがトリガーされた場合のメールの表示例が示されます。

:::image type="content" source="media/howto-configure-rules/email-action.png" alt-text="ルールのメール アクションを示すスクリーンショット。":::

## <a name="create-a-webhook-action"></a>Webhook アクションを作成する

Webhook を使用すると、IoT Central アプリを他のアプリケーションやサービスに接続して、リモート監視や通知を行うことができます。 IoT Central アプリ内でルールがトリガーされるたびに、Webhook は、接続されている他のアプリケーションやサービスに対して自動的に通知を行います。 ルールがトリガーされるたびに、IoT Central アプリから他のアプリケーションの HTTP エンドポイントに POST 要求が送信されます。 ペイロードには、デバイスの詳細とルールのトリガーの詳細が含まれます。

この例では *RequestBin* に接続し、ルールが実行された場合に通知を受け取るようにします。

1. [RequestBin](https://requestbin.net/) を開きます。

1. 新しい RequestBin を作成し、**Bin URL** をコピーします。

1. ルールにアクションを追加します。

    :::image type="content" source="media/howto-configure-rules/webhook-create.png" alt-text="Webhook の作成画面を示すスクリーンショット。":::

1. Webhook アクションを選択し、表示名を入力して、RequestBin URL を **[コールバック URL]** として貼り付けます。

1. ルールを保存します。

ルールがトリガーされると、新しい要求が RequestBin に表示されます。

### <a name="payload"></a>ペイロード

ルールがトリガーされると、コールバック URL に対して HTTP POST 要求が行われます。 要求には、テレメトリ、デバイス、ルール、およびアプリケーションの詳細を含む JSON ペイロードが含まれています。 ペイロードは次の JSON スニペットのようになります。

```json
{
    "timestamp": "2020-04-06T00:20:15.06Z",
    "action": {
        "id": "<id>",
        "type": "WebhookAction",
        "rules": [
            "<rule_id>"
        ],
        "displayName": "Webhook 1",
        "url": "<callback_url>"
    },
    "application": {
        "id": "<application_id>",
        "displayName": "Contoso",
        "subdomain": "contoso",
        "host": "contoso.azureiotcentral.com"
    },
    "device": {
        "id": "<device_id>",
        "etag": "<etag>",
        "displayName": "MXChip IoT DevKit - 1yl6vvhax6c",
        "instanceOf": "<device_template_id>",
        "simulated": true,
        "provisioned": true,
        "approved": true,
        "cloudProperties": {
            "City": {
                "value": "Seattle"
            }
        },
        "properties": {
            "deviceinfo": {
                "firmwareVersion": {
                    "value": "1.0.0"
                }
            }
        },
        "telemetry": {
            "<interface_instance_name>": {
                "humidity": {
                    "value": 47.33228889360127
                }
            }
        }
    },
    "rule": {
        "id": "<rule_id>",
        "displayName": "Humidity monitor"
    }
}
```

ルールによって一定期間にわたって集計テレメトリが監視されている場合、ペイロードには次のようなテレメトリ セクションが含まれます。

```json
{
    "telemetry": {
        "<interface_instance_name>": {
            "Humidity": {
                "avg": 39.5
            }
        }
    }
}
```

### <a name="data-format-change-notice"></a>データ形式の変更に関する通知

**2020 年 4 月 3 日** より前に作成および保存された Webhook が 1 つ以上ある場合は、その Webhook を削除して新しい Webhook を作成します。 古い Webhook では、非推奨のペイロード形式が使用されています。

```json
{
    "id": "<id>",
    "displayName": "Webhook 1",
    "timestamp": "2019-10-24T18:27:13.538Z",
    "rule": {
        "id": "<id>",
        "displayName": "High temp alert",
        "enabled": true
    },
    "device": {
        "id": "mx1",
        "displayName": "MXChip IoT DevKit - mx1",
        "instanceOf": "<device-template-id>",
        "simulated": true,
        "provisioned": true,
        "approved": true
    },
    "data": [{
        "@id": "<id>",
        "@type": ["Telemetry"],
        "name": "temperature",
        "displayName": "Temperature",
        "value": 66.27310467496761,
        "interfaceInstanceName": "sensors"
    }],
    "application": {
        "id": "<id>",
        "displayName": "x - Store Analytics Checkout",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="create-an-azure-monitor-group-action"></a>Azure Monitor グループ アクションを作成する

このセクションでは、[Azure Monitor](../../azure-monitor/overview.md) "*アクション グループ*" を使用して、複数のアクションを IoT Central ルールに関連付ける方法について説明します。 複数のルールにアクション グループを関連付けることができます。 [アクション グループ](../../azure-monitor/alerts/action-groups.md)は、Azure サブスクリプションの所有者によって定義された通知設定のコレクションです。

[Azure portal でアクション グループを作成して管理](../../azure-monitor/alerts/action-groups.md)できます。あるいは、[Azure Resource Manager テンプレートを利用して](../../azure-monitor/alerts/action-groups-create-resource-manager-template.md)アクション グループを作成し、管理できます。

アクション グループでできること:

- メール、SMS、音声通話で通知を送信します。
- Webhook を呼び出すなど、操作を実行します。

次のスクリーンショットでは、メールや SMS の通知を送信し、Webhook を呼び出すアクション グループを確認できます。

:::image type="content" source="media/howto-configure-rules/action-group.png" alt-text="Azure portal のアクション グループを示すスクリーンショット。":::

IoT Central ルールでアクション グループを使用するには、IoT Central アプリケーションと同じ Azure サブスクリプションにアクション グループを置く必要があります。

IoT Central のルールにアクションを追加するときに、 **[Azure Monitor アクション グループ]** を選択します。

Azure サブスクリプションからアクション グループを選択します。

:::image type="content" source="media/howto-configure-rules/choose-action-group.png" alt-text="IoT Central ルールのアクション グループを示すスクリーンショット。":::

**[保存]** を選択します。 これでこのアクション グループはアクションの一覧に表示され、ルールがトリガーされたときに実行されます。

次の表は、サポートされているアクションの種類に送信される情報をまとめたものです。

| アクションの種類 | 出力形式 |
| ----------- | -------------- |
| Email       | 標準 IoT Central メール テンプレート |
| SMS         | Azure IoT Central alert: ${applicationName} - "${ruleName}" triggered on "${deviceName}" at ${triggerDate} ${triggerTime} |
| 音声       | Azure I.O.T Central alert: rule "${ruleName}" triggered on device "${deviceName}" at ${triggerDate} ${triggerTime}, in application ${applicationName} |
| Webhook     | { "schemaId" : "AzureIoTCentralRuleWebhook", "data": {[regular webhook payload](howto-create-webhooks.md#payload)}} |

次のテキストは、アクション グループからの SMS メッセージの例です。

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="use-rules-with-iot-edge-modules"></a>IoT Edge モジュールでのルールの使用

IoT Edge モジュールに適用されるルールには制限が適用されます。 モジュールが異なるテレメトリに基づくルールは、有効なルールとして評価されません。 次に例を示します。ルールの最初の条件はモジュール A の温度テレメトリに基づくもので、ルールの 2 番目の条件はモジュール B の湿度テレメトリに基づくものであるとします。この場合、2 つの条件はモジュールが異なっているため、無効な条件のセットになります。 このルールは有効ではないため、ルールを保存しようとするとエラーがスローされます。

## <a name="next-steps"></a>次のステップ

これで、Azure IoT Central アプリケーションでルールを構成する方法を習得したことになるため、Power Automate または Azure Logic Apps を使用して、[高度なルールを構成](howto-configure-rules-advanced.md)する方法を参照してください。
