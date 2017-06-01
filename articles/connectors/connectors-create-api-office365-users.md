---
title: "Logic Apps に Office 365 ユーザー コネクタを追加する | Microsoft Docs"
description: "Office 365 Users コネクタと REST API パラメーターの概要"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b2146481-9105-4f56-b4c2-7ae340cb922f
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 8f544b781bbf11214183bc0f46215bb9e16c9f55
ms.contentlocale: ja-jp
ms.lasthandoff: 05/17/2017


---
# <a name="get-started-with-the-office-365-users-connector"></a>Office 365 Users コネクタの使用
Office 365 ユーザーに接続して、プロファイルの取得、ユーザーの検索などを行います。 Office 365 ユーザーは、次のことを行えます。

* Office 365 ユーザーから取得したデータに基づいてビジネス フローを構築できます。 
* 直属の部下の取得、上司のユーザーのプロファイルの取得などのアクションを使用できます。 また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。 たとえば、ユーザーの直属の部下を取得し、この情報を利用して、SQL Azure Database を更新します。 

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関する記事を参照してください。

## <a name="create-a-connection-to-office-365-users"></a>Office 365 Users への接続を作成します
このコネクタをロジック アプリに追加する場合は、Office 365 ユーザー アカウントにサインインして、ロジック アプリでアカウントに接続できるようにする必要があります。

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

接続を作成したら、ユーザー ID など、Office 365 ユーザー プロパティを入力します。 これらのプロパティについては、このトピックの **REST API リファレンス** をご覧ください。

## <a name="view-the-swagger"></a>Swagger の表示

[Swagger の詳細](/connectors/officeusers/)を参照してください。

## <a name="more-connectors"></a>その他のコネクタ
[API リスト](apis-list.md)に戻ります。
