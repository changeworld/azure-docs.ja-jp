---
title: イベント配信、マネージド サービス ID、およびプライベート リンク
description: この記事では、Azure イベント グリッド トピックに対してマネージド サービス ID を有効にする方法を説明します。 これを使用して、サポートされている配信先にイベントを転送します。
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 76f10b4627dc9578b1e616a868eab03431b59b69
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625280"
---
# <a name="event-delivery-with-a-managed-identity"></a>マネージド ID を使用したイベント配信
この記事では、Azure Event Grid システム トピック、カスタム トピック、またはドメインに[マネージド サービス ID](../active-directory/managed-identities-azure-resources/overview.md) を使用する方法を説明します。 これを使用して、Service Bus のキューとトピック、イベント ハブ、ストレージ アカウントなどの、サポートされている配信先にイベントを転送します。



## <a name="prerequisites"></a>前提条件
1. システム割り当て ID をシステム トピック、カスタム トピック、またはドメインに割り当てます。 
    - カスタム トピックとドメインについては、[カスタム トピックとドメインのマネージド ID の有効化](enable-identity-custom-topics-domains.md)に関する記事を参照してください。 
    - システム トピックについては、[システム トピックのマネージド ID の有効化](enable-identity-system-topics.md)に関する記事を参照してください。
1. 配信先 (Service Bus キューなど) の適切なロール (Service Bus データ送信者など) に ID を追加する。 詳細な手順については、[配信先の Azure ロールへの ID の追加](add-identity-roles.md)に関する記事を参照してください。

    > [!NOTE]
    > 現時点では、[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用してイベントを配信することはできません。 詳細については、この記事の最後にある「[プライベート エンドポイント](#private-endpoints)」セクションを参照してください。 

## <a name="create-event-subscriptions-that-use-an-identity"></a>ID を使用するイベント サブスクリプションを作成する
システム マネージド ID を持つイベント グリッド カスタム トピック、システム トピック、またはドメインを作成し、その ID を配信先の適切なロールに追加したら、ID を使用してサブスクリプションを作成することができます。 

### <a name="use-the-azure-portal"></a>Azure ポータルの使用
イベント サブスクリプションを作成する際、 **[エンドポイントの詳細]** セクションにエンドポイントに対するシステム割り当て ID の使用を有効にするオプションが表示されます。 

![Service Bus キューへのイベント サブスクリプションを作成する際に、ID を有効にする](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

また、 **[追加機能]** タブで、配信不能処理でシステム割り当て ID の使用を有効にすることもできます。 

![配信不能処理でシステム割り当て ID を有効にする](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="use-the-azure-cli---service-bus-queue"></a>Azure CLI の使用 - Service Bus キュー 
このセクションでは、Azure CLI を使用してシステム割り当て ID の使用を有効にし、イベントを Service Bus キューに配信する方法について学びます。 ID は **Azure Service Bus データ送信者** ロールのメンバーである必要があります。 また、配信不能処理に使用されるストレージ アカウントの **ストレージ BLOB データ共同作成者** ロールのメンバーである必要もあります。 

#### <a name="define-variables"></a>変数の定義
最初に、CLI コマンドで使用する次の変数の値を指定します。 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>配信用に、マネージド ID を使用してイベント サブスクリプションを作成する 
このサンプル コマンドでは、エンドポイントの種類を **Service Bus キュー** に設定して、イベント グリッド カスタム トピックのイベント サブスクリプションを作成します。 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery-and-dead-lettering"></a>配信および配信不能処理用に、マネージド ID を使用してイベント サブスクリプションを作成する
このサンプル コマンドでは、エンドポイントの種類を **Service Bus キュー** に設定して、イベント グリッド カスタム トピックのイベント サブスクリプションを作成します。 また、システム マネージド ID を配信不能処理に使用することも指定します。 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="use-the-azure-cli---event-hubs"></a>Azure CLI の使用 - Event Hubs 
このセクションでは、Azure CLI を使用し、システム割り当て ID の使用を有効にして、イベントをイベント ハブに配信する方法を学びます。 ID は、**Azure Event Hubs データ送信者** ロールのメンバーである必要があります。 また、配信不能処理に使用されるストレージ アカウントの **ストレージ BLOB データ共同作成者** ロールのメンバーである必要もあります。 

#### <a name="define-variables"></a>変数の定義
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>配信用に、マネージド ID を使用してイベント サブスクリプションを作成する 
このサンプル コマンドでは、エンドポイントの種類を **Event Hubs** に設定して、イベント グリッド カスタム トピックのイベント サブスクリプションを作成します。 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>配信用および配信不能にするために、マネージド ID を使用してイベント サブスクリプションを作成する 
このサンプル コマンドでは、エンドポイントの種類を **Event Hubs** に設定して、イベント グリッド カスタム トピックのイベント サブスクリプションを作成します。 また、システム マネージド ID を配信不能処理に使用することも指定します。 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="use-the-azure-cli---azure-storage-queue"></a>Azure CLI の使用 - Azure Storage キュー 
このセクションでは、Azure CLI を使用し、システム割り当て ID の使用を有効にして、イベントを Azure Storage キューに配信する方法を学びます。 ID は、ストレージ アカウントの **ストレージ キュー データ メッセージ送信者** ロールのメンバーである必要があります。 また、配信不能処理に使用されるストレージ アカウントの **ストレージ BLOB データ共同作成者** ロールのメンバーである必要もあります。

#### <a name="define-variables"></a>変数の定義  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>配信用に、マネージド ID を使用してイベント サブスクリプションを作成する 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>配信用および配信不能にするために、マネージド ID を使用してイベント サブスクリプションを作成する 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```

## <a name="private-endpoints"></a>プライベート エンドポイント
現時点では、[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用してイベントを配信することはできません。 つまり、配信されたイベント トラフィックがプライベート IP 空間から外に出てはならないという、ネットワークの分離の厳格な要件がある場合はサポートされません。 

ただし、暗号化されたチャネルと、パブリック IP 空間を使用する送信者 (この場合は Event Grid) の既知の ID を使用してイベントを送信する安全な方法の呼び出しが要件で必要とされる場合は、この記事で示したように、システム マネージド ID が構成された Azure イベント グリッド カスタム トピックまたはドメインを使用して、Event Hubs、Service Bus、または Azure Storage サービスにイベントを配信できます。 その後、Azure Functions で構成されたプライベート リンクまたは仮想ネットワークにデプロイされた Webhook を使用して、イベントをプルできます。 次のサンプルを参照してください:「[Azure Functions を使用してプライベート エンドポイントに接続する](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/)」。

この構成では、トラフィックはパブリック IP/インターネット経由で Event Grid から Event Hubs、Service Bus、または Azure Storage に送られますが、チャネルを暗号化でき、Event Grid のマネージド ID が使用されます。 Azure Functions、または仮想ネットワークにデプロイされた Webhook を、プライベート リンク経由で Event Hubs、Service Bus、または Azure Storage を使用するように構成すると、トラフィックのそのセクションは確実に Azure 内に留まります。


## <a name="next-steps"></a>次のステップ
マネージド ID については、[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)に関する記事を参照してください。
