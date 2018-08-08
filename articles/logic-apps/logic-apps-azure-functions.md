---
title: Azure Functions を使用して Azure Logic Apps にカスタム コードを追加して実行する | Microsoft Docs
description: Azure Functions を使用して Azure Logic Apps にカスタム コード スニペットを追加して実行する方法について説明します
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/25/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 20ad738541554279ff9fd6dd6babe90a38676c00
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263192"
---
# <a name="add-and-run-custom-code-snippets-in-azure-logic-apps-with-azure-functions"></a>Azure Functions を使用して Azure Logic Apps にカスタム コード スニペットを追加して実行する

ロジック アプリの特定の問題に対処するための最小限のコードを作成して実行する場合は、[Azure Functions](../azure-functions/functions-overview.md) を使用して独自の関数を作成することができます。 このサービスは、Node.js または C# で記述されるカスタム コード スニペットをロジック アプリに作成して実行する機能を提供します。コードを実行するために、アプリ全体またはインフラストラクチャの作成を考慮する必要はありません。 Azure Functions は、クラウド内でサーバーレス コンピューティングを実現します。次のようなタスクを実行するために便利です。

* Node.js または C# でサポートされている関数を使用して、ロジック アプリの動作を拡張します。
* ロジック アプリのワークフローで計算を実行します。
* ロジック アプリのフィールドで高度な書式設定や計算を行います。

また、[Azure 関数内からロジック アプリを呼び出す](#call-logic-app)こともできます。

## <a name="prerequisites"></a>前提条件

この記事の手順を実行するには、次のものが必要となります。

* Azure サブスクリプションがない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。 

* 関数を追加するロジック アプリ

  ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」と「[クイックスタート: 初めてのロジック アプリ ワークフローの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)」を参照してください。

* ロジック アプリの最初のステップになる[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts) 

  実行する関数のアクションを追加するには、ロジック アプリがトリガーによって開始される必要があります。

* Azure 関数のコンテナーである Azure 関数アプリと Azure 関数。 関数アプリを持っていない場合は、[自分の関数アプリをまず作成](../azure-functions/functions-create-first-azure-function.md)する必要があります。 次に、ロジック アプリ デザイナーで、[ロジック アプリの外部に個別に](#create-function-external)、または[ロジック アプリの内部に](#create-function-designer)、関数を作成することができます。

  Azure 関数アプリおよび関数がロジック アプリと連携するための要件は、新規の場合でも既存の場合でも次のとおりです。

  * 関数アプリは、ロジック アプリと同じ Azure サブスクリプションに属している必要があります。

  * 関数は **JavaScript** または **C#** の**ジェネリック Webhook** 関数テンプレートを使用する必要があります。 このテンプレートは、ロジック アプリから `application/json` 型のコンテンツを受け入れることができます。 これらのテンプレートは、アプリに関数を追加するときにこれらのテンプレートで作成したカスタム関数をロジック アプリ デザイナーが見つけて表示するためにも役立ちます。

  * 関数テンプレートの **[モード]** プロパティが **[Webhook]** に設定され、**[webhook の種類]** プロパティが **[Generic JSON]\(汎用 JSON\)** に設定されていることを確認してください。

    1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a> にサインインします。
    2. Azure のメイン メニューで、**[Function App]** を選択します。 
    3. **[Function App]** の一覧で関数アプリを選択し、関数を展開して、**[統合]** を選択します。 
    4. テンプレートの **[モード]** プロパティが **[Webhook]** に設定され、**[webhook の種類]** プロパティが **[Generic JSON]\(汎用 JSON\)** に設定されていることを確認してください。 

  * 関数に [API 定義](../azure-functions/functions-openapi-definition.md) (以前の [Swagger ファイル](http://swagger.io/)) がある場合、ロジック アプリ デザイナーは関数パラメーターの操作用に、より豊富なエクスペリエンスを提供します。 
  Swagger 記述を持つ関数をロジック アプリが見つけてアクセスできるようにするには、[以下の手順に従って関数アプリを設定](#function-swagger)します。

<a name="create-function-external"></a>

## <a name="create-functions-outside-logic-apps"></a>ロジック アプリの外部に関数を作成する

<a href="https://portal.azure.com" target="_blank">Azure portal</a> で、Azure 関数アプリを作成します。このアプリは、ロジック アプリと同じ Azure サブスクリプションを持つ必要があります。その後、Azure 関数を作成します。 Azure Functions を初めて使用する場合は、[Azure portal で初めての関数を作成する](../azure-functions/functions-create-first-azure-function.md)方法を学習してください。ただし、追加してロジック アプリから呼び出すことができる Azure 関数を作成するための以下の要件に注意してください。

* **JavaScript** または **C#** の**ジェネリック Webhook** 関数テンプレートを必ず選択します。

  ![ジェネリック Webhook - JavaScript または C#](./media/logic-apps-azure-functions/generic-webhook.png)

* Azure 関数を作成した後、テンプレートの **[モード]** および **[webhook の種類]** プロパティが正しく設定されていることを確認します。

  1. **[Function App]** の一覧で、関数を展開して、**[統合]** を選択します。 

  2. テンプレートの **[モード]** プロパティが **[Webhook]** に設定され、**[webhook の種類]** プロパティが **[Generic JSON]\(汎用 JSON\)** に設定されていることを確認してください。 

     ![関数テンプレートの [統合] プロパティ](./media/logic-apps-azure-functions/function-integrate-properties.png)

<a name="function-swagger"></a>

* 必要に応じて、関数のために、以前 [Swagger ファイル](http://swagger.io/)と呼ばれていた [API 定義を生成](../azure-functions/functions-openapi-definition.md)すると、ロジック アプリ デザイナーでより豊富なエクスペリエンスを使用して関数パラメーターを操作できます。 Swagger 記述を持つ関数をロジック アプリが見つけてアクセスできるように関数アプリを設定するには、次のようにします。

  * 関数アプリがアクティブに実行されていることを確認します。

  * 関数アプリで、すべてのオリジンが許可されるように、次のように[クロス オリジン リソース共有 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) を設定します。

    1. **[Function App]** の一覧で関数アプリを選択し、**[プラットフォーム機能]** > **[CORS]** の順に選択します。

       ![関数アプリ、[プラットフォーム機能]、[CORS] の順に選択する](./media/logic-apps-azure-functions/function-platform-features-cors.png)

    2. **[CORS]** でワイルドカード文字 `*` を追加しますが、リスト内の他のすべてのオリジンを削除し、**[保存]** を選択します。

       ![関数アプリ、[プラットフォーム機能]、[CORS] の順に選択する](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

### <a name="access-property-values-inside-http-requests"></a>HTTP 要求の内部でプロパティ値にアクセスする

Webhook 関数は、入力として HTTP 要求を受け取り、それらの要求を他の関数に渡すことができます。 たとえば、Logic Apps には [DateTime 値を変換する関数](../logic-apps/workflow-definition-language-functions-reference.md)がありますが、この基本的なサンプル JavaScript 関数は、関数に渡された要求オブジェクト内のプロパティにアクセスしてそのプロパティ値を操作する方法を示しています。 オブジェクト内のプロパティにアクセスするために、この例では[ドット (.) 演算子](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors)を使用しています。 

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

この関数の内部で何が起きるかを、次に示します。

1. 関数は `data` 変数を作成し、`request` オブジェクト内の `body` オブジェクトをその変数に割り当てます。 この関数はドット (.) 演算子を使用して、`request` オブジェクト内の `body` オブジェクトを参照します。 

   ```javascript
   var data = request.body;
   ```

2. これで、この関数は `data` 変数を介して `date` プロパティにアクセスし、`ToDateString()` 関数を呼び出すことによってそのプロパティ値を DateTime 型から DateString 型に変換することができます。 関数は、関数の応答の `body` プロパティを介して、結果も返します。 

   ```javascript
   body: data.date.ToDateString();
   ```

Azure 関数を作成できたので、[ロジック アプリに関数を追加する](#add-function-logic-app)方法の手順に従ってください。

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>ロジック アプリの内部で関数を作成する

ロジック アプリ デザイナーで、ロジック アプリ内から開始される Azure 関数を作成するには、まず Azure 関数アプリを用意する必要があります。このアプリは、関数のコンテナーです。 関数アプリを持っていない場合は、まず関数アプリを作成します。 「[Azure Portal で初めての関数を作成する](../azure-functions/functions-create-first-azure-function.md)」を参照してください。 

1. <a href="https://portal.azure.com" target="_blank">Azure portal</a> のロジック アプリ デザイナーでロジック アプリを開きます。 

2. 関数を作成して追加するステップの下で、**[新しいステップ]** > **[アクションの追加]** の順にクリックします。 

3. 検索ボックスで、フィルターとして「azure functions」と入力します。
アクションの一覧で、**[Azure 関数を選択する - Azure Functions]** というアクションを選択します。 

   !["Azure functions" を探す](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. 関数アプリの一覧で、関数アプリを選択します。 アクションの一覧が開いたら、**[Azure Functions - 新しい関数を作成する]** というアクションを選択します。

   ![関数アプリを選択する](./media/logic-apps-azure-functions/select-function-app-create-function.png)

5. 関数定義エディターで、関数を定義します。

   1. **[関数名]** ボックスで関数の名前を指定します。 

   2. **[コード]** ボックスで、関数コードをテンプレートに追加します。関数の実行が終了した後にロジック アプリに返す応答とペイロードも含めます。 
   テンプレート コード内のコンテキスト オブジェクトには、ロジック アプリが関数に渡すメッセージとコンテンツを記述します。たとえば、次のようになります。

      ![関数を定義する](./media/logic-apps-azure-functions/function-definition.png)

      関数内では、次の構文を使用して、コンテキスト オブジェクトのプロパティを参照できます。

      ```text
      context.<token-name>.<property-name>
      ```
      この例では、次のような構文を使用します。

      ```text
      context.body.content
      ```

   3. 操作が完了したら、**[作成]** を選択します。

6. **[要求本文]** ボックスで、関数の入力として渡すコンテキスト オブジェクトを指定します。書式は JavaScript Object Notation (JSON) にする必要があります。 **[要求本文]** ボックスをクリックすると、動的コンテンツの一覧が開き、前のステップの使用可能なプロパティのトークンを選択することができます。 

   この例では、電子メール トリガーの **[本文]** トークンのオブジェクトを渡します。  

   ![[要求本文] の例 - コンテキスト オブジェクトのペイロード](./media/logic-apps-azure-functions/function-request-body-example.png)

   コンテキスト オブジェクトの内容に基づいて、ロジック アプリ デザイナーは、インラインで編集できる関数テンプレートを生成します。 
   Logic Apps は、入力コンテキスト オブジェクトに基づく変数も作成します。

   この例では、コンテキスト オブジェクトは文字列としてキャストされないため、コンテンツは JSON ペイロードに直接追加されます。 
   ただし、オブジェクトが JSON トークン (文字列、JSON オブジェクト、または JSON 配列である必要があります) でない場合は、エラーが発生します。 
   文字列としてコンテキスト オブジェクトをキャストするには、たとえば次のように二重引用符を追加します。

   ![オブジェクトを文字列としてキャストする](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

7. 使用するメソッド、要求ヘッダー、クエリ パラメーターなど、その他の詳細を指定するには、**[詳細オプションを表示する]** を選択します。

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>ロジック アプリに既存の関数を追加する

ロジック アプリから既存の Azure 関数を呼び出すには、ロジック アプリ デザイナーで他のアクションと同様に Azure 関数を追加します。 

1. <a href="https://portal.azure.com" target="_blank">Azure portal</a> のロジック アプリ デザイナーでロジック アプリを開きます。 

2. 関数を追加するステップの下で、**[新しいステップ]** > **[アクションの追加]** の順にクリックします。 

3. 検索ボックスで、フィルターとして「azure functions」と入力します。
アクションの一覧で、**[Azure 関数を選択する - Azure Functions]** というアクションを選択します。 

   !["Azure functions" を探す](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. 関数アプリの一覧で、関数アプリを選択します。 関数の一覧が表示されたら、関数を選択します。 

   ![関数アプリと Azure 関数を選択する](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   API 定義 (Swagger 記述) を持ち、[ロジック アプリが探してアクセスできるように設定された](#function-swagger)関数の場合は、**[Swagger アクション]** を選択することができます。

   ![関数アプリ、[Swagger アクション]、および Azure 関数を選択する](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

5. **[要求本文]** ボックスで、関数の入力として渡すコンテキスト オブジェクトを指定します。書式は JavaScript Object Notation (JSON) にする必要があります。 このコンテキスト オブジェクトは、ロジック アプリが関数に送信するメッセージとコンテンツを記述します。 

   **[要求本文]** ボックスをクリックすると、動的コンテンツの一覧が開き、前のステップの使用可能なプロパティのトークンを選択することができます。 
   この例では、電子メール トリガーの **[本文]** トークンのオブジェクトを渡します。

   ![[要求本文] の例 - コンテキスト オブジェクトのペイロード](./media/logic-apps-azure-functions/function-request-body-example.png)

   この例では、コンテキスト オブジェクトは文字列としてキャストされないため、コンテンツは JSON ペイロードに直接追加されます。 
   ただし、オブジェクトが JSON トークン (文字列、JSON オブジェクト、または JSON 配列である必要があります) でない場合は、エラーが発生します。 
   文字列としてコンテキスト オブジェクトをキャストするには、たとえば次のように二重引用符を追加します。

   ![オブジェクトを文字列としてキャストする](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

6. 使用するメソッド、要求ヘッダー、クエリ パラメーターなど、その他の詳細を指定するには、**[詳細オプションを表示する]** を選択します。

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>関数からロジック アプリを呼び出す

Azure 関数の内部からロジック アプリをトリガーするには、そのロジック アプリに呼び出し可能なエンドポイント、より具体的には **Request** トリガーが必要です。 次に、関数の内部から、その **Request** トリガーの URL に HTTP POST 要求を送信し、ロジック アプリで処理するペイロードを含めます。 詳細については、「[ロジック アプリを呼び出し、トリガーし、入れ子にする](../logic-apps/logic-apps-http-endpoint.md)」をご覧ください。 

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](http://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

* [Logic Apps のコネクタ](../connectors/apis-list.md)について確認します。
