<properties
    pageTitle="Azure ポータルを使用して Service Bus 名前空間を作成する | Microsoft Azure"
    description="Service Bus の使用を開始するには、名前空間が必要です。Azure ポータルを使用して名前空間を作成する方法を次に示します。"
    services="service-bus"
    documentationCenter=".net"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="jotaub"/>

# Azure ポータルを使用して Service Bus 名前空間を作成する

名前空間は、すべてのメッセージング コンポーネントの共通コンテナーです。複数のキューとトピックを 1 つの名前空間に格納できます。多くの場合、名前空間はアプリケーション コンテナーとして機能します。現在、Service Bus 名前空間の作成には 2 つの方法があります。

1.	Azure ポータル (この記事)

2.	[リソース マネージャーのテンプレート][create-namespace-using-arm]

## Azure ポータルでの名前空間の作成

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

お疲れさまでした。 これで、Service Bus メッセージング名前空間が作成されました。

## 次のステップ

Azure Service Bus メッセージングのさらに高度な機能を紹介するサンプルについては、[GitHub リポジトリ](https://github.com/Azure-Samples/azure-servicebus-messaging-samples][github-samples)を参照してください。

[create-namespace-using-arm]: ../service-bus-messaging/service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

<!---HONumber=AcomDC_0928_2016-->