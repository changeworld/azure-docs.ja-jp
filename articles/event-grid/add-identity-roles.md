---
title: Azure Event Grid の配信先のロールにマネージド ID を追加する
description: この記事では、Azure Service Bus や Azure Event Hubs などの配信先の Azure ロールに、マネージド ID を追加する方法について説明します。
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 1578e4c24201614ce89351b3c3cee52a09cadc30
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280481"
---
# <a name="grant-managed-identity-the-access-to-event-grid-destination"></a>マネージド ID に Event Grid の配信先へのアクセスを許可する
このセクションでは、システム トピック、カスタム トピックまたはドメインの ID を Azure ロールに追加する方法について説明します。 

## <a name="prerequisites"></a>前提条件
次の記事の指示に従って、システムによって割り当てられたマネージド ID を割り当てます。

- [カスタム トピックまたはドメイン](enable-identity-custom-topics-domains.md)
- [システム トピック](enable-identity-system-topics.md)

## <a name="supported-destinations-and-azure-roles"></a>サポートされている配信先と Azure ロール
イベント グリッド カスタム トピックまたはドメインに対して ID を有効にすると、Azure によって Azure Active Directory 内で自動的に ID が作成されます。 カスタム トピックまたはドメインによってサポート対象の配信先にイベントを転送できるように、適切な Azure ロールにこの ID を追加します。 たとえば、ある Azure Event Hubs 名前空間の **Azure Event Hubs データ送信者** ロールに ID を追加すると、イベント グリッド カスタム トピックによってその名前空間のイベント ハブにイベントを転送できるようになります。 

現在、Azure イベント グリッドでは、システム割り当てマネージド ID で構成されたカスタム トピックやドメインによって、次の配信先にイベントを転送することをサポートしています。 この表は、カスタム トピックによってイベントを転送できるようにするために、ID を含めるべきロールも示しています。

| 到着地 | Azure ロール | 
| ----------- | --------- | 
| Service Bus のキューとトピック | [Azure Service Bus データ送信者](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Azure Event Hubs | [Azure Event Hubs データ送信者](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Azure BLOB ストレージ | [ストレージ BLOB データ共同作成者](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) |
| Azure Queue Storage |[ストレージ キュー データ メッセージ送信者](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) | 


## <a name="use-the-azure-portal"></a>Azure ポータルの使用
カスタム トピックやドメインによってイベントを配信先に転送できるようにするために、Azure portal を使用して、カスタム トピックやドメインの ID を適切なロールに割り当てることができます。 

次の例では、**msitesttopic** という名称のイベント グリッド カスタム トピックのマネージド ID を、キューまたはトピックのリソースが含まれている Service Bus の名前空間の **Azure Service Bus データ送信者** ロールに追加します。 名前空間レベルでロールに追加すると、イベント グリッド カスタム トピックによりイベントをその名前空間内のすべてのエンティティに転送できるようになります。 

1. [Azure portal](https://portal.azure.com) で、ご利用の **Service Bus 名前空間** に移動します。 
1. 左ペインの **[アクセス制御]** を選択します。 
1. **[ロールの割り当てを追加する]** セクションで **[追加]** を選択します。 
1. **[ロールの割り当ての追加]** ページで、次の手順のようにします。
    1. ロールを選択します。 この例では、**Azure Service Bus データ送信者** です。 
    1. イベント グリッド カスタム トピックまたはドメインの **ID** を選択します。 
    1. **[保存]** を選んで構成を保存します。

表に示されている他のロールへの ID の追加の手順も同様です。 

## <a name="use-the-azure-cli"></a>Azure CLI の使用
このセクションの例では、Azure CLI を使用して ID を Azure ロールに追加する方法を説明します。 サンプル コマンドは、イベント グリッド カスタム トピックのものです。 イベント グリッド ドメインのコマンドも同様です。 

### <a name="get-the-principal-id-for-the-custom-topics-system-identity"></a>カスタム トピックのシステム ID のプリンシパル ID を取得する 
最初に、カスタム トピックのシステム マネージド ID のプリンシパル ID を取得して、その ID を適切なロールに割り当てます。

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>様々なスコープでイベント ハブへのロールの割り当てを作成する 
次の CLI の例は、カスタム トピックの ID を、名前空間レベルまたはイベント ハブ レベルで **Azure Event Hubs データ送信者** ロールに追加する方法を示しています。 名前空間レベルでロールの割り当てを作成すると、カスタム トピックによりイベントをその名前空間のすべてのイベント ハブに転送できるようになります。 ロールの割り当てをイベント ハブ レベルで作成すると、カスタム トピックによりイベントをその特定のイベント ハブにのみ転送できるようになります。 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>様々なスコープで Service Bus トピックへのロールの割り当てを作成する 
次の CLI の例は、イベント グリッド カスタム トピックの ID を、名前空間レベルまたは Service Bus トピック レベルで **Azure Service Bus データ送信者** ロールに追加する方法を示しています。 名前空間レベルでロールの割り当てを作成すると、イベント グリッド トピックによりイベントをその名前空間内のすべてのエンティティ (Service Bus キューまたはトピック) に転送できるようになります。 ロールの割り当てを Service Bus キューまたはトピックのレベルで作成すると、イベント グリッド カスタム トピックにより、その特定の Service Bus のキューまたはトピックにのみイベントを転送できるようになります。 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="next-steps"></a>次の手順
システムによって割り当てられた ID をシステム トピック、カスタム トピック、またはドメインに割り当て、配信先の適切なロールに ID を追加しました。ID を使用した配信先へのイベント配信については、「[マネージド ID を使用してイベントを配信する](managed-service-identity.md)」を参照してください。


