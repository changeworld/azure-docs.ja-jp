---
title: Azure Resource Manager テンプレートのサンプル - Event Grid | Microsoft Docs
description: Event Grid 用の Azure Resource Manager テンプレートのサンプル
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 09/18/2018
ms.author: tomfitz
ms.openlocfilehash: c462334597b41b914b6a0a0e3c8a67ad97e2bd7a
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295582"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Event Grid 用の Azure Resource Manager テンプレート

次の表は、Event Grid 用の Azure Resource Manager テンプレートのリンク一覧です。

| | |
|-|-|
|**Event Grid サブスクリプション**||
| [WebHook エンドポイントを使用するカスタム トピックとサブスクリプション](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Event Grid のカスタム トピックをデプロイします。 WebHook エンドポイントを使用するカスタム トピックへのサブスクリプションを作成します。 |
| [EventHub エンドポイントを使用するカスタム トピック サブスクリプション](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| カスタム トピックへの Event Grid サブスクリプションを作成します。 このサブスクリプションでは、エンドポイントに Event Hub を使用します。 |
| [Azure サブスクリプションまたはリソース グループ サブスクリプション](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook)| リソース グループまたは Azure サブスクリプションのイベントにサブスクライブします。 デプロイ時にターゲットとして指定するリソース グループがイベントのソースになります。 このサブスクリプションでは、エンドポイントに Webhook を使用します。 |
| [BLOB ストレージ アカウントとサブスクリプション](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)| Azure Blob Storage アカウントをデプロイし、そのストレージ アカウントのイベントをサブスクライブします。 |
| | |
