---
title: "Azure Functions を使用したロジック アプリへのカスタム コードの追加 | Microsoft Docs"
description: "Azure Functions を使用した Azure Logic Apps のカスタム コードの作成"
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
ms.sourcegitcommit: c63dde728eaaf8237970e05cc524c6220b69a074
ms.openlocfilehash: 8b68f017a2c7a17603508438b0d4bd760bec4f78
ms.lasthandoff: 02/15/2017


---
# <a name="add-custom-code-to-azure-logic-apps-with-azure-functions"></a>Azure Functions を使用した Azure Logic Apps へのカスタム コードの追加

ロジック アプリで Azure Functions を使用することにより、C# または node.js のカスタム スニペットを実行できます。 
[Azure Functions](../azure-functions/functions-overview.md) は、Microsoft Azure でサーバーを使用しない計算を提供し、以下のタスクを実行するのに便利です。

* ロジック アプリでのフィールドの高度な書式設定や計算
* ワークフローでの計算の実行
* C# または node.js でサポートされる関数を使用したロジック アプリ機能の拡張

## <a name="create-functions-for-logic-apps"></a>ロジック アプリ用の関数を作成する

**汎用 Webhook - Node** または**汎用 Webhook - C#** テンプレートから、Azure Functions ポータルで関数を作成することをお勧めします。 そうすれば、ロジック アプリから `application/json` を受け取るテンプレートが自動的に生成されます。 これらのテンプレートから作成する関数は自動的に検出されて、Logic App デザイナーの **[自分のリージョンの Azure Functions]** に表示されます。

Azure Portal の関数の **[統合]** ウィンドウで、**[モード]** が **[Webhook]**、**[Webhook の種類]** が **[汎用 JSON]** に設定されていることがテンプレートで示される必要があります。 

Webhook 関数は、要求を受け取ると、それを `data` 変数経由でメソッドに渡します。 ペイロードのプロパティにアクセスするには、 `data.foo`のようなドット表記を使用します。 たとえば、DateTime 値を日付文字列に変換する単純な JavaScript 関数は次の例のようになります。

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## <a name="call-azure-functions-from-logic-apps"></a>ロジック アプリから Azure Functions を呼び出す

サブスクリプション内のコンテナーを一覧表示し、呼び出す関数を選択するには、Logic App デザイナーで、**[アクション]** メニューをクリックし、**[自分のリージョンの Azure Functions]** から選択します。

関数を選択すると、入力ペイロード オブジェクトの指定を求められます。 このオブジェクトは、ロジック アプリから関数に送信されるメッセージで、JSON オブジェクトである必要があります。 たとえば、Salesforce トリガーから **Last Modified** の日付を渡す場合、関数ペイロードは次の例のようになります。

![最終更新日][1]

## <a name="trigger-logic-apps-from-a-function"></a>関数からロジック アプリをトリガーする

関数内からロジック アプリをトリガーすることができます。 「[呼び出し可能なエンドポイントとしてのロジック アプリ](logic-apps-http-endpoint.md)」を参照してください。 手動トリガーのあるロジック アプリを作成してから、関数内から手動トリガーの URL に対する HTTP POST を生成し、ロジック アプリに送信するペイロードを含めます。

### <a name="create-a-function-from-logic-app-designer"></a>Logic App デザイナーから関数を作成する

Node.js webhook 関数は、デザイナーから作成することもできます。 最初に、 **[自分のリージョンの Azure Functions]** を選択し、関数用にコンテナーを選択します。 コンテナーがまだない場合は、 [Azure Functions ポータル](https://functions.azure.com/signin)から作成する必要があります。 **[新規作成]**を選択します。  

計算するデータに基づいてテンプレートを生成するには、関数に渡すコンテキスト オブジェクトを指定します。 このオブジェクトは JSON オブジェクトである必要があります。 たとえば、FTP アクションからファイル コンテンツを渡す場合、コンテキストのペイロードは次の例のようになります。

![コンテキスト ペイロード][2]

> [!NOTE]
> このオブジェクトは文字列としてキャストされなかったため、コンテンツは JSON ペイロードに直接追加されます。 ただし、オブジェクトが JSON トークン (つまり、文字列または JSON オブジェクトか配列) でない場合は、エラーが発生します。 文字列としてオブジェクトをキャストするには、この記事の最初の図に示されているように引用符を追加します。
> 

その後、デザイナーによって、インラインで作成できる関数テンプレートが生成されます。 変数は、関数に渡す予定のコンテキストに基づいて事前に作成されます。

<!--Image references-->
[1]: ./media/logic-apps-azure-functions/callfunction.png
[2]: ./media/logic-apps-azure-functions/createfunction.png

