---
title: Office 365 への接続のビデオ
description: Azure Logic Apps を使用して、Office 365 ビデオのビデオを管理するタスクとワークフローを自動化します
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/18/2016
tags: connectors
ms.openlocfilehash: 8ac6b7b411e7f42dd076c5b16e7b500a819c617f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665787"
---
# <a name="manage-videos-in-office365-video-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して Office 365 ビデオのビデオを管理する

Office 365 ビデオに関する情報の取得、ビデオの一覧の取得などを行うには、Office 365 ビデオに接続します。 Office 365 ビデオは、次のことを行えます。

* Office 365 ビデオから取得したデータに基づいてビジネス フローを構築できます。 

* ビデオ ポータルの状態の確認、チャネル内にあるすべてのビデオの一覧の取得などのアクションを使用できます。 また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。 

たとえば、Bing Search コネクタを使用して Office 365 ビデオを検索でき、Office 365 Video コネクタを使用してそのビデオに関する情報を取得できます。 ビデオが要件を満たしている場合は、Facebook にこのビデオを投稿できます。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事を参照してください。

## <a name="connect-to-office365-video"></a>Office 365 ビデオに接続する

このコネクタをロジック アプリに追加する場合は、Office 365 Video アカウントにサインインして、ロジック アプリでアカウントに接続できるようにする必要があります。

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

接続を作成したら、テナント名またはチャネル ID など、Office 365 ビデオ プロパティを入力します。 

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/office365videoconnector/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="next-steps"></a>次のステップ

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。