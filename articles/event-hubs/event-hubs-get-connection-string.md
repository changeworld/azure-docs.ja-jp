---
title: 接続文字列を取得する - Azure Event Hubs | Microsoft Docs
description: この記事では、クライアントが Azure Event Hubs への接続に使用できる接続文字列を取得する方法について説明します。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 263dba970b015d466b3fd46f390a063e87de2e2c
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53093318"
---
# <a name="get-an-event-hubs-connection-string"></a>Event Hubs の接続文字列の取得

Event Hubs を使用するには、Event Hubs の名前空間を作成する必要があります。 名前空間は、複数の Event Hubs/Kafka トピックを収容できるスコープ コンテナーです。 この名前空間より、一意の [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) が提供されます。 名前空間を作成した後は、Event Hubs との通信に必要な接続文字列を取得できます。

Azure Event Hubs の接続文字列には、次のコンポーネントが埋め込まれています。

* FQDN = 作成した Event Hubs 名前空間の FQDN (これには、Event Hubs 名前空間の名前に続けて servicebus.windows.net が含まれます)
* SharedAccessKeyName = アプリケーションの SAS キーに選んだ名前
* SharedAccessKey = キーの生成された値。

接続文字列テンプレートは次のようになります
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

接続文字列は、たとえば次のようになります `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

この記事では、接続文字列を取得するさまざまな方法について説明します。

## <a name="get-connection-string-from-the-portal"></a>ポータルから接続文字列を取得する

Event Hubs 名前空間を作成したら、下に示すように、ポータルの [概要] セクションで接続文字列を取得できます。

![Event Hubs の接続文字列](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)

[概要] セクションの接続文字列のリンクをクリックすると、下の図に示すように、[SAS ポリシー] タブが開きます。

![Event Hubs の SAS ポリシー](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)

新しい SAS ポリシーを追加して接続文字列を取得するか、既に作成されている既定のポリシーを使用します。 ポリシーを開くと、下の図に示すように、接続文字列が取得されます。

![Event Hubs の接続文字列の取得](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Azure PowerShell を使用した接続文字列の取得
下に示すように、Get-AzureRmEventHubNamespaceKey を使用して、指定するポリシー/ルール名の接続文字列を取得することができます。

```azurepowershell-interactive
Get-AzureRmEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

詳細については、[Azure Event Hubs の PowerShell モジュール](https://docs.microsoft.com/powershell/module/azurerm.eventhub/get-azurermeventhubkey)に関するページを参照してください。

## <a name="getting-the-connection-string-with-azure-cli"></a>Azure CLI を使用した接続文字列の取得
次を使用して、名前空間の接続文字列を取得できます。

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

詳細については、[Event Hubs の Azure CLI](https://docs.microsoft.com/cli/azure/eventhubs) に関するページを参照してください。

## <a name="next-steps"></a>次の手順

Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs の概要](event-hubs-what-is-event-hubs.md)
* [Event Hub を作成する](event-hubs-create.md)
