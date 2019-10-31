---
title: Azure Functions を使用してロジック アプリを呼び出す - Azure Logic Apps
description: Azure Service Bus をリッスンすることでロジック アプリを呼び出すかトリガーする Azure 関数を作成する
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 2ab6ace7c30c3dd385928b6b0ae8000485d5f495
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680139"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Azure Functions と Azure Service Bus を使用してロジック アプリを呼び出すか、またはトリガーする

[Azure Functions](../azure-functions/functions-overview.md) を使用すると、実行時間の長いリスナーまたはタスクをデプロイする必要がある場合にロジック アプリをトリガーできます。 たとえば、[Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) キューでリッスンする Azure 関数を作成し、ロジック アプリをプッシュ トリガーとして直ちに起動できます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* Azure Service Bus 名前空間。 名前空間がない場合は、[最初に名前空間を作成](../service-bus-messaging/service-bus-create-namespace-portal.md)します。

* Azure 関数のコンテナーである Azure 関数アプリ。 関数アプリがない場合は、[最初に関数アプリを作成](../azure-functions/functions-create-first-azure-function.md)し、ランタイム スタックとして .NET を選択するようにしてください。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

## <a name="create-logic-app"></a>ロジック アプリを作成する

このシナリオでは、トリガーする各ロジック アプリを実行する関数が用意されています。 まず、HTTP 要求トリガーで開始されるロジック アプリを作成します。 関数は、キュー メッセージが受信されたときに、そのエンドポイントを呼び出します。  

1. [Azure portal](https://portal.azure.com) にサインインして、空のロジック アプリを作成します。

   ロジック アプリを初めて使用する場合は、[クイック スタート:初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

1. 検索ボックスに「HTTP 要求」と入力します。 トリガーの一覧から、**HTTP 要求の受信時**

   ![トリガーの選択](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   要求トリガーでは、キュー メッセージで使用する JSON スキーマを必要に応じて入力できます。 JSON スキーマは、ロジック アプリ デザイナーが入力データの構造を理解できるよう支援すると共に、その出力をユーザーがワークフローで使用しやすくします。

1. スキーマを指定するには、 **[要求本文の JSON スキーマ]** ボックスにスキーマを入力します。その例を次に示します。

   ![JSON スキーマの指定](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   スキーマはなくても JSON 形式のサンプル ペイロードがあれば、そのペイロードからスキーマを生成することができます。

   1. 要求トリガーで **[サンプルのペイロードを使用してスキーマを生成する]** を選択します。

   1. **[サンプルの JSON ペイロードを入力するか、貼り付けます]** でサンプル ペイロードを入力し、 **[完了]** を選択します。

      ![サンプル ペイロードの入力](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   このサンプル ペイロードから生成されてトリガーに表示されるスキーマは次のとおりです。

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "number": {
                  "type": "integer"
               },
               "street": {
                  "type": "string"
               },
               "city": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "integer"
               },
               "country": {
                  "type": "string"
               }
            }
         }
      }
   }
   ```

1. キュー メッセージを受信した後に実行する他のアクションをすべて追加します。

   たとえば、Office 365 Outlook コネクタを使ってメールを送信できます。

1. ロジック アプリを保存します。これで、このロジック アプリのトリガーに使用するコールバック URL が生成されます。 このコールバック URL は、後で Azure Service Bus キューのトリガーのコードで使用します。

   このコールバック URL は、**HTTP POST URL** プロパティに表示されます。

   ![トリガー用に生成されたコールバック URL](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Azure 関数を作成する

次に、トリガーとして機能し、キューをリッスンする関数を作成します。

1. Azure portal で関数アプリを開いて展開します (まだ開いていない場合)。 

1. 該当する関数アプリ名の **[関数]** を展開します。 **[関数]** ウィンドウの **[新しい関数]** を選択します。

   ![[関数] を展開し、[新しい関数] を選択する](./media/logic-apps-scenario-function-sb-trigger/create-new-function.png)

1. ランタイム スタックとして .NET を選択した新しい関数アプリを作成したか、または既存の関数アプリを使用しているかに基づいて、次のテンプレートを選択します。

   * 新しい関数アプリの場合は、次のテンプレートを選択します。**Service Bus キューのトリガー**

     ![新しい関数アプリのテンプレートを選択する](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * 既存の関数アプリの場合は、次のテンプレートを選択します。**Service Bus キューのトリガー - C#**

     ![既存の関数アプリのテンプレートを選択する](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. **[Azure Service Bus Queue trigger] (Azure Service Bus キューのトリガー)** ウィンドウで、トリガーの名前を指定し、キューの **Service Bus 接続** (これは Azure Service Bus SDK の `OnMessageReceive()` リスナーを使用します) を設定して **[作成]** を選択します。

1. キュー メッセージをトリガーとして使用して、前に作成したロジック アプリのエンドポイントを呼び出す基本的な関数を作成します。 この例では、`application/json` メッセージのコンテンツの種類を使用していますが、必要に応じて種類を変更できます。 可能な場合は、HTTP クライアントのインスタンスを再利用します。 詳細については、「[Azure Functions での接続の管理](../azure-functions/manage-connections.md)」を参照してください。

   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Callback URL for previously created Request trigger
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible
   private static HttpClient httpClient = new HttpClient();

   public static void Run(string myQueueItem, ILogger log)
   {
       log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

       var response = httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
   }
   ```

1. 関数をテストするには、[Service Bus エクスプ ローラー](https://github.com/paolosalvatori/ServiceBusExplorer)などのツールを使用してキュー メッセージを追加します。

   関数がメッセージを受信した直後にロジック アプリが起動します。

## <a name="next-steps"></a>次の手順

[HTTP エンドポイントを使用してワークフローを呼び出すか、トリガーするか、または入れ子にする](../logic-apps/logic-apps-http-endpoint.md)