---
title: Azure Resource Manager テンプレートのサンプル - Event Grid | Microsoft Docs
description: この記事では、GitHub にある一連の Azure Event Grid 用 Azure Resource Manager テンプレート サンプルを紹介しています。
ms.topic: sample
ms.date: 07/07/2020
ms.openlocfilehash: d8b3cc846ca1bdb032e20d0d904a061b04f473b3
ms.sourcegitcommit: eda26a142f1d3b5a9253176e16b5cbaefe3e31b3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2021
ms.locfileid: "109735748"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Event Grid 用の Azure Resource Manager テンプレート

テンプレートで使用する JSON の構文とプロパティについては、「[Microsoft.EventGrid resource types (Microsoft.EventGrid のリソースの種類)](/azure/templates/microsoft.eventgrid/allversions)」を参照してください。 次の表は、Event Grid 用の Azure Resource Manager テンプレートのリンク一覧です。

## <a name="event-grid-subscriptions"></a>Event Grid サブスクリプション
- [Webhook エンドポイントを使用するカスタム トピックとサブスクリプション](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventgrid/event-grid) - Event Grid のカスタム トピックをデプロイします。 WebHook エンドポイントを使用するカスタム トピックへのサブスクリプションを作成します。 
- [EventHub エンドポイントを使用するカスタム トピック サブスクリプション](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventgrid/event-grid-event-hubs-handler) - カスタム トピックへの Event Grid サブスクリプションを作成します。 このサブスクリプションでは、エンドポイントに Event Hub を使用します。 
- [Azure サブスクリプションまたはリソース グループ サブスクリプション](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventgrid/event-grid-resource-events-to-webhook) - リソース グループまたは Azure サブスクリプションのイベントをサブスクライブします。 デプロイ時にターゲットとして指定するリソース グループがイベントのソースになります。 このサブスクリプションでは、エンドポイントに Webhook を使用します。 
- [BLOB ストレージ アカウントとサブスクリプション](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventgrid/event-grid-subscription-and-storage) - Azure Blob Storage アカウントをデプロイし、そのストレージ アカウントのイベントをサブスクライブします。 

## <a name="next-steps"></a>次のステップ
次のサンプルを参照してください。

- [PowerShell のサンプル](powershell-samples.md)
- [CLI のサンプル](cli-samples.md)
