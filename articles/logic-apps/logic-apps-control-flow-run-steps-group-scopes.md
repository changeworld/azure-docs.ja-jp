---
title: グループの状態に基づいてアクションを実行するスコープを追加する - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps のグループ アクションの状態に基づいてワークフロー アクションを実行するスコープを作成する方法です
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 1258175eb3d28d39be8be08498ba8d2e0998aa43
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298816"
---
# <a name="create-scopes-that-run-workflow-actions-based-on-group-status-in-azure-logic-apps"></a>Azure Logic Apps のグループの状態に基づいてワークフロー アクションを実行するスコープを作成する

別のアクションのグループが成功または失敗した後にのみアクションを実行するには、そのアクションを "*スコープ*" の中にグループ化します。 この構造は、アクションを論理グループとして整理し、そのグループの状態を評価して、そのスコープの状態に基づいてアクションを実行するときに便利です。 そのスコープ内のすべてのアクションの実行が完了すると、スコープも独自の状態を取得します。 たとえば、[例外とエラー処理](../logic-apps/logic-apps-exception-handling.md#scopes)を実装するときにスコープを使用できます。 

スコープの状態を調べるには、ロジック アプリの実行状態を判定するのと同じ基準 ("成功"、"失敗"、"取り消し済み" など) を使用できます。 既定では、スコープのすべてのアクションが成功すると、そのスコープの状態は "成功" とマークされます。 ただし、スコープ内のいずれかのアクションが失敗するかキャンセルされると、そのスコープの状態は "失敗" とマークされます。 スコープの制限については、[制限と構成](../logic-apps/logic-apps-limits-and-config.md)に関するページをご覧ください。 

一例として、特定のアクションを実行するスコープと、そのスコープの状態をチェックする条件を使用する、基本のロジック アプリを紹介します。 スコープ内のいずれかのアクションが失敗または予期せず終了した場合、そのスコープはそれぞれ "失敗" または "Aborted (中止)" とマークされ、ロジック アプリが [Scope failed]\(スコープが失敗した場合\) のメッセージを送信します。 スコープ内のすべてのアクションが成功した場合、ロジック アプリは [Scope succeeded]\(スコープが成功した場合\) のメッセージを送信します。

![[スケジュール - 繰り返し] トリガーを設定する](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>前提条件

この記事の例に従って進めるには、次の項目が必要です。

* Azure サブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 

* Logic Apps でサポートされる任意の電子メール プロバイダーの電子メール アカウント。 この例では、Outlook.com を使用します。 別のプロバイダーを使用する場合、フロー全般は変わりませんが、UI の表示が異なります。

* Bing 地図のキー。 このキーを取得するには、<a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">Bing 地図のキーの取得</a>に関する記事をご覧ください。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

## <a name="create-sample-logic-app"></a>サンプルのロジック アプリを作成する

まず、このサンプルのロジック アプリを作成し、後でスコープを追加します。

![サンプルのロジック アプリを作成する](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* Bing 地図のサービスを指定した間隔でチェックする **[スケジュール - 繰り返し]** トリガー
* 2 つの場所の間の移動時間をチェックする **[Bing Maps - Get route]\(Bing 地図 - ルートを取得する\)** アクション
* 移動時間が指定した移動時間を超えるかどうかをチェックする条件文
* 現在の移動時間が指定した時間を超えたことで電子メールを送信するアクション

ロジック アプリはいつでも保存できるため、頻繁に保存してください。

1. まだサインインしていない場合は、<a href="https://portal.azure.com" target="_blank">Azure Portal</a> にサインインします。 空のロジック アプリを作成します。

2. **[間隔]** を "1" に、**[頻度]** を [分] に設定して、**[スケジュール - 繰り返し]** トリガーを追加します。

   ![[スケジュール - 繰り返し] トリガーを設定する](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > ビューを視覚的に簡略化し、デザイナーで各アクションの詳細を非表示にするには、これらのステップを進むにつれ各アクションのシェイプを折りたたみます。

3. **[Bing Maps - Get route]\(Bing 地図 - ルートを取得する\)** アクションを追加します。 

   1. Bing 地図の接続が存在しない場合、接続を作成するように求められます。

      | Setting | 値 | [説明] |
      | ------- | ----- | ----------- |
      | **Connection Name** | BingMapsConnection | 接続の名前を指定します。 | 
      | **API キー** | <*your-Bing-Maps-key*> | あらかじめ取得しておいた Bing 地図のキーを入力します。 | 
      ||||  

   2. この画像の下の表に示すように、**[Get route]\(ルートを取得する\)** アクションを設定します。

      ![[Bing Maps - Get route]\(Bing 地図 - ルートを取得する\) アクションを設定する](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      これらのパラメーターの詳細については、「[Calculate a route (ルートの計算)](https://msdn.microsoft.com/library/ff701717.aspx)」を参照してください。

      | Setting | 値 | [説明] |
      | ------- | ----- | ----------- |
      | **通過地点 1** | <*start*> | ルートの出発地を入力します。 | 
      | **通過地点 2** | <*end*> | ルートの目的地を入力します。 | 
      | **回避** | なし | ルート上で回避する要素 (高速道路、通行料金など) を入力します。 使用できる値については、「[Calculate a route](https://msdn.microsoft.com/library/ff701717.aspx)」 (ルートを計算する) をご覧ください。 | 
      | **最適化** | timeWithTraffic | ルートを最適化するためのパラメーター (距離、最新の交通情報を加味した移動時間など) を選択します。 この例では、値 "timeWithTraffic" を使用します。 | 
      | **距離単位** | <*your-preference*> | ルートを計算する距離の単位を入力します。 この例では、値 "Mile (マイル)" を使用します。 | 
      | **Travel mode (移動手段)** | Driving (車) | ルートの移動手段を入力します。 この例では、値 "Driving (車)" を使用します。 | 
      | **Transit Date-Time (交通機関の日時)** | なし | 乗り換えモードの場合のみ適用されます。 | 
      | **Transit Date-Type Type (交通機関の日時の種類)** | なし | 乗り換えモードの場合のみ適用されます。 | 
      ||||  

4. 現在の交通量を加味した移動時間が指定の時間を超えるかどうかをチェックする条件を追加します。 この例では、この画像の下の手順に従います。

   ![条件をビルドする](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. 条件の名前をわかりやすく「**If traffic time more than specified time (移動時間が指定した時間を超える場合)**」に変更します。

   2. パラメーターの一覧から、**[Travel Duration Traffic]\(移動時間の交通量\)** フィールドを選択します (秒単位)。 

   3. 比較演算子として、**[次の値より大きい]** を選択します。

   4. 比較する値として、「**600**」秒 (10 分) と入力します。

5. 条件の **[If true]\(true の場合\)** ブランチに、お使いの電子メール プロバイダーの "電子メールを送信する" アクションを追加します。 この画像の下の表に示すように、このアクションを設定します。

   ![[If true]\(true の場合\) ブランチに [Send an email]\(電子メールを送信する\) アクションを追加する](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. **[宛先]** フィールドに、テスト用に自分の電子メール アドレスを入力します。

   2. **[件名]** フィールドに、次のテキストを入力します。

      ```Time to leave: Traffic more than 10 minutes```

   3. **[本文]** フィールドに、次のテキストを入力し、末尾にスペースを追加します。 

      ```Travel time: ```

      **[本文]** フィールドにカーソルが表示されている間は、この時点で使用できる任意のパラメーターを選択できるように、動的コンテンツ リストが開いたままになります。

   4. 動的コンテンツ リストの **[式]** を選択します。

   5. **div( )** 関数を探して選択します。

   6. カーソルが関数の括弧内にある間に、**[動的コンテンツ]** を選択して、次に **[Travel Duration Traffic]\(移動時間の交通量\)** パラメーターを追加します。

   7. 動的パラメーター リストの **[Get route]\(ルートを取得する\)** で、**[Travel Duration Traffic]\(移動時間の交通量\)** フィールドを選択します。

      ![[Travel Duration Traffic]\(移動時間の交通量\) フィールドを選択する](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   8. フィールドが JSON 形式に解決された後、数字 ```60``` に続けて**カンマ** (```,```) を追加して、**[Travel Duration Traffic]\(移動時間の交通量\)** の値を秒から分に変換します。 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      これで式は次の例のようになります。

      ![式を仕上げる](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   9. 完成したら、**[OK]** を選択してください。

  10. 式が解決された後は、テキスト ``` minutes``` と先頭にスペースを追加します。
  
      これで **[本文]** フィールドは次の例のようになります。

      ![完成した[本文] フィールド](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)

6. ロジック アプリを保存し、

次に、スコープを追加して特定のアクションをグループ化し、それらの状態を評価します。

## <a name="add-a-scope"></a>スコープを追加する

1. まだ開いていない場合は、ロジック アプリ デザイナーでロジック アプリを開きます。 

2. 必要なワークフローの場所にスコープを追加します。 例: 

   * ロジック アプリ ワークフローの既存のステップ間にスコープを追加するには、スコープ追加する矢印にポインターを移動します。 
   **プラス記号** (**+**) > **[Add a scope]\(スコープを追加する\)** を選択します。

     ![スコープを追加する](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

     ワークフローの末尾にスコープを追加するときは、ロジック アプリの一番下で **[+ 新しいステップ]** > **[...More]\(...その他\)** > **[Add a scope]\(スコープを追加する\)** を選択します。

3. ここで、スコープ内で実行するステップを追加するか、既存のステップをドラッグします。 この例では、次のアクションをスコープにドラッグします。
      
   * **[Get route]\(ルートを取得する\)**
   * **[If traffic time more than specified time]\(移動時間が指定した時間を超える場合\)**。**[true]** と **[false]** の両方のブランチが含まれます。

   これでロジック アプリは次の例のようになります。

   ![スコープが追加された状態](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

4. スコープの下に、スコープの状態をチェックする条件を追加します。 条件の名前をわかりやすく「**If scope failed (スコープが失敗した場合)**」に変更します。

   ![スコープの状態をチェックする条件を追加する](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
5. スコープの状態が `Failed` または `Aborted` のどちらと等しいかどうかをチェックする、この式をビルドします。

   ![スコープの状態をチェックする式を追加する](./media/logic-apps-control-flow-run-steps-group-scopes/build-expression-check-scope-status.png)

   または、この式をテキストとして追加し、**[Edit in advanced mode]\(詳細設定モードで編集する\)** を選択します。

   ```@equals('@result(''Scope'')[0][''status'']', 'Failed, Aborted')```

6. **[If true]\(true の場合\)** ブランチと **[If false]\(false の場合\)** ブランチに、実行するアクション (例: 電子メールまたはメッセージを送信する) を追加します。

   ![スコープの状態をチェックする式を追加する](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

7. ロジック アプリを保存し、

完成したロジック アプリの、すべてのシェイプが展開された状態では、次の例のようになります。

![完成したロジック アプリにスコープが追加された状態](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>作業をテストする

デザイナーのツールバーで、**[実行]** を選択します。 すべてのアクションが成功した場合、ロジック アプリは [Scope succeeded]\(スコープが成功した場合\) のメッセージを送信します。 いずれかのアクションが失敗した場合は、[Scope failed]\(スコープが失敗した場合\) のメッセージが送信されます。 

<a name="scopes-json"></a>

## <a name="json-definition"></a>JSON の定義

コード ビューで操作している場合、代わりにロジック アプリの JSON 定義でスコープ構造を定義できます。 たとえば、前のロジック アプリのトリガーとアクションの JSON 定義は、次のようになります。

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    },
  }
}
```

```json
"actions": {
  "If_scope_failed": {
    "type": "If",
    "actions": {
      "Scope_failed": {
        "type": "ApiConnection",
        "inputs": {
          "body": {
            "Body": "Scope failed",
            "Subject": "Scope failed",
            "To": "<your-email@domain.com>"
          },
          "host": {
            "connection": {
              "name": "@parameters('$connections')['outlook']['connectionId']"
            }
          },
          "method": "post",
          "path": "/Mail"
        },
        "runAfter": {}
      }
    },
    "else": {
      "actions": {
        "Scope_succeded": {
          "type": "ApiConnection",
          "inputs": {
            "body": {
              "Body": "None",
              "Subject": "Scope succeeded",
              "To": "<your-email@domain.com>"
            },
            "host": {
              "connection": {
               "name": "@parameters('$connections')['outlook']['connectionId']"
              }
            },
            "method": "post",
            "path": "/Mail"
          },
          "runAfter": {}
        }
      }
    },
    "expression": "@equals('@result(''Scope'')[0][''status'']', 'Failed, Aborted')",
    "runAfter": {
      "Scope": [
        "Succeeded"
      ]
    }
  },
  "Scope": {
    "type": "Scope",
    "actions": {
      "Get_route": {
        "type": "ApiConnection",
        "inputs": {
          "host": {
            "connection": {
              "name": "@parameters('$connections')['bingmaps']['connectionId']"
            }
          },
          "method": "get",
          "path": "/REST/V1/Routes/Driving",
          "queries": {
            "distanceUnit": "Mile",
            "optimize": "timeWithTraffic",
            "travelMode": "Driving",
            "wp.0": "<start>",
            "wp.1": "<end>"
          }
        },
        "runAfter": {}
      },
      "If_traffic_time_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'], 60)} minutes",
                 "Subject": "Time to leave: Traffic more than 10 minutes",
                 "To": "<your-email@domain.com>"
              },
              "host": {
                "connection": {
                   "name": "@parameters('$connections')['outlook']['connectionId']"
                }
              },
              "method": "post",
              "path": "/Mail"
            },
            "runAfter": {}
          }
        },
        "expression": "@greater(body('Get_route')?['travelDurationTraffic'], 600)",
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
}
```

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能や提案について投稿や投票を行うには、[Azure Logic Apps のユーザー フィードバック サイト](http://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

* [条件に基づいてステップを実行する (条件付きステートメント)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [さまざまな値に基づいてステップを実行する (switch ステートメント)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [ステップを実行して繰り返す (ループ)](../logic-apps/logic-apps-control-flow-loops.md)
* [並列ステップを実行または結合する (分岐)](../logic-apps/logic-apps-control-flow-branches.md)
