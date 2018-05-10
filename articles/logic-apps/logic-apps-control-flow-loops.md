---
title: ループ - 配列または繰り返しアクションの処理 - Azure Logic Apps | Microsoft Docs
description: "\"for each\" ループ、または繰り返しアクションが指定された配列を、ロジック アプリで特定の条件が満たされるまで処理します"
services: logic-apps
keywords: for each ループ
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 4029da2c7ad59b1e61dabe0af252834746a4c5c6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
---
# <a name="loops-process-arrays-or-repeat-actions-until-a-condition-is-met"></a>ループ: 条件が満たされるまで配列または繰り返しアクションを処理する

ロジック アプリで配列を反復処理するには、["Foreach" ループ](#foreach-loop)を使用するか、[シーケンシャル "Foreach" ループ](#sequential-foreach-loop)を使用します。 標準の "Foreach" ループのサイクルは並列で実行され、シーケンシャル "Foreach" ループは 1 回ずつ実行されます。 "Foreach" ループ が 1 つのロジック アプリ実行で処理できる配列項目の最大数については、[制限と構成](../logic-apps/logic-apps-limits-and-config.md)に関するページをご覧ください。 

> [!TIP] 
> 配列を受け取るトリガーがあり、各配列項目のワークフローを実行する場合は、[**SplitOn** トリガー プロパティ](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)を使用してその配列を "*デタッチ*" できます。 
  
条件を満たすまたは一部の状態が変更されるまでアクションを繰り返し実行するには、["Until" ループ](#until-loop)を使用します。 ロジック アプリは、まずループ内ですべてのアクションを実行し、最後の手順で状態をチェックします。 条件が満たされると、ループが停止します。 満たされないと、ループが繰り返されます。 1 つのロジック アプリ実行で処理できる "Until" ループの最大数については、[制限と構成](../logic-apps/logic-apps-limits-and-config.md)に関するページをご覧ください。 

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>"Foreach" ループ

配列内の各項目のアクションを繰り返すには、ロジック アプリのワークフローで "Foreach" ループを使用します。 "Foreach" ループには複数のアクションを指定し、それぞれに "Foreach" ループを入れ子にできます。 既定では、標準の "Foreach" ループのサイクルは並列に実行されます。 "Foreach" ループが実行できる並列サイクルの最大数については、[制限と構成](../logic-apps/logic-apps-limits-and-config.md)に関するページをご覧ください。

> [!NOTE] 
> "Foreach" ループは配列でのみ機能し、ループ内のアクションは `@item()` リファレンスを使用して配列内の各項目を処理します。 配列にないデータを指定すると、ロジック アプリのワークフローが失敗します。 

たとえば、このロジック アプリは Web サイトの RSS フィードから概要 (日単位) を送信します。 アプリは "Foreach" ループを使用して、新しく見つかった項目それぞれについて電子メールを送信します。

1. Outlook.com や Office 365 の Outlook アカウントを使用して、[このサンプルのロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。

2. RSS トリガーと電子メール送信アクションの間に、"Foreach" ループを追加します。 

   ステップの間にループを追加するには、ループを追加する位置の矢印の上にポインターを移動します。 
   表示される**プラス記号** (**+**) を選択し、**[Add a for each]\(for each の追加\)** を選択します。

   ![ステップの間に "Foreach" ループを追加する](media/logic-apps-control-flow-loops/add-for-each-loop.png)

3. これでループをビルドします。 **[動的なコンテンツの追加]** リストが表示された後に、**[Select an output from previous steps]\(前のステップから出力を選択する\)** で、RSS トリガーからの出力である **[フィード リンク]** 配列を選択します。 

   ![動的コンテンツ リストから選択する](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > 選択できるのは。前のステップからの配列の出力 "*のみ*" です。

   選択した配列がここに表示されます。

   ![配列を選択する](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. 各配列項目でアクションを実行するには、**[Send an email]\(電子メールを送信する\)** アクションを **For each** ループにドラッグします。 

   ロジック アプリは次の例のようになります。

   !["Foreach"ループにステップを追加する](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. ロジック アプリを保存し、 ロジック アプリを手動でテストするには、デザイナーのツール バーで **[実行]** を選択します。

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>"Foreach" ループの定義 (JSON)

ロジック アプリをコード ビューで操作している場合、代わりに次の例のようにロジック アプリの JSON 定義で `Foreach` ループを定義できます。

``` json
"actions": {
    "myForEachLoopName": {
        "type": "Foreach",
        "actions": {
            "Send_an_email": {
                "type": "ApiConnection",
                "inputs": {
                    "body": {
                        "Body": "@{item()}",
                        "Subject": "New CNN post @{triggerBody()?['publishDate']}",
                        "To": "me@contoso.com"
                    },
                    "host": {
                        "api": {
                            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/office365"
                        },
                        "connection": {
                            "name": "@parameters('$connections')['office365']['connectionId']"
                        }
                    },
                    "method": "post",
                    "path": "/Mail"
                },
                "runAfter": {}
            }
        },
        "foreach": "@triggerBody()?['links']",
        "runAfter": {},
    }
},
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>"Foreach" ループ: シーケンシャル

既定では、"Foreach" ループ内の各サイクルでは、各配列項目が並列に実行されます。 各サイクルをシーケンシャルに実行するには、"Foreach" ループに **[シーケンシャル]** オプションを設定します。

1. ループの右上隅で、**省略記号** (**...**) > **[設定]** と選択します。

   !["Foreach" ループで、[...] > [設定] を選択する](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

2. **[シーケンシャル]** の設定をオンにし、**[終了]** を選択します。

   !["Foreach" ループの [シーケンシャル] 設定をオンにする](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

ロジック アプリの JSON 定義で、**operationOptions** パラメーターを `Sequential` に設定することもできます。 例: 

``` json
"actions": {
    "myForEachLoopName": {
        "type": "Foreach",
        "actions": {
            "Send_an_email": {               
            }
        },
        "foreach": "@triggerBody()?['links']",
        "runAfter": {},
        "operationOptions": "Sequential"
    }
},
```

<a name="until-loop"></a>

## <a name="until-loop"></a>"Until" ループ
  
条件を満たすまたは一部の状態が変更されるまでアクションを繰り返し実行するには、ロジック アプリのワークフローで "Until" ループを使用します。 "Until" ループを使用する一般的なユース ケースをいくつか紹介します。

* 必要なレスポンスを受け取るまでエンドポイントを呼び出す。
* データベースにレコードを作成し、そのレコード内の特定のフィールドが承認されるまで待ち、処理を続行する。 

たとえば、このロジック アプリは、毎日午前 8 時 00 分に、変数の値が 10 と等しくなるまである変数を増分させます。 その後、このロジック アプリは現在の値を確認する電子メールを送信します。 この例では Office 365 Outlook を使用していますが、Logic Apps でサポートされている電子メール プロバイダーであれば何でも使用できます ([コネクターの一覧はこちらで確認できます](https://docs.microsoft.com/connectors/))。 別のメール アカウントをお使いの場合でも、全体的な手順は同じですが、UI がやや異なる場合があります。 

1. 空のロジック アプリを作成します。 ロジック アプリ デザイナーで、"繰り返し" を検索し、トリガー **[スケジュール - 繰り返し]** を選択します。 

   ![[スケジュール - 繰り返し] トリガーを追加する](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

2. 間隔、頻度、時刻を設定して、トリガーが実行されるタイミングを指定します。 時刻を設定するには、**[詳細オプションを表示する]** を選択します。

   ![[スケジュール - 繰り返し] トリガーを追加する](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | プロパティ | 値 |
   | -------- | ----- |
   | **間隔** | 1 | 
   | **頻度** | 日 |
   | **設定時刻 (時間)** | 8 |
   ||| 

3. トリガーで、**[新しいステップ]** > **[アクションの追加]** の順に選びます。 "変数" を検索し、アクション **[変数 - 変数を初期化する]** を選択します。

   ![[変数 - 変数を初期化する] アクションを追加する](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

4. 次の値を使用して変数を設定します。

   ![変数のプロパティを設定する](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | プロパティ | 値 | [説明] |
   | -------- | ----- | ----------- |
   | **名前** | 制限 | 変数の名前 | 
   | **種類** | 整数 | 変数のデータ型 | 
   | **値** | 0 | 変数の開始値 | 
   |||| 

5. **[変数を初期化する]** アクションの下で、**[新しいステップ]** > **[詳細]** と選択します。 ループ **[Add a do until]\(do until の追加\)** を選択します。

   !["do until" ループを追加する](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

6. **Limit** 変数と **is equal to** 演算子を選択して、ループの終了条件をビルドします。 比較対象値として **10** と入力します。

   ![ループを停止させる終了条件をビルドする](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

7. ループ内で、**[アクションの追加]** を選択します。 "変数" を検索し、アクション **[変数 - 変数の値を増やす]** を選択します。

   ![変数の値を増やすアクションを追加する](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

8. **[名前]** には、**Limit** 変数を選択します。 **[値]** には、「1」と入力します。 

   !["Limit" を 1 ずつ増やす](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

9. そのループの下のループ外の位置に、電子メールを送信するアクションを追加します。 メッセージに従ってメール アカウントにサインインします。

   ![電子メールを送信するアクションを追加する](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

10. 電子メールのプロパティを設定します。 件名に **Limit** 変数を追加します。 こうすることで、次のように、変数の現在の値が指定の条件と一致していることを確認できます。

    ![電子メールのプロパティを設定する](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

    | プロパティ | 値 | [説明] |
    | -------- | ----- | ----------- | 
    | **To** | *<email-address@domain>* | 受信者の電子メール アドレス。 テストのために、自分の電子メール アドレスを使用します。 | 
    | **[件名]** | "Limit" の現在の値は **Limit** | 電子メールの件名を指定します。 この例では、**Limit** 変数が含まれていることを確認してください。 | 
    | **本文** | <*email-content*> | 送信する電子メール メッセージの内容を指定します。 この例では、任意のテキストを入力してください。 | 
    |||| 

11. ロジック アプリを保存し、 ロジック アプリを手動でテストするには、デザイナーのツール バーで **[実行]** を選択します。

    ロジックの実行が開始された後に、指定した内容の電子メールを受け取ります。

    ![受信した電子メール](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>無限ループを防ぐ

"Until" ループには既定の制限が設定されており、次のいずれかの条件が発生すると実行が中止されます。

| プロパティ | 既定値 | [説明] | 
| -------- | ------------- | ----------- | 
| **カウント** | 60 | ループが終了するまでに実行されるループの最大数。 既定値は、60 サイクルです。 | 
| **タイムアウト** | PT1H | ループが終了するまでにループが実行される最大時間数。 既定値は 1 時間で、ISO 8601 形式で指定されます。 <p>タイムアウト値は、ループのサイクルごとに評価されます。 ループ内のいずれかのアクションがタイムアウト制限よりも長い時間がかかった場合、現在のサイクルは中止されませんが、制限の条件が満たされていないため、次のサイクルは始まりません。 | 
|||| 

既定の制限を変更するには、ループ アクションのシェイプで **[詳細オプションを表示する]** を選択します。

<a name="until-json"></a>

## <a name="until-definition-json"></a>"Until" 定義 (JSON)

ロジック アプリをコード ビューで操作している場合、代わりに次の例のようにロジック アプリの JSON 定義で `Until` ループを定義できます。

``` json
"actions": {
    "Initialize_variable": {
        // Definition for initialize variable action
    },
    "Send_an_email": {
        // Definition for send email action
    },
    "Until": {
        "type": "Until",
        "actions": {
            "Increment_variable": {
                "type": "IncrementVariable",
                "inputs": {
                    "name": "Limit",
                    "value": 1
                },
                "runAfter": {}
            }
        },
        "expression": "@equals(variables('Limit'), 10)",
        // To prevent endless loops, an "Until" loop 
        // includes these default limits that stop the loop. 
        "limit": { 
            "count": 60,
            "timeout": "PT1H"
        },
        "runAfter": {
            "Initialize_variable": [
                "Succeeded"
            ]
        },
    }
},
```

もう 1 つの例では、この "Until" ループはリソースを作成し、HTTP レスポンスのボディが "完了" 状態で返されるときに中止される、HTTP エンドポイントを呼び出します。 無限ループを防ぐために、このループは次の条件のいずれかが発生する場合でも中止されます。

* ループが 10 回実行された。これは `count` 属性で指定されています。 既定値は 60 回です。 
* ループが 2 時間実行を試行した。これは `timeout` 属性で ISO 8601 形式で指定されています。 既定値は 1 時間です。
  
``` json
"actions": {
    "myUntilLoopName": {
        "type": "Until",
        "actions": {
            "Create_new_resource": {
                "type": "Http",
                "inputs": {
                    "body": {
                        "resourceId": "@triggerBody()"
                    },
                    "url": "https://domain.com/provisionResource/create-resource",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                },
                "runAfter": {},
                "type": "ApiConnection"
            }
        },
        "expression": "@equals(triggerBody(), 'Completed')",
        "limit": {
            "count": 10,
            "timeout": "PT2H"
        },
        "runAfter": {}
    }
},
```

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能や提案について投稿や投票を行うには、[Azure Logic Apps のユーザー フィードバック サイト](http://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

* [条件に基づいてステップを実行する (条件付きステートメント)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [さまざまな値に基づいてステップを実行する (switch ステートメント)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [並列ステップを実行または結合する (分岐)](../logic-apps/logic-apps-control-flow-branches.md)
* [グループ化されたアクションの状態に基づいてステップを実行する (スコープ)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
