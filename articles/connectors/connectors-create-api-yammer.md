---
title: Azure Logic Apps に Yammer コネクタを追加する | Microsoft Docs
description: Yammer コネクタと REST API パラメーターの概要
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 7f1758e9b95f534b23188f427ae0edaddbb29a48
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-yammer-connector"></a>Yammer コネクタの使用
Yammer に接続し、エンタープライズ ネットワークの会話にアクセスします。 Yammer で次のことができます。

* Yammer から取得したデータに基づいてビジネス フローを構築します。 
* グループまたはフォローしているフィードで新しいメッセージが発生したときにトリガーを使用します。
* アクションを使用し、メッセージを投稿したり、すべてのメッセージを取得したりします。 また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。 たとえば、新しいメッセージが表示されたら、Office 365 を使用して電子メールを送信できます。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事を参照してください。

## <a name="create-a-connection-to-yammer"></a>Yammer への接続を作成する
Yammer コネクタを使用するには、最初に**接続**を作成し、以下のプロパティの詳細を指定します。 

| プロパティ | 必須 | [説明] |
| --- | --- | --- |
| トークン |[はい] |Yammer 資格情報を入力します |

> [!INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]
> 

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/yammer/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="more-connectors"></a>その他のコネクタ
[API リスト](apis-list.md)に戻ります。