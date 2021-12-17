---
title: Kubernetes 上の Azure Event Grid - イベント ハンドラーとしての Webhook
description: この記事では、Azure Arc に接続されている Kubernetes クラスターで Event Grid トピックを作成し、そのトピックのサブスクリプションを作成する方法について説明します。
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 06/17/2021
ms.topic: quickstart
ms.openlocfilehash: aa4c83d5e702c8f39a88c285c716a84ba2716d45
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337568"
---
# <a name="route-cloud-events-to-webhooks-with-azure-event-grid-on-kubernetes"></a>Kubernetes 上の Azure Event Grid でクラウド イベントを Webhook にルーティングする
このクイックスタートでは、Kubernetes 上の Event Grid でトピックを作成し、トピックのサブスクリプションを作成し、サンプル イベントをトピックに送信してシナリオをテストします。 

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]


## <a name="prerequisites"></a>前提条件

1. [Kubernetes クラスターを Azure Arc に接続する](../../azure-arc/kubernetes/quickstart-connect-cluster.md)。
1. [Kubernetes クラスターに Event Grid 拡張機能をインストールする](install-k8s-extension.md)。 この拡張機能によって、Event Grid を Kubernetes クラスターにデプロイします。 


## <a name="create-a-custom-location"></a>カスタムの場所を作成する
Azure の場所の拡張としてカスタムの場所を使用すると、Event Grid トピックのようなリソースをデプロイするターゲットの場所として、Azure Arc 対応 Kubernetes クラスターを使用できます。 カスタムの場所はクラスター内の名前空間を表し、トピックとイベント サブスクリプションをデプロイする場所となります。 このセクションでは、カスタムの場所を作成します。 

1. Azure Arc クラスター、リソース グループ、カスタムの場所の名前の値を保持するため、次の変数を宣言します。 これらのステートメントをエディターにコピーし、値を置き換え、コピーして bash ウィンドウに貼り付けます。  

    ```azurecli-interactive
    resourcegroupname="<AZURE RESOURCE GROUP NAME>"
    arcclustername="<AZURE ARC CLUSTER NAME>"
    customlocationname="<CUSTOM LOCATION NAME>"
    ```
1. Azure Arc に接続されたクラスターのリソース ID を取得します。 コマンドを実行する前に、Azure Arc クラスターの名前とリソース グループ パラメーターの値を更新します。 

    ```azurecli-interactive
    hostresourceid=$(az connectedk8s show -n $arcclustername -g $resourcegroupname --query id -o tsv)    
    ```
1. Event Grid 拡張機能のリソース ID を取得します。 このステップでは、Event Grid 拡張機能に指定した名前が **eventgrid-ext** であるものとします。Azure Arc クラスターとリソース グループの名前を更新してから、コマンドを実行します。 

    ```azurecli-interactive
    clusterextensionid=$(az k8s-extension show --name eventgrid-ext --cluster-type connectedClusters -c $arcclustername -g $resourcegroupname  --query id -o tsv)    
    ```
1. 上の 2 つの値を使用して、カスタムの場所を作成します。 コマンドを実行する前に、カスタムの場所とリソース グループの名前を更新します。 

    ```azurecli-interactive
    az customlocation create -n $customlocationname -g $resourcegroupname --namespace arc --host-resource-id $hostresourceid --cluster-extension-ids $clusterextensionid    
    ```
1. カスタムの場所のリソース ID を取得します。 コマンドを実行する前に、カスタムの場所の名前を更新します。 

    ```azurecli-interactive
    customlocationid=$(az customlocation show -n $customlocationname -g $resourcegroupname --query id -o tsv)    
    ```

    カスタムの場所の作成の詳細については、「[Azure Arc 対応 Kubernetes にカスタムの場所を作成および管理する](../../azure-arc/kubernetes/custom-locations.md)」を参照してください。 

## <a name="create-a-topic"></a>トピックを作成する
このセクションでは、前のステップで作成したカスタムの場所にトピックを作成します。 リソース グループとイベント グリッド トピックの名前を更新してから、コマンドを実行します。 米国東部以外の場所を使用している場合は、場所を更新します。 

1. トピック名を保持するための変数を宣言します。 

    ```azurecli-interactive
    topicname="<TOPIC NAME>"
    ```
4. 次のコマンドを実行して、トピックを作成します。 

    ```azurecli-interactive
    az eventgrid topic create -g $resourcegroupname --name $topicname --kind azurearc --extended-location-name $customlocationid --extended-location-type customlocation --input-schema CloudEventSchemaV1_0 --location $region    
    ```

    CLI コマンドの詳細については、「[`az eventgrid topic create`](/cli/azure/eventgrid/topic#az_eventgrid_topic_create)」を参照してください。

## <a name="create-a-message-endpoint"></a>メッセージ エンドポイントの作成

カスタム トピックのサブスクリプションを作成する前に、イベント メッセージのエンドポイントを作成します。 通常、エンドポイントは、イベント データに基づくアクションを実行します。 このクイック スタートを簡素化するために、イベント メッセージを表示する[構築済みの Web アプリ](https://github.com/Azure-Samples/azure-event-grid-viewer)をデプロしします。 デプロイされたソリューションには、App Service プラン、App Service Web アプリ、および GitHub からのソース コードが含まれています。

1. この記事ページで **[Deploy to Azure]\(Azure にデプロイ\)** を選択して、ソリューションを自分のサブスクリプションにデプロイします。 Azure portal で、パラメーターの値を指定します。

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"  alt="Button to deploy to Azure."></a>
1. デプロイが完了するまでに数分かかる場合があります。 デプロイが成功した後で、Web アプリを表示して、実行されていることを確認します。 Web ブラウザーで `https://<your-site-name>.azurewebsites.net` にアクセスします

    デプロイが失敗した場合は、エラー メッセージを確認してください。 Web サイト名が既に取得されていることが原因である可能性があります。 テンプレートを再度デプロイし、サイトに別の名前を選択します。 
1. サイトは表示されますが、イベントはまだ送信されていません。

   ![新しいサイトを表示する](../media/custom-event-quickstart-portal/view-site.png)

## <a name="create-a-subscription"></a>サブスクリプションの作成
サブスクライバーは、トピックに発行されたイベントの受信登録ができます。 イベントを受信するには、関心のあるトピックの Event Grid サブスクリプションを作成する必要があります。 イベント サブスクリプションでは、これらのイベントが送信される宛先を定義します。 サポートされているすべての宛先またはハンドラーの詳細については、「[イベント ハンドラー](event-handlers.md)」を参照してください。

WebHook (HTTPS エンドポイント) の宛先を使用してイベント サブスクリプションを作成するには、イベント サブスクリプションの名前を入力し、Web サイトの名前を更新して、次のコマンドを実行します。

```azurecli-interactive
topicid=$(az eventgrid topic show --name $topicname --resource-group $resourcegroupname --query id -o tsv)
az eventgrid event-subscription create --name <EVENT SUBSCRIPTION NAME> --source-resource-id $topicid --endpoint https://<SITE NAME>.azurewebsites.net/api/updates
```


CLI コマンドの詳細については、「[`az eventgrid event-subscription create`](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create)」を参照してください。

## <a name="send-events-to-the-topic"></a>トピックにイベントを送信する
1. 次のコマンドを実行して、トピックの **エンドポイント** を取得します。コマンドをコピーして貼り付けた後、コマンドを実行する前に **トピック名** と **リソース グループ名** を更新してください。 このトピック エンドポイントにサンプル イベントを発行します。 

    ```azurecli
    az eventgrid topic show --name $topicname -g $resourcegroupname --query "endpoint" --output tsv
    ```
2. 次のコマンドを実行して、カスタム トピックの **キー** を取得します。コマンドをコピーして貼り付けた後、コマンドを実行する前に **トピック名** と **リソース グループ** 名を更新してください。 これはトピックの主キーです。 このキーを Azure portal から取得するには、 **[Event Grid トピック]** ページの **[アクセス キー]** タブに切り替えます。 カスタム トピックにイベントを投稿できるようにするには、アクセス キーが必要です。 

    ```azurecli
    az eventgrid topic key list --name $topicname -g $resourcegroupname --query "key1" --output tsv
    ```
1. 次の **Curl** コマンドを実行して、イベントを送信します。 コマンドを実行する前に、手順 1 および 2 で指定したエンドポイントの URL とキーを指定します。 

    ```bash
    curl  -k -X POST -H "Content-Type: application/cloudevents-batch+json" -H "aeg-sas-key: <KEY_FROM_STEP_2>" -g <ENDPOINT_URL_FROM_STEP_1> \
    -d  '[{ 
          "specversion": "1.0",
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
    }]'
    ```
    
    手順 1 のトピック エンドポイント URL がプライベート IP アドレスの場合 (Event Grid ブローカーのサービスの種類が ClusterIP の場合など)、クラスター内の別のポッド内から **Curl** を実行して、その IP アドレスにアクセスできます。 たとえば、次の手順を実行できます。

    1. 次の構成でマニフェスト ファイルを作成します。 必要に応じて、``dnsPolicy`` を調整することもできます。 詳細については、「[サービスとポッドの DNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/)」を参照してください。
    
        ```yml
        apiVersion: v1
        kind: Pod
        metadata:
            name: test-pod2
        spec:
            containers:
              - name: nginx
                image: nginx
            hostNetwork: true
            dnsPolicy: ClusterFirstWithHostNet       
        ```
    1. ポッドを作成します。
        ```bash
            kubectl apply -f <name_of_your_yaml_manifest_file>
        ```
    1. ポッドが実行されていることを確認します。
        ```bash
            kubectl get pod test-pod
        ```
    1. コンテナーからシェル セッションを開始します。
        ```bash
            kubectl exec --stdin --tty test-pod -- /bin/bash
        ```

    この時点で、クラスター内の実行中のコンテナーからのシェル セッションが開始され、前の手順で説明した **Curl** コマンドを実行できます。

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
