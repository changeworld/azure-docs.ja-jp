<properties
   pageTitle="Logic Apps での Azure Functions の使用 | Microsoft Azure"
   description="Logic Apps で Azure Functions を使用する方法"
   services="logic-apps,functions"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="09/01/2016"
   ms.author="jehollan"/>

# Logic Apps での Azure Functions の使用

ロジック アプリで Azure Functions を使用することにより、C# または node.js のカスタム スニペットを実行できます。[Azure Functions](../azure-functions/functions-overview.md) を使用すると、Microsoft Azure でサーバーを使わずに計算を実行できます。これは、次のタスクを実行するのに役立ちます。

* アクションの値の書式設定 (例: DateTime から日付文字列への変換)
* ワークフロー内での計算の実行
* C# または node.js でサポートされる関数を使用した Logic Apps の機能の拡張

## Logic Apps 用の関数を作成する

"**汎用 Webhook - Node**" または "**汎用 Webhook - C#**" テンプレートを使用して、Azure Functions ポータルで新しい関数を作成することをお勧めします。そうすれば、ロジック アプリから `application/json` を受け取るテンプレートが自動的に生成されます。これらのテンプレートを使用する関数は自動的に検出されて、Logic Apps デザイナーの **[自分のリージョンの Azure Functions]** に一覧表示されます。

Webhook 関数は、要求を受け取ると、それを `data` 変数経由でメソッドに渡します。ペイロードのプロパティにアクセスするには、`data.foo` のようなドット表記を使用します。たとえば、DateTime 値を日付文字列に変換する単純な JavaScript 関数は次の例のようになります。

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## ロジック アプリから Azure Functions を呼び出す

デザイナーで **[アクション]** メニューをクリックすると、**[自分のリージョンの Azure Functions]** を選択できます。これを選択すると、サブスクリプションに含まれるコンテナーの一覧が表示されるため、呼び出す関数を選択できます。

関数を選択すると、入力ペイロード オブジェクトの指定を求められます。これは、ロジック アプリから関数に送信されるメッセージで、JSON オブジェクトである必要があります。たとえば、Salesforce トリガーから **Last Modified** の日付を渡す場合、関数ペイロードは次のようになります。

![最終変更日][1]

## 関数からロジック アプリをトリガーする

関数内からロジック アプリをトリガーすることもできます。この操作を行うには、手動トリガーが組み込まれたロジック アプリを作成するだけです。詳細については、「[呼び出し可能なエンドポイントとしてのロジック アプリ](app-service-logic-http-endpoint.md)」を参照してください。その後、関数内で、手動トリガーの URL に対する HTTP POST を生成し、ロジック アプリに送信するペイロードを含めます。

### デザイナーから関数を作成する

Node.js webhook 関数は、デザイナー内から作成することもできます。最初に、**[自分のリージョンの Azure Functions]** を選択し、関数用にコンテナーを選択します。コンテナーがまだない場合は、[Azure Functions ポータル](https://functions.azure.com/signin)から作成する必要があります。**[新規作成]** を選択します。

計算するデータに基づいてテンプレートを生成するには、関数に渡すコンテキスト オブジェクトを指定します。これは JSON オブジェクトである必要があります。たとえば、FTP アクションからファイル コンテンツを渡す場合、コンテキストのペイロードは次のようになります。

![コンテキスト ペイロード][2]

>[AZURE.NOTE] このオブジェクトは文字列としてキャストされなかったため、コンテンツは JSON ペイロードに直接追加されます。ただし、JSON トークン (つまり、文字列か JSON オブジェクトまたは配列) でない場合は、エラーになります。文字列としてキャストするには、この記事の最初の図に示されているように引用符を追加します。

その後、デザイナーによって、インラインで作成できる関数テンプレートが生成されます。変数は、関数に渡す予定のコンテキストに基づいて事前に作成されます。




<!--Image references-->
[1]: ./media/app-service-logic-azure-functions/callFunction.png
[2]: ./media/app-service-logic-azure-functions/createFunction.png

<!---HONumber=AcomDC_0907_2016-->