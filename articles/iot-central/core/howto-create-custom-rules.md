---
title: カスタム ルールと通知を使用して Azure IoT Central を拡張する |Microsoft Docs
description: ソリューション開発者は、デバイスがテレメトリの送信を停止したときに電子メール通知を送信するように IoT Central アプリケーションを構成します。 このソリューションでは、Azure Stream Analytics、Azure Functions、SendGrid を使用します。
author: dominicbetts
ms.author: dobett
ms.date: 02/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 3d528ba1bf1e7ba0c13d5bcf8abb140365cbe7d6
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121861522"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Stream Analytics、Azure Functions、SendGrid を使用してカスタム ルールで Azure IoT Central を拡張する

この攻略ガイドでは、カスタム ルールと通知を使用して IoT Central アプリケーションを拡張する方法を説明します。 この例では、デバイスがテレメトリの送信を停止したときのオペレーターへの通知の送信を示します。 このソリューションでは、[Azure Stream Analytics](../../stream-analytics/index.yml) クエリを使用して、デバイスが利用統計情報の送信をいつ停止したかを検出します。 Stream Analytics ジョブは、[SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/) を使用して通知メールを送信するために、[Azure Functions](../../azure-functions/index.yml) を使用します。

この攻略ガイドでは、既に組み込みのルールとアクションを使用して実行できることを超えて IoT Central を拡張する方法を示します。

この攻略ガイドでは、以下の方法について説明します。

* *継続的データ エクスポート* を使用してテレメトリをストリーム配信します。
* デバイスがデータの送信を停止したときに検出する Stream Analytics クエリを作成します。
* Azure Functions と SendGrid サービスを使用してメール通知を送信します。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドの手順を完了するには、アクティブな Azure サブスクリプションが必要です。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

### <a name="iot-central-application"></a>IoT Central アプリケーション

[Azure IoT Central アプリケーション マネージャー](https://aka.ms/iotcentral) Web サイト上で、次の設定を使用して IoT Central アプリケーションを作成します。

| 設定 | 値 |
| ------- | ----- |
| 料金プラン | Standard |
| アプリケーション テンプレート | ストア内分析 – 条件監視 |
| アプリケーション名 | 既定値を受け入れるか、独自の名前を選択します |
| URL | 既定値を受け入れるか、独自の一意の URL プレフィックスを選択します |
| ディレクトリ | Azure Active Directory テナント |
| Azure サブスクリプション | お使いの Azure サブスクリプション |
| リージョン | 最も近いリージョン |

この記事の例とスクリーンショットでは、**米国** リージョンを使用します。 近くの場所を選択して、必ずすべてのリソースを同じリージョン内に作成してください。

このアプリケーション テンプレートには、テレメトリを送信する 2 つのシミュレートされたサーモスタット デバイスが含まれます。

### <a name="resource-group"></a>Resource group

作成するその他のリソースを格納するため、**DetectStoppedDevices** という [リソース グループを Azure portal で作成](https://portal.azure.com/#create/Microsoft.ResourceGroup)します。 Azure リソースは IoT Central アプリケーションと同じ場所に作成してください。

### <a name="event-hubs-namespace"></a>Event Hubs 名前空間

以下の設定を使用して、[Azure portal で Event Hubs 名前空間を作成](https://portal.azure.com/#create/Microsoft.EventHub)します。

| 設定 | 値 |
| ------- | ----- |
| Name    | 名前空間名を選択します |
| Pricing tier | Basic |
| サブスクリプション | 該当するサブスクリプション |
| Resource group | DetectStoppedDevices |
| Location | East US |
| スループット単位 | 1 |

### <a name="stream-analytics-job"></a>Stream Analytics ジョブ

以下の設定を使用して、[Azure portal で Stream Analytics ジョブを作成](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob)します。

| 設定 | 値 |
| ------- | ----- |
| Name    | ジョブ名を選択します |
| サブスクリプション | 該当するサブスクリプション |
| Resource group | DetectStoppedDevices |
| Location | 米国東部 |
| ホスティング環境 | クラウド |
| [ストリーミング ユニット] | 3 |

### <a name="function-app"></a>関数アプリ

以下の設定を使用して、[Azure portal で関数アプリを作成](https://portal.azure.com/#create/Microsoft.FunctionApp)します。

| 設定 | 値 |
| ------- | ----- |
| アプリの名前    | 関数アプリ名を選択します |
| サブスクリプション | 該当するサブスクリプション |
| Resource group | DetectStoppedDevices |
| OS | Windows |
| ホスティング プラン | 従量課金プラン |
| Location | East US |
| ランタイム スタック | .NET |
| ストレージ | 新規作成 |

### <a name="sendgrid-account-and-api-keys"></a>SendGrid アカウントと API キー

Sendgrid アカウントをお持ちでない場合は、開始する前に[無料アカウント](https://app.sendgrid.com/)を作成してください。

1. 左側のメニューにある Sendgrid ダッシュボードの [設定] で、 **[API キー]** を選択します。
1. **[API キーの作成]** をクリックします。
1. 新しい API キーに **AzureFunctionAccess** という名前を指定します。
1. **[Create & View]\(作成して表示\)** をクリックします。

    :::image type="content" source="media/howto-create-custom-rules/sendgrid-api-keys.png" alt-text="SendGrid API キーの作成のスクリーン ショット。":::

この後、API キーが付与されます。 後で使用できるようにこの文字列を保存します。

## <a name="create-an-event-hub"></a>イベント ハブの作成

テレメトリをイベント ハブに連続してエクスポートするように IoT Central アプリケーションを構成できます。 このセクションでは、IoT Central アプリケーションからテレメトリを受信するイベント ハブを作成します。 このイベント ハブは、処理するためにテレメトリを Stream Analytics ジョブに配信します。

1. Azure portal で Event Hubs 名前空間に移動し、 **[+ イベント ハブ]** を選択します。
1. イベント ハブに **centralexport** という名前を付けて、 **[作成]** を選択します。

次のスクリーンショットのような Event Hubs 名前空間が作成されます。 

```:::image type="content" source="media/howto-create-custom-rules/event-hubs-namespace.png" alt-text="Screenshot of Event Hubs namespace." border="false":::

## Define the function

This solution uses an Azure Functions app to send an email notification when the Stream Analytics job detects a stopped device. To create your function app:

1. In the Azure portal, navigate to the **App Service** instance in the **DetectStoppedDevices** resource group.
1. Select **+** to create a new function.
1. Select **HTTP Trigger**.
1. Select **Add**.

    :::image type="content" source="media/howto-create-custom-rules/add-function.png" alt-text="Image of the Default HTTP trigger function"::: 

## Edit code for HTTP Trigger

The portal creates a default function called **HttpTrigger1**:

```:::image type="content" source="media/howto-create-custom-rules/default-function.png" alt-text="Screenshot of Edit HTTP trigger function.":::

1. Replace the C# code with the following code:

    ```csharp
    #r "Newtonsoft.Json"
    #r "SendGrid"
    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);  

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    You may see an error message until you save the new code.
1. Select **Save** to save the function.

## Add SendGrid Key

To add your SendGrid API Key, you need to add it to your **Function Keys** as follows:

1. Select **Function Keys**.
1. Choose **+ New Function Key**.
1. Enter the *Name* and *Value* of the API Key you created before.
1. Click **OK.**

    :::image type="content" source="media/howto-create-custom-rules/add-key.png" alt-text="Screenshot of Add Sangrid Key.":::


## Configure HttpTrigger function to use SendGrid

To send emails with SendGrid, you need to configure the bindings for your function as follows:

1. Select **Integrate**.
1. Choose **Add Output** under **HTTP ($return)**.
1. Select **Delete.**
1. Choose **+ New Output**.
1. For Binding Type, then choose **SendGrid**.
1. For SendGrid API Key Setting Type, click New.
1. Enter the *Name* and *Value* of your SendGrid API key.
1. Add the following information:

| Setting | Value |
| ------- | ----- |
| Message parameter name | Choose your name |
| To address | Choose the name of your To Address |
| From address | Choose the name of your From Address |
| Message subject | Enter your subject header |
| Message text | Enter the message from your integration |

1. Select **OK**.

    :::image type="content" source="media/howto-create-custom-rules/add-output.png" alt-text="Screenshot of Add SandGrid Output.":::


### Test the function works

To test the function in the portal, first choose **Logs** at the bottom of the code editor. Then choose **Test** to the right of the code editor. Use the following JSON as the **Request body**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

**[ログ]** パネルに関数ログのメッセージが表示されます。

```:::image type="content" source="media/howto-create-custom-rules/function-app-logs.png" alt-text="Function log output":::

After a few minutes, the **To** email address receives an email with the following content:

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Stream Analytics クエリを追加する

このソリューションでは、Stream Analytics クエリを使用して、デバイスが 120 秒以上にわたってテレメトリの送信を停止したときに検出します。 このクエリは、入力としてイベント ハブからのテレメトリを使用します。 ジョブはクエリの結果を関数アプリに送信します。 このセクションでは、Stream Analytics ジョブを構成します。

1. Azure portal で Stream Analytics ジョブに移動し、 **[ジョブ トポロジ]** で **[入力]** を選択し、 **[+ ストリーム入力の追加]** を選択してから、 **[イベント ハブ]** を選択します。
1. 次の表の情報を使用して、以前に作成したイベント ハブを使用して入力を構成してから、 **[保存]** を選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | 入力のエイリアス | centraltelemetry |
    | サブスクリプション | 該当するサブスクリプション |
    | Event Hub 名前空間 | Event Hub 名前空間 |
    | イベント ハブ名 | 既存のものを使用 - **centralexport** |

1. **[ジョブ トポロジ]** で **[出力]** を選択し、 **[+ 追加]** を選択してから、 **[Azure 関数]** を選択します。
1. 次の表の情報を使用して出力を構成してから、 **[保存]** を選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | 出力エイリアス | emailnotification |
    | サブスクリプション | 該当するサブスクリプション |
    | 関数アプリ | 関数アプリ |
    | Function  | HttpTrigger1 |

1. **[ジョブ トポロジ]** で **[クエリ]** を選び、既存のクエリを次の SQL に置き換えます。

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. **[保存]** を選択します。
1. Stream Analytics ジョブを開始するには、 **[概要]** 、 **[開始]** 、 **[今すぐ]** 、 **[開始]** の順に選択します。

    :::image type="content" source="media/howto-create-custom-rules/stream-analytics.png" alt-text="Stream Analytics のスクリーンショット。":::

## <a name="configure-export-in-iot-central"></a>IoT Central でエクスポートを構成する 

[Azure IoT Central アプリケーション マネージャー](https://aka.ms/iotcentral) Web サイト上で、作成した IoT Central アプリケーションに移動します。

このセクションでは、シミュレートされたデバイスからイベント ハブにテレメトリをストリーム配信するようにアプリケーションを構成します。 エクスポートを構成するには、次の手順に従います。

1. **[データのエクスポート]** ページに移動し、 **[+ 新規]** を選択してから、 **[Azure Event Hubs]** を選択します。
1. 以下の設定を使用してエクスポートを構成してから、 **[保存]** を選択します。 

    | 設定 | 値 |
    | ------- | ----- |
    | 表示名 | Event Hubs へのエクスポート |
    | 有効 | On |
    | エクスポートするデータの種類 | 製品利用統計情報 |
    | エンリッチメント | エクスポートされたデータの編成方法についての必要なキーと値を入力します | 
    | 宛先 | 新規作成し、データのエクスポート先の情報を入力します |

    :::image type="content" source="media/howto-create-custom-rules/cde-configuration.png" alt-text="データのエクスポートのスクリーンショット。":::

エクスポートの状態が **[実行中]** になるまで待ってから、続行します。

## <a name="test"></a>テスト

ソリューションをテストするために、IoT Central からシミュレートされた停止しているデバイスへの継続的データ エクスポートを無効にすることができます。

1. IoT Central アプリケーションで **[継続的データ エクスポート]** ページに移動し、 **[Event Hubs にエクスポート]** のエクスポート構成を選択します。
1. **[有効]** を **[オフ]** に設定し、 **[保存]** を選択します。
1. 少なくとも 2 分後に、次の例の内容のような 1 つまたは複数のメールが **[宛先]** メール アドレスに届きます。

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>片付ける

この手順の後で片付けて、不要なコストがかからないようにするには、Azure portal で **DetectStoppedDevices** リソース グループを削除します。

アプリケーション内の **[管理]** ページから IoT Central アプリケーションを削除することができます。

## <a name="next-steps"></a>次のステップ

この攻略ガイドで学習した内容は次のとおりです。

* *継続的データ エクスポート* を使用してテレメトリをストリーム配信します。
* デバイスがデータの送信を停止したときに検出する Stream Analytics クエリを作成します。
* Azure Functions と SendGrid サービスを使用してメール通知を送信します。

カスタム ルールと通知を作成する方法がわかったので、次は [カスタム分析を使用して Azure IoT Central を拡張する](howto-create-custom-analytics.md)方法を学習することをお勧めします。
