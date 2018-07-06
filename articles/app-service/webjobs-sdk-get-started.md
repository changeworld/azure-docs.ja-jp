---
title: Azure WebJobs SDK の概要
description: イベント ドリブンのバックグラウンド処理のための WebJobs SDK の概要。 Azure サービスとサード パーティのサービス内のデータにアクセスする方法を説明します。
services: app-service\web, storage
documentationcenter: .net
author: tdykstra
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: tdykstra
ms.openlocfilehash: 5a3070582a32adeda45dff233782c3a971885c14
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37018457"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>イベント ドリブンのバックグラウンド処理で Azure WebJobs SDK の使用を開始する

この記事では、Azure WebJobs SDK プロジェクトを作成し、ローカルで実行し、Azure App Service にデプロイする方法を示します。

手順は [Visual Studio 2017](https://www.visualstudio.com/vs/) に関するものですが、[Visual Studio Code](https://code.visualstudio.com/) などの他のツールでも同じタスクを実行できます。

## <a name="what-is-the-azure-webjobs-sdk"></a>Azure Web ジョブ SDK とは

Azure WebJobs SDK は、Azure サービス内のデータにアクセスするバックグラウンド処理コードを記述するタスクを簡素化するフレームワークです。 この SDK は、キューに追加された新しいメッセージなど、機能をトリガーする必要のあるイベントを指定するための宣言構文を備えています。 同様の宣言構文で、関数がトリガーされた後にデータの読み取りと書き込みを制御します。 Azure およびサードパーティのサービスへのアクセスに関連付けられている低レベルのコーディング作業のほとんどが、このトリガーとバインドのシステムによって行われます。

### <a name="functions-triggers-and-bindings"></a>関数、トリガー、バインド

WebJobs SDK プロジェクトでは、1 つ以上の "*関数*" を定義します。 関数とは、メソッド シグネチャ内にトリガー属性を持つメソッドです。 トリガーは関数を呼び出すための条件を指定し、バインドは読み取りと書き込みの内容を指定します。 たとえば、次の関数のトリガー属性は、キュー メッセージが `items` キューに出現するたびに関数を呼び出すようランタイムに通知します。 `Blob` 属性は、キュー メッセージを使用して *workitems* コンテナー内の BLOB を読み取るようランタイムに通知します。 `queueTrigger` パラメーターで提供されるキュー メッセージの内容は、BLOB の名前です。

```cs
public static void Run(
    [QueueTrigger("items")] string myQueueItem,
    [Blob("workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

### <a name="versions-2x-and-3x"></a>バージョン 2.x および 3.x

手順では、WebJobs SDK バージョン 2.x プロジェクトを作成する方法を示し、3.x (プレビュー版) との相違点を示します。 3.x で導入された主な変更は、.NET Framework の代わりに .NET Core を使用することです。

### <a name="azure-functions"></a>Azure Functions

[Azure Functions](../azure-functions/functions-overview.md) は WebJobs SDK に基づいており、WebJobs SDK を直接使用する必要がない場合のオプションです。 Azure Functions 1.x では WebJobs SDK 2.x を使用します。 詳しくは、[Azure Functions と WebJobs SDK の比較](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

この記事では、[Azure アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)と [Azure App Service のアプリ](app-service-web-overview.md)に関する経験があることを想定しています。 この記事の手順を完了するには、次のことが条件となります。

* **Azure 開発**ワークロードと共に [Visual Studio 2017 をインストール](https://docs.microsoft.com/visualstudio/install/)します。 Visual Studio が既にあっても、そのワークロードがない場合は、**[ツール] > [Get Tools and Features]\(ツールと機能の取得\)** を選択してワークロードを追加します。
* [App Service アプリを作成](app-service-web-get-started-dotnet-framework.md)します。 Web ジョブをデプロイできるものが既にある場合は、新規に作成する代わりにそれを使用できます。

## <a name="create-a-project"></a>プロジェクトの作成

1. Visual Studio で、**[ファイル] > [新しいプロジェクト]** を選択します。

2. **[Windows クラシック デスクトップ] > [コンソール アプリ (.NET Framework)]** を選択します。

   3.x プロジェクトを作成するには、**[.NET Core] > [コンソール アプリ (.NET Core)]** を選択します。
   
   > [!NOTE]
   > 3.x はまだプレビュー中であり、開発中です。 この記事に示した 3.x の手順は不完全です。最新の情報については、ページの一番下にある GitHub の問題を参照してください。

3. プロジェクトに *WebJobsSDKSample* という名前を付け、**[OK]** を選択します。

   ![[新しいプロジェクト] ダイアログ](./media/webjobs-sdk-get-started/new-project.png)

## <a name="add-webjobs-nuget-package"></a>WebJobs NuGet パッケージの追加

1. NuGet パッケージ `Microsoft.Azure.WebJobs` の最新の安定した 2.x バージョンをインストールします  (WebJobs SDK 3.x の場合は、最新の 3.x バージョンを選択します)。
 
   バージョン 2.2.0 用の **パッケージ マネージャー コンソール** コマンドを次に示しします。

   ```powershell
   Install-Package Microsoft.Azure.WebJobs -version 2.2.0
   ``` 

## <a name="create-the-jobhost"></a>JobHost の作成

`JobHost` オブジェクトは関数のランタイム コンテナーで、トリガーをリッスンし、関数を呼び出します。 

1. *Program.cs* で、`using` ステートメントを追加します。

   ```cs
   using Microsoft.Azure.WebJobs;
   ```

1. `Main` メソッドを次のコードに置き換えます。

   ```cs
   static void Main()
   {
       var config = new JobHostConfiguration();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

## <a name="enable-console-logging"></a>コンソール ログ記録の有効化

WebJobs SDK プロジェクトにログインするためのオプションはいくつかあります。 お勧めのオプションは、[ASP.NET Core 用に開発されたログ記録フレームワーク](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging)です。 このフレームワークでは、ストレージ メディアとフィルター処理のパフォーマンスと柔軟性が向上します。 

ここでは、新しいフレームワークを使用するコンソール ログ記録を設定します。

1. 次の NuGet パッケージの最新の安定したバージョンをイストールします。

   * `Microsoft.Extensions.Logging` - ログ記録フレームワーク。
   * `Microsoft.Extensions.Logging.Console` -コンソール "*プロバイダー*"。 プロバイダーは、特定の宛先 (この場合はコンソール) にログを送信します。 
 
   バージョン 2.0.1 用の**パッケージ マネージャー コンソール** コマンドを次に示します。

   ```powershell
   Install-Package Microsoft.Extensions.Logging -version 2.0.1
   ``` 

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
   ``` 

2. *Program.cs* で、`using` ステートメントを追加します。

   ```cs
   using Microsoft.Extensions.Logging;
   ```

3. `Main` メソッドで、`JobHost` を作成する前に `JobHostConfiguration` を更新するコードを追加します。
 
   ```
   config.DashboardConnectionString = "";
   var loggerFactory = new LoggerFactory();
   config.LoggerFactory = loggerFactory
       .AddConsole();
   ```

   このコードは、次の変更を加えます。

   * [ダッシュボード ログ記録](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs)を無効にします。 ダッシュボードはレガシ監視ツールであり、ダッシュボード ログ記録は高スループットの実稼働シナリオにはお勧めしません。
   * 既定の[フィルター](webjobs-sdk-how-to.md#log-filtering)を使用してコンソールのプロバイダーを追加します。 

   `Main` メソッドは次のようになります。

   ```
   var config = new JobHostConfiguration();
   config.DashboardConnectionString = "";
   var loggerFactory = new LoggerFactory();
   config.LoggerFactory = loggerFactory
       .AddConsole();
   var host = new JobHost(config);
   host.RunAndBlock();
   ```
   
## <a name="create-a-function"></a>関数を作成する

1. *Functions.cs* をプロジェクト フォルダーに作成し、テンプレート コードを次のコードで置き換えます。

   ```cs
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;

   namespace WebJobsSDKSample
   {
       public class Functions
       {
           public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
           {
               logger.LogInformation(message);
           }
       }
   }
   ```

   `QueueTrigger` 属性は、`queue` と呼ばれる Azure Storage キューに新しいメッセージが書き込まれたときに、この関数を呼び出すようランタイムに通知します。 キュー メッセージの内容は、`message` パラメーター内のメソッド コードに提供されます。 メソッドの本文では、トリガー データを処理します。 この例では、コードはメッセージをログに記録するだけです。

   `message` パラメーターは文字列である必要はありません。 JSON オブジェクト、バイト配列、または [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage) オブジェクトにバインドすることもできます。 [キュー トリガーの使用方法](../azure-functions/functions-bindings-storage-queue.md#trigger---usage)に関するページをご覧ください。 バインドの種類 (キュー、BLOB、テーブルなど) ごとに、バインドできる異なる種類のパラメーター セットがあります。

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

ローカルで実行する Azure ストレージ エミュレーターには、WebJobs SDK に必要な機能がすべてあるわけではありません。 そのため、ここでは、Azure にストレージ アカウントを作成し、それを使用するようにプロジェクトを構成します。

1. **サーバー エクスプローラー**を開き、Azure にサインインします。 **[Azure]** ノードを右クリックし、**[Microsoft Azure サブスクリプションへの接続]** を選択します。

   ![Azure へのサインイン](./media/webjobs-sdk-get-started/sign-in.png)

1. **サーバー エクスプローラー**の **Azure** ノードで **[Storage]** を右クリックし、**[ストレージ アカウントの作成]** をクリックします。

   ![[ストレージ アカウントの作成] メニュー](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

2. **[ストレージ アカウントの作成]** ダイアログ ボックスで、ストレージ アカウントの一意の名前を入力します。

3. App Service アプリを作成したのと同じ**リージョン**または近くのリージョンを選択します。

1. **[作成]** を選択します。

   ![ストレージ アカウントの作成](./media/webjobs-sdk-get-started/create-storage-account.png)

1. **サーバー エクスプローラー**の **[Storage]** ノードで、新しいストレージ アカウントを選択します。 **[プロパティ]** ウィンドウで、**[接続文字列]** 値フィールドの右側にある省略記号 (**...**) を選択します。

   ![接続文字列の省略記号](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

2. 接続文字列をコピーし、この値をいつでももう一度コピーできる任意の場所に保存します。

   ![接続文字列のコピー](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-for-running-locally"></a>ローカルで実行するためのストレージの構成

WebJobs SDK は、アプリ設定コレクション内で Storage の接続文字列を検索します。 ローカルで実行すると、*App.config* ファイルや環境変数内でこの値が検索されます。

1. 次の XML を *App.config* ファイルの `<configuration>` 開始タグの直後に追加します。

   ```xml
   <connectionStrings>
     <add name="AzureWebJobsStorage" connectionString="{storage connection string}" />
   </connectionStrings>
   ```

2. *{storage connection string}* を先ほどコピーした接続文字列で置き換えます。

   後ほど Azure で App Service アプリを構成するときに、接続文字列をもう一度使用します。

## <a name="test-locally"></a>ローカルでテストする

ここでは、プロジェクトをローカルでビルドおよび実行し、キュー メッセージを作成して関数をトリガーします。

1. Ctrl キーを押しながら F5 キーを押してプロジェクトを実行します。

   コンソールは、ランタイムが関数を見つけ、キュー メッセージのトリガーを待機していることを示します。

   ```console
   Found the following functions:
   WebJobsSDKSample.Functions.ProcessQueueMessage
   info: Host.Startup[0]
         Found the following functions:
         WebJobsSDKSample.Functions.ProcessQueueMessage
   Job host started
   info: Host.Startup[0]
         Job host started
   ```

   `ServicePointManager` 設定に関する警告メッセージが表示されることがあります。 このプロジェクトで行うテストでは、警告を無視してかまいません。 警告について詳しくは、[WebJobs SDK の使用方法](webjobs-sdk-how-to.md#jobhost-servicepointmanager-settings)に関するページをご覧ください。

2. コンソール ウィンドウを閉じます。

1. **サーバー エクスプローラー**で、新しいストレージ アカウントのノードを展開し、**[キュー]** を右クリックします。 

2. **[キューの作成]** を選択します。 

3. キューの名前として「*キュー*」と入力し、**[OK]** を選択します。

   ![キューの作成](./media/webjobs-sdk-get-started/create-queue.png)

4. 新しいキューのノードを右クリックし、**[キューの表示]** を選択します。

5. **メッセージの追加**アイコンを選択します。

   ![キューの作成](./media/webjobs-sdk-get-started/create-queue-message.png)

6. **[メッセージの追加]** ダイアログで、「*Hello World!*」を  **[メッセージ テキスト]** として入力し、**[OK]** を選択します。

   ![キューの作成](./media/webjobs-sdk-get-started/hello-world-text.png)

7. プロジェクトを再度実行します。

   `ProcessQueueMessage` 関数で `QueueTrigger` 属性を使用したので、WeJobs SDK ランタイムは起動時にキュー メッセージをリッスンします。 "*キュー*" という名前のキューで新しいキュー メッセージを検索し、関数を呼び出します。

   [キュー ポーリング指数バックオフ](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm)により、ランタイムがメッセージを検索して関数を呼び出すのに 2 分ほどかかる場合があります。 [開発モード](webjobs-sdk-how-to.md#jobhost-development-settings)で実行すると、この待機時間を短縮できます。

  コンソール出力は次のようになります。

   ```console
   Found the following functions:
   WebJobsSDKSample.Functions.ProcessQueueMessage
   info: Host.Startup[0]
         Found the following functions:
         WebJobsSDKSample.Functions.ProcessQueueMessage
   Job host started
   info: Host.Startup[0]
         Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   info: Function[0]
         Hello World!
   info: Host.Results[0]
         Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   ```

8. コンソール ウィンドウを閉じます。

## <a name="add-application-insights-logging"></a>Application Insights ログの追加

プロジェクトを Azure で実行する場合、コンソール出力を表示して関数の実行を監視することはできません。 お勧めする監視ソリューションは [Application Insights](../application-insights/app-insights-overview.md) です。 詳しくは、「[Azure Functions を監視する](../azure-functions/functions-monitoring.md)」をご覧ください。

ここでは、Azure にデプロイする前に次のタスクを実行して Application Insights のログ記録を設定します。

* 使用する App Service アプリと Application Insights インスタンスがあることをご確認ください。
* Application Insights インスタンスおよび先ほど作成したストレージ アカウントを使用するように App Service アプリを構成します。
* Application Insights へのログ記録用のプロジェクトを設定します。

### <a name="create-app-service-app-and-application-insights-instance"></a>App Service アプリと Application Insights のインスタンスの作成

1. 使用できる App Service アプリがまだない場合は、[作成](app-service-web-get-started-dotnet-framework.md)します。

2. 使用できる Application Insights リソースがまだない場合は、[作成](../application-insights/app-insights-create-new-resource.md)します。 **[アプリケーションの種類]** を **[全般]** に設定し、「**インストルメンテーション キーをコピー**」の後のセクションをスキップします。

3. 使用する Application Insights リソースが既にある場合は、[インストルメンテーション キーをコピー](../application-insights/app-insights-create-new-resource.md#copy-the-instrumentation-key)します。

### <a name="configure-app-settings"></a>アプリケーションの設定の構成 

1. **サーバー エクスプローラー**で、**[Azure]** の下の **[App Service]** ノードを展開します。

1. App Service アプリがあるリソース グループを展開し、App Service アプリを右クリックします。

3. **[設定の表示]** を選択します。

4. **[接続文字列]** ボックスで、次のエントリを追加します。

   |Name  |接続文字列  |データベースの種類|
   |---------|---------|------|
   |AzureWebJobsStorage | {先ほどコピーした Storage 接続文字列}|カスタム|
   
6. **[アプリケーション設定]** ボックスに Application Insights インストルメンテーション キーがない場合は、先ほどコピーしたキーを追加します  (App Service アプリを作成する方法によっては、インストルメンテーション キーが既にある場合があります)。

   |Name  |値  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {instrumentation key} |

2. *{instrumentation key}* を、使用している Application Insights リソースからのインストルメンテーション キーで置き換えます。

2. **[保存]** を選択します。

1. 次の XML を *App.config* ファイルの接続文字列コレクションの直後に追加します。

   ```xml
   <appSettings>
     <add key="APPINSIGHTS_INSTRUMENTATIONKEY" value="{instrumentation key}" />
   </appSettings>
   ```

2. *{instrumentation key}* を、使用している Application Insights リソースからのインストルメンテーション キーで置き換えます。

   このデータを *App.config* ファイルに追加すると、プロジェクトをローカルで実行するときに Application Insights の接続をテストすることができます。 

3. 変更を保存します。

### <a name="add-application-insights-logging-provider"></a>Application Insights ログ プロバイダーの追加

1. Application Insights ログ プロバイダー用の NuGet パッケージの最新の安定した 2.x バージョンをインストールします: `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`  (WebJobs SDK 3.x の場合は、最新の 3.x バージョンのパッケージを選択します)。

   バージョン 2.2.0 用の **パッケージ マネージャー コンソール** コマンドを次に示しします。

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
   ``` 

1. .NET 構成マネージャー用の NuGet パッケージの最新の安定した 4.x バージョンをインストールします: `System.Configuration.ConfigurationManager`。

   バージョン 4.4.1 用の**パッケージ マネージャー コンソール** コマンドを次に示します。

   ```powershell
   Install-Package System.Configuration.ConfigurationManager -version 4.4.1
   ``` 

2. *Program.cs* を開き、構成マネージャー用の `using` ステートメントを追加します。

   ```csharp
   using System.Configuration;
   ```

2. `Main` メソッドのコードを次のコードに置き換えます。

   ```csharp
   using (var loggerFactory = new LoggerFactory())
   {
       var config = new JobHostConfiguration();
       var instrumentationKey =
           ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];
       config.DashboardConnectionString = "";
       config.LoggerFactory = loggerFactory
           .AddApplicationInsights(instrumentationKey, null)
           .AddConsole();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

   このコードは、次の変更を加えます。

   * 既定の[フィルター](webjobs-sdk-how-to.md#log-filtering)を使用して Application Insights ログ プロバイダーを追加します。ローカルで実行している場合は、すべての情報と高レベルのログがコンソールと Application Insights の両方に記録されるようになります。 
   * `LoggerFactory` オブジェクトを `using` ブロックに挿入して、ホストが終了したときにログ出力がフラッシュされるようにします。 

## <a name="test-application-insights-logging"></a>Application Insights ログ記録のテスト

ここでは、もう一度ローカルで実行して、ログ データが Application Insights とコンソールに記録されていることを確認します。

1. 「*Hello App Insights!*」をメッセージ テキストとして入力すること以外は[前](#trigger-the-function)に行ったのと同じ方法で、**サーバー エクスプローラー**を使用してキュー メッセージを 作成します。

1. プロジェクトを実行します。

   WebJobs SDK がキュー メッセージを処理し、コンソール ウィンドウにログが表示されます。

1. コンソール ウィンドウを閉じます。

1. [Azure Portal](https://portal.azure.com/) を開き、Application Insights リソースに移動します。

2. **[検索]** を選択します。

   ![[検索] の選択](./media/webjobs-sdk-get-started/select-search.png)

1. "*Hello App Insights!*" メッセージが表示されない場合は、 **[更新]** を数分ごとに定期的に選択します  (Application Insights クライアントが処理するログをフラッシュするのに少し時間がかかるので、ログはすぐには表示されません)。

   ![Application Insights のログ](./media/webjobs-sdk-get-started/logs-in-ai.png)

3. コンソール ウィンドウを閉じます。

## <a name="deploy-as-a-webjob"></a>Web ジョブとしてデプロイする

このセクションでは、プロジェクトを Web ジョブとしてデプロイします。 [先ほど作成した](#create-app-service-app-and-application-insights-instance) App Service アプリにデプロイします。 Azure での実行中にコードをテストするには、キュー メッセージを作成することで関数の呼び出しをトリガーします。

1. **ソリューション エクスプローラー**でプロジェクトを右クリックしてから、**[Publish as Azure WebJob]\(Azure Web ジョブとして発行\)** を選択します。

1. **[Azure Web ジョブの追加]** ダイアログで、**[OK]** を選択します。

   ![Azure Web ジョブの追加](./media/webjobs-sdk-get-started/add-azure-webjob.png)

   Visual Studio では、Web ジョブ発行用の NuGet パッケージが自動的にインストールされます。

1. **発行**ウィザードの **[プロファイル]** ステップで、**[Microsoft Azure App Service]** を選択します。

   ![[発行] ダイアログ](./media/webjobs-sdk-get-started/publish-dialog.png)

1. **[App Service]** ダイアログで、**自分のリソース グループ > 自分の App Service アプリ**の順に選択し、**[OK]** を選択します。

   ![[App Service] ダイアログ](./media/webjobs-sdk-get-started/app-service-dialog.png)

1. ウィザードの **[接続]** ステップで、**[発行]** を選択します。

## <a name="trigger-the-function-in-azure"></a>Azure で関数をトリガーする

1. ローカルで実行していないことを確認します (コンソール ウィンドウがまだ開いている場合は閉じます)。 そうしないと、作成するキュー メッセージが最初にローカル インスタンスによって処理される可能性があります。

1. 「*Hello Azure!*」と入力すること以外は[前](#trigger-the-function)に行ったのと同じ方法で、**サーバー エクスプローラー**を使用してキュー メッセージを作成します。

7. Visual Studio の **[キュー]** ページを更新すると、Azure App Service で実行されている関数で処理されるので、新しいメッセージが消えます。

   > [!TIP]
   > Azure でテストするときに、[開発モード](webjobs-sdk-how-to.md#jobhost-development-settings)を使用して、キュー トリガー関数がすぐに呼び出されることを確認し、[キュー ポーリング指数バックオフ](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm)による遅延を回避します。

### <a name="view-logs-in-application-insights"></a>Application Insights のログの表示

1. [Azure Portal](https://portal.azure.com/) を開き、Application Insights リソースに移動します。

2. **[検索]** を選択します。

1. "*Hello Azure!*" メッセージが表示されない場合は、 **[更新]** を数分ごとに定期的に選択します 

   Web ジョブで実行されている関数からのログを確認します。これには、前のセクションで入力した "*Hello Azure!*"  テキストが含まれます。

## <a name="add-an-input-binding"></a>入力バインディングの追加

入力バインディングでは、データを読み取るコードを簡略化します。 この例では、キュー メッセージは BLOB 名になり、BLOB 名を使用して Azure Storage 内で BLOB を検索し、読み取ります。

1. *Functions.cs* で、`ProcessQueueMessage` メソッドを次のコードに置き換えます。

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   このコードでは、`queueTrigger` は[バインディング式](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns)なので、実行時に別の値に解決されます。  実行時には、キュー メッセージの内容を含みます。

2. `using` を追加します。

   ```cs
   using System.IO;
   ```

3. ストレージ アカウントで BLOB コンテナーを作成します。

   a. **サーバー エクスプローラー**で、ストレージ アカウントのノードを展開し、**[BLOB]** を右クリックして **[BLOB コンテナーの作成]** を選択します。

   b. **[BLOB コンテナーの作成]** ダイアログで、コンテナー名として「*container*」を入力し、**[OK]** をクリックします。

4. BLOB コンテナーに *Program.cs* ファイルをアップロードします  (このファイルは、ここでは例として使用しています。任意のテキスト ファイルをアップロードし、そのファイルの名前でキュー メッセージを作成できます)。

   a. **サーバー エクスプローラー**で、作成したコンテナーのノードをダブルクリックします。

   b. **[コンテナー]** ウィンドウで **アップロード** ボタンをクリックします。

   ![BLOB のアップロード ボタン](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. *Program.cs* を検索して選択し、**[OK]** を選択します。

5. *Program.cs* をメッセージのテキストとして、以前に作成したキューにキュー メッセージを作成します。

   ![キュー メッセージ Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

6. プロジェクトを実行します。

   キュー メッセージによって関数がトリガーされ、BLOB が読み取られて、その長さがログに記録されます。 コンソール出力は次のようになります。

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```

## <a name="add-an-output-binding"></a>出力バインディングを追加する

出力バインディングでは、データを書き込むコードを簡略化します。 この例では、サイズをログに記録する代わりに BLOB のコピーを書き込むことで、前の例を変更します。

1. `ProcessQueueMessage` メソッドを次のコードに置き換えます。

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

5. *Program.cs* をメッセージのテキストとして別のキュー メッセージを作成します。

6. プロジェクトを実行します。

   キュー メッセージによって関数がトリガーされ、BLOB が読み取られ、その長さがログに記録されて、新しい BLOB が作成されます。 コンソール出力は同じですが、BLOB コンテナー ウィンドウに移動して **[更新]** を選択すると、*copy-Program.cs* という名前の新しい BLOB が表示されます。

## <a name="next-steps"></a>次の手順

このガイドでは、WebJobs SDK プロジェクトを作成、実行、デプロイする方法を説明しました。

WebJobs SDK プロジェクトに組み込まれるすべてのものを示すために、プロジェクトを最初から作成する手順を示しました。 ただし、次にプロジェクトを作成する際には、**クラウド** カテゴリの **Azure WebJob** テンプレートを使用することを検討してください。 このテンプレートでは、NuGet パッケージとサンプル コードが既に設定されたプロジェクトを作成します。 新しいログ記録フレームワークを使用するためにサンプル コードの変更が必要になる可能性があります。

詳しくは、[WebJobs SDK の使用方法](webjobs-sdk-how-to.md)に関する記事をご覧ください。
