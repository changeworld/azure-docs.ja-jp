---
title: Office 365 ユーザーへの接続
description: Azure Logic Apps を使用して Office 365 ユーザー プロファイル内のプロファイルを取得および管理するタスクとワークフローを自動化する
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: 0361d42ea614b394142e32a9193ab1d48d1a4a6c
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2020
ms.locfileid: "84194233"
---
# <a name="get-and-manage-profiles-in-office-365-users-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して Office 365 ユーザー内のプロファイルを取得および管理する

Office 365 ユーザーに接続して、プロファイルの取得、ユーザーの検索などを行います。 Office 365 ユーザーは、次のことを行えます。

* Office 365 ユーザーから取得したデータに基づいてビジネス フローを構築できます。 
* 直属の部下の取得、上司のユーザーのプロファイルの取得などのアクションを使用できます。 また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。 たとえば、ユーザーの直属の部下を取得し、この情報を利用して、Azure SQL Database 内のデータベースを更新します。 

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事を参照してください。

## <a name="create-a-connection-to-office-365-users"></a>Office 365 Users への接続を作成します

このコネクタをロジック アプリに追加する場合は、Office 365 ユーザー アカウントにサインインして、Azure Logic Apps がそのアカウントに接続できるようにする必要があります。

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

接続を作成したら、ユーザー ID など、Office 365 ユーザー プロパティを入力します。 これらのプロパティについては、この記事の **REST API リファレンス**を参照してください。

## <a name="connector-specific-details"></a>コネクタ固有の詳細

コネクタの Swagger の説明に記載されているトリガー、アクション、および制限に関する技術的な詳細については、[コネクタのリファレンス ページ](/connectors/officeusers/)を確認してください。

## <a name="next-steps"></a>次のステップ

* 他の[Logic Apps コネクタ](apis-list.md)を確認します。