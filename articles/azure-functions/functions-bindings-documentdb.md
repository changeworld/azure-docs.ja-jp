<properties
	pageTitle="Azure Functions における DocumentDB のバインド | Microsoft Azure"
	description="Azure Functions で Azure DocumentDB のバインドを使用する方法について説明します。"
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="08/22/2016"
	ms.author="chrande; glenga"/>

# Azure Functions における DocumentDB のバインド

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

この記事では、Azure Functions で Azure DocumentDB のバインドを構成したりコーディングしたりする方法について説明します。

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a id="docdbinput"></a>Azure DocumentDB 入力バインド

入力バインドでは、DocumentDB コレクションからドキュメントを読み込んでそれをバインドに直接渡すことができます。ドキュメント ID は、関数を呼び出したトリガーに基づいて決定することができます。C# 関数で、レコードに加えられた変更は、関数が正常に終了したときに、コレクションに自動的に再送信されます。

#### DocumentDB 入力バインドの function.json

*function.json* ファイルは、次のプロパティを提供します。

- `name`: ドキュメントの関数コードで使用される変数名。
- `type`: "documentdb" に設定する必要があります。
- `databaseName`: ドキュメントを含むデータベース。
- `collectionName`: ドキュメントを含むコレクション。
- `id`: 取得するドキュメントの ID。このプロパティは、"{queueTrigger}" と同類のバインドをサポートします。ここでは、ドキュメント ID としてキュー メッセージの文字列値を使用します。
- `connection`: この文字列は、DocumentDB アカウントのエンドポイントに設定されたアプリケーション設定である必要があります。[Integrate] \(統合) タブからアカウントを選択した場合、新しいアプリ設定が yourAccount\_DOCUMENTDB という名前形式で自動的に作成されます。アプリ設定を手動で作成する必要がある場合は、実際の接続文字列を次の形式にする必要があります: AccountEndpoint=<アカウントのエンドポイント>;AccountKey=<プライマリ アクセス キー>;。
- `direction : *"in"* に設定する必要があります。

*function.json* の例:
 
	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "id" : "{queueTrigger}",
	      "connection": "MyAccount_DOCUMENTDB",     
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### Azure DocumentDB 入力コード例 (C# キュー トリガー)
 
上記の function.json の例を使用して、DocumentDB 入力バインドはキュー メッセージと一致する ID を持つドキュメントを取得し、それを document パラメーターに渡します。そのドキュメントが見つからない場合、document パラメーターは null になります。関数の終了時に、ドキュメントは新しいテキスト値で更新されます。
 
	public static void Run(string myQueueItem, dynamic document)
	{   
	    document.text = "This has changed.";
	}

#### Azure DocumentDB 入力コード例 (F# キュー トリガー)

上記の function.json の例を使用して、DocumentDB 入力バインドはキュー メッセージと一致する ID を持つドキュメントを取得し、それを document パラメーターに渡します。そのドキュメントが見つからない場合、document パラメーターは null になります。関数の終了時に、ドキュメントは新しいテキスト値で更新されます。

	open FSharp.Interop.Dynamic
	let Run(myQueueItem: string, document: obj) =
	    document?text <- "This has changed."

次のように、NuGet を使用して `FSharp.Interop.Dynamic` および `Dynamitey` パッケージをパッケージ依存関係として指定する `project.json` ファイルが必要です。

	{
	  "frameworks": {
	    "net46": {
	      "dependencies": {
	        "Dynamitey": "1.0.2",
	        "FSharp.Interop.Dynamic": "3.0.0"
	      }
	    }
	  }
	}

このファイルは NuGet を使用して依存関係を取得し、それをスクリプト内で参照します。

#### Azure DocumentDB 入力コード例 (Node.js キュー トリガー)
 
上記の function.json の例を使用して、DocumentDB 入力バインドはキュー メッセージ文字列と一致する ID を持つドキュメントを取得し、それを `documentIn` バインド プロパティに渡します。Node.js 関数では、更新されたドキュメントは再びコレクションに送信されません。ただし、入力バインドを、更新をサポートする `documentOut` という名前の DocumentDB 出力バインドに直接渡すことができます。このコード例では、入力ドキュメントの text プロパティを更新し、出力ドキュメントとして設定します。
 
	module.exports = function (context, input) {   
	    context.bindings.documentOut = context.bindings.documentIn;
	    context.bindings.documentOut.text = "This was updated!";
	    context.done();
	};

## <a id="docdboutput"></a>Azure DocumentDB 出力バインド

関数を使用して、**Azure DocumentDB Document** 出力バインドを使って、JSON ドキュメントを Azure DocumentDB データベースに書き込むことができます。Azure DocumentDB の詳細については、「[DocumentDB の概要](../documentdb/documentdb-introduction.md)」および「[概要チュートリアル](../documentdb/documentdb-get-started.md)」を参照してください。

#### DocumentDB 出力バインドの function.json

function.json ファイルは、次のプロパティを提供します。

- `name`: 新しいドキュメントの関数コードで使用される変数名。
- `type`: *"documentdb"* に設定する必要があります。
- `databaseName`: 新しいドキュメントが作成されるコレクションを含むデータベース。
- `collectionName`: 新しいドキュメントが作成されるコレクション。
- `createIfNotExists`: これは、存在しない場合にコレクションを作成するかどうかを示すブール値です。既定値は *false* です。これは、新しいコレクションが予約済みのスループットで作成されるためです。これにより、価格に影響が及びます。詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/documentdb/)を参照してください。
- `connection`: この文字列は、DocumentDB アカウントのエンドポイントに設定された**アプリケーション設定**である必要があります。**[統合]** タブからアカウントを選択した場合、新しいアプリ設定が `yourAccount_DOCUMENTDB` という名前形式で自動的に作成されます。アプリ設定を手動で作成する必要がある場合は、実際の接続文字列を次の形式にする必要があります: `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;`。
- `direction` : *"out"* に設定する必要があります。
 
function.json の例:

	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "createIfNotExists": false,
	      "connection": "MyAccount_DOCUMENTDB",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}


#### Azure DocumentDB 出力コード例 (Node.js キュー トリガー)

	module.exports = function (context, input) {
	   
	    context.bindings.document = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	 
	    context.done();
	};

出力ドキュメント:

	{
	  "text": "I'm running in a Node function! Data: 'example queue data'",
	  "id": "01a817fe-f582-4839-b30c-fb32574ff13f"
	}
 

#### Azure DocumentDB 出力コード例 (F# キュー トリガー)

	open FSharp.Interop.Dynamic
	let Run(myQueueItem: string, document: obj) =
	    document?text <- (sprintf "I'm running in an F# function! %s" myQueueItem)

#### Azure DocumentDB 出力コード例 (C# キュー トリガー)


	using System;

	public static void Run(string myQueueItem, out object document, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
	   
	    document = new {
	        text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}


#### ファイル名を設定する Azure DocumentDB 出力コード例

関数でドキュメントの名前を設定するには、単に `id` 値を設定します。たとえば、従業員の JSON コンテンツが次のようなキューにドロップされた場合は、以下のようになります。

	{
	  "name" : "John Henry",
      "employeeId" : "123456",
	  "address" : "A town nearby"
	}

キュー トリガー関数で、次の C# コードを使用できます。
	
	#r "Newtonsoft.Json"
	
	using System;
	using Newtonsoft.Json;
	using Newtonsoft.Json.Linq;
	
	public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
	    
	    dynamic employee = JObject.Parse(myQueueItem);
	    
	    employeeDocument = new {
	        id = employee.name + "-" + employee.employeeId,
	        name = employee.name,
	        employeeId = employee.employeeId,
	        address = employee.address
	    };
	}

または、次のような同等の F# コードを使用できます。

	open FSharp.Interop.Dynamic
	open Newtonsoft.Json

	type Employee = {
	    id: string
	    name: string
	    employeeId: string
	    address: string
	}

	let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
	    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
	    let employee = JObject.Parse(myQueueItem)
	    employeeDocument <-
	        { id = sprintf "%s-%s" employee?name employee?employeeId
	          name = employee?name
	          employeeId = employee?id
	          address = employee?address }

出力例:

	{
	  "id": "John Henry-123456",
	  "name": "John Henry",
	  "employeeId": "123456",
	  "address": "A town nearby"
	}

## 次のステップ

[AZURE.INCLUDE [次のステップ](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->