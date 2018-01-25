---
title: "Azure Logic Apps での MailChimp コネクタ | Microsoft Docs"
description: "Azure App Service を使用してロジック アプリを作成します。 MailChimp は、電子メール マーケティング活動を管理および自動化するための企業向け SaaS サービスです。このサービスにより、マーケティング電子メールの送信、自動メッセージ、対象を絞ったキャンペーンなどを行うことができます。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 36559de2-94f0-4355-b492-2926dfc56486
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 50919fd903c24e4748a6efe29af2c188b1c3c195
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-mailchimp-connector"></a>MailChimp コネクタの使用
MailChimp は、電子メール マーケティング活動を管理および自動化するための企業向け SaaS サービスです。このサービスにより、マーケティング電子メールの送信、自動メッセージ、対象を絞ったキャンペーンなどを行うことができます。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事を参照してください。

## <a name="create-a-connection-to-mailchimp"></a>MailChimp への接続の作成
MailChimp を使用してロジック アプリを作成するには、まず**接続**を作成してから、次のプロパティの詳細を指定する必要があります。

| プロパティ | 必須 | [説明] |
| --- | --- | --- |
| トークン |[はい] |MailChimp の資格情報を提供します |

> [!INCLUDE [Steps to create a connection to MailChimp](../../includes/connectors-create-api-mailchimp.md)]
> 


## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/mailchimp/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="more-connectors"></a>その他のコネクタ
[API リスト](apis-list.md)に戻ります。