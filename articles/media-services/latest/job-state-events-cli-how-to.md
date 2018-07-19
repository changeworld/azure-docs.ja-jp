---
title: Azure Media Services イベントをカスタム Web エンドポイントにルーティングする | Microsoft Docs
description: Media Services のジョブ状態変更イベントをサブスクライブするには、Azure Event Grid を使用します。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: e9df0cd24ef890765b78c25a073d671889be10a7
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723744"
---
# <a name="route-azure-media-services-events-to-a-custom-web-endpoint-using-cli"></a>CLI を使用して Azure Media Services のイベントをカスタム Web エンドポイントにルーティングする

Azure Event Grid は、クラウドのイベント処理サービスです。 この記事では、Azure CLI を使用して Azure Media Services のジョブ状態変更イベントをサブスクライブし、イベントをトリガーして結果を表示します。 

通常、webhook や Azure 関数など、イベントに応答するエンドポイントが、イベントの送信先になります。 このチュートリアルでは、webhook の作成と設定方法を示します。

この記事の手順の最後に、イベント データがエンドポイントに送信済みであることを確認できます。

## <a name="log-in-to-azure"></a>Azure にログインする

[Azure portal](http://portal.azure.com) にログインし、以降の手順で示すように CLI コマンドを実行するために **CloudShell** を起動します。

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降が必要です。 お使いのバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

Media Services アカウント名、ストレージ名、およびリソース名として使用した値を覚えておいてください。

## <a name="enable-event-grid-resource-provider"></a>Event Grid リソース プロバイダーを有効にする

最初に行う必要があるのは、サブスクリプションで Event Grid リソース プロバイダーが有効になっていることの確認です。 

**Azure** Portal で、次の操作を行います。

1. [サブスクリプション] に移動します。
2. サブスクリプションを選択します。
3. [設定] で、[リソース プロバイダー] を選択します。
4. "EventGrid" を検索します。
5. Event Grid が登録されていることを確認します。 登録されていなければ、**[登録]** ボタンをクリックします。  

## <a name="create-a-generic-azure-function-webhook"></a>汎用の Azure Function webhook を作成する 

### <a name="create-a-message-endpoint"></a>メッセージ エンドポイントの作成

Event Grid のアーティクルをサブスクライブする前に、メッセージを収集して表示できるようにするエンドポイントを作成します。

[汎用 webhook](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function)に関する記事で説明されているように、汎用 webhook によってトリガーされる関数を作成します。 このチュートリアルでは、**C#** コードが使用されます。

Webhook が作成されたら、**Azure** Portal ウィンドウの上部にある "*[関数の URL の取得]*" をクリックすることで、URL をコピーします。 URL の最後の部分 (*&clientID=default*) は不要です。

![webhook を作成する](./media/job-state-events-cli-how-to/generic_webhook_files.png)

### <a name="validate-the-webhook"></a>webhook を検証する

Event Grid に独自の webhook エンドポイントを登録すると、エンドポイントの所有権を証明するための単純な検証コードを含む POST 要求が送信されます。 アプリからは検証コードをエコーで応答する必要があります。 Event Grid は、検証に合格していない webhook エンドポイントにイベントを配信しません。 詳細については、「[Event Grid security and authentication](https://docs.microsoft.com/azure/event-grid/security-authentication)」(Event Grid のセキュリティと認証) を参照してください。 このセクションでは、検証に合格するために定義する必要がある 2 つの部分を定義します。

#### <a name="update-the-source-code"></a>ソース コードを更新する

webhook を作成すると、**run.csx** ファイルがブラウザーに表示されます。 既定のコードを以下のコードに置き換えます。 

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"Webhook was triggered!");

    string jsonContent = await req.Content.ReadAsStringAsync();
    string eventGridValidation = 
        req.Headers.FirstOrDefault( x => x.Key == "Aeg-Event-Type" ).Value?.FirstOrDefault();

    dynamic eventData = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"event: {eventData}");

    if (eventGridValidation != String.Empty)
    {
        if (eventData[0].data.validationCode !=String.Empty && eventData[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent")
        {
            return req.CreateResponse(HttpStatusCode.OK, new 
            {
                validationResponse = eventData[0].data.validationCode
            });
        }
    }
    
    log.Info(jsonContent);

    return req.CreateResponse(HttpStatusCode.OK);
}
```

#### <a name="update-test-request-body"></a>テストの要求本文を更新する

**Azure** Portal ウィンドウの右側に、2 つのタブ (**[ファイルの表示]** と **[テスト]**) が表示されます。 **[テスト]** タブを選びます。**[要求本文]** に、次の json を貼り付けます。 そのまま貼り付けることができます。値を変更する必要はありません。

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2017-08-06T22:09:30.740323Z"
}
]
```

ウィンドウの上部にある **[保存] と [実行]** をクリックします。

![要求本文](./media/job-state-events-cli-how-to/generic_webhook_test.png)

## <a name="register-for-the-event-grid-subscription"></a>Event Grid サブスクリプションを登録する 

どのイベントを追跡するかを Event Grid に通知するアーティクルをサブスクライブします。次の例では、作成した Media Services アカウントをサブスクライブし、イベント通知のエンドポイントとして作成した Azure Function webhook の URL を渡します。 

`<event_subscription_name>` をイベント サブスクリプションの一意の名前に置き換えます。 `<resource_group_name>` と `<ams_account_name>` には、先ほど作成した値を使用します。  `<endpoint_URL>` には、エンドポイント URL を貼り付けます。 URL から "*&clientID=default*" を削除します。 サブスクライブ時にエンドポイントを指定することによって、そのエンドポイントに対するイベントのルーティングが Event Grid によって行われます。 

```cli
amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $amsResourceId \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

Media Services アカウントのリソース ID 値は、次のようになります。

/subscriptions/81212121-2f4f-4b5d-a3dc-ba0015515f7b/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amstestaccount

## <a name="test-the-events"></a>イベントをテストする

エンコード ジョブを作成します。 たとえば、クイックスタート: [ビデオ ファイルのストリーミング](stream-files-dotnet-quickstart.md)の説明に従います。

以上でイベントがトリガーされ、そのメッセージが、Event Grid によってサブスクライブ時に構成したエンドポイントに送信されました。 先ほど作成した webhook を参照します。 **[監視]** と **[更新]** をクリックします。 ジョブの状態変更イベントが表示されます。"Queued"、"Scheduled"、"Processing"、"Finished"、"Error"、"Canceled"、"Canceling"。  詳細については、「[Media Services イベントのスキーマ](media-services-event-schemas.md)」を参照してください。

例: 

```json
[{
  "topic": "/subscriptions/<subscription id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job id>",
  "eventType": "Microsoft.Media.JobStateChange",
  "eventTime": "2018-04-20T21:17:26.2534881",
  "id": "<id>",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

![イベントのテスト](./media/job-state-events-cli-how-to/test_events.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

引き続きこのストレージ アカウントとイベント サブスクリプションを使用する場合は、この記事で作成したリソースをクリーンアップしないでください。 引き続き使用する予定がない場合は、次のコマンドを使用して、この記事で作成したリソースを削除します。

`<resource_group_name>` は、先ほど作成したリソース グループに置き換えます。

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>次の手順

[イベントへの応答](reacting-to-media-services-events.md)

## <a name="see-also"></a>関連項目

[Azure CLI](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
