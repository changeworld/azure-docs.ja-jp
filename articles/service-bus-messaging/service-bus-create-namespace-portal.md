---
title: "Azure Portal を使用して Service Bus 名前空間を作成する | Microsoft Docs"
description: "Service Bus の使用を開始するには、名前空間が必要です。 Azure ポータルを使用して名前空間を作成する方法を次に示します。"
services: service-bus
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 08/22/2016
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a341d32149a84b9959afb6c3c1796c66c4d593cd


---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Azure Portal を使用して Service Bus 名前空間を作成する
名前空間は、すべてのメッセージング コンポーネントの共通コンテナーです。 複数のキューとトピックを 1 つの名前空間に格納できます。多くの場合、名前空間はアプリケーション コンテナーとして機能します。 現在、Service Bus 名前空間の作成には 2 つの方法があります。

1. Azure ポータル (この記事)
2. [Resource Manager テンプレート][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Azure Portal での名前空間の作成
[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

ご利用ありがとうございます。 これで、Service Bus メッセージング名前空間が作成されました。

## <a name="next-steps"></a>次のステップ
Azure Service Bus メッセージングのさらに高度な機能を紹介するサンプルについては、[GitHub サンプル][github-samples]を参照してください。

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples



<!--HONumber=Nov16_HO2-->


