---
title: Box への接続
description: Box で Azure Logic Apps を使用してファイルを作成および管理するタスクとワークフローを自動化する
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: c7f97ff33742eb545cbfbd7521ba135584851e5e
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666773"
---
# <a name="create-and-manage-files-in-box-by-using-azure-logic-apps"></a>Box で Azure Logic Apps を使用してファイルを作成および管理する

この記事では、Box コネクタを使用して、ロジック アプリ内から Box 内のファイルを作成および管理する方法を説明します。 この方法により、ファイルやその他のアクションを管理するタスクおよびワークフローを自動化するロジック アプリを作成できます。次に例を示します。

* Box から取得したデータに基づいてビジネス フローを構築できます。

* ファイルの作成時または更新時に、自動化されたタスクとワークフローをトリガーします。

* ファイルをコピーするか削除するアクションを実行します。

  これらのアクションで応答を取得すると、他のアクションでその出力を使用できるようになります。 
  たとえば、Box 上でファイルが変更された場合、Office 365 を使用して、そのファイルをメールで送信できます。

## <a name="prerequisites"></a>前提条件

* [Box アカウント](https://www.box.com/home)

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 

* Box アカウントにアクセスするロジック アプリ。 Box トリガーを使用してロジック アプリを起動するには、[空のロジック アプリ](../logic-apps/quickstart-create-first-logic-app-workflow.md)が必要です。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識。
ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの Open API (以前の Swagger) ファイルによって記述される、トリガー、アクション、制限などの技術的詳細については、[コネクタのリファレンス ページ](/connectors/box/)を参照してください。

## <a name="next-steps"></a>次のステップ

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。