---
title: Azure テンプレートを使用した Azure Service Bus の名前空間とキューの作成
description: クイック スタート:Azure Resource Manager テンプレートを使用した、Service Bus の名前空間とキューの作成
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: a6bfb5fd-7b98-4588-8aa1-9d5f91b599b6
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: quickstart
ms.custom: subject-armqs
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 03/30/2020
ms.author: spelluru
ms.openlocfilehash: b08253104eeb61f6bb09fde507473d235a996494
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422668"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>クイック スタート:Azure Resource Manager テンプレートを使用した、Service Bus の名前空間とキューの作成

この記事では、Service Bus の名前空間とその名前空間内のキューを作成する Azure Resource Manager テンプレートを使用する方法を示します。 この記事では、デプロイ対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを指定する方法を説明します。 このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

なし

## <a name="create-a-service-bus-namespace-and-a-queue"></a>Service Bus の名前空間とキューを作成する

### <a name="review-the-template"></a>テンプレートを確認する

このクイック スタートで使用されるテンプレートは [Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/201-servicebus-create-queue)からのものです。

:::code language="json" source="~/quickstart-templates/201-servicebus-create-queue/azuredeploy.json" range="1-75" highlight="31-63":::

このテンプレートに定義されているリソースは次のとおりです。

- [**Microsoft.ServiceBus/namespaces**](/azure/templates/microsoft.servicebus/namespaces)
- [**Microsoft.ServiceBus/namespaces/queues**](/azure/templates/microsoft.servicebus/namespaces/queues)

> [!NOTE]
> 次の Azure Resource Manager テンプレートは、ダウンロードしてデプロイすることができます。
>
> * [キューと承認規則を含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-auth-rule.md)
> * [トピックとサブスクリプションを含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-topic.md)
> * [Service Bus 名前空間の作成](service-bus-resource-manager-namespace.md)
> * [トピック、サブスクリプション、ルールを含んだ Service Bus の名前空間を作成する](service-bus-resource-manager-namespace-topic-with-rule.md)

その他のテンプレートについては、「[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)」をご覧ください。

### <a name="deploy-the-template"></a>テンプレートのデプロイ

このテンプレートでは、キューを含んだ Service Bus 名前空間をデプロイします。

[Service Bus キュー](service-bus-queues-topics-subscriptions.md#queues)では、コンシューマーが競合している場合のメッセージ配信に先入れ先出し法 (FIFO) を使用します。

デプロイメントを自動的に実行するには、次のボタンをクリックします。後で簡単にクリーンアップできるよう、デプロイ用に新しいリソース グループを作成してください。

[![Azure へのデプロイ](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="verify-the-deployment"></a>デプロイを検証する

1. 上部にある **[通知]** を選択して、デプロイの状態を確認します。 デプロイが正常に完了するまで待ちます。 次に、通知メッセージの **[リソース グループに移動]** を選択して、Service Bus 名前空間を含んだリソース グループのページに移動します。 

    ![デプロイからの通知](./media/service-bus-resource-manager-namespace-queue/notification.png)
2. Service Bus 名前空間がリソースの一覧に表示されていることを確認します。 

    ![リソース グループ - 名前空間](./media/service-bus-resource-manager-namespace-queue/resource-group-namespace.png)
3. 一覧から名前空間を選択して、 **[Service Bus 名前空間]** ページを表示します。 

## <a name="cleanup-resources"></a>リソースをクリーンアップする

1. Azure portal で、自分のリソース グループの **[リソース グループ]** ページに移動します。
2. ツール バーから **[リソース グループの削除]** を選びます。 
3. リソース グループの名前を入力し、 **[削除]** を選択します。 

    ![リソース グループ - 削除](./media/service-bus-resource-manager-namespace-queue/resource-group-delete.png)

## <a name="next-steps"></a>次のステップ

名前空間/キューの承認規則を作成する方法を説明している次のトピックを参照してください。

[Service Bus の名前空間とキューに使用する承認規則を Azure Resource Manager テンプレートで作成する](service-bus-resource-manager-namespace-auth-rule.md)

次の記事を参照して、これらのリソースの管理方法を確認してください。

* [PowerShell で Service Bus を管理する](service-bus-manage-with-ps.md)
* [Service Bus リソースを Service Bus Explorer で管理する](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
