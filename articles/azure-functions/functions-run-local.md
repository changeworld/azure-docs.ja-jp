---
title: "ローカルでの Azure Functions の開発と実行 | Microsoft Docs"
description: "Azure 関数を Azure Functions で実行する前に、ローカル コンピューターでコーディングしてテストする方法について説明します。"
services: functions
documentationcenter: na
author: lindydonna
manager: erikre
editor: 
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/08/2016
ms.author: donnam
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 478c2ffbb0bfe5b1595bc6ea1bebb2144aebc728
ms.contentlocale: ja-jp
ms.lasthandoff: 06/09/2017


---
# <a name="code-and-test-azure-functions-locally"></a>Azure Functions をローカルでコーディングしてテストする

任意のコード エディターとローカルの開発ツールを使用して、Azure Functions ランタイムをローカルで実行できます。 Azure でイベントをトリガーし、C# 関数および JavaScript 関数をデバッグします。

まず、npm から [Azure Functions Core Tools] をインストールします。 Azure Functions Core Tools は、ローカル バージョンの Azure Functions ランタイムで、ローカルの Windows コンピューターで実行できます。 エミュレーターまたはシミュレーターではありません。 Azure で実行されているランタイムと同じです。

[Azure Functions Core Tools] で追加されるコマンドのエイリアスを次に示します。
- `func`
- `azfun`
- `azurefunctions`

Azure Functions Core Tools は[オープン ソースであり、GitHub でホストされています](https://github.com/azure/azure-functions-cli)。 バグまたは機能要求を提出するには、[GitHub の問題をオープン](https://github.com/azure/azure-functions-cli/issues)してください。

## <a name="create-a-local-functions-project"></a>ローカル関数プロジェクトを作成する

ローカルで実行中は、Functions プロジェクトが、host.json ファイルと local.settings.json ファイルが含まれるディレクトリです。 このディレクトリは、Azure の関数アプリに相当します。 Azure Functions のフォルダー構造の詳細については、[Azure Functions の開発者向けガイド](functions-reference.md#folder-structure)を参照してください。

コマンド プロンプトで、次のコマンドを実行します。

```
func init MyFunctionProj
```

出力は次のテキストのようになります。

```
Writing .gitignore
Writing host.json
Writing local.settings.json
Created launch.json
Initialized empty Git repository in D:/Code/Playground/MyFunctionProj/.git/
```

Git リポジトリの作成を除外するには、`--no-source-control [-n]` オプションを使用します。

<a name="local-settings"></a>

## <a name="local-settings-file"></a>ローカル設定ファイル

local.settings.json ファイルには、アプリの設定、接続文字列、および Azure Functions Core Tools の設定が格納されます。 その構造を次に示します。

```json
{
  "IsEncrypted": false,   // If set to true, all values are encrypted by using a local machine key. Use with "func settings" commands.
  "Values": {
    "AzureWebJobsStorage": "<connection string>",   // This is required for all triggers except HTTP.
    "AzureWebJobsDashboard": "<connection string>", // Optional, controls whether to log to the Monitor tab in the portal.
  },
  "Host": {
    "LocalHttpPort": 7071, // If specified, this is the default port for "host start" and "run". Can be overridden by using the --port command-line option.
    "CORS": "*"            // Origins to allow in the CORS setting.
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```

**Values** コレクションでアプリ設定を指定します。 この設定は、環境変数として読み取ることができます。 C# では、`System.Environment.GetEnvironmentVariable` または `ConfigurationManager.AppSettings` を使用します。 JavaScript では、`process.env` を使用します。 システム環境変数と同じ設定が指定されている場合、その設定は、local.settings.json の値に優先します。

アプリ設定 **AzureWebJobsStorage** は、HTTP 以外のすべてのトリガーについて、Azure Functions ランタイムで必要とされる特別な設定です。 内部的には、ランタイムによりキューが作成され、このストレージ アカウントのトリガーが管理されます。 **AzureWebJobsStorage** に値が指定されていない場合、HTTP 以外のトリガーを使用すると、次のメッセージが表示されます。

"*local.settings.json に AzureWebJobsStorage の値がありません。これは HTTP 以外のすべてのトリガーに必要です。"func azure functionapp fetch-app-settings <functionAppName>" を実行するか、local.settings.json で接続文字列を指定することができます*"

> [!NOTE]
> 接続文字列 "AzureWebJobsStorage": "UseDevelopmentStorage=true" を介して、ローカル ストレージ エミュレーターを使用できます。 ただし、Azure Storage サービスと比べると、動作が異なる可能性があります。

次の設定により、ローカルの Functions ホストがカスタマイズされます。
- `LocalHttpPort`」を参照してください。 `func host start` `func run` に対して使用する既定のポート。 `--port` コマンド ライン オプションは、この値に優先します。
- `CORS`」を参照してください。 CORS が許可されているオリジン (スペースなしのコンマ区切りのリストで指定)。 すべてを許可するには "*" を使用します。

`ConnectionStrings` オブジェクトで接続文字列を指定できます。 接続文字列は、プロバイダー名 **System.Data.SqlClient** を含む環境に追加されます。

トリガーとバインディングのほとんどについて、local.settings.json に環境変数またはアプリ設定の名前である **Connection** プロパティが含まれます。 アプリ設定の値が見つからない場合は、次のメッセージが表示されます。

"*警告: "BlobTriggerCSharp\function.json" の "blobTrigger" で設定されている "connection" プロパティと一致する local.settings.json に、"MyStorageConnection" という名前の値がありません。"func azure functionapp fetch-app-settings <functionAppName>" を実行するか、local.settings.json で接続文字列を指定することができます*"

local.settings.json ファイルは、Azure Functions Core Tools でのみ使用されます。 Azure でアプリ設定と接続文字列を設定するには、**[アプリケーション設定]** ブレードを使用します。

### <a name="configure-app-settings"></a>アプリケーションの設定の構成

接続文字列の値を設定するには、次のいずれかの操作を行います。
- [Azure ストレージ エクスプローラー](http://storageexplorer.com/)で、接続文字列を手動で入力します。
- **func azure functionapp fetch-app-settings \<FunctionAppName\>** を使用します。 **azure login** が必要です。
- **func azure functionapp storage fetch-connection-string \<StorageAccountName\>** を使用します。 **azure login** が必要です。

## <a name="create-a-function"></a>関数を作成する

関数を作成するには、`func new` を実行します。 このコマンドには、次の省略可能な引数があります。

- `--language [-l]`」を参照してください。 テンプレート プログラミング言語。C#、F#、JavaScript など。
- `--template [-t]`」を参照してください。 テンプレート名。
- `--name [-n]`」を参照してください。 関数名。

たとえば、JavaScript HTTP トリガーを作成するには、次を実行します。

```
func new --language JavaScript --template HttpTrigger --name MyHttpTrigger
```

キューによってトリガーされる関数を作成するには、次を実行します。

```
func new --language JavaScript --template QueueTrigger --name QueueTriggerJS
```

## <a name="run-functions-locally"></a>関数をローカルで実行する

Functions プロジェクトを実行するには、Functions ホストを実行します。 ホストによって、プロジェクトのすべての関数に対するトリガーが有効になります。

```
func host start
```

`func host start` では次のオプションを使用できます。

- `--port [-p]`」を参照してください。 ローカル ポート。このポートでリッスンします。 既定値: 7071。
- `--debug <type>`」を参照してください。 オプションは VSCode と VS です。
- `--cors`」を参照してください。 CORS オリジンのコンマ区切りのリスト (スペースなし)。
- `--nodeDebugPort [-n]`」を参照してください。 使用するノード デバッガーのポート。 既定値: launch.json または 5858 の値。
- `--debugLevel [-d]`」を参照してください。 コンソール トレース レベル (オフ、詳細、情報、警告、またはエラー)。 既定値: 情報。
- `--timeout [-t]`」を参照してください。 Functions ホスト開始のタイムアウト (秒単位)。 既定値: 20 秒。
- `--useHttps`」を参照してください。 https://localhost:{port} rather than to http://localhost:{port} にバインドします。 既定では、このオプションにより、信頼された証明書がコンピューターに作成されます。
- `--pause-on-error`」を参照してください。 プロセスを終了する前に、追加入力を一時停止します。 統合開発環境 (IDE) から Azure Functions Core Tools を起動する場合に役立ちます。

Functions ホストの起動時、HTTP によってトリガーされる関数の URL が出力されます。

```
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug"></a>デバッグ

デバッガーをアタッチするには、`--debug` 引数を渡します。 JavaScript 関数をデバッグするには、Visual Studio Code を使用します。 C# 関数については、Visual Studio を使用します。

C# 関数をデバッグするには、`--debug vs` を使用します。 または、[Azure Functions Visual Studio 2017 Tools](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/) を使用します。 

ホストを起動して、JavaScript のデバッグを設定するには、次を実行します。

```
func host start --debug vscode
```

その後、Visual Studio Code の **[デバッグ]** ビューで、**[Attach to Azure Functions]\(Azure Functions にアタッチ\)** を選択します。 ブレークポイントをアタッチし、変数の調査して、コードをステップ実行できます。

![Visual Studio Code での JavaScript デバッグ](./media/functions-run-local/vscode-javascript-debugging.png)

### <a name="call-a-function-by-using-test-data"></a>テスト データを使用して関数を呼び出す

`func run <FunctionName>` を使用して、関数を直接呼び出すこともできます。 このコマンドは、関数の入力データを提供できる、Azure Portal の **[テスト]** タブと似ています。 このコマンドにより、Functions ホスト全体が起動します。

`func run` では次のオプションを使用できます。

- `--content [-c]`」を参照してください。 インライン コンテンツ。
- `--debug [-d]`」を参照してください。 関数を実行する前に、デバッガーを、ホスト プロセスにアタッチします。
- `--timeout [-t]`」を参照してください。 Functions ホストの準備が完了するまでの待機時間 (秒単位)。
- `--file [-f]`」を参照してください。 コンテンツとして使用するファイル名。
- `--no-interactive`」を参照してください。 入力を促しません。 自動シナリオで便利です。

たとえば、HTTP によってトリガーされる関数を呼び出して、コンテンツ本文を渡すには、次のコマンドを実行します。

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish-a-function-app"></a>関数アプリの発行

Azure で Functions プロジェクトを関数アプリに発行するには、`publish` コマンドを使用します。

```
func azure functionapp publish <FunctionAppName>
```

以下のオプションを使用できます。

- `--publish-local-settings [-i]`」を参照してください。  local.settings.json の設定を Azure に発行し、設定が既に存在する場合は上書きを促します。
- `--overwrite-settings [-y]`」を参照してください。 `-i` で使用する必要があります。 値が異なる場合は、Azure の AppSettings をローカル値で上書きします。 既定値は prompt です。

`publish` コマンドは、Functions プロジェクト ディレクトリのコンテンツをアップロードします。 ファイルをローカルで削除しても、このコマンドによって、そのファイルが Azure から削除されることはありません。 こうしたファイルを削除するには、Azure Functions ポータルで Kudu を使用します。 Kudu を起動するには、Azure Functions ポータルで、**[プラットフォーム機能]** > **[高度なツール (Kudu)]** を選択します。 


<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
