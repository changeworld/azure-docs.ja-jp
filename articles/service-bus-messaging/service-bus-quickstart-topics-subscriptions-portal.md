---
title: Azure portal を使用して Service Bus のトピックとサブスクリプションを作成する
description: クイック スタート:このクイック スタートでは、Azure portal を使用して、Service Bus のトピックとそのサブスクリプションを作成する方法について説明します。
author: spelluru
ms.author: spelluru
ms.date: 09/15/2021
ms.topic: quickstart
ms.custom: mode-portal
ms.openlocfilehash: c76c152c48500de4cf6b9ab381fa2fd5ccbbb848
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083633"
---
# <a name="use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Azure portal を使用して Service Bus トピックとそのサブスクリプションを作成する
このクイック スタートでは、Azure portal を使用して Service Bus トピックを作成した後、そのトピックのサブスクリプションを作成します。 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Service Bus トピックとサブスクリプションとは
Service Bus のトピックとサブスクリプションは、メッセージ通信の *発行/サブスクライブ* モデルをサポートします。 トピックとサブスクリプションを使用すると、分散アプリケーションのコンポーネントが互いに直接通信することがなくなり、仲介者の役割を果たすトピックを介してメッセージをやり取りすることになります。

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

各メッセージが 1 つのコンシューマーによって処理される Service Bus キューとは異なり、トピックとサブスクリプションは、発行/サブスクライブ パターンを使用して "1 対多" 形式の通信を行います。 複数のサブスクリプションを 1 つのトピックに登録できます。 トピックに送信されたメッセージはサブスクリプションに渡され、各サブスクリプションで独立して処理できます。 トピックにとってのサブスクリプションは、トピックに送信されたメッセージのコピーを受け取る仮想キューのようなものです。 トピックに対するフィルター ルールをサブスクリプション単位で登録することもできます。この場合、トピックに送信されるどのメッセージをどのトピック サブスクリプションで受信するかのフィルター処理や制限が可能になります。

Service Bus のトピックとサブスクリプションを使用すると、多数のユーザーとアプリケーションの間でやり取りされる膨大な数のメッセージを処理することもできます。

[!INCLUDE [service-bus-create-namespace-portal](./includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](./includes/service-bus-create-topics-three-subscriptions-portal.md)]

> [!NOTE]
> Service Bus リソースは、[Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) で管理できます。 Service Bus Explorer を使用すると、ユーザーは Service Bus 名前空間に接続し、簡単な方法でメッセージング エンティティを管理できます。 このツールには、インポート/エクスポート機能や、トピック、キュー、サブスクリプション、リレー サービス、通知ハブ、イベント ハブをテストする機能などの高度な機能が用意されています。 

## <a name="next-steps"></a>次のステップ
この記事では、Service Bus 名前空間と、その名前空間内のトピック、さらに、そのトピックに対する 3 つのサブスクリプションを作成しました。 トピックにメッセージを発行したり、サブスクリプションのメッセージをサブスクライブしたりする方法については、「**メッセージの発行とサブスクライブ**」セクションで、次のいずれかのクイックスタートを参照してください。 

- [.NET](service-bus-dotnet-how-to-use-topics-subscriptions.md)
- [Java](service-bus-java-how-to-use-topics-subscriptions.md)
- [JavaScript](service-bus-nodejs-how-to-use-topics-subscriptions.md)
- [Python](service-bus-python-how-to-use-topics-subscriptions.md)
- [PHP](service-bus-php-how-to-use-topics-subscriptions.md)
