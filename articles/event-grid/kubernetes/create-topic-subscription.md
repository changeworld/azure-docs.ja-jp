---
title: Kubernetes 上の Azure Event Grid - イベント ハンドラーとしての Webhook
description: この記事では、Azure Arc に接続されている Kubernetes クラスターで Event Grid トピックを作成し、そのトピックのサブスクリプションを作成する方法について説明します。
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: quickstart
ms.openlocfilehash: c0e2a4422cea681a3bccee0739b8c26350803eb8
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386983"
---
# <a name="route-cloud-events-to-webhooks-with-azure-event-grid-on-kubernetes"></a>Kubernetes 上の Azure Event Grid でクラウド イベントを Webhook にルーティングする
このクイックスタートでは、Kubernetes 上の Event Grid でトピックを作成し、トピックのサブスクリプションを作成し、サンプル イベントをトピックに送信してシナリオをテストします。 

[!INCLUDE [event-grid-preview-feature-note.md](../../../includes/event-grid-preview-feature-note.md)]


## <a name="prerequisites"></a>前提条件

1. [Kubernetes クラスターを Azure Arc に接続する](../../azure-arc/kubernetes/quickstart-connect-cluster.md)。
1. [Kubernetes クラスターに Event Grid 拡張機能をインストールする](install-k8s-extension.md)。 この拡張機能によって、Event Grid を Kubernetes クラスターにデプロイします。 
1. [カスタムの場所を作成する](../../azure-arc/kubernetes/custom-locations.md)。 カスタムの場所はクラスター内の名前空間を表し、トピックとイベント サブスクリプションがデプロイされる場所です。

## <a name="create-a-topic"></a>トピックを作成する

### <a name="azure-cli"></a>Azure CLI
次の Azure CLI コマンドを実行して、トピックを作成します。

```azurecli-interactive
az eventgrid topic create --name <EVENT GRID TOPIC NAME> \
                        --resource-group <RESOURCE GROUP NAME> \
                        --location <REGION> \
                        --kind azurearc \
                        --extended-location-name /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ExtendedLocation/customLocations/<CUSTOM LOCATION NAME> \
                        --extended-location-type customlocation \
                        --input-schema CloudEventSchemaV1_0
```
コマンドを実行する前に、プレースホルダーの値を指定します。
- Event Grid トピックを作成する必要がある Azure リソース グループの名前。 
- トピックの名前。 
- トピックのリージョン。
- カスタムの場所のリソース ID で、次の値を指定します。
    - カスタムの場所が存在する Azure サブスクリプションの ID。
    - カスタムの場所が含まれているリソース グループの名前。
    - カスタムの場所の名前

CLI コマンドの詳細については、「[`az eventgrid topic create`](/cli/azure/eventgrid/topic#az_eventgrid_topic_create)」を参照してください。

## <a name="create-a-message-endpoint"></a>メッセージ エンドポイントの作成
カスタム トピックのサブスクリプションを作成する前に、イベント メッセージのエンドポイントを作成します。 通常、エンドポイントは、イベント データに基づくアクションを実行します。 このクイック スタートを簡素化するために、イベント メッセージを表示する[構築済みの Web アプリ](https://github.com/Azure-Samples/azure-event-grid-viewer)をデプロしします。 デプロイされたソリューションには、App Service プラン、App Service Web アプリ、および GitHub からのソース コードが含まれています。

1. この記事ページで **[Deploy to Azure]\(Azure にデプロイ\)** を選択して、ソリューションを自分のサブスクリプションにデプロイします。 Azure portal で、パラメーターの値を指定します。

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"  alt="Button to Deploy to Aquent." /></a>
1. デプロイが完了するまでに数分かかる場合があります。 デプロイが成功した後で、Web アプリを表示して、実行されていることを確認します。 Web ブラウザーで `https://<your-site-name>.azurewebsites.net` にアクセスします

    デプロイが失敗した場合は、エラー メッセージを確認してください。 Web サイト名が既に取得されていることが原因である可能性があります。 テンプレートを再度デプロイし、サイトに別の名前を選択します。 
1. サイトは表示されますが、イベントはまだ送信されていません。

   ![新しいサイトを表示する](../media/custom-event-quickstart-portal/view-site.png)

## <a name="create-a-subscription"></a>サブスクリプションの作成
サブスクライバーは、トピックに発行されたイベントの受信登録ができます。 イベントを受信するには、関心のあるトピックの Event Grid サブスクリプションを作成する必要があります。 イベント サブスクリプションでは、これらのイベントが送信される宛先を定義します。 サポートされているすべての宛先またはハンドラーの詳細については、「[イベント ハンドラー](event-handlers.md)」を参照してください。


### <a name="azure-cli"></a>Azure CLI
WebHook (HTTPS エンドポイント) の宛先を持つイベント サブスクリプションを作成するには、次の Azure CLI コマンドを実行します。

```azurecli-interactive
az eventgrid event-subscription create --name <EVENT SUBSCRIPTION NAME> \
                                    --source-resource-id /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<TOPIC'S RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAme> \
                                    --endpoint https://<SITE NAME>.azurewebsites.net/api/updates
```

コマンドを実行する前に、プレースホルダーの値を指定します。
- 作成されるイベント サブスクリプションの名前。 

- **トピックのリソース ID** で、次の値を指定します。
    - サブスクリプションを作成する Azure サブスクリプションの ID。 
    - トピックが含まれているリソース グループの名前。
    - トピックの名前。 
- エンドポイントには、Event Grid ビューアーの Web サイトの名前を指定します。
    
CLI コマンドの詳細については、「[`az eventgrid event-subscription create`](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create)」を参照してください。


## <a name="send-events-to-the-topic"></a>トピックにイベントを送信する
1. 次のコマンドを実行して、トピックの **エンドポイント** を取得します。コマンドをコピーして貼り付けた後、コマンドを実行する前に **トピック名** と **リソース グループ名** を更新してください。 このトピック エンドポイントにサンプル イベントを発行します。 

    ```azurecli
    az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv
    ```
2. 次のコマンドを実行して、カスタム トピックの **キー** を取得します。コマンドをコピーして貼り付けた後、コマンドを実行する前に **トピック名** と **リソース グループ** 名を更新してください。 これはトピックの主キーです。 このキーを Azure portal から取得するには、 **[Event Grid トピック]** ページの **[アクセス キー]** タブに切り替えます。 カスタム トピックにイベントを投稿できるようにするには、アクセス キーが必要です。 

    ```azurecli
    az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv
    ```
3. 次のコンテンツを使用して **evt.json** という名前のファイルを作成します。 

    ```json
    [{
          "specVersion": "1.0",
          "type" : "orderCreated",
          "source": "myCompanyName/us/webCommerceChannel/myOnlineCommerceSiteBrandName",
          "id" : "eventId-n",
          "time" : "2020-12-25T20:54:07+00:00",
          "subject" : "account/acct-123224/order/o-123456",
          "dataSchema" : "1.0",
          "data" : {
             "orderId" : "123",
             "orderType" : "PO",
             "reference" : "https://www.myCompanyName.com/orders/123"
          }
    }]
    ```
4. 次の **Curl** コマンドを実行して、イベントを送信します。 コマンドを実行する前に、手順 1 および 2 で指定したエンドポイントの URL とキーを指定します。 

    ```
    curl -k -X POST -H "Content-Type: application/cloudevents-batch+json" -H "aeg-sas-key: <KEY FROM STEP 2>" -g -d @evt.json <ENDPOINT URL from STEP 1>
    ```

    > [!NOTE]
    > プログラミング言語を使用してクラウド イベントを送信する方法については、次のサンプルを参照してください。 
    > - [C#](/samples/azure/azure-sdk-for-net/azure-event-grid-sdk-samples/)。
    > - [Java](/samples/azure/azure-sdk-for-java/eventgrid-samples/)
    > - [JavaScript](/samples/azure/azure-sdk-for-js/eventgrid-javascript/) および [TypeScript](/samples/azure/azure-sdk-for-js/eventgrid-typescript/)
    > - [Python](/samples/azure/azure-sdk-for-python/eventgrid-samples/)

### <a name="verify-in-the-event-grid-viewer"></a>Event Grid ビューアーでの確認
以上でイベントがトリガーされ、そのメッセージが、Event Grid によってサブスクライブ時に構成したエンドポイントに送信されました。 Web アプリを表示して、送信したイベント確認します。

:::image type="content" source="./media/create-topic-subscription/viewer-received-event.png" alt-text="受信したイベントを Event Grid ビューアーで表示する":::

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。 
- [イベント ハンドラーと変換先](event-handlers.md) - Kubernetes での Event Grid でサポートされているすべてのイベント ハンドラーと宛先についての情報を提供します。 
- [イベントのフィルター処理](filter-events.md) - イベント サブスクリプションでのフィルター イベントに関する情報を提供します。 