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
ms.date: 03/27/2018
ms.author: tomfitz
ms.openlocfilehash: f4d6a663b4e0d2c2166028051e713668534b20bc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Event Grid 用の Azure Resource Manager テンプレート

次の表は、Event Grid 用の Azure Resource Manager テンプレートのリンク一覧です。

| | |
|-|-|
|**Event Grid サブスクリプション**||
| [WebHook エンドポイントを使用するカスタム トピックとサブスクリプション](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Event Grid のカスタム トピックをデプロイします。 WebHook エンドポイントを使用するカスタム トピックへのサブスクリプションを作成します。 |
| [EventHub エンドポイントを使用するカスタム トピック サブスクリプション](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeCustomTopicToEventHub.json)| 既に存在するカスタム トピックへの Event Grid サブスクリプションを作成します。 このサブスクリプションでは、エンドポイントに Event Hub を使用します。 |
| [リソース グループのサブスクリプション](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeResourceGroupToWebHook.json)| リソース グループのイベントをサブスクライブします。 デプロイ時にターゲットとして指定するリソース グループがイベントのソースになります。 このサブスクリプションでは、エンドポイントに Event Hub を使用します。 |
| [BLOB ストレージ アカウントとサブスクリプション](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/createBlobAndSubscribe.json)| Azure Blob Storage アカウントをデプロイし、そのストレージ アカウントのイベントをサブスクライブします。 |
| | |
