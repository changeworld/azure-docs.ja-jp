---
title: Azure Functions の外部ファイル バインディング (試験段階)
description: Azure Functions の外部ファイル バインディングを使用する
services: functions
author: craigshoemaker
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: cshoe
ms.openlocfilehash: 39036f17b8ceafebbe3660f2074e2a6c84c4df03
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2018
ms.locfileid: "50248695"
---
# <a name="azure-functions-external-file-bindings-experimental"></a>Azure Functions の外部ファイル バインディング (試験段階)
この記事では、Azure Functions でさまざまな SaaS プロバイダー (Dropbox、Google ドライブなど) のファイルを操作する方法について説明します。 Azure Functions は、外部ファイルのトリガー、入力、および出力のバインディングをサポートしています。 これらのバインディングでは、SaaS プロバイダーへの API 接続を作成するか、または Function App のリソース グループにある既存の API 接続を使用します。

> [!IMPORTANT]
> 外部ファイル バインディングは試験段階であり、一般公開 (GA) されない可能性があります。 外部ファイル バインディングは Azure Functions 1.x にのみ含まれており、Azure Functions 2.x に追加される予定はありません。 SaaS プロバイダーのデータにアクセスする必要があるシナリオでは、[関数を呼び出すロジック アプリケーション](functions-twitter-email.md)の使用を検討してください。 [Logic Apps ファイル システム コネクタ](../logic-apps/logic-apps-using-file-connector.md)に関するページを参照してください。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="available-file-connections"></a>使用できるファイル接続

|コネクタ|トリガー|入力|出力|
|:-----|:---:|:---:|:---:|
|[Box](https://www.box.com)|○|○|○
|[Dropbox](https://www.dropbox.com)|○|○|○
|[FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)|○|○|○
|[OneDrive](https://onedrive.live.com)|○|○|○
|[OneDrive for Business](https://onedrive.live.com/about/business/)|○|○|○
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|○|○|○
|[Google ドライブ](https://www.google.com/drive/)||○|○|

> [!NOTE]
> 外部ファイル接続を [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list) で使用することもできます。

## <a name="trigger"></a>トリガー

外部ファイル トリガーを使用すると、リモート フォルダーを監視して、変更が検出されたときに関数コードを実行できます。

## <a name="trigger---example"></a>トリガー - 例

言語固有の例をご覧ください。

* [C# スクリプト](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-script-example"></a>トリガー - C# スクリプトの例

次の例は、*function.json* ファイルの外部ファイル トリガー バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数は、監視対象のフォルダーに追加される各ファイルの内容を記録します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

C# スクリプト コードを次に示します。

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
}
```

### <a name="trigger---javascript-example"></a>トリガー - JavaScript の例

次の例は、*function.json* ファイルの外部ファイル トリガー バインドと、そのバインドが使用される [JavaScript スクリプト関数](functions-reference-node.md)を示しています。 この関数は、監視対象のフォルダーに追加される各ファイルの内容を記録します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

JavaScript コードを次に示します。

```javascript
module.exports = function(context) {
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

## <a name="trigger---configuration"></a>トリガー - 構成

次の表は、*function.json* ファイルで設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 説明|
|---------|---------|----------------------|
|**type** | `apiHubFileTrigger` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction** | `in` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 関数コード内のイベント項目を表す変数の名前。 | 
|**connection**| 接続文字列が格納されるアプリ設定を指定します。 このアプリ設定は、Azure Portal の [統合] の UI で接続を追加すると自動的に作成されます。|
|**path** | 監視するフォルダー。必要に応じて名前パターンも指定できます。|

### <a name="name-patterns"></a>名前のパターン

ファイル名のパターンは、`path` プロパティで指定できます。 参照されるフォルダーが SaaS プロバイダーに存在している必要があります。

次に例を示します。

```json
"path": "input/original-{name}",
```

このパスは、*input* フォルダーの *original-File1.txt* という名前のファイルを探し、関数コード内の `name` 変数の値は `File1.txt` になります。

別の例:

```json
"path": "input/{filename}.{fileextension}",
```

このパスは、*original-File1.txt* という名前のファイルを探し、関数コード内の `filename` 変数および `fileextension` 変数の値は *original-File1* と *txt* になります。

ファイルの種類を制限するには、ファイル拡張子に固定値を使用します。 例: 

```json
"path": "samples/{name}.png",
```

この場合、*samples* フォルダー内の *.png* ファイルのみが関数をトリガーします。

中かっこは、名前のパターン内の特殊文字です。 名前に中かっこがあるファイル名を指定する場合は、中かっこを二重にします。
例: 

```json
"path": "images/{{20140101}}-{name}",
```

このパスは、*images* フォルダーの *{20140101}-soundfile.mp3* という名前のファイルを探し、関数コード内の `name` 変数の値は *soundfile.mp3* になります。

## <a name="trigger---usage"></a>トリガー - 使用方法

C# 関数の場合、入力ファイル データにバインドするには、関数のシグネチャで `<T> <name>` などの名前付きパラメーターを使用します。
`T` はデータの逆シリアル化先のデータ型です。`paramName` は[トリガー JSON](#trigger) で指定した名前です。 Node.js 関数の場合、`context.bindings.<name>` を使用して入力ファイル データにアクセスします。

ファイルを、次のいずれかの型に逆シリアル化できます。

* 任意の [Object](https://msdn.microsoft.com/library/system.object.aspx) - JSON でシリアル化されたファイル データに有効です。
  カスタム入力型を宣言した場合 (例: `FooType`)、Azure Functions は、指定した型に JSON データを逆シリアル化しようとします。
* 文字列 - テキスト ファイル データに有効です。

また、C# 関数では、次の型のどれにでもバインドすることができ、Functions ランタイムはその型を使用してファイル データを逆シリアル化しようとします。

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

<!--- ## Trigger - file receipts
The Azure Functions runtime makes sure that no external file trigger function gets called more than once for the same new or updated file.
It does so by maintaining *file receipts* to determine if a given file version has been processed.

File receipts are stored in a folder named *azure-webjobs-hosts* in the Azure storage account for your function app
(specified by the `AzureWebJobsStorage` app setting). A file receipt has the following information:

* The triggered function ("*&lt;function app name>*.Functions.*&lt;function name>*", for example: "functionsf74b96f7.Functions.CopyFile")
* The folder name
* The file type ("BlockFile" or "PageFile")
* The file name
* The ETag (a file version identifier, for example: "0x8D1DC6E70A277EF")

To force reprocessing of a file, delete the file receipt for that file from the *azure-webjobs-hosts* folder manually.
--->

## <a name="trigger---poison-files"></a>トリガー - 有害ファイル

外部ファイル トリガー関数が失敗すると、Azure Functions はその関数を特定のファイルに対して既定で最大 5 回再試行します (これは最初の試行を含む数字です)。
試行が 5 回とも失敗した場合、Functions は *webjobs-apihubtrigger-poison* という名前の Storage キューにメッセージを追加します。 有害なファイルのキュー メッセージは次のプロパティを持つ JSON オブジェクトです。

* FunctionId (形式: *&lt;Function App 名>*.Functions.*&lt;関数名>*)
* FileType
* FolderName
* FileName
* ETag (ファイルのバージョン識別子。たとえば、"0x8D1DC6E70A277EF")

## <a name="input"></a>入力

Azure 外部ファイル入力バインディングにより、外部フォルダー内のファイルを関数で使用できるようになります。

## <a name="input---example"></a>入力 - 例

言語固有の例をご覧ください。

* [C# スクリプト](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-script-example"></a>入力 - C# スクリプトの例

次の例は、*function.json* ファイルの外部ファイルの入力および出力バインディングと、そのバインディングを使用する [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数は、入力ファイルを出力ファイルにコピーします。

*function.json* ファイルのバインディング データを次に示します。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C# スクリプト コードを次に示します。

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

### <a name="input---javascript-example"></a>入力 - JavaScript の例

次の例は、*function.json* ファイルの外部ファイルの入力および出力バインディングと、そのバインディングを使用する [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は、入力ファイルを出力ファイルにコピーします。

*function.json* ファイルのバインディング データを次に示します。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

JavaScript コードを次に示します。

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

## <a name="input---configuration"></a>入力 - 構成

次の表は、*function.json* ファイルで設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 説明|
|---------|---------|----------------------|
|**type** | `apiHubFile` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction** | `in` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 関数コード内のイベント項目を表す変数の名前。 | 
|**connection**| 接続文字列が格納されるアプリ設定を指定します。 このアプリ設定は、Azure Portal の [統合] の UI で接続を追加すると自動的に作成されます。|
|**path** | フォルダー名とファイル名を含める必要があります。 たとえば、関数内に[キュー トリガー](functions-bindings-storage-queue.md)がある場合は、`"path": "samples-workitems/{queueTrigger}"` を使用して、トリガー メッセージで指定されたファイル名と同じ名前を持つ `samples-workitems` フォルダー内のファイルをポイントできます。   

## <a name="input---usage"></a>入力 - 使用方法

C# 関数の場合、入力ファイル データにバインドするには、関数のシグネチャで `<T> <name>` などの名前付きパラメーターを使用します。 `T` はデータの逆シリアル化先のデータ型です。`name` は入力バインドで指定した名前です。 Node.js 関数の場合、`context.bindings.<name>` を使用して入力ファイル データにアクセスします。

ファイルを、次のいずれかの型に逆シリアル化できます。

* 任意の [Object](https://msdn.microsoft.com/library/system.object.aspx) - JSON でシリアル化されたファイル データに有効です。
  カスタム入力型を宣言した場合 (例: `InputType`)、Azure Functions は、指定した型に JSON データを逆シリアル化しようとします。
* 文字列 - テキスト ファイル データに有効です。

また、C# 関数では、次の型のどれにでもバインドすることができ、Functions ランタイムはその型を使用してファイル データを逆シリアル化しようとします。

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

## <a name="output"></a>出力

Azure 外部ファイル出力バインディングにより、関数で外部フォルダーにファイルを書き込むことが可能になります。

## <a name="output---example"></a>出力 - 例

[入力バインドの例](#input---example)のセクションを参照してください。

## <a name="output---configuration"></a>出力 - 構成

次の表は、*function.json* ファイルで設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 説明|
|---------|---------|----------------------|
|**type** | `apiHubFile` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction** | `out` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 関数コード内のイベント項目を表す変数の名前。 | 
|**connection**| 接続文字列が格納されるアプリ設定を指定します。 このアプリ設定は、Azure Portal の [統合] の UI で接続を追加すると自動的に作成されます。|
|**path** | フォルダー名とファイル名を含める必要があります。 たとえば、関数内に[キュー トリガー](functions-bindings-storage-queue.md)がある場合は、`"path": "samples-workitems/{queueTrigger}"` を使用して、トリガー メッセージで指定されたファイル名と同じ名前を持つ `samples-workitems` フォルダー内のファイルをポイントできます。   

## <a name="output---usage"></a>出力 - 使用方法

C# 関数の場合、出力ファイルにバインドするには、関数のシグネチャで `out <T> <name>` などの名前付き `out` パラメーターを使用します。`T` はデータのシリアル化先のデータ型です。`name` は出力バインドで指定した名前です。 Node.js 関数の場合、`context.bindings.<name>` を使用して出力ファイルにアクセスします。

出力ファイルには、次のいずれかの型で書き込むことができます。

* 任意の [Object](https://msdn.microsoft.com/library/system.object.aspx) - JSON でのシリアル化に有効です。
  カスタム出力型を宣言した場合 (例: `out OutputType paramName`)、Azure Functions は、オブジェクトを JSON にシリアル化しようとします。 関数の終了時に出力パラメーターが null の場合、Functions ランタイムはファイルを null オブジェクトとして作成します。
* 文字列 - (`out string paramName`) テキスト ファイル データに有効です。 Functions ランタイムは、関数の終了時に文字列パラメーターが null でない場合にのみファイルを作成します。

C# 関数の場合は、次の型のいずれかに出力することもできます。

* `TextWriter`
* `Stream`
* `CloudFileStream`
* `ICloudFile`
* `CloudBlockFile`
* `CloudPageFile`

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)
