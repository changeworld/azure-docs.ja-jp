---
title: Azure Logic Apps から Twitter に接続する
description: Azure Logic Apps を使用して、Twitter アカウントからのツイートの監視と管理に加え、フォロワー、フォローしているユーザー、他のユーザー、タイムラインなどに関するデータの取得を行うタスクとワークフローを自動化します
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 8ffd0fd558cf759fadd912de9dff4acf49d9659f
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789088"
---
# <a name="monitor-and-manage-twitter-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して Twitter の監視と管理を行う

Azure Logic Apps aと Twitter コネクターを使用して、Twitter で気になること (ツイート、フォロワー、ユーザーとフォローしているユーザー、タイムラインなど) の監視と管理を行う自動化されらタスクとワークフローを作成できます。他にも、次のようなアクションを実行できます。

* ツイートを監視、投稿、および検索します。
* フォロワー、フォローしているユーザー、タイムラインなどのデータを取得します。

Twitter アカウントから応答を取得し、その出力を他のアクションが使用できるようにするトリガーを使用できます。 Twitter アカウントでタスクを実行するアクションを使用できます。 他のアクションに Twitter アクションからの出力を使用させることもできます。 たとえば、特定のハッシュタグ付きの新しいツイートが出現したときに、Slack コネクタでメッセージを送信できます。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 

* Twitter アカウントとユーザー資格情報。

   接続を作成してお使いの Twitter アカウントにアクセスしてよいという承認が、この資格情報によってロジック アプリに与えられます。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* Twitter アカウントにアクセスするロジック アプリ。 Twitter トリガーで開始するには、[空のロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。 Twitter アクションを使用するには、**繰り返し**トリガーなど、別のトリガーでロジック アプリを開始します。

## <a name="connect-to-twitter"></a>Twitter への接続

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure portal](https://portal.azure.com) にサインインし、ロジック アプリ デザイナーでロジック アプリを開きます (まだ開いていない場合)。

1. パスを選択します。 

   * 空のロジック アプリの場合: 検索ボックスに、フィルターとして「Twitter」と入力します。 
   トリガーの一覧で、目的のトリガーを選択します。 

     または

   * 既存のロジック アプリの場合: 
   
     * アクションを追加する最後のステップの下で、 **[新しいステップ]** を選択します。 

       または

     * アクションを追加するステップの間で、ステップ間の矢印の上にポインターを移動します。 
     表示されるプラス記号 ( **+** ) を選択し、 **[アクションの追加]**  を選択します。
     
       検索ボックスに、フィルターとして「Twitter」と入力します。 
       アクションの一覧で、目的のアクションを選択します。

1. Twitter へのサインインを求められたら、今すぐサインインして、ロジック アプリのアクセスを承認できるようにします。

1. 選択したトリガーまたはアクションのために必要な詳細を指定し、ロジック アプリのワークフローの構築を続けます。

## <a name="examples"></a>例

### <a name="twitter-trigger-when-a-new-tweet-is-posted"></a>Twitter トリガー:When a new tweet is posted (新しいツイートの投稿時)

このトリガーは、トリガーが新しいツイート (たとえば #Seattle というハッシュタグ付きのツイート) を検出すると、ロジック アプリ ワークフローを開始します。 このため、これらのツイートが検出されたときに、たとえばツイートの内容が含まれるファイルを Dropbox コネクタを使用して Dropbox アカウントなどのストレージに追加できます。 

必要に応じて、対象のツイートは少なくとも指定した数のフォロワーを持つユーザーが投稿している必要があるという条件を含めることができます。

**企業での使用例**: このトリガーを使用して、自社に関するツイートを監視し、ツイートのコンテンツを SQL データベースにアップロードできます。

### <a name="twitter-action-post-a-tweet"></a>Twitter アクション:ツイートを投稿する

このアクションは、ツイートを投稿しますが、そのツイートに既に説明したトリガーによって検出されたツイートのコンテンツが含まれるようにアクションを設定できます。 

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの OpenAPI (以前の Swagger) の説明に記載されているトリガー、アクション、および制限に関する技術的な詳細については、コネクタの[リファレンス ページ](/connectors/twitterconnector/)を参照してください。

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
