---
title: Outlook.com に接続する
description: Outlook.com REST API と Azure Logic Apps を使用して、メール、カレンダー、および連絡先を管理する
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 750efc2cb928bf127c4f3c68d5a58c5f52ca7d51
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789369"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-with-azure-logic-apps"></a>Azure Logic Apps を使用して、Outlook.com のメール、カレンダー、および連絡先を管理する

この記事では、Box コネクタを使用して、ロジック アプリ内で Outlook.com アカウントを作成および管理する方法を説明します。 この方法により、Outlook.com アカウントのタスクとワークフローを自動化するロジック アプリを作成できます。次に例を示します。

* メールを送信する。 
* 会議のスケジュールを設定する。
* 連絡先を追加する。 

ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* [Outlook.com アカウント](https://outlook.live.com/owa/)

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 

* Outlook.com アカウントにアクセスするロジック アプリ。 Outlook トリガーを使用してロジック アプリを起動するには、[空のロジック アプリ](../logic-apps/quickstart-create-first-logic-app-workflow.md)が必要です。 

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識。

## <a name="connect-to-outlookcom"></a>Outlook.com に接続する

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Connect to Outlook.com](../../includes/connectors-create-api-outlook.md)]

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの Swagger ファイルによって記述される、トリガー、アクション、制限などの技術的詳細については、[コネクタのリファレンス ページ](/connectors/outlook/)を参照してください。 

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。