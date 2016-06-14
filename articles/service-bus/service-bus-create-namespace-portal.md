<properties
   pageTitle="Azure ポータルを使用して Service Bus 名前空間を作成する | Microsoft Azure"
   description="Service Bus の使用を開始するには、名前空間が必要です。Azure ポータルを使用して名前空間を作成する方法を次に示します。"
   services="service-bus"
   documentationCenter=".net"
   authors="jtaubensee"
   manager="timlt"
   editor="sethmanheim"/>

<tags
   ms.service="service-bus"
   ms.devlang="tbd"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="dotnet"
   ms.workload="na"
   ms.date="06/07/2016"
   ms.author="jotaub@microsoft.com"/>

#Azure ポータルを使用して Service Bus 名前空間を作成する
名前空間は、すべてのメッセージング コンポーネントの共通コンテナーです。複数のキューとトピックを 1 つの名前空間に格納できます。名前空間は、多くの場合、アプリケーション コンテナーとして機能します。現在、2 つの異なる方法で Service Bus 名前空間を作成できます。

1.	Azure ポータル (この記事)

2.	[ARM テンプレート][create-namespace-using-arm]

##Azure ポータルでの名前空間の作成
[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

ご利用ありがとうございます。 これで、Service Bus 名前空間が作成されました。

##次のステップ

GitHub リポジトリにある Azure Service Bus メッセージングの高度な機能をいくつか紹介しているサンプルを確認してください。[https://github.com/Azure-Samples/azure-servicebus-messaging-samples][github-samples]

[create-namespace-using-arm]: ./service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

<!---HONumber=AcomDC_0608_2016-->