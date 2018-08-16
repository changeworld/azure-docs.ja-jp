---
title: Azure Functions Core Tools の操作 | Microsoft Docs
description: Azure 関数を Azure Functions で実行する前に、ローカル コンピューターのコマンド プロンプトまたはターミナルでコーディングしてテストする方法について説明します。
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
ms.date: 06/26/2018
ms.author: glenga
ms.openlocfilehash: 57011e1f7633688e00a4639ba36fd4442073161d
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39618616"
---
# <a name="work-with-azure-functions-core-tools"></a>Azure Functions Core Tools の操作

Azure Functions Core Tools を使用すると、ローカル コンピューター上のコマンド プロンプトまたはターミナルから関数を開発およびテストできます。 ローカル関数はライブ Azure サービスに接続できるため、完全な Functions ランタイムを使用してローカル コンピューター上で関数をデバッグすることができます。 Azure サブスクリプションに関数アプリをデプロイすることもできます。

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

## <a name="core-tools-versions"></a>Core Tools のバージョン

Azure Functions Core Tools には、2 つのバージョンがあります。 使用するバージョンは、ローカル開発環境、選択した言語、および必要なサポートのレベルによって異なります。

+ [バージョン 1.x](#v1): ランタイムのバージョン 1.x をサポートします。これは、一般公開 (GA) 段階です。 ツールのこのバージョンは Windows コンピューター上でのみサポートされ、[npm パッケージ](https://docs.npmjs.com/getting-started/what-is-npm)からインストールされます。 このバージョンでは、正式にサポートされていない試験段階の言語で関数を作成できます。 詳細については、「[Azure Functions でサポートされている言語](supported-languages.md)」を参照してください

+ [バージョン 2.x](#v2): [ランタイムのバージョン 2.x](functions-versions.md) をサポートします。 このバージョンは、[Windows](#windows-npm)、[macOS](#brew)、および [Linux](#linux) に対応しています。 インストールには、プラットフォーム固有のパッケージ マネージャーまたは npm を使用します。 2.x ランタイムと同様に、Core Tools のこのバージョンは現在プレビュー段階です。 

特に記載がない限り、この記事の例ではバージョン 2.x を対象にしています。 破壊的変更やお知らせを含むバージョン 2.x についての重要な更新プログラムを受け取るには、[Azure App Service のお知らせ](https://github.com/Azure/app-service-announcements/issues)のリポジトリをご覧ください。

## <a name="install-the-azure-functions-core-tools"></a>Azure Functions Core Tools のインストール

[Azure Functions Core Tools] は、Azure Functions ランタイムを実行するのと同じランタイムのバージョンを含み、ローカルの開発コンピューターで実行できます。 また、このツールには、関数を作成し、Azure に接続し、関数プロジェクトをデプロイするためのコマンドも用意されています。

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

1. [Windows 用 .NET Core 2.1](https://www.microsoft.com/net/download/windows) をインストールします。

2. [Node.js]をインストールします。これには、npm が同梱されています。 2x バージョンのツールの場合、Node.js 8.5 以降のバージョンのみがサポートされています。

3. 次のコマンドを使って、Core Tools のパッケージをインストールします。

    ```bash
    npm install -g azure-functions-core-tools@core
    ```

#### <a name="brew"></a>Homebrew による MacOS

次の手順では、Homebrew を使用して macOS 上に Core Tools をインストールします。

1. [macOS 用 .NET Core 2.1](https://www.microsoft.com/net/download/macos) をインストールします。

2. まだインストールしていない場合は、[Homebrew](https://brew.sh/) をインストールします。

3. 次のコマンドを使って、Core Tools のパッケージをインストールします。

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools 
    ```

#### <a name="linux"></a>APT による Linux (Ubuntu/Debian)

次の手順では [APT ](https://wiki.debian.org/Apt)を使用して、Ubuntu/Debian Linux ディストリビューションに Core Tools をインストールします。 他の Linux ディストリビューションについては、[Core Tools の readme](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux) に関するページを参照してください。

1. [Linux 用 .NET Core 2.1](https://www.microsoft.com/net/download/linux) をインストールします。

2. 次のコマンドを使って、Microsoft プロダクト キーを信頼済みとして登録します。

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

3. Ubuntu サーバーで、次の表の該当するバージョンのいずれかが実行されていることを確認します。 apt ソースを追加するには、次のコマンドを実行します。

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    sudo apt-get update
    ```

    | Linux ディストリビューション | Version |
    | --------------- | ----------- |
    | Ubuntu 17.10    | `artful`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

4. 次のコマンドを使って、Core Tools のパッケージをインストールします。

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

## <a name="create-a-local-functions-project"></a>ローカル関数プロジェクトを作成する

関数プロジェクト ディレクトリには、[host.json](functions-host-json.md) ファイル、[local.settings.json](#local-settings-file) ファイル、および個々の関数のコードを含むサブフォルダーが含まれています。 このディレクトリは、Azure の関数アプリに相当します。 Functions のフォルダー構造の詳細については、[Azure Functions の開発者向けガイド](functions-reference.md#folder-structure)を参照してください。

バージョン 2.x では、初期化時にプロジェクトの既定の言語を選択する必要があります。さらに、追加されたすべての関数で、既定の言語テンプレートが使用されます。 バージョン 1.x では、関数を作成するたびに言語を指定します。

ターミナル ウィンドウまたはコマンド プロンプトで、次のコマンドを実行してプロジェクトおよびローカルの Git リポジトリを作成します。

```bash
func init MyFunctionProj
```

バージョン 2.x では、コマンドを実行するときにプロジェクトのランタイムを選択する必要があります。 JavaScript 関数を開発する場合は、**node** を選択します。

```output
Select a worker runtime:
dotnet
node
java
```

上/下方向キーを使用して言語を選択し、Enter キーを押します。 出力は、次の JavaScript プロジェクトの例のようになります。

```output
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
```

ローカル Git リポジトリを使用せずにプロジェクトを作成する場合は、`--no-source-control [-n]` オプションを使用します。

> [!IMPORTANT]
> 既定では、Core Tools のバージョン 2.x では、.NET ランタイムの関数アプリ プロジェクトは [C# クラス プロジェクト](functions-dotnet-class-library.md) (.csproj) として作成されます。 Visual Studio 2017 または Visual Studio Code で使用できるこれらの C# プロジェクトは、テスト中および Azure への発行時にコンパイルされます。 バージョン 1.x およびポータルで作成される同じ C# スクリプト (.csx) ファイルを代わりに作成および使用したい場合は、関数を作成して展開するときに、`--csx` パラメーターを指定する必要があります。

## <a name="register-extensions"></a>拡張機能を登録する

Azure Functions ランタイムのバージョン 2.x では、関数アプリで使用するバインディング拡張機能 (バインディングの種類) を明示的に登録する必要があります。

[!INCLUDE [Register extensions](../../includes/functions-core-tools-install-extension.md)]

詳しくは、「[Azure Functions でのトリガーとバインドの概念](functions-triggers-bindings.md#register-binding-extensions)」をご覧ください。

## <a name="local-settings-file"></a>ローカル設定ファイル

local.settings.json ファイルには、アプリの設定、接続文字列、および Azure Functions Core Tools の設定が格納されます。 その構造を次に示します。

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*"
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

| Setting      | 説明                            |
| ------------ | -------------------------------------- |
| **IsEncrypted** | **true** に設定すると、すべての値がローカル コンピューターのキーを使用して暗号化されます。 `func settings` コマンドと共に使用されます。 既定値は **false** です。 |
| **値** | ローカルで実行するときに使用されるアプリケーション設定と接続文字列のコレクションです。 これらの値は、**AzureWebJobsStorage** や **AzureWebJobsDashboard** など、Azure 内の関数アプリのアプリ設定に対応します。 多くのトリガーおよびバインドには、[BLOB Storage トリガー](functions-bindings-storage-blob.md#trigger---configuration)の **Connection** など、接続文字列アプリ設定を参照するプロパティがあります。 このようなプロパティでは、**Values** 配列にアプリケーション設定を定義する必要があります。 <br/>**AzureWebJobsStorage** は、HTTP 以外のトリガーに必要なアプリ設定です。 [Azure ストレージ エミュレーター](../storage/common/storage-use-emulator.md)がローカルにインストールされている場合は、**AzureWebJobsStorage** を `UseDevelopmentStorage=true` に設定できます。Core Tools はエミュレーターを使用します。 これは開発中には便利ですが、展開する前に実際のストレージに接続してテストする必要があります。 |
| **Host** | このセクションの設定により、ローカルで実行時の Functions ホスト プロセスをカスタマイズできます。 |
| **LocalHttpPort** | ローカルの Functions ホストの実行時に使用される既定のポートを設定します (`func host start`と`func run`)。 `--port` コマンド ライン オプションは、この値に優先します。 |
| **CORS** | [クロス オリジン リソース共有 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) で許可されるオリジンを定義します。 スペースなしのコンマ区切りのリストでオリジンを指定します。 ワイルドカード値 (\*) がサポートされており、これによって任意のオリジンからの要求を許可できます。 |
| **ConnectionStrings** | 関数のバインディングで使用される接続文字列にこのコレクションを使用しないでください。 このコレクションは、[Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx) など、構成ファイルの **ConnectionStrings** セクションから接続文字列を取得するのが一般的なフレームワークでのみ使用されます。 このオブジェクト内の接続文字列は、[System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) のプロバイダーの種類と共に、環境に追加されます。 他のアプリ設定では、このコレクション内の項目は Azure に発行されません。 関数アプリの設定の**接続文字列**のコレクションに、これらの値を明示的に追加する必要があります。 関数のコードで [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) を作成する場合は、接続文字列の値を他の接続と共に**アプリケーションの設定**に格納する必要があります。 |

関数アプリの設定値は、コードの中で環境変数として読み込むこともできます。 詳細については、以下の言語固有のリファレンス トピックの「環境変数」のセクションを参照してください。

+ [C# プリコンパイル済み](functions-dotnet-class-library.md#environment-variables)
+ [C# スクリプト (.csx)](functions-reference-csharp.md#environment-variables)
+ [F#](functions-reference-fsharp.md#environment-variables)
+ [Java](functions-reference-java.md#environment-variables) 
+ [JavaScript](functions-reference-node.md#environment-variables)

local.settings.json ファイル内の設定は、ローカルで実行されている Functions ツールでのみ使用されます。 既定では、プロジェクトが Azure に発行されても、これらの設定は自動的に移行されません。 [発行する際](#publish)に `--publish-local-settings` スイッチを使用して、これらの設定が Azure 内の関数アプリに追加されていることを確認してください。 **ConnectionStrings** 内の値は発行されません。

有効なストレージ接続文字列が **AzureWebJobsStorage** に設定されておらず、エミュレーターが使用されていない場合は、次のエラー メッセージが表示されます。  

>local.settings.json に AzureWebJobsStorage の値がありません。 これは HTTP 以外のすべてのトリガーに必要です。 "func azure functionapp fetch-app-settings <functionAppName>" を実行するか、local.settings.json で接続文字列を指定することができます。

### <a name="get-your-storage-connection-strings"></a>ストレージ接続文字列の取得

開発のためにストレージ エミュレーターを使用している場合であっても、実際のストレージに接続してテストすることができます。 [ストレージ アカウントを作成済み](../storage/common/storage-create-storage-account.md)である場合は、次の方法のいずれかで、有効なストレージ接続文字列を取得できます。

+ [Azure ポータル]から設定。 ストレージ アカウントに移動し、**設定**の**アクセス キー**を選択してから、いずれかの**接続文字列**の値をコピーします。

  ![Azure Portal から接続文字列をコピーする](./media/functions-run-local/copy-storage-connection-portal.png)

+ [Azure Storage Explorer](http://storageexplorer.com/) を使用して、Azure アカウントに接続します。 **Explorer** でサブスクリプションを展開し、ストレージ アカウントを選択して、プライマリまたはセカンダリの接続文字列をコピーします。 

  ![Storage Explorer から接続文字列をコピーする](./media/functions-run-local/storage-explorer.png)

+ Core Tools を使用して、次のコマンドのいずれで Azure から接続文字列をダウンロードします。

    + 既存の関数アプリからすべての設定をダウンロードします。

    ```bash
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    + 特定のストレージ アカウントの接続文字列を取得します。

    ```bash
    func azure storage fetch-connection-string <StorageAccountName>
    ```
    
    Azure にまだサインインしていない場合は、サインインするように求められます。

## <a name="create-func"></a>関数を作成する

関数を作成するには、次のコマンドを実行します。

```bash
func new
```

バージョン 2.x では、`func new` を実行したときに関数アプリの既定の言語のテンプレートを選択するように求められ、次に関数の名前を選択するように求められます。 バージョン 1.x では、さらに言語を選択するように求められます。

```output
Select a language: Select a template:
Blob trigger
Cosmos DB trigger
Event Grid trigger
HTTP trigger
Queue trigger
SendGrid
Service Bus Queue trigger
Service Bus Topic trigger
Timer trigger
```

次のキュー トリガーの出力に示すように、指定した関数名のサブフォルダーに関数のコードが生成されます。

```output
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
```

次の引数を使用して、コマンドでこれらのオプションを指定することもできます。

| 引数     | 説明                            |
| ------------------------------------------ | -------------------------------------- |
| **`--language -l`**| テンプレート プログラミング言語。C#、F#、JavaScript など。 このオプションは、バージョン 1.x で必須です。 バージョン 2.x では、このオプションを使用したり、プロジェクトの既定の言語を選択したりしないでください。 |
| **`--template -t`** | サポートされている各言語で使用可能なテンプレートの完全な一覧を表示するには、`func templates list` コマンドを使います。   |
| **`--name -n`** | 関数名。 |
| **`--csx`** | (バージョン 2.x) バージョン 1.x およびポータルで使用されるものと同じ C# スクリプト (.csx) テンプレートを生成します。 |

たとえば、JavaScript HTTP トリガーを 1 つのコマンドで作成するには、次を実行します。

```bash
func new --template "Http Trigger" --name MyHttpTrigger
```

キューによってトリガーされる関数を 1 つのコマンドで作成するには、次を実行します。

```bash
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="start"></a>関数をローカルで実行する

Functions プロジェクトを実行するには、Functions ホストを実行します。 ホストによって、プロジェクトのすべての関数に対するトリガーが有効になります。

```bash
func host start
```

`func host start` では、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | ローカル ポート。このポートでリッスンします。 既定値: 7071。 |
| **`--debug <type>`** | [Visual Studio Code](https://code.visualstudio.com/tutorials/functions-extension/getting-started) または [Visual Studio 2017](functions-dotnet-class-library.md) から **func.exe** プロセスにアタッチできるように、デバッグ ポートを開いた状態でホストを起動します。 *\<type\>* オプションは `VSCode` と `VS` です。  |
| **`--cors`** | CORS オリジンのコンマ区切りのリスト (スペースなし)。 |
| **`--nodeDebugPort -n`** | 使用するノード デバッガーのポート。 既定値: launch.json または 5858 の値。 |
| **`--debugLevel -d`** | コンソール トレース レベル (オフ、詳細、情報、警告、またはエラー)。 既定値: 情報。|
| **`--timeout -t`** | Functions ホスト開始のタイムアウト (秒単位)。 既定値: 20 秒。|
| **`--useHttps`** | `http://localhost:{port}` ではなく `https://localhost:{port}` にバインドします。 既定では、このオプションにより、信頼された証明書がコンピューターに作成されます。|
| **`--pause-on-error`** | プロセスを終了する前に、追加入力を一時停止します。 Visual Studio または VS Code から Core Tools を起動するときに使用します。|

Functions ホストの起動時、HTTP によってトリガーされる関数の URL が出力されます。

```bash
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

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

```bash
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```
次の例は、要求本文で _name_ を渡す POST 要求から呼び出される同じ関数です。

```bash
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

```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>バージョン 1.x での `func run` コマンドの使用

>[!IMPORTANT]  
> `func run` コマンドは、ツールのバージョン 2.x ではサポートされていません。 詳細については、「[Azure Functions ランタイム バージョンをターゲットにする方法](set-runtime-version.md)」を参照してください。

`func run <FunctionName>` を使用して関数を直接呼び出し、関数の入力データを渡すこともできます。 このコマンドは、Azure Portal の **[テスト]** タブを使用して関数を実行するのと似ています。 

`func run` では、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | インライン コンテンツ。 |
| **`--debug -d`** | 関数を実行する前に、デバッガーを、ホスト プロセスにアタッチします。|
| **`--timeout -t`** | Functions ホストの準備が完了するまでの待機時間 (秒単位)。|
| **`--file -f`** | コンテンツとして使用するファイル名。|
| **`--no-interactive`** | 入力を促しません。 自動シナリオで便利です。|

たとえば、HTTP によってトリガーされる関数を呼び出して、コンテンツ本文を渡すには、次のコマンドを実行します。

```bash
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

### <a name="viewing-log-files-locally"></a>ログ ファイルをローカルに表示する

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="publish"></a>Azure に発行する

Azure で Functions プロジェクトを関数アプリに発行するには、`publish` コマンドを使用します。

```bash
func azure functionapp publish <FunctionAppName>
```

以下のオプションを使用できます。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  local.settings.json の設定を Azure に発行し、設定が既に存在する場合は上書きを促します。 ストレージ エミュレーターを使用している場合は、アプリ設定を[実際のストレージ接続](#get-your-storage-connection-strings)に変更します。 |
| **`--overwrite-settings -y`** | `-i` で使用する必要があります。 値が異なる場合は、Azure の AppSettings をローカル値で上書きします。 既定値は prompt です。|

このコマンドは、Azure で既存の関数アプリに公開されるコマンドです。 `<FunctionAppName>` がサブスクリプションに存在しない場合は、エラーが発生します。 Azure CLI を使用してコマンド プロンプトまたはターミナル ウィンドウから関数アプリを作成する方法については、「[サーバーレス実行用の Function App を作成する](./scripts/functions-cli-create-serverless.md)」を参照してください。

`publish` コマンドは、Functions プロジェクト ディレクトリのコンテンツをアップロードします。 ローカルでファイルを削除する場合、`publish` コマンドではファイルは Azure から削除されません。 Azure 内のファイルは、[Azure ポータル] の [Kudu ツール](functions-how-to-use-azure-function-app-settings.md#kudu) を使用することで削除できます。  

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
[Azure ポータル]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
