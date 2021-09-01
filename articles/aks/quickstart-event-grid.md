---
title: Azure Event Grid を使用して Azure Kubernetes Service イベントにサブスクライブする (プレビュー)
description: Azure Event Grid を使用して、Azure Kubernetes Service イベントにサブスクライブします。
services: container-service
author: zr-msft
ms.topic: article
ms.date: 07/12/2021
ms.author: zarhoads
ms.openlocfilehash: cc4801dc588216cbcfb2c6c2742643b2e0546e63
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2021
ms.locfileid: "113734238"
---
# <a name="quickstart-subscribe-to-azure-kubernetes-service-aks-events-with-azure-event-grid-preview"></a>クイックスタート: Azure Event Grid を使用して Azure Kubernetes Service (AKS) イベントにサブスクライブする (プレビュー)

Azure Event Grid は、パブリッシュ/サブスクライブ モデルを使用した画一的なイベント使用を提供する、完全に管理されたイベント ルーティング サービスです。

このクイックスタートでは、AKS クラスターを作成し、AKS イベントをサブスクライブします。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free)を作成できます。
* [Azure CLI がインストールされていること](/cli/azure/install-azure-cli)。

### <a name="register-the-eventgridpreview-preview-feature"></a>`EventgridPreview` プレビュー機能を登録する

この機能を使用するには、サブスクリプションで `EventgridPreview` 機能フラグも有効にする必要があります。

`EventgridPreview` 機能フラグは、次の例のとおり、[az feature register][az-feature-register] コマンドを使用して登録します。

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EventgridPreview"
```

状態が *[登録済み]* と表示されるまでに数分かかります。 登録の状態は、[az feature list][az-feature-list] コマンドで確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EventgridPreview')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="create-an-aks-cluster"></a>AKS クラスターを作成する

[az aks create][az-aks-create] コマンドを使用して、AKS クラスターを作成します。 次の例では、リソース グループ *MyResourceGroup* と、1 つのノードを持つ *MyResourceGroup* リソース グループ内の *MyAKS* という名前のクラスターを作成します。

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus  --node-count 1 --generate-ssh-keys
```

## <a name="subscribe-to-aks-events"></a>AKS イベントをサブスクライブする

[az eventhubs namespace create][az-eventhubs-namespace-create] と [az eventhubs eventhub create][az-eventhubs-eventhub-create] を使用して、名前空間とイベント ハブを作成します。 次の例では、名前空間 *MyNamespace* と、*MyNamespace* 内のイベント ハブ *MyEventGridHub* を、いずれも *MyResourceGroup* リソース グループ内に作成します。

```azurecli
az eventhubs namespace create --location eastus --name MyNamespace -g MyResourceGroup
az eventhubs eventhub create --name MyEventGridHub --namespace-name MyNamespace -g MyResourceGroup
```

> [!NOTE]
> 名前空間の *名前* は一意である必要があります。

[az eventgrid event-subscription create][az-eventgrid-event-subscription-create] を使用して AKS イベントをサブスクライブします。

```azurecli
SOURCE_RESOURCE_ID=$(az aks show -g MyResourceGroup -n MyAKS --query id --output tsv)
ENDPOINT=$(az eventhubs eventhub show -g MyResourceGroup -n MyEventGridHub --namespace-name MyNamespace --query id --output tsv)
az eventgrid event-subscription create --name MyEventGridSubscription \
--source-resource-id $SOURCE_RESOURCE_ID \
--endpoint-type eventhub \
--endpoint $ENDPOINT
```

`az eventgrid event-subscription list` を使用して AKS イベントへのサブスクリプションを確認します。

```azurecli
az eventgrid event-subscription list --source-resource-id $SOURCE_RESOURCE_ID
```

次の出力例は、*MyAKS* クラスターからのイベントをサブスクライブし、それらのイベントが *MyEventGridHub* イベント ハブに配信されるのを示しています。

```output
$ az eventgrid event-subscription list --source-resource-id $SOURCE_RESOURCE_ID
[
  {
    "deadLetterDestination": null,
    "deadLetterWithResourceIdentity": null,
    "deliveryWithResourceIdentity": null,
    "destination": {
      "deliveryAttributeMappings": null,
      "endpointType": "EventHub",
      "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNamespace/eventhubs/MyEventGridHub"
    },
    "eventDeliverySchema": "EventGridSchema",
    "expirationTimeUtc": null,
    "filter": {
      "advancedFilters": null,
      "enableAdvancedFilteringOnArrays": null,
      "includedEventTypes": [
        "Microsoft.ContainerService.NewKubernetesVersionAvailable"
      ],
      "isSubjectCaseSensitive": null,
      "subjectBeginsWith": "",
      "subjectEndsWith": ""
    },
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/MyAKS/providers/Microsoft.EventGrid/eventSubscriptions/MyEventGridSubscription",
    "labels": null,
    "name": "MyEventGridSubscription",
    "provisioningState": "Succeeded",
    "resourceGroup": "MyResourceGroup",
    "retryPolicy": {
      "eventTimeToLiveInMinutes": 1440,
      "maxDeliveryAttempts": 30
    },
    "systemData": null,
    "topic": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/MyResourceGroup/providers/microsoft.containerservice/managedclusters/MyAKS",
    "type": "Microsoft.EventGrid/eventSubscriptions"
  }
]
```

AKS イベントが発生すると、イベント ハブにそれらのイベントが表示されます。 たとえば、クラスターに対して利用できる Kubernetes バージョンの一覧が変更された場合、`Microsoft.ContainerService.NewKubernetesVersionAvailable` イベントが表示されます。 AKS によって出力されるイベントの詳細については、「[Event Grid ソースとしての Azure Kubernetes Service (AKS)][aks-events]」を参照してください。

## <a name="delete-the-cluster-and-subscriptions"></a>クラスターとサブスクリプションを削除する

[az group delete][az-group-delete] コマンドを使用して、リソース グループ、AKS クラスター、名前空間、イベント ハブ、およびすべての関連リソースを削除します。

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> クラスターを削除したとき、AKS クラスターで使用される Azure Active Directory サービス プリンシパルは削除されません。 サービス プリンシパルを削除する手順については、[AKS のサービス プリンシパルに関する考慮事項と削除][sp-delete]に関するページを参照してください。
> 
> マネージド ID を使用した場合、ID はプラットフォームによって管理されるので、削除する必要はありません。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Kubernetes クラスターをデプロイし、Azure Event Hub で AKS イベントをサブスクライブしました。

AKS の詳細を参照し、デプロイの例の完全なコードを確認するには、Kubernetes クラスター チュートリアルに進んでください。

> [!div class="nextstepaction"]
> [AKS チュートリアル][aks-tutorial]

[aks-events]: ../event-grid/event-schema-aks.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-eventhubs-namespace-create]: /cli/azure/eventhubs/namespace?view=azure-cli-latest&preserve-view=true#az-eventhubs-namespace-create
[az-eventhubs-eventhub-create]: /cli/azure/eventhubs/eventhub?view=azure-cli-latest&preserve-view=true#az-eventhubs-eventhub-create
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription?view=azure-cli-latest&preserve-view=true#az-eventgrid-event-subscription-create
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-group-delete]: /cli/azure/group#az_group_delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
