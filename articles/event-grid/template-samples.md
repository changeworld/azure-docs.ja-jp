---
title: Azure Resource Manager テンプレートのサンプル - Event Grid | Microsoft Docs
description: この記事では、GitHub にある一連の Azure Event Grid 用 Azure Resource Manager テンプレート サンプルを紹介しています。
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 38d8db0bcc504760595fe51b63072f63e785577a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "76720624"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Event Grid 用の Azure Resource Manager テンプレート

テンプレートで使用する JSON の構文とプロパティについては、「[Microsoft.EventGrid resource types (Microsoft.EventGrid のリソースの種類)](/azure/templates/microsoft.eventgrid/allversions)」を参照してください。 次の表は、Event Grid 用の Azure Resource Manager テンプレートのリンク一覧です。

| | |
|-|-|
|**Event Grid サブスクリプション**||
| [WebHook エンドポイントを使用するカスタム トピックとサブスクリプション](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Event Grid のカスタム トピックをデプロイします。 WebHook エンドポイントを使用するカスタム トピックへのサブスクリプションを作成します。 |
| [EventHub エンドポイントを使用するカスタム トピック サブスクリプション](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| カスタム トピックへの Event Grid サブスクリプションを作成します。 このサブスクリプションでは、エンドポイントに Event Hub を使用します。 |
| [Azure サブスクリプションまたはリソース グループ サブスクリプション](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook)| リソース グループまたは Azure サブスクリプションのイベントにサブスクライブします。 デプロイ時にターゲットとして指定するリソース グループがイベントのソースになります。 このサブスクリプションでは、エンドポイントに Webhook を使用します。 |
| [BLOB ストレージ アカウントとサブスクリプション](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)| Azure Blob Storage アカウントをデプロイし、そのストレージ アカウントのイベントをサブスクライブします。 |
| | |
