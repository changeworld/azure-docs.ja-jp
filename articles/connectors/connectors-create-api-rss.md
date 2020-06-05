---
title: Azure Logic Apps から RSS フィードに接続する
description: Azure Logic Apps を使用して RSS フィードを監視および管理するタスクとワークフローを自動化する
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 1e95b7bfce3d57dfe579a5622c3c13fac2b6af68
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834817"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して RSS フィードを管理する

Azure Logic Apps と RSS コネクタを使用すると、次のような、任意の RSS フィードに対する自動化されたタスクやワークフローを作成することができます。

* RSS フィード項目が発行されるのを監視します。
* すべての RSS フィード項目を一覧表示します。

RSS (Rich Site Summary。Really Simple Syndication とも呼ばれる) は、Web シンジケーションの一般的な形式であり、ブログの投稿やニュースのヘッドラインなど、頻繁に更新されるコンテンツの発行に使用されます。 多くのコンテンツ発行元は RSS フィードを提供しているため、ユーザーはそのコンテンツにサブスクライブすることができます。 

RSS トリガーを使用すると、RSS フィードからの応答を取得し、出力を他のアクションで使用することができます。 ロジック アプリで RSS アクションを使用して、RSS フィードのタスクを実行することができます。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 

* RSS フィードの URL

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* RSS フィードにアクセスするためのロジック アプリ。 RSS トリガーで開始するには、[空のロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。 RSS アクションを使用するには、**Recurrence** トリガーなど、別のトリガーでロジック アプリを開始します。

## <a name="connect-to-an-rss-feed"></a>RSS フィードに接続する

1. [Azure portal](https://portal.azure.com) にサインインし、ロジック アプリ デザイナーでロジック アプリを開きます (まだ開いていない場合)。

1. 以下からパスを選択します。 

   * 空のロジック アプリの場合、検索ボックスに、フィルターとして「rss」と入力します。 トリガーの一覧で、目的のトリガーを選択します。 

     または

   * 既存のロジック アプリの場合、アクションを追加する手順で、 **[新しいステップ]** を選択します。 検索ボックスに、フィルターとして「rss」と入力します。 アクションの一覧で、目的のアクションを選択します。

1. 選択したトリガーまたはアクションのために必要な詳細を指定し、ロジック アプリのワークフローの構築を続けます。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの OpenAPI (以前の Swagger) の説明に記載されているトリガー、アクション、および制限に関する技術的な詳細については、コネクタの[リファレンス ページ](/connectors/rss/)を参照してください。

## <a name="get-support"></a>サポートを受ける

* ご質問がある場合は、[Azure Logic Apps に関する Microsoft Q&A 質問ページ](https://docs.microsoft.com/answers/topics/azure-logic-apps.html)を参照してください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次のステップ

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。