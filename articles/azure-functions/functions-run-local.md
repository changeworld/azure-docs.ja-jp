---
title: ローカルでの Azure Functions の開発と実行 | Microsoft Docs
description: Azure 関数を Azure Functions で実行する前に、ローカル コンピューターでコーディングしてテストする方法について説明します。
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 10/12/2017
ms.author: glenga
ms.openlocfilehash: f3278c064a01e3dea1d7a629b4a7b2e846a71208
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2018
---
# <a name="code-and-test-azure-functions-locally"></a>Azure Functions をローカルでコーディングしてテストする

[Azure Portal] では Azure Functions の開発用およびテスト用ツールの完全なセットが提供されていますが、多くの開発者はローカルでの開発を選択します。 Azure Functions では、お気に入りのコード エディターとローカル開発ツールを使用して、ローカル コンピューターで簡単に関数を開発し、テストできます。 独自の関数を使用して Azure でイベントをトリガーし、ローカル コンピューターで C# 関数や JavaScript 関数をデバッグできます。 

Visual Studio C# の開発者は、Azure Functions を [Visual Studio 2017 に統合](functions-develop-vs.md)することもできます。

>[!IMPORTANT]  
> 同じ関数アプリにローカル開発とポータル開発を混在させないでください。 ローカル プロジェクトから関数を発行するときは、ポータルではプロジェクト コードを管理または変更しないようにしてください。

## <a name="install-the-azure-functions-core-tools"></a>Azure Functions Core Tools のインストール

[Azure Functions Core Tools] は、ローカル バージョンの Azure Functions ランタイムで、ローカルの開発コンピューターで実行できます。 エミュレーターまたはシミュレーターではありません。 Azure で Functions を実行するランタイムと同じです。 Azure Functions Core Tools には、次の 2 つのバージョンがあります。

+ [バージョン 1.x](#v1): ランタイムのバージョン 1.x をサポートします。 このバージョンは Windows コンピューター上でのみサポートされ、[npm パッケージ](https://docs.npmjs.com/getting-started/what-is-npm)からインストールされます。
+ [バージョン 2.x](#v2): ランタイムのバージョン 2.x をサポートします。 このバージョンは、[Windows](#windows-npm)、[macOS](#brew)、および [Linux](#linux) に対応しています。 インストールには、プラットフォーム固有のパッケージ マネージャーまたは npm を使用します。 

### <a name="v1"></a>バージョン 1.x

元のバージョンのツールは、Functions 1.x ランタイムを使用します。 このバージョンは .NET Framework (4.7.1) を使用し、Windows コンピューターでのみサポートされます。 バージョン 1.x のツールをインストールする前に、npm を含む [NodeJS をインストール](https://docs.npmjs.com/getting-started/installing-node)する必要があります。

次のコマンドを使用して、バージョン 1.x ツールをインストールします。

```bash
npm install -g azure-functions-core-tools
```

### <a name="v2"></a>バージョン 2.x

>[!NOTE]
> Azure Functions ランタイム 2.0 はプレビュー段階であり、現在のところ、Azure Functions のすべての機能はサポートされていません。 詳細については、[Azure Functions のバージョン](functions-versions.md)に関するページを参照してください。 

バージョン 2.x のツールは、.NET Core 上に構築されている Azure Functions ランタイム 2.x を使用します。 このバージョンは、[Windows](#windows-npm)、[macOS](#brew)、および [Linux](#linux)など、.NET Core 2.x が対応しているすべてのプラットフォームでサポートされます。

#### <a name="windows-npm"></a>Windows

次の手順では、npm を使用して Windows 上に Core Tools をインストールします。 また、[Chocolatey](https://chocolatey.org/) を使用することもできます。 詳細については、[Core Tools の readme ](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows)に関するページを参照してください。

1. [Windows 用 .NET Core 2.0 ](https://www.microsoft.com/net/download/windows)をインストールします。

2. [Node.js]をインストールします。これには、npm が同梱されています。 2x バージョンのツールの場合、Node.js 8.5 以降のバージョンのみがサポートされています。

3. 次のコマンドを使って、Core Tools のパッケージをインストールします。

  ```bash
  npm install -g azure-functions-core-tools@core
  ```

#### <a name="brew"></a>Homebrew による MacOS

次の手順では、Homebrew を使用して macOS 上に Core Tools をインストールします。

1. [macOS 用 .NET Core 2.0 ](https://www.microsoft.com/net/download/macos)をインストールします。

1. まだインストールしていない場合は、[Homebrew](https://brew.sh/) をインストールします。

2. 次のコマンドを使って、Core Tools のパッケージをインストールします。

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools 
    ```

#### <a name="linux"></a>APT による Linux (Ubuntu/Debian)

次の手順では [APT ](https://wiki.debian.org/Apt)を使用して、Ubuntu/Debian Linux ディストリビューションに Core Tools をインストールします。 他の Linux ディストリビューションについては、[Core Tools の readme](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux) に関するページを参照してください。

1. [Linux 用 .NET Core 2.0 ](https://www.microsoft.com/net/download/linux)をインストールします。

1. 次のコマンドを使って、Microsoft プロダクト キーを信頼済みとして登録します。

  ```bash
  curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
  sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
  ```

2.  次のコマンドの `<version>` を以下の表の適切なバージョン名に変更して、パッケージ フィードを設定します。

  ```bash
  sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-<version>-prod <version> main" > /etc/apt/sources.list.d/dotnetdev.list'
  sudo apt-get update
  ```

  | Linux ディストリビューション | `<version>` |
  | --------------- | ----------- |
  | Ubuntu 17.10    | `artful`    |
  | Ubuntu 17.04    | `zesty`     |
  | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

3. 次のコマンドを使って、Core Tools のパッケージをインストールします。

  ```bash
  sudo apt-get install azure-functions-core-tools
  ```

## <a name="run-azure-functions-core-tools"></a>Azure Functions Core Tools の実行
 
Azure Functions Core Tools で追加されるコマンドのエイリアスを次に示します。
* **func**
* **azfun**
* **azurefunctions**

これらすべてのエイリアスは、`func` が例に示されている場所で使用できます。

```
func init MyFunctionProj
```

## <a name="create-a-local-functions-project"></a>ローカル関数プロジェクトを作成する

ローカルで実行中の場合、Functions プロジェクトは、[host.json](functions-host-json.md) ファイルと [local.settings.json](#local-settings-file) ファイルが含まれるディレクトリです。 このディレクトリは、Azure の関数アプリに相当します。 Azure Functions のフォルダー構造の詳細については、[Azure Functions の開発者向けガイド](functions-reference.md#folder-structure)を参照してください。

ターミナル ウィンドウまたはコマンド プロンプトで、次のコマンドを実行してプロジェクトおよびローカルの Git リポジトリを作成します。

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

ローカル Git リポジトリを使用せずにプロジェクトを作成する場合は、`--no-source-control [-n]` オプションを使用します。

## <a name="register-extensions"></a>拡張機能を登録する

Azure Functions ランタイムのバージョン 2.x では、関数アプリで使用する[バインディング拡張機能](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/README.md)を明示的に登録する必要があります。 

[!INCLUDE [Register extensions](../../includes/functions-core-tools-install-extension.md)]

詳しくは、「[Azure Functions でのトリガーとバインドの概念](functions-triggers-bindings.md#register-binding-extensions)」をご覧ください。

## <a name="local-settings-file"></a>ローカル設定ファイル

local.settings.json ファイルには、アプリの設定、接続文字列、および Azure Functions Core Tools の設定が格納されます。 その構造を次に示します。

```json
{
  "IsEncrypted": false,   
  "Values": {
    "AzureWebJobsStorage": "<connection string>", 
    "AzureWebJobsDashboard": "<connection string>" 
  },
  "Host": {
    "LocalHttpPort": 7071, 
    "CORS": "*" 
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```
| Setting      | [説明]                            |
| ------------ | -------------------------------------- |
| **IsEncrypted** | **true** に設定すると、すべての値がローカル コンピューターのキーを使用して暗号化されます。 `func settings` コマンドと共に使用されます。 既定値は **false** です。 |
| **Values** | ローカルで実行するときに使用されるアプリケーション設定のコレクションです。 **AzureWebJobsStorage** と **AzureWebJobsDashboard** は例です。完全な一覧については、[アプリの設定リファレンス](functions-app-settings.md)に関するページを参照してください。 多くのトリガーおよびバインドには、BLOB Storage トリガーの **Connection** など、アプリ設定を参照するプロパティがあります。 このようなプロパティでは、**Values** 配列にアプリケーション設定を定義する必要があります。 また、このことは、`%AppSettingName%` のようにパーセント記号で値をラッピングしてアプリ設定名に設定される、任意のバインド プロパティにも該当します。 |
| **Host** | このセクションの設定により、ローカルで実行時の Functions ホスト プロセスをカスタマイズできます。 | 
| **LocalHttpPort** | ローカルの Functions ホストの実行時に使用される既定のポートを設定します (`func host start`と`func run`)。 `--port` コマンド ライン オプションは、この値に優先します。 |
| **CORS** | [クロス オリジン リソース共有 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) で許可されるオリジンを定義します。 スペースなしのコンマ区切りのリストでオリジンを指定します。 ワイルドカード値 (\*) がサポートされており、これによって任意のオリジンからの要求を許可できます。 |
| **ConnectionStrings** | 関数のデータベース接続文字列が含まれています。 このオブジェクト内の接続文字列は、**System.Data.SqlClient** のプロバイダーの種類と共に、環境に追加されます。  | 

この設定は、コードの中で環境変数として読み込むこともできます。 詳細については、以下の言語固有のリファレンス トピックの「環境変数」のセクションを参照してください。

+ [C# プリコンパイル済み](functions-dotnet-class-library.md#environment-variables)
+ [C# スクリプト (.csx)](functions-reference-csharp.md#environment-variables)
+ [F#](functions-reference-fsharp.md#environment-variables)
+ [Java](functions-reference-java.md#environment-variables) 
+ [JavaScript](functions-reference-node.md#environment-variables)

local.settings.json ファイル内の設定は、ローカルで実行されている Functions ツールでのみ使用されます。 既定では、プロジェクトが Azure に発行されても、これらの設定は自動的に移行されません。 [発行する際](#publish)に `--publish-local-settings` スイッチを使用して、これらの設定が Azure 内の関数アプリに追加されていることを確認してください。

有効なストレージ接続文字列が **AzureWebJobsStorage** に設定されていない場合は、次のエラー メッセージが表示されます。  

>local.settings.json に AzureWebJobsStorage の値がありません。 これは HTTP 以外のすべてのトリガーに必要です。 "func azure functionapp fetch-app-settings <functionAppName>" を実行するか、local.settings.json で接続文字列を指定することができます。
  
[!INCLUDE [Note to not use local storage](../../includes/functions-local-settings-note.md)]

### <a name="configure-app-settings"></a>アプリケーションの設定の構成

接続文字列の値を設定するには、次のいずれかのオプションを構成します。
* [Azure Storage Explorer](http://storageexplorer.com/) で、接続文字列を入力します。
* 次のコマンドのいずれかを使用します。

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```
    どちらのコマンドも、最初に Azure にサインインする必要があります。

<a name="create-func"></a>
## <a name="create-a-function"></a>関数を作成する

関数を作成するには、次のコマンドを実行します。

```
func new
``` 
`func new` では、次の省略可能な引数がサポートされています。

| 引数     | [説明]                            |
| ------------ | -------------------------------------- |
| **`--language -l`** | テンプレート プログラミング言語。C#、F#、JavaScript など。 |
| **`--template -t`** | テンプレート名。 |
| **`--name -n`** | 関数名。 |

たとえば、JavaScript HTTP トリガーを作成するには、次を実行します。

```
func new --language JavaScript --template HttpTrigger --name MyHttpTrigger
```

キューによってトリガーされる関数を作成するには、次を実行します。

```
func new --language JavaScript --template QueueTrigger --name QueueTriggerJS
```
<a name="start"></a>
## <a name="run-functions-locally"></a>関数をローカルで実行する

Functions プロジェクトを実行するには、Functions ホストを実行します。 ホストによって、プロジェクトのすべての関数に対するトリガーが有効になります。

```
func host start
```

`func host start` では、次のオプションがサポートされています。

| オプション     | [説明]                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | ローカル ポート。このポートでリッスンします。 既定値: 7071。 |
| **`--debug <type>`** | オプションは `VSCode` と `VS` です。 |
| **`--cors`** | CORS オリジンのコンマ区切りのリスト (スペースなし)。 |
| **`--nodeDebugPort -n`** | 使用するノード デバッガーのポート。 既定値: launch.json または 5858 の値。 |
| **`--debugLevel -d`** | コンソール トレース レベル (オフ、詳細、情報、警告、またはエラー)。 既定値: 情報。|
| **`--timeout -t`** | Functions ホスト開始のタイムアウト (秒単位)。 既定値: 20 秒。|
| **`--useHttps`** | http://localhost:{port} ではなく https://localhost:{port} にバインドします。 既定では、このオプションにより、信頼された証明書がコンピューターに作成されます。|
| **`--pause-on-error`** | プロセスを終了する前に、追加入力を一時停止します。 統合開発環境 (IDE) から Azure Functions Core Tools を起動する場合に役立ちます。|

Functions ホストの起動時、HTTP によってトリガーされる関数の URL が出力されます。

```
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug-in-vs-code-or-visual-studio"></a>VS Code または Visual Studio でのデバッグ

デバッガーをアタッチするには、`--debug` 引数を渡します。 JavaScript 関数をデバッグするには、Visual Studio Code を使用します。 C# 関数については、Visual Studio を使用します。

C# 関数をデバッグするには、`--debug vs` を使用します。 または、[Azure Functions Visual Studio 2017 Tools](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/) を使用することもできます。 

ホストを起動して、JavaScript のデバッグを設定するには、次を実行します。

```
func host start --debug vscode
```

> [!IMPORTANT]
> デバッグについては、Node.js 8.x のみがサポートされています。 Node.js 9.x はサポートされていません。 

その後、Visual Studio Code の **[デバッグ]** ビューで、**[Attach to Azure Functions]\(Azure Functions にアタッチ\)** を選択します。 ブレークポイントをアタッチし、変数の調査をして、コードをステップ実行できます。

![Visual Studio Code での JavaScript デバッグ](./media/functions-run-local/vscode-javascript-debugging.png)

### <a name="passing-test-data-to-a-function"></a>関数へのテスト データの受け渡し

関数をローカルでテストするには、[Functions ホストを起動](#start)し、HTTP 要求を使用してローカル サーバーでエンドポイントを呼び出します。 呼び出すエンドポイントは、関数の種類によって異なります。 

>[!NOTE]  
> このトピックの例では、cURL ツールを使用して端末またはコマンド プロンプトから HTTP 要求を送信します。 お好みのツールを使用して HTTP 要求をローカル サーバーに送信できます。 Linux ベースのシステムでは既定で cURL ツールを使用できます。 Windows では、最初にダウンロードし、[cURL ツール](https://curl.haxx.se/)をインストールする必要があります。

関数のテストの全般的な情報については、「[Azure Functions のコードをテストするための戦略](functions-test-a-function.md)」を参照してください。

#### <a name="http-and-webhook-triggered-functions"></a>HTTP と webhook でトリガーされる関数

次のエンドポイントを呼び出して、HTTP と webhook でトリガーされる関数をローカルで実行できます。

    http://localhost:{port}/api/{function_name}

Functions ホストがリッスンしているのと同じサーバー名とポートを使用していることを確認してください。 これは、Functions ホストの起動時に生成される出力で確認できます。 トリガーでサポートされている任意の HTTP メソッドを使用して、この URL を呼び出すことができます。 

次の cURL コマンドは、`MyHttpTrigger` クイックスタート関数を、クエリ文字列で渡された _name_ パラメーターを使用して、GET 要求からトリガーします。 

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```
次の例は、要求本文で _name_ を渡す POST 要求から呼び出される同じ関数です。

```
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

ブラウザーから GET 要求を行ってクエリ文字列でデータを渡すことが可能です。 その他すべての HTTP メソッドについては、cURL、Fiddler、Postman、または類似の HTTP テスト ツールを使用する必要があります。  

#### <a name="non-http-triggered-functions"></a>HTTP でトリガーされない関数
HTTP トリガーと webhook を除く、あらゆる種類の関数の場合、管理エンドポイントを呼び出すことによって、関数をローカルでテストできます。 HTTP POST 要求を使ってローカル サーバーでこのエンドポイントを呼び出すと、関数がトリガーされます。 必要に応じて、POST 要求の本体でテスト データを実行に渡すことができます。 この機能は、Azure Portal の **[テスト]** タブに似ています。  

次の管理者エンドポイントを呼び出して、非 HTTP 関数をトリガーします。

    http://localhost:{port}/admin/functions/{function_name}

テスト データを関数の管理者エンドポイントに渡すには、そのデータを POST 要求メッセージの本文で提供する必要があります。 メッセージ本文は、次の JSON 形式にする必要があります。

```JSON
{
    "input": "<trigger_input>"
}
```` 
`<trigger_input>` 値には、関数が必要とする形式でデータが含まれています。 次の cURL の例は、`QueueTriggerJS` 関数に対する POST です。 この場合、入力は、キューにあることが期待されるメッセージに相当する文字列です。      

```
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>バージョン 1.x での `func run` コマンドの使用

>[!IMPORTANT]  
> `func run` コマンドは、ツールのバージョン 2.x ではサポートされていません。 詳細については、「[Azure Functions ランタイム バージョンをターゲットにする方法](set-runtime-version.md)」を参照してください。

`func run <FunctionName>` を使用して関数を直接呼び出し、関数の入力データを渡すこともできます。 このコマンドは、Azure Portal の **[テスト]** タブを使用して関数を実行するのと似ています。 

`func run` では、次のオプションがサポートされています。

| オプション     | [説明]                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | インライン コンテンツ。 |
| **`--debug -d`** | 関数を実行する前に、デバッガーを、ホスト プロセスにアタッチします。|
| **`--timeout -t`** | Functions ホストの準備が完了するまでの待機時間 (秒単位)。|
| **`--file -f`** | コンテンツとして使用するファイル名。|
| **`--no-interactive`** | 入力を促しません。 自動シナリオで便利です。|

たとえば、HTTP によってトリガーされる関数を呼び出して、コンテンツ本文を渡すには、次のコマンドを実行します。

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

### <a name="viewing-log-files-locally"></a>ログ ファイルをローカルに表示する

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="publish"></a>Azure に発行する

Azure で Functions プロジェクトを関数アプリに発行するには、`publish` コマンドを使用します。

```
func azure functionapp publish <FunctionAppName>
```

以下のオプションを使用できます。

| オプション     | [説明]                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  local.settings.json の設定を Azure に発行し、設定が既に存在する場合は上書きを促します。|
| **`--overwrite-settings -y`** | `-i` で使用する必要があります。 値が異なる場合は、Azure の AppSettings をローカル値で上書きします。 既定値は prompt です。|

このコマンドは、Azure で既存の関数アプリに公開されるコマンドです。 `<FunctionAppName>` がサブスクリプションに存在しない場合は、エラーが発生します。 Azure CLI を使用してコマンド プロンプトまたはターミナル ウィンドウから関数アプリを作成する方法については、「[サーバーレス実行用の Function App を作成する](./scripts/functions-cli-create-serverless.md)」を参照してください。

`publish` コマンドは、Functions プロジェクト ディレクトリのコンテンツをアップロードします。 ローカルでファイルを削除する場合、`publish` コマンドではファイルは Azure から削除されません。 Azure 内のファイルは、[Azure Portal] の [Kudu ツール](functions-how-to-use-azure-function-app-settings.md#kudu) を使用することで削除できます。  

>[!IMPORTANT]  
> Azure で関数アプリを作成すると、既定でバージョン 1.x の Function ランタイムが使用されます。 関数アプリでバージョン 2.x のランタイムを使用するには、アプリケーション設定 `FUNCTIONS_EXTENSION_VERSION=beta` を追加します。  
次の Azure CLI コードを使用して、この設定を関数アプリに追加します。 
```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings FUNCTIONS_EXTENSION_VERSION=beta   
```

## <a name="next-steps"></a>次の手順

Azure Functions Core Tools は[オープン ソースであり、GitHub でホストされています](https://github.com/azure/azure-functions-cli)。  
バグまたは機能要求を提出するには、[GitHub の問題をオープン](https://github.com/azure/azure-functions-cli/issues)してください。 

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
