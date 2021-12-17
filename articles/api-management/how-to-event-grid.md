---
title: Azure API Management から Event Grid にイベントを送信する
description: このクイックスタートでは、Azure API Management インスタンス用の Event Grid イベントを有効にして、イベントをサンプル アプリケーションに送信します。
author: dlepow
ms.topic: how-to
ms.service: api-management
ms.author: danlep
ms.date: 11/2/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: b98e958869b30a90d7006020768cdc7d8c1d62c7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131063064"
---
# <a name="send-events-from-api-management-to-event-grid"></a>API Management から Event Grid にイベントを送信する

他のサービスにイベント通知を送信して、ダウンストリームのプロセスをトリガーできるように、API Management は [Azure Event Grid](../event-grid/overview.md) と統合されています。 Event Grid は、パブリッシュ/サブスクライブ モデルを使用する、フル マネージドのイベント ルーティング サービスです。 Event Grid は、[Azure Functions](../azure-functions/functions-overview.md) や [Azure Logic Apps](../logic-apps/logic-apps-overview.md) などの Azure s サービスの組み込みサポートを備えており、webhook を使って Azure 以外のサービスにイベント アラートを配信できます。

たとえば、Event Grid との統合を使用して、API Management インスタンスにユーザーが追加されるたびにデータベースを更新し、課金アカウントを作成し、メール通知を送信するアプリケーションを構築できます。

この記事では、API Management インスタンスの Event Grid イベントをサブスクライブし、イベントをトリガーし、データを処理するエンドポイントにイベントを送信します。 シンプルにするため、メッセージを収集して表示する 1 つのサンプル Web アプリにイベントを送信します。

:::image type="content" source="media/how-to-event-grid/event-grid-viewer-intro.png" alt-text="Event Grid ビューアーの API Management イベント":::

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]
- API Management サービスをまだ保有していない場合は、[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関するクイックスタートを完了します。
- API Management インスタンスで、[システム割り当てマネージド ID](api-management-howto-use-managed-service-identity.md#create-a-system-assigned-managed-identity) を有効にします。
- サンプル エンドポイントをデプロイする[リソース グループ](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)がない場合は作成します。

## <a name="create-an-event-endpoint"></a>イベント エンドポイントの作成

このセクションでは、Resource Manager テンプレートを使用して、構築済みのサンプル Web アプリケーションを Azure App Service にデプロイします。 その後、API Management インスタンスの Event Grid イベントをサブスクライブし、このアプリを、イベントの送信先エンドポイントとして指定します。

サンプル アプリをデプロイするには、Azure CLI、Azure PowerShell、または Azure portal を利用できます。 次の例では、Azure CLI の [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) コマンドを使用します。

* `RESOURCE_GROUP_NAME` を、既存のリソース グループの名前に設定します
* `SITE_NAME` を、Web アプリの一意の名前に設定します

  サイト名は Web アプリの完全修飾ドメイン名 (FQDN) の一部であるため、Azure 内で一意にする必要があります。 後のセクションで、Web ブラウザーを使用してアプリの FQDN に移動し、イベントを表示します。

```azurecli-interactive
RESOURCE_GROUP_NAME=<your-resource-group-name>
SITE_NAME=<your-site-name>

az deployment group create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

デプロイが成功したら (数分をかかる場合があります)、ブラウザーを開いて、ご自身の Web アプリに移動し、アプリが実行中であることを確認します。

`https://<your-site-name>.azurewebsites.net`

サンプル アプリが、イベント メッセージなしでレンダリングされているはずです。

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-api-management-events"></a>API Management イベントをサブスクライブする

Event Grid では、"*トピック*" をサブスクライブすることで、どのイベントを追跡し、どこにイベントを送信するかをその Event Grid に伝えます。 ここでは、API Management インスタンス内のイベントのサブスクリプションを作成します。

1. [Azure portal](https://portal.azure.com) で、API Management インスタンスに移動します。
1. **[イベント] > [+ イベント サブスクリプション]** を選択します。 
1. **[基本]** タブで次の操作を行います。
    * イベント サブスクリプションのわかりやすい **[名前]** を入力します。
    * **[イベントの種類]** で、Event Grid に送信する 1 つ以上の API Management イベントの種類を選択します。 この記事の例では、少なくとも **Microsoft.APIManagement.ProductCreated** を選択します。 
    * **[エンドポイントの詳細]** で、 **[Web Hook]** イベントの種類を選択し、 **[エンドポイントの選択]** をクリックして、Web アプリの URL を入力し、それに `api/updates` を付加します。 例: `https://myapp.azurewebsites.net/api/updates`.
    * **[選択の確認]** を選択します。
1. 残りのタブの設定は既定値のままにして、 **[作成]** を選択します。

    :::image type="content" source="media/how-to-event-grid/create-event-subscription.png" alt-text="Azure portal でのイベント サブスクリプションの作成":::

## <a name="trigger-and-view-events"></a>イベントをトリガーおよび表示する

サンプル アプリが稼働し、Event Grid で API Management インスタンスをサブスクライブしたら、いつでもイベントを生成できます。

たとえば、API Management インスタンスで[製品を作成](./api-management-howto-add-products.md)します。 イベント サブスクリプションに **Microsoft.APIManagement.ProductCreated** イベントが含まれる場合、製品を作成すると、Web アプリ エンドポイントにプッシュされるイベントがトリガーされます。 

Event Grid ビューアー Web アプリに移動すると、`ProductCreated` イベントが表示されています。 イベントの横にあるボタンを選択して、詳細を表示します。 

:::image type="content" source="media/how-to-event-grid/event-grid-viewer-product-created.png" alt-text="Event Grid ビューアーの製品の作成イベント":::

## <a name="event-grid-event-schema"></a>Event Grid イベント スキーマ

API Management イベントのデータには、`resourceUri` が含まれています。これにより、イベントをトリガーする API Management リソースが識別されます。 API Management イベントのメッセージ スキーマの詳細については、Event Grid のドキュメントを参照してください。

[API Management 用の Azure Event Grid イベント スキーマ](../event-grid/event-schema-api-management.md)

## <a name="next-steps"></a>次のステップ

* [Azure メッセージング サービスの中から選択する - Azure Event Grid、Event Hubs、および Service Bus](../event-grid/compare-messaging-services.md)
* [イベントのサブスクライブ](../event-grid/subscribe-through-portal.md)に関する詳細を確認します。
