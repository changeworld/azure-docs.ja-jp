---
title: SendGrid | Microsoft Docs
description: Azure App Service を使用してロジック アプリを作成します。 SendGrid 接続プロバイダーを使用して、電子メールを送信し、受信者リストを管理できます。
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: bc4f1fc2-824c-4ed7-8de8-e82baff3b746
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: e87173b4a31a721ca3cd5af41d4a6d725c46c972
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-sendgrid-connector"></a>SendGrid コネクタの使用
SendGrid 接続プロバイダーを使用して、電子メールを送信し、受信者リストを管理できます。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事を参照してください。

## <a name="create-a-connection-to-sendgrid"></a>SendGrid への接続の作成
SendGrid を使用してロジック アプリを作成するには、まず**接続**を作成してから、次のプロパティの詳細を指定する必要があります。 

| プロパティ | 必須 | [説明] |
| --- | --- | --- |
| ApiKey |[はい] |SendGrid API キーを指定します。 |

> [!INCLUDE [Steps to create a connection to SendGrid](../../includes/connectors-create-api-sendgrid.md)]
> 


接続を作成したら、その接続を使用してアクションを実行し、トリガーをリッスンできます。

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/sendgrid/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="more-connectors"></a>その他のコネクタ
[API リスト](apis-list.md)に戻ります。