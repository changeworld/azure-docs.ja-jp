<properties
   pageTitle="Logic Apps での Azure Functions の使用 | Microsoft Azure"
   description="Logic Apps で Azure Functions を使用する方法"
   services="app-service\logic,functions"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/14/2016"
   ms.author="jehollan"/>
   
# Logic Apps での Azure Functions の使用

ロジック アプリで Azure Functions を利用することで、C# または Node.js のカスタム スニペットを実行できます。[Azure Functions](../azure-functions/functions-overview.md) は、Microsoft Azure でサーバーを使用しないコンピューティングを実現するサービスです。このサービスは、次のシナリオの多くで Logic Apps 内から使用できます。

* アクションの値の書式設定 (例: DateTime から日付文字列への変換)
* ワークフロー内での計算の実行
* C# または Node.js でサポートされている関数を使用した Logic Apps 機能の拡張

## Logic Apps 用の Azure 関数を作成する

Functions ポータルで、"汎用 Node Webhook" または "汎用 C# Webhook" テンプレートを使用して新しい Azure 関数を作成することをお勧めします。これにより、ロジック アプリから `application/json` を受け取るテンプレートが自動的に設定されます。さらに、これらのテンプレートを使用する関数が自動的に検出され、Logic Apps デザイナーの [Azure Functions in my region] (自分のリージョンの Azure Functions) の下に表示されます。

Webhook 関数は、要求を受け取ると、それを `data` 変数経由でメソッドに渡します。ペイロードのプロパティにアクセスするには、`data.foo` のようなドット表記を使用します。たとえば、DateTime 値を日付文字列に変換する単純な JavaScript 関数は次のようになります。

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## ロジック アプリから Azure 関数を呼び出す

デザイナーで、アクションのドロップダウン メニューをクリックすると、[Azure Functions in my Region] (自分のリージョンの Azure Functions) を選択できます。 これを選択すると、サブスクリプションに含まれるコンテナーの一覧が表示されるため、呼び出す関数を選択できます。関数を選択すると、入力ペイロード オブジェクトを指定するように求められます。これは、ロジック アプリから関数に送信されるメッセージで、JSON オブジェクトである必要があります。たとえば、salesforce トリガーから "Last Modified Date" を渡す場合、関数ペイロードは次のようになります。

![][1]

## Azure 関数からロジック アプリをトリガーする

関数内からロジック アプリをトリガーすることもできます。この操作を行うには、手動トリガーが組み込まれたロジック アプリを作成するだけです (詳細については、[こちら](app-service-logic-http-endpoint.md)を参照してください)。その後、Azure 関数内で、手動トリガーの URL に対する HTTP POST を生成し、ロジック アプリに送信するペイロードを含めます。

### デザイナーから関数を作成する

Node.js webhook 関数は、デザイナー内から作成することもできます。最初に、[Azure Functions in my Region] (自分のリージョンの Azure Functions) を選択し、関数用にコンテナーを選択します。コンテナーがまだない場合は、[Azure Functions ポータル](https://functions.azure.com/signin)から作成する必要があります。次に、[Create New] (新規作成) を選択します。 計算するデータに基づいてテンプレートを生成するには、関数に渡す予定のコンテキスト オブジェクトを指定してください。これは JSON オブジェクトである必要があります。たとえば、FTP アクションからファイル コンテンツを渡す場合、コンテキストのペイロードは次のようになります。

![][2]

>[AZURE.NOTE] 引用符を追加したことでこのオブジェクトは文字列としてキャストされなくなったため、コンテンツは JSON ペイロードに直接追加されます。これが JSON トークン (つまり、文字列か JSON オブジェクトまたは配列) でない場合は、エラーになります。文字列としてキャストするには、上記の Salesforce の例のように引用符を追加するだけです。

その後、デザイナーによって、関数テンプレートが生成されます。これはインラインで作成できます。変数は、関数に渡す予定のコンテキストに基づいて事前に作成されます。

<!--Image references-->
[1]: ./media/app-service-logic-azure-functions/callFunction.png
[2]: ./media/app-service-logic-azure-functions/createFunction.png

<!---HONumber=AcomDC_0525_2016-->