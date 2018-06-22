---
title: switch ステートメントをワークフローに追加する - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps の特定の値に基づいてワークフロー アクションを制御する switch ステートメントを作成する方法です
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: e15f89d4b7e33ce7e28676c219344f7d7d9cd465
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299618"
---
# <a name="create-switch-statements-that-run-workflow-actions-based-on-specific-values-in-azure-logic-apps"></a>Azure Logic Apps の特定の値に基づいてワークフロー アクションを実行する switch ステートメントを作成する

オブジェクト、式、またはトークンの値に基づいて特定のアクションを実行するには、*switch* ステートメントを追加します。 この構造は、オブジェクト、式、トークンを評価し、その結果と一致するケースを選択して、そのケースだけの特定のアクションを実行します。 switch ステートメントが実行されるときは、1 つのケースのみが結果と一致する必要があります。

たとえば、電子メールで選択されたオプションに基づいてさまざまなステップを実行するロジック アプリが必要な場合があります。 この例では、ロジック アプリは Web サイトの RSS フィードで新しいコンテンツをチェックします。 RSS フィードに新しい項目が出現すると、ロジック アプリは電子メールを承認者に送信します。 承認者が選択する "承認" または "拒否" に基づいて、ロジック アプリが異なるステップを実行します。

> [!TIP]
> すべてのプログラミング言語と同様に、Switch ステートメントは等値演算子のみをサポートします。 その他の関係演算子 ("より大きい" など) が必要な場合は、[条件付きステートメント](#conditions)を使用します。
> 確定的な実行動作が確実に行われるようにするには、ケースに動的トークンまたは式ではなく、一意で静的な値を含める必要があります。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* この記事の例に従うには、Outlook.com や Office 365 の Outlook アカウントを使用して、[このサンプルのロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。

  1. 電子メールを送信するアクションを追加するときは、**[承認の電子メールを送信します]** を選択します。

     ![[承認の電子メールを送信します] を選択する](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  2. 承認の電子メールの受信者の電子メール アドレスなど、必須フィールドに指定します。 
  **[ユーザー オプション]** に「Approve, Reject」と入力します。

     ![電子メールの詳細を入力する](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-a-switch-statement"></a>switch ステートメントを追加する

1. サンプル ワークフローの最後で、**[+ 新しいステップ]** > **[...詳細]** > **[Switch Case の追加]** の順に選択します。 

   ![switch ステートメントを追加する](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   1 つのケースと 1 つの既定ケースを含む switch ステートメントが表示されます。 
   switch ステートメントでは、既定のケースに加えて、少なくとも 1 つのケースが必要です。 

   ステップとステップの間に switch ステートメントを追加するときは、switch ステートメントを追加する場所で矢印の上にポインターを重ねます。 
   表示される**プラス記号** (**+**) を選択し、**[Switch Case の追加]** を選択します。

4. **[On]\(オン\)** ボックスで、出力によって実行するアクションが決まる **[SelectedOption]\(SelectedOption\)** フィールドを選択します。 
   
   表示される **[動的なコンテンツの追加]** リストからフィールドを選択します。

5. 承認者が `Approve` または `Reject` を選択したケースを処理するために、**[ケース]** と **[既定]** の間にもう 1 つのケースを追加します。 
   
6. 次のアクションを対応するケースに追加します。

   | ケース番号 | **SelectedOption** | アクションを表示します。 |
   |:------ |:-------------------|:------ |
   | ケース 1 | **Approve** | 承認者が **[Approve]** を選択した場合のみ、RSS 項目の詳細を送信するために Outlook **[Send an email]\(電子メールの送信\)** アクションを追加します。 |
   | ケース 2 | **Reject** | RSS 項目が拒否されたことを他の承認者に通知するために、Outlook **[Send an email]\(電子メールの送信\)** アクションを追加します。 |
   | 既定値 | "\<なし\>" | 対処不要です。 この例では、**[SelectedOption]\(SelectedOption\)** に 2 つしかオプションがないため、**[既定]** ケースは空です。 |
   |         |          |

   ![Switch ステートメント](./media/logic-apps-control-flow-switch-statement/switch.png)

7. ロジック アプリを保存し、 

   この例を手動でテストするには、ロジック アプリが新しい RSS 項目を見つけて承認の電子メールを送信するまで、**[実行]** を選択します。 
   **[Approve]** を選択して結果を確認します。

## <a name="json-definition"></a>JSON の定義

switch ステートメントを使用してロジック アプリを作成しました。ここで、switch ステートメントの背後にあるコード定義の概要を見てみましょう。

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case" : {
         "actions" : {
           "Send_an_email": { }
         },
         "case" : "Approve"
      },
      "Case_2" : {
         "actions" : {
           "Send_an_email_2": { }
         },
         "case" : "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

| ラベル              | 説明 |
| :----------------- | :---------- |
| `"Switch"`         | switch ステートメントの名前です。わかりやすいように変更することができます。 |
| `"type": "Switch"` | アクションが switch ステートメントであることを指定します。 |
| `"expression"`     | この例では、承認者が選択するオプションを指定します。これは、定義の中で後で宣言される各ケースに照らして評価されます。 |
| `"cases"` | 任意の数のケースを定義します。 各ケースで、`"Case_*"` はケースの既定の名前です。わかりやすいように名前を変更できます。 |
| `"case"` | ケースの値を指定します。これは、switch 式の比較対象であり、一意の定数値にする必要があります。 switch 式の結果と一致するケースがない場合は、`"default"` セクションのアクションが実行されます。
|           |         |

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能や提案について投稿や投票を行うには、[Azure Logic Apps のユーザー フィードバック サイト](http://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

* [条件に基づいてステップを実行する (条件付きステートメント)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [ステップを実行して繰り返す (ループ)](../logic-apps/logic-apps-control-flow-loops.md)
* [並列ステップを実行または結合する (分岐)](../logic-apps/logic-apps-control-flow-branches.md)
* [グループ化されたアクションの状態に基づいてステップを実行する (スコープ)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
