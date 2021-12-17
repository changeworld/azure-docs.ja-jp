---
title: Azure Service Bus の名前空間とエンティティをプログラムで管理する
description: この記事では、Service Bus の名前空間とエンティティを動的に、またはプログラミングによってプロビジョニングする方法について説明します。
ms.topic: article
ms.date: 08/06/2021
ms.openlocfilehash: 2962d5d8aebcf0a24535b7d9c1538dc69bd11e65
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129709129"
---
# <a name="dynamically-provision-service-bus-namespaces-and-entities"></a>Service Bus の名前空間とエンティティの動的なプロビジョニング 
Azure Service Bus には、Service Bus の名前空間とエンティティを動的にプロビジョニングできるライブラリが用意されています。 これにより、複雑なデプロイとメッセージング シナリオが可能になり、プロビジョニングするエンティティをプログラムを使用して決定できます。

## <a name="overview"></a>概要
Azure Service Bus のリソースをプログラムで管理する方法は 2 つあります。 1 つ目は [Azure Resource Manager](../azure-resource-manager/management/overview.md) ベースのライブラリを使用する方法です。この方法では、名前空間、キュー、トピック、サブスクリプション、ルール、SAS ポリシーを管理できます。 Azure Resource Manager ベースのライブラリでは、Azure Active Directory を介した認証がサポートされますが、接続文字列を使用した認証はサポートされません。 2 つ目の方法は、メッセージの送受信に使用するものと同じ Service Bus クライアント ライブラリを利用する方法です。 クライアント ライブラリには、*既存の* 名前空間でキュー、トピック、サブスクリプション、ルールを管理するのに役立つ API も用意されています。 このライブラリでは接続文字列を使用した認証がサポートされています。 採用する方法を決めるときには以下を検討します。 

Azure Resource Manager ベースのライブラリは、キュー、トピック、サブスクリプションなどの Service Bus の名前空間とエンティティを管理する場合に、Azure portal、CLI、PowerShell と同じ機能を提供します。管理操作に Azure portal、CLI、または PowerShell を使用し、動的な方法を使用する場合、これらのライブラリの方が優れた選択肢になる可能性があります。 

ただし、メッセージの送受信などのサービス固有の操作に Service Bus クライアント ライブラリを既に使用していて、Service Bus のエンティティも管理する必要がある場合、同じライブラリを使用する方が便利な場合があります。 クライアント ライブラリには、Azure Resource Manager ベースのライブラリによって提供される管理機能のサブセットを提供する `ServiceBusAdministrationClient` があります (これは以前のライブラリでは `ServiceBusManagementClient` と呼ばれます)。 Azure Resource Manager ベースのライブラリでは Service Bus の名前空間とエンティティの両方を管理できますが、クライアント ライブラリでは既存の名前空間内のエンティティのみを管理できますが、名前空間自体は管理 *できません*。

## <a name="manage-using-azure-resource-manager-based-libraries"></a>Azure Resource Manager ベースのライブラリを使用して管理する

Azure Resource Manager ベースのライブラリを使用すると、名前空間、キュー、トピック、サブスクリプション、ルール、SAS ポリシーを管理できます。  このライブラリは、Azure Active Directory (Azure AD) を使用した認証 *のみ* をサポートしています。接続文字列はサポートしていません。 

| Language | Package | ドキュメント | サンプル|
|-|-|-|-|
|.NET | [Microsoft.Azure.Management.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.Management.ServiceBus/) |[Microsoft.Azure.Management.ServiceBus の API リファレンス](/dotnet/api/microsoft.azure.management.servicebus)|[.NET](https://github.com/Azure-Samples/service-bus-dotnet-management/tree/a55185bef30d1763c1a8182a3361dbb548bad436) |
| Java | [azure-resourcemanager-servicebus](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-servicebus)|[com.azure.resourcemanager.servicebus の API リファレンス](/java/api/com.azure.resourcemanager.servicebus)|[Java](https://github.com/Azure-Samples/service-bus-java-manage-publish-subscribe-with-basic-features/tree/e4718a825e8fcfe58e5921770ff8084da67ccd89)|
| JavaScript |[@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus)|[@azure/arm-servicebus の API リファレンス](/javascript/api/@azure/arm-servicebus/)||
|Python|[azure-mgmt-servicebus](https://pypi.org/project/azure-mgmt-servicebus/)|[azure-mgmt-servicebus の API リファレンス](/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus)||


### <a name="fluent-net-and-java-libraries"></a>Fluent .NET および Java ライブラリ
Fluent バージョンの Azure Resource Manager ベースのライブラリがあります。 

|Language|Package|ドキュメント|
|-|-|-|
|.NET|[Microsoft.Azure.Management.ServiceBus.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.ServiceBus.Fluent/) |[Microsoft.Azure.Management.ServiceBus.Fluent の API リファレンス](/dotnet/api/microsoft.azure.management.servicebus.fluent) |
| Java|[azure-resourcemanager-servicebus](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-servicebus)|[com.azure.resourcemanager.servicebus.fluent の API リファレンス](/java/api/com.azure.resourcemanager.servicebus.fluent) |

## <a name="manage-using-service-bus-client-libraries"></a>Service Bus クライアント ライブラリを使用して管理する 

メッセージの送受信のような操作に使用される Service Bus クライアント ライブラリを使用して、*既存* の Service Bus の名前空間でキュー、トピック、サブスクリプション、ルールを管理することもできます。 この機能は、最新ライブラリの `ServiceBusAdministrationClient` および以前のライブラリの `ServiceBusManagementClient` を介して使用できます。 最新ライブラリの使用を強くお勧めします。

### <a name="latest-service-bus-libraries"></a>最新の Service Bus ライブラリ
|Language|Package|ドキュメント|サンプル|
|-|-|-|-|
|.NET|  [Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus)|[ServiceBusAdministrationClient](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient)|[.NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)|
|Java|[azure-messaging-servicebus](https://search.maven.org/artifact/com.azure/azure-messaging-servicebus)|[ServiceBusAdministrationAsyncClient](/java/api/com.azure.messaging.servicebus.administration.servicebusadministrationasyncclient)、[ServiceBusAdministrationClient](/java/api/com.azure.messaging.servicebus.administration.servicebusadministrationclient)| [Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)|
|JavaScript|[@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus)|[ServiceBusAdministrationClient](/javascript/api/@azure/service-bus/servicebusadministrationclient)|[JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)/[TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)|
|Python|[azure-servicebus](https://pypi.org/project/azure-servicebus/)|[ServiceBusAdministrationClient](/python/api/azure-servicebus/azure.servicebus.management.servicebusadministrationclient)|[Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)|

### <a name="legacy-service-bus-libraries"></a>以前の Service Bus ライブラリ
|Language|Package|ドキュメント|サンプル|
|-|-|-|-|
|.NET|[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)|[ManagementClient](/dotnet/api/microsoft.azure.servicebus.management.managementclient)|[.NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus)|
|Java|[azure-mgmt-servicebus](https://search.maven.org/artifact/com.microsoft.azure/azure-mgmt-servicebus)|[ManagementClientAsync](/java/api/com.microsoft.azure.servicebus.management.managementclientasync)、[ManagementClient](/java/api/com.microsoft.azure.servicebus.management.managementclient)|[Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java)|


## <a name="next-steps"></a>次のステップ
- 最新の Service Bus ライブラリを使用してキューとの間でメッセージを送受信する: [.NET](./service-bus-dotnet-get-started-with-queues.md#send-messages-to-the-queue)、[Java](./service-bus-java-how-to-use-queues.md)、[JavaScript](./service-bus-nodejs-how-to-use-queues.md)、[Python](./service-bus-python-how-to-use-queues.md)
- 最新の Service Bus ライブラリを使用してトピックにメッセージを送信し、サブスクリプションからメッセージを受信する: [.NET](./service-bus-dotnet-how-to-use-topics-subscriptions.md)、[Java](./service-bus-java-how-to-use-topics-subscriptions.md)、[JavaScript](./service-bus-nodejs-how-to-use-topics-subscriptions.md)、[Python](./service-bus-python-how-to-use-topics-subscriptions.md)