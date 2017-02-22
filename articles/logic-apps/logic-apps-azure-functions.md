---
title: "Logic Apps での Azure Functions の使用 | Microsoft Docs"
description: "Logic Apps で Azure Functions を使用する方法"
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: de073c5a5cda5b4b1caacd0163f785f6295f35ba


---
# <a name="using-azure-functions-with-logic-apps"></a>Logic Apps での Azure Functions の使用
ロジック アプリで Azure Functions を使用することにより、C# または node.js のカスタム スニペットを実行できます。  [Azure Functions](../azure-functions/functions-overview.md) を使用すると、Microsoft Azure でサーバーを使わずに計算を実行できます。 これは、次のタスクを実行するのに役立ちます。

* ロジック アプリ内のフィールドの高度な書式設定や計算
* ワークフロー内での計算の実行
* C# または node.js でサポートされる関数を使用した Logic Apps の機能の拡張

## <a name="create-a-function-for-logic-apps"></a>Logic Apps 用の関数を作成する
**汎用 Webhook - Node** または**汎用 Webhook - C#** テンプレートを使用して、Azure Functions ポータルで新しい関数を作成することをお勧めします。 そうすれば、ロジック アプリから `application/json` を受け取るテンプレートが自動的に生成されます。  Azure Functions で **[統合]** タブを選択すると、**[モード]** が **[Webhook]** に、**[Webhook の種類]** が **[汎用 JSON]** に設定されます。  これらのテンプレートを使用する関数は自動的に検出されて、Logic Apps デザイナーの **[自分のリージョンの Azure Functions]**

Webhook 関数は、要求を受け取ると、それを `data` 変数経由でメソッドに渡します。 ペイロードのプロパティにアクセスするには、 `data.foo`のようなドット表記を使用します。  たとえば、DateTime 値を日付文字列に変換する単純な JavaScript 関数は次の例のようになります。

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## <a name="call-azure-functions-from-a-logic-app"></a>ロジック アプリから Azure Functions を呼び出す
デザイナーで **[アクション]** メニューをクリックすると、**[自分のリージョンの Azure Functions]** を選択できます。  これを選択すると、サブスクリプションに含まれるコンテナーの一覧が表示されるため、呼び出す関数を選択できます。  

関数を選択すると、入力ペイロード オブジェクトの指定を求められます。 これは、ロジック アプリから関数に送信されるメッセージで、JSON オブジェクトである必要があります。 たとえば、Salesforce トリガーから **Last Modified** の日付を渡す場合、関数ペイロードは次のようになります。

![最終変更日][1]

## <a name="trigger-logic-apps-from-a-function"></a>関数からロジック アプリをトリガーする
関数内からロジック アプリをトリガーすることもできます。  この操作を行うには、手動トリガーが組み込まれたロジック アプリを作成するだけです。 詳細については、「 [呼び出し可能なエンドポイントとしてのロジック アプリ](logic-apps-http-endpoint.md)」を参照してください。  その後、関数内で、手動トリガーの URL に対する HTTP POST を生成し、ロジック アプリに送信するペイロードを含めます。

### <a name="create-a-function-from-the-designer"></a>デザイナーから関数を作成する
Node.js webhook 関数は、デザイナー内から作成することもできます。 最初に、 **[自分のリージョンの Azure Functions]** を選択し、関数用にコンテナーを選択します。  コンテナーがまだない場合は、 [Azure Functions ポータル](https://functions.azure.com/signin)から作成する必要があります。 **[新規作成]**を選択します。  

計算するデータに基づいてテンプレートを生成するには、関数に渡すコンテキスト オブジェクトを指定します。 これは JSON オブジェクトである必要があります。 たとえば、FTP アクションからファイル コンテンツを渡す場合、コンテキストのペイロードは次のようになります。

![コンテキスト ペイロード][2]

> [!NOTE]
> このオブジェクトは文字列としてキャストされなかったため、コンテンツは JSON ペイロードに直接追加されます。 ただし、JSON トークン (つまり、文字列か JSON オブジェクトまたは配列) でない場合は、エラーになります。 文字列としてキャストするには、この記事の最初の図に示されているように引用符を追加します。
> 
> 

その後、デザイナーによって、インラインで作成できる関数テンプレートが生成されます。 変数は、関数に渡す予定のコンテキストに基づいて事前に作成されます。

<!--Image references-->
[1]: ./media/logic-apps-azure-functions/callfunction.png
[2]: ./media/logic-apps-azure-functions/createfunction.png



<!--HONumber=Jan17_HO3-->


