<properties
	pageTitle="Azure Functions のローカルでの開発と実行 |Microsoft Azure"
	description="Azure App Service で実行する前に、Visual Studio で Azure Functions をコーディングしてテストする方法について説明します。"
	services="functions"
	documentationCenter="na"
	authors="tdykstra"
	manager="erikre"
	editor=""/>

<tags
	ms.service="functions"
	ms.workload="na"
	ms.tgt_pltfrm="multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="glenga"/>

# Visual Studio で Azure Functions をコーディングしてテストする方法

## Overview

この記事では、[WebJobs.Script](https://github.com/Azure/azure-webjobs-sdk-script/) GitHub リポジトリをダウンロードし、そのリポジトリに含まれている Visual Studio ソリューションを実行することで、[Azure Functions](functions-overview.md) をローカルで実行する方法について説明します。

Azure Functions のランタイムは、WebJobs.Script オープン ソース プロジェクトの実装です。このプロジェクトは [Azure WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md) を基盤に構築され、どちらのフレームワークもローカルで実行できます。ただし、WebJobs SDK では、ストレージ エミュレーターでサポートされないストレージ アカウントの機能を使用するため、Azure ストレージ アカウントに接続する必要があります。

関数は Azure ポータルで簡単にコーディングおよびテストできますが、Azure で実行する前にローカルで作業した方が良い場合があります。たとえば、Visual Studio は [IntelliSense](https://msdn.microsoft.com/library/hcw1s69b.aspx) を備えているため、Azure Functions でサポートされる言語の中には、Visual Studio でコードを記述した方が簡単なものもあります。また、関数はリモートでデバッグできますが、ローカルでデバッグした方が迅速で簡単な場合もあります。ローカルで実行する際に、関数コードや WebJobs スクリプト ホスト コードにブレークポイントを設定してデバッグできます。

>[AZURE.NOTE] Azure Functions は現在プレビュー段階で、ツールを含めて全体的なエクスペリエンスは迅速に開発が進んでいます。この記事で説明している手順には最終的なローカル開発エクスペリエンスは反映されていませんが、ぜひ[フィードバックをお寄せ](https://feedback.azure.com/forums/355860-azure-functions)ください。

## 前提条件

### Azure アカウントと関数アプリ

この記事では、ポータルで [Azure Functions](functions-overview.md) を利用したことがあり、[トリガー、バインド、JobHost](functions-reference.md) などの Azure Functions の概念を把握していることを前提としています。

関数をローカルで実行すると、コンソール ウィンドウに何らかの出力が表示されますが、関数呼び出しとログを確認するには、ライブ関数アプリによってホストされているダッシュボードを使用する必要があります。

### Visual Studio 2015 と最新の Azure SDK for .NET

Visual Studio 2015 を持っていない場合、または最新の Azure SDK を持っていない場合は、[Azure SDK for Visual Studio 2015 をダウンロード](http://go.microsoft.com/fwlink/?linkid=518003)してください。Visual Studio 2015 をまだ持っていない場合は、Visual Studio 2015 は SDK と共に自動的にインストールされます。

### 条件付きの前提条件

実行する関数で次のような Azure リソースやソフトウェアを使用する場合は、それらをインストールしておく必要があります。

* Azure リソース
	* Service Bus
	* Easy Tables
	* DocumentDB
	* Event Hubs
	* Notification Hubs

* コンパイラとスクリプト エンジン
	* F#
	* BASH
	* Python
	* PHP

設定する必要のある環境変数を含め、これらの要件の詳細については、[WebJobs.Script リポジトリの Wiki ページ](https://github.com/Azure/azure-webjobs-sdk-script/wiki/home)を参照してください。

WebJobs.SDK プロジェクトに貢献することが目的の場合は、条件付きの前提条件をすべて満たして、完全なテストを実行する必要があります。

## ローカルで実行するには

1. Webjobs.Script リポジトリを[複製](https://github.com/Azure/azure-webjobs-sdk-script/)または[ダウンロード](https://github.com/Azure/azure-webjobs-sdk-script/archive/master.zip)します。

2. ストレージ接続文字列用の環境変数を設定します。

	* AzureWebJobsStorage
	* AzureWebJobsDashboard

	環境変数用のこのような環境変数の値は、App Service の **[アプリケーションの設定]** ポータル ブレードから取得できます。

	a.**[関数コンテナー]** ブレードで、**[Function app settings]** (関数アプリの設定) をクリックします。

	![Click Function App Settings](./media/functions-run-local/clickfuncappsettings.png)
 
	b.**[Function App Settings]** (関数アプリの設定) ブレードで、**[Go to App Service Settings]** (App Service の設定に移動) をクリックします。

	![Click App Service Settings](./media/functions-run-local/clickappsvcsettings.png)
 
	c.**[設定]** ブレードで、**[アプリケーション設定]** をクリックします。

	![Click Application Settings](./media/functions-run-local/clickappsettings.png)
 
	d.**[アプリケーションの設定]** ブレードで **[アプリの設定]** セクションが表示されるまで下へスクロールし、WebJobs SDK の設定を探します。

	![WebJobs settings](./media/functions-run-local/wjsettings.png)

	e.`AzureWebJobsStorage` アプリ設定と同じ名前と値で環境変数を設定します。

	f.`AzureWebJobsDashboard` アプリ設定に対しても同じ操作を行います。

2. AzureWebJobsServiceBus という環境変数を作成し、Service Bus 接続文字列を設定します。

	この環境変数は、Service Bus のバインドに必要です。Service Bus のバインドを使用しない場合でも、設定することをお勧めします。シナリオによっては、バインドを使用するかどうかに関係なく、Service Bus 接続文字列を設定していないと、例外が表示される場合があります。

3. 必要なその他の環境変数がすべて設定されていることを確認します (前の「[条件付きの前提条件](#conditional-prerequisites)」セクションを参照してください)。

4. Visual Studio を起動し、WebJobs.Script ソリューションを開きます。

6. スタートアップ プロジェクトを設定します。HTTP または WebHook トリガーを使用する関数を実行する場合は、**WebJobs.Script.WebHost** を選択します。それ以外の場合は、**WebJobs.Script.Host** を選択します。

4. スタートアップ プロジェクトが WebJobs.Script.Host の場合は、次の手順を実行します。

	a.**ソリューション エクスプローラー**で、WebJobs.Script.Host プロジェクトを右クリックしてから **[プロパティ]** をクリックします。

	b.**[プロジェクトのプロパティ]** ウィンドウの **[デバッグ]** タブで、**[コマンドライン引数]** を `..\..\..\..\sample` に設定します。

	![Command line arguments](./media/functions-run-local/cmdlineargs.png)

	これは、リポジトリ内の *sample* フォルダーへの相対パスです。*sample* フォルダーには、グローバル設定が含まれている *host.json* ファイルと、各サンプル関数のフォルダーが含まれています。

	作業を開始するには、提供されている *sample* フォルダーを使用することが一番簡単です。後で *sample* フォルダーに独自の関数を追加することや、*host.json* と関数フォルダーが含まれている任意のフォルダーを使用することができます。

5. スタートアップ プロジェクトが WebJobs.Script.WebHost の場合は、次の手順を実行します。

	a.AzureWebJobsScriptRoot 環境変数を `sample` フォルダーの完全なパスに設定します。

	b.新しい環境変数の値を反映するために、Visual Studio を再起動します。

	HTTP トリガー関数を実行する方法の詳細については、[API キー](#api-keys)に関するセクションを参照してください。

5. *sample\\host.json* ファイルを開き、`functions` プロパティを追加して、実行する関数を指定します。

	たとえば、次の JSON では、WebJobs SDK の JobHost が探す関数は 2 つだけです。

		{
		  "functions": [ "TimerTrigger-CSharp", "QueueTrigger-CSharp"],
		  "id": "5a709861cab44e68bfed5d2c2fe7fc0c"
		}

	*sample* フォルダーの代わりに独自のフォルダーを使用する場合は、実行する関数のみをそのフォルダーに含めます。そうすると、*host.json* の `functions` プロパティを省略できます。
 
6. ソリューションをビルドして実行します。

	コンソール ウィンドウには、`host.json` ファイルで指定した関数のみを JobHost が探すことが示されます。

		Found the following functions:
		Host.Functions.QueueTrigger-CSharp
		Host.Functions.TimerTrigger-CSharp
		Job host started

	WebHost プロジェクトを開始すると、対応するベース URL で提供するコンテンツがそのプロジェクトには存在しないため、空のブラウザー ページが表示されます。HTTP トリガー関数に使用する URL については、[API キー](#apikeys)のセクションを参照してください。

## 関数の出力の確認

関数呼び出しとそのログ出力を確認するには、関数アプリのダッシュボードに移動します。

ダッシュボードの URL は次のとおりです。

	https://{function app name}.scm.azurewebsites.net/azurejobs/#/functions

**[関数]** ページには、実行された関数の一覧と、関数呼び出しの一覧が表示されます。

![Invocation Detail](./media/functions-run-local/invocationdetail.png)

呼び出しをクリックすると、**[Invocation Details]** (呼び出しの詳細) ページが表示され、関数がトリガーされた日時、おおよその実行時間、正常に完了したかどうかが示されます。**[Toggle Output]** (出力の切り替え) をクリックすると、関数コードによって書き込まれたログが表示されます。

![Invocation Detail](./media/functions-run-local/invocationdetail.png)

## <a id="apikeys"></a> HTTP トリガーの API キー

HTTP または WebHook 関数を実行するには、*function.json* ファイルに `"authLevel": "anonymous"` を含める場合を除き、API キーが必要です。

たとえば、API キーが `12345` の場合は、WebJobs.Script.WebHost プロジェクトを実行中に、次の URL で *HttpTrigger* 関数をトリガーできます。

	http://localhost:28549/api/httptrigger?code=12345

(代わりに、`x-functions-key` HTTP ヘッダーに API キーを配置することもできます。)

API キーは、WebJobs.Script.WebHost プロジェクトの [App\_Data/secrets](https://github.com/Azure/azure-webjobs-sdk-script/tree/master/src/WebJobs.Script.WebHost/App_Data/secrets) フォルダーにある `.json` ファイルに格納されます。

### すべての HTTP および WebHook 関数に適用される API キー

*App\_Data/secrets* フォルダーの *host.json* ファイルには、次の 2 つのキーがあります。

```json
{
  "masterKey": "hyexydhln844f2mb7hyexydhln844f2mb7",
  "functionKey": "7hyexydhn844f2mb7hyexydhln844f2mb7"
}
```

`functionKey` プロパティは、HTTP または WebHook 関数のオーバーライドが定義されていない場合に、その特定の関数に使用できるキーを格納します。この機能により、関数を作成するたびに常に新しい API キーを定義する必要がなくなります。

`masterKey` プロパティは、次のような一部のテスト シナリオに役に立つキーを格納します。

* マスター キーを使用して WebHook 関数を呼び出すと、WebJobs SDK が WebHook プロバイダーの署名の検証をバイパスする場合。

* マスター キーを使用して HTTP または WebHook 関数を呼び出すと、*function.json* ファイルで無効になっていても関数がトリガーされる場合。Azure ポータルでは、これを使用して、無効になっている関数に対しても **[実行]** ボタンが機能するようにしています。
 
### 個々の関数に適用される API キー

*{function name}.json* という名前のファイルには、特定の関数の API キーが含まれています。たとえば、次の例の *App\_Data/secrets/HttpTrigger.json* の JSON コンテンツでは、`HttpTrigger` 関数の API キーを設定します。

```json
{
  "key": "844f2mdhn844f2mb7hyexydhln844f2mb7"
}
```

## NuGet パッケージの参照を関数内で使用する  

現在 NuGet の参照に適用される処理の形式上、必ずホストの実行中に *project.json* ファイルに "触れる" ようにしてください。ホストはファイルの変更を監視し、変更が検出されたときに復元を開始します。また、*NuGet.exe* (3.3.0 を推奨) は、実際のパスで指定するか、または *NuGet.exe* へのパスが AzureWebJobs\_NuGetPath という名前の環境変数に設定されている必要があります。

## トラブルシューティング

Visual Studio の実行中に行われた環境変数の変更は自動的に反映されません。Visual Studio の起動後に環境変数を追加または変更した場合は、最新の値が反映されるように、Visual Studio をシャットダウンしてから再起動してください。

**[例外設定]** ウィンドウ (ウィンドウを開くには、Ctrl + Alt + E キーを押します) で **[Common Language Runtime Exceptions]** を選択すると、デバッグ時に例外の詳細情報が表示される場合があります。

デバッグ時に例外情報を表示するもう 1 つの方法では、スクリプト ホストのメイン ループの `catch` ブロックにブレークポイントを設定します。このブロックは、WebJobs.Script プロジェクトの *Host/ScriptHostManager.cs* 内にある `RunAndBlock` メソッドにあります。

## 次のステップ

詳細については、次のリソースを参照してください。

* [Azure Functions developer reference (Azure Functions 開発者向けリファレンス)](functions-reference.md)
* [Azure Functions C# developer reference (Azure Functions C# 開発者向けリファレンス)](functions-reference-csharp.md)
* [Azure Functions F# 開発者向けリファレンス](functions-reference-fsharp.md)
* [Azure Functions NodeJS 開発者向けリファレンス](functions-reference-node.md)
* [Azure Functions のトリガーとバインドに関する記事](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0921_2016-->