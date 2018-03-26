---
title: ロジック アプリで Office 365 Video コネクタを使用する | Microsoft Docs
description: Microsoft Azure App Service Logic Apps で Office 365 Video コネクタを使用する方法の概要について説明します
services: ''
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: 738e5aa7-2523-4116-8b65-211b9063852d
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 30ec8c7aa55013668d2ec0897278d435b9e864c7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-office365-video-connector"></a>Office 365 Video コネクタの使用
Office 365 ビデオに関する情報の取得、ビデオの一覧の取得などを行うには、Office 365 ビデオに接続します。 Office 365 ビデオは、次のことを行えます。

* Office 365 ビデオから取得したデータに基づいてビジネス フローを構築できます。 
* ビデオ ポータルの状態の確認、チャネル内にあるすべてのビデオの一覧の取得などのアクションを使用できます。 また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。 たとえば、Bing Search コネクタを使用して Office 365 ビデオを検索でき、Office 365 Video コネクタを使用してそのビデオに関する情報を取得できます。 ビデオが要件を満たしている場合は、Facebook にこのビデオを投稿できます。 

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事を参照してください。

## <a name="create-a-connection-to-office365-video-connector"></a>Office 365 Video コネクタへの接続を作成する
このコネクタをロジック アプリに追加する場合は、Office 365 Video アカウントにサインインして、ロジック アプリでアカウントに接続できるようにする必要があります。

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]
> 
> 

接続を作成したら、テナント名またはチャネル ID など、Office 365 ビデオ プロパティを入力します。 


## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/office365videoconnector/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="more-connectors"></a>その他のコネクタ
[API リスト](apis-list.md)に戻ります。