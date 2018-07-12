---
title: Azure Logic Apps に Twilio コネクタを追加する | Microsoft Docs
description: Twilio コネクタと REST API パラメーターの概要
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/19/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 8bcf69a7c8e04cb45d795fd0d6f20d477c15865d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38652007"
---
# <a name="get-started-with-the-twilio-connector"></a>Twilio コネクタの使用
Twilio に接続し、グローバル SMS、MMS、IP メッセージを送受信します。 Twilio では次のことができます。

* Twilio から取得したデータに基づいてビジネス フローを構築します。 
* メッセージを取得したり、メッセージを一覧表示したりするアクションを使用します。 また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。 たとえば、新しい Twilio メッセージを取得したとき、そのメッセージを Service Bus ワークフローで利用できます。 

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事を参照してください。

## <a name="create-a-connection-to-twilio"></a>Twilio への接続を作成する
このコネクタをロジック アプリに追加するときに、次の Twilio 値を入力します。

| プロパティ | 必須 | 説明 |
| --- | --- | --- |
| Account ID |[はい] |Twilio アカウント ID を入力します。 |
| Access Token |[はい] |Twilio アカウント トークンを入力します。 |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

Twilio アクセス トークンがない場合は、「[User Identity & Access Tokens (ユーザー ID とアクセス トークン)](https://www.twilio.com/docs/api/chat/guides/identity)」を参照してください。

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/twilio/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="more-connectors"></a>その他のコネクタ
[API リスト](apis-list.md)に戻ります。