---
title: "SQL Database を使用して Azure で ASP.NET アプリを作成する | Microsoft Docs"
description: "Azure で動作し、SQL データベースに接続する ASP.NET アプリの入手方法を説明します。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/07/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: d7006a50d35412021f7e475df526661854b23dc8
ms.lasthandoff: 04/22/2017


---
# <a name="create-an-aspnet-app-in-azure-with-sql-database"></a>SQL Database を使用して Azure で ASP.NET アプリを作成する

このチュートリアルでは、Azure でデータ主導の ASP.NET Web アプリを開発し、それを Azure SQL Database に接続して、データ主導の機能を有効にする方法について説明します。 これが完了すると、ASP.NET アプリケーションは [Azure App Service](../app-service/app-service-value-prop-what-is.md) 内で実行され、SQL Database に接続された状態になります。

![Azure Web アプリで発行された ASP.NET アプリケーション](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

## <a name="before-you-begin"></a>開始する前に

このサンプルを実行する前に、[無料の Visual Studio 2017 Community エディションをダウンロードし、インストールします](https://www.visualstudio.com/downloads/)。 Visual Studio のセットアップ中に、必ず **[Azure の開発]** を有効にしてください。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>サンプルのダウンロード
この手順では、サンプルの ASP.NET アプリケーションをダウンロードします。

### <a name="get-the-sample-project"></a>サンプル プロジェクトを入手する

[こちら](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip)をクリックして、サンプル プロジェクトをダウンロードします。

ダウンロードした `dotnet-sqldb-tutorial-master.zip` を作業ディレクトリに展開します。

> [!TIP]
> 同じサンプル プロジェクトを、GitHub リポジトリを複製することによって入手できます。
>
> ```bash
> git clone https://github.com/Azure-Samples/dotnet-sqldb-tutorial.git
> ```
>
>

このサンプル プロジェクトには、[Entity Framework Code First](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) で構築された、単純な [ASP.NET MVC](https://www.asp.net/mvc) CRUD (作成、読み取り、更新、削除) アプリケーションが含まれています。

### <a name="run-the-application"></a>アプリケーションの実行

展開したディレクトリから、Visual Studio 2017 で `dotnet-sqldb-tutorial-master\DotNetAppSqlDb.sln` を起動します。

サンプル ソリューションを開いたら、`F5` キーを押してブラウザーで実行します。

単純な To Do リストがホーム ページに表示されます。 空のリストに、いくつかの To Do を追加してみてください。

![[新しい ASP.NET プロジェクト] ダイアログ ボックス](./media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

データベース コンテキストでは、`MyDbConnection` という接続文字列を使用します。 この接続文字列は、`Web.config` で定義され、`Models\MyDatabaseContext.cs` で参照されます。 後で Azure Web アプリを Azure SQL Database に接続する際に必要になるのは、この接続文字列名のみです。 

## <a name="publish-to-azure-with-sql-database"></a>SQL Database を使用して Azure に発行する

**ソリューション エクスプローラー**で **DotNetAppSqlDb** プロジェクトを右クリックし、**[発行]** を選択します。

![ソリューション エクスプローラーから発行する](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

**[Microsoft Azure App Service]** が選択されていることを確認し、**[発行]** をクリックします。

![プロジェクトの概要ページから発行する](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-to-app-service.png)

**[App Service の作成]** ダイアログ ボックスが表示されます。このダイアログ ボックスでは、Azure で ASP.NET Web アプリを実行するために必要なすべての Azure リソースを作成することができます。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

**[App Service の作成]** ダイアログ ボックスで、**[アカウントの追加]** をクリックし、Azure サブスクリプションにサインインします。 既に Microsoft アカウントにサインインしている場合は、アカウントが Azure サブスクリプションを保持していることを確認します。 サインインしている Microsoft アカウントが Azure サブスクリプションを備えていない場合は、正しいアカウントをクリックして追加します。
   
![Azure へのサインイン](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

サインインしたら、このダイアログ ボックスで、Azure Web アプリに必要なすべてのリソースの作成を開始できます。

### <a name="create-a-resource-group"></a>リソース グループの作成

まず、"_リソース グループ_" が必要です。 

> [!NOTE] 
> リソース グループとは、Web アプリ、データベース、ストレージ アカウントなどの Azure リソースのデプロイと管理に使用する論理コンテナーです。
>
>

**[リソース グループ]** の横にある **[新規]** をクリックします。

リソース グループに **myResourceGroup** という名前を付けて、**[OK]** をクリックします。

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

Azure Web アプリには、"_App Service プラン_" も必要です。 

> [!NOTE]
> App Service プランは、アプリをホストするために使用される物理リソースのコレクションを表しています。 App Service プランに割り当てられたすべてのアプリは、プランで定義されたリソースを共有します。これにより、複数のアプリをホストする際にコストを節約できます。 
>
> App Service プランには、次の定義があります。
>
> - リージョン (北ヨーロッパ、米国東部、東南アジア)
> - インスタンス サイズ (Small、Medium、Large)
> - スケール カウント (インスタンス数 1、2、3 など) 
> - SKU (Free、Shared、Basic、Standard、Premium)
>
>

**[App Service プラン]** の横にある **[新規]** をクリックします。 

**[App Service プランの構成]** ダイアログ ボックスで、新しい App Service プランを次の設定で構成します。

- **[App Service プラン]**: 「**myAppServicePlan**」と入力します。 
- **[場所]**: **[西ヨーロッパ]**、または他の任意のリージョンを選択します。
- **[サイズ]**: **[無料]**、または他の任意の[価格レベル](https://azure.microsoft.com/pricing/details/app-service/)を選択します。

**[OK]**をクリックします。

![Create App Service plan](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

### <a name="configure-the-web-app-name"></a>Web アプリ名を構成する

**[Web アプリ名]** に一意のアプリ名を入力します。 この名前は、アプリで既定の DNS 名の一部として使用されます (`<app_name>.azurewebsites.net`)。そのため、Azure のすべてのアプリ間で一意である必要があります。 後で、アプリをユーザーに公開する前に、カスタム ドメイン名をアプリにマップできます。

自動的に生成される名前をそのまま使用することもできます。この名前は、既に一意になっています。

次の手順の準備をするには、**[ほかの Azure サービスを探す]** をクリックします。

![Web アプリ名を構成する](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-name.png)

### <a name="create-a-sql-server-instance"></a>SQL Server インスタンスを作成する

**[サービス]** タブで、**[SQL Database]** の隣にある **[+]** アイコンをクリックします。 

**[SQL Database の構成]** ダイアログで、**[SQL Server]** の隣にある **[New (新規)]** をクリックします。 

**[サーバー名]** に一意の名前を入力します。 この名前は、データベース サーバーで既定の DNS 名の一部として使用されます (`<server_name>.database.windows.net`)。そのため、Azure のすべての SQL Server インスタンス間で一意である必要があります。 

他のフィールドを適宜構成して、**[OK]** をクリックします。

![SQL Server インスタンスを作成する](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

### <a name="configure-the-sql-database"></a>SQL データベースを構成する

**[データベース名]** に「`myToDoAppDb`」または任意の名前を入力します。

**[接続文字列名]** に「`MyDbConnection`」と入力します。 この名前は、`Models\MyDatabaseContext.cs` で参照されている接続文字列と一致する必要があります。

![SQL Database を構成する](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

### <a name="create-and-publish-the-web-app"></a>Web アプリを作成して発行する

**[作成]**をクリックします。 

ウィザードで Azure リソースの作成が完了すると、Azure への ASP.NET アプリケーションの初回発行が自動的に行われ、発行された Azure Web アプリが既定のブラウザーで開きます。

空のリストに、いくつかの To Do 項目を追加してみてください。

![Azure Web アプリで発行された ASP.NET アプリケーション](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

ご利用ありがとうございます。 データ主導の ASP.NET アプリケーションを Azure App Services でライブ実行することができました。

## <a name="access-the-sql-database-locally"></a>SQL データベースにローカルでアクセスする

Visual Studio では、**SQL Server オブジェクト エクスプローラー**を使用して、新しい SQL データベースの確認と管理が簡単にできます。

### <a name="create-a-database-connection"></a>データベース接続を作成する

`Ctrl`+`\` キーと `Ctrl`+`S` キーを押して、**SQL Server オブジェクト エクスプローラー**を開きます。

**SQL Server オブジェクト エクスプローラー**の上部で、**[SQL Server の追加]** ボタンをクリックします。

### <a name="configure-the-database-connection"></a>データベース接続を構成する

**[接続]** ダイアログで、**Azure** ノードを展開します。 Azure 上のすべての SQL データベースが一覧表示されます。

前に作成した SQL データベースを選択します。 前に使用した接続は、一番下に自動的に入力されます。

前に使用したデータベース管理者のパスワードを入力し、**[接続]** をクリックします。

![Visual Studio からデータベース接続を構成する](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

### <a name="allow-client-connection-from-your-computer"></a>コンピューターからのクライアント接続を許可する

**[新しいファイアウォール規則の作成]** ダイアログが開かれています。 既定では、SQL Server インスタンスはお使いの Azure Web アプリなどの Azure サービスからの接続のみを許可します。 Visual Studio からデータベースに直接接続するには、SQL Server インスタンスでファイアウォール規則を作成し、ローカル コンピューターのパブリック IP アドレスを許可する必要があります。

Visual Studio では、これを簡単に行うことができます。 ダイアログには、既にコンピューターのパブリック IP アドレスが入力されています。

**クライアント IP を追加**するための項目が選択されていることを確認し、**[OK]** をクリックします。 

![SQL Server インスタンスのファイアウォールを設定する](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

Visual Studio で SQL Server インスタンスのファイアウォール設定の作成が完了すると、**SQL Server オブジェクト エクスプローラー**に接続が表示されます。

ここでは、クエリの実行やビューとストアド プロシージャの作成など、最も一般的なデータベース操作を実行できます。 次の例は、データベースのデータを表示する方法を示しています。 

![SQL Database オブジェクトを確認する](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>Code First Migrations を使用してアプリを更新する

この手順では、Entity Framework の Code First Migrations を使用して、データベース スキーマに変更を加え、Azure に発行します。

### <a name="update-your-data-model"></a>データ モデルを更新する

コード エディターで `Models\Todo.cs` を開きます。 `ToDo` クラスに次のプロパティを追加します。

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Code First Migrations をローカルで実行する

次に、いくつかのコマンドを実行して、localdb データベースを更新します。 

**[ツール]** メニューで、**[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** の順にクリックします。 通常、コンソールは下のウィンドウで開きます。

次のように Code First Migrations を有効にします。

```PowerShell
Enable-Migrations
```

次のように移行を追加します。

```PowerShell
Add-Migration AddProperty
```

次のように localdb データベースを更新します。

```PowerShell
Update-Database
```

`F5` キーを押してアプリケーションを実行し、変更をテストします。

エラーが発生せずにアプリケーションが読み込まれたら、Code First Migrations は成功です。 ただし、ページはまだ変わっていないように見えます。これは、この新しいプロパティがまだアプリケーション ロジックで使用されていないためです。 

### <a name="use-the-new-property"></a>新しいプロパティを使用する

`Done` プロパティを使用するために、コードにいくつかの変更を加えます。 このチュートリアルでは、わかりやすくするために `Index` ビューと `Create` ビューのみを変更して、実際のプロパティを確認します。

`Controllers\TodosController.cs`を開きます。

`Create()` メソッドを探し、`Bind` 属性内のプロパティの一覧に `Done` を追加します。 完了すると、`Create()` メソッドのシグネチャは次のようになります。

```csharp
public ActionResult Create([Bind(Include = "id,Description,CreatedDate,Done")] Todo todo)
```

`Views\Todos\Create.cshtml`を開きます。

Razor コードでは、`model.Description` を使用する `<div class="form-group">` タグ、`model.CreatedDate` を使用する別の `<div class="form-group">` タグが表示されます。 これら 2 つのタグの直後に、次のように、`model.Done` を使用する別の `<div class="form-group">` タグを追加します。

```csharp
<div class="form-group">
    @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
    <div class="col-md-10">
        <div class="checkbox">
            @Html.EditorFor(model => model.Done)
            @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
        </div>
    </div>
</div>
```

`Views\Todos\Index.cshtml`を開きます。

空の `<th></th>` タグを探します。 このタグのすぐ上に、次の Razor コードを追加します。

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

`Html.ActionLink()` ヘルパー メソッドを含む `<td>` タグを探します。 このタグのすぐ上に、次の Razor コードを追加します。

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

これだけで、`Index` ビューと `Create` ビューの変更を確認できます。 

再度 `F5` キーを押して、アプリケーションを実行します。

これで、To Do 項目を追加し、**[完了]** チェック ボックスをオンにすることができるようになります。 そうすると、完了済みの項目としてホームページに表示されます。 `Edit` ビューを変更していないため、この時点ではこれしかできませんので、注意してください。

### <a name="enable-code-first-migrations-in-azure"></a>Azure で Code First Migrations を有効にする

データベースの移行を含むコードの変更に成功したので、Azure Web アプリに発行し、SQL Database も Code First Migrations を使用して更新します。

前と同じように、プロジェクトを右クリックし、**[発行]** を選択します。

**[設定]** をクリックし、発行ウィザードを開きます。

![発行の設定を開く](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

ウィザードで **[次へ]** をクリックします。

SQL Database の接続文字列が **[MyDatabaseContext (MyDbConnection)]** に入力されていることを確認します。 ドロップダウン リストから **myToDoAppDb** データベースを選択する必要があります。 

**[Code First Migrations を実行する (アプリケーション開始時に実行)]** チェック ボックスをオンにし、**[保存]** をクリックします。

![Azure Web アプリで Code First Migrations を有効にする](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

### <a name="publish-your-changes"></a>変更を発行する

Azure Web アプリで Code First Migrations を有効にしたので、後はコードの変更を発行するだけです。

発行ページで **[発行]** をクリックします。

再度、新しい To Do 項目を作成してみてください。その後、**[完了]** を選択すると、完了済みの項目としてホームページに表示されます。

![Code First Migration の手順後の Azure Web アプリ](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

> [!NOTE]
> 既存のすべての To Do 項目がまだ表示されていることに注意してください。 ASP.NET アプリケーションを再発行しても、SQL データベースの既存のデータは消失しません。 また、Code First Migrations によって変更されるのはデータ スキーマのみであり、既存のデータはそのまま残されます。
>
>

## <a name="stream-application-logs"></a>アプリケーション ログをストリーミングする

Azure Web アプリから Visual Studio に、トレース メッセージを直接ストリーミングすることができます。

`Controllers\TodosController.cs`を開きます。

各アクションが `Trace.WriteLine()` メソッドから開始されることに注意してください。 このコードは、トレース メッセージを Azure Web アプリに追加することがどれほど簡単であるかを示すために追加されています。

### <a name="open-server-explorer"></a>サーバー エクスプローラーを開く

**サーバー エクスプローラー**で、Azure Web アプリのログ記録を構成できます。 

これを開くには、`Ctrl`+`Alt`+`S` キーを押します。

### <a name="enable-log-streaming"></a>ログ ストリーミングを有効にする

**サーバー エクスプローラー**で、**[Azure]** > **[App Service]** の順に展開します。

最初に Azure Web アプリを作成したときに作成した **myResourceGroup** リソース グループを展開します。

Azure Web アプリを右クリックし、**[ストリーミング ログの表示]** を選択します。

![ログ ストリーミングを有効にする](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

ログが **[出力]** ウィンドウにストリーミングされるようになりました。 

![出力ウィンドウでのログ ストリーミング](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

しかし、トレース メッセージはまだ表示されません。 最初に **[ストリーミング ログの表示]** を選択する際、Azure Web アプリによってトレース レベルが `Error` に設定されるためです。このレベルでは、エラー イベントのみが (`Trace.TraceError()` メソッドで) 記録されます。

### <a name="change-trace-levels"></a>トレース レベルを変更する

トレース レベルを変更して別のトレース メッセージを出力するには、**サーバー エクスプローラー**に戻ります。

Azure Web アプリを再度右クリックし、**[設定]** を選択します。

**[Application Logging (File System) (アプリケーションのログ記録 (ファイル システム))]** ボックスの一覧の **[詳細]** をクリックします。 [ **Save**] をクリックします。

![トレース レベルを [詳細] に変更する](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

> [!TIP]
> 異なるトレース レベルを試しながら、各レベルでどのような種類のメッセージが表示されるかを確認することができます。 たとえば、**[情報]** レベルを指定した場合、`Trace.TraceInformation()`、`Trace.TraceWarning()`、`Trace.TraceError()` で作成されたすべてのログが表示されますが、`Trace.WriteLine()` で作成されたログは除外されます。
>
>

ブラウザーで、Azure の To Do リスト アプリケーションの各所をクリックしてみてください。 Visual Studio の **[出力]** ウィンドウにトレース メッセージがストリーミングされます。

```
Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
```

### <a name="stop-log-streaming"></a>ログ ストリーミングを停止する

ログ ストリーミング サービスを停止するには、**[出力]** ウィンドウの **[監視の停止]** ボタンをクリックします。

![ログ ストリーミングを停止する](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="manage-your-azure-web-app"></a>Azure Web アプリを管理する

Azure Portal に移動し、作成した Web アプリを表示します。 

そのためには、[https://portal.azure.com](https://portal.azure.com) にサインインします。

左側のメニューで **[App Service]** をクリックした後、Azure Web アプリの名前をクリックします。

![Azure Web アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

Web アプリの "_ブレード_" (水平方向に開かれるポータル ページ) が表示されます。 

既定では、Web アプリのブレードは **[概要]** ページを表示します。 このページでは、アプリの動作状態を見ることができます。 ここでは、参照、停止、開始、再開、削除のような基本的な管理タスクも行うことができます。 ブレードの左側にあるタブは、開くことができるさまざまな構成ページを示しています。 

![Azure Portal の App Service ブレード](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

ブレードのこれらのタブは、Web アプリに追加することができるさまざまな優れた機能を示しています。 次の一覧では、ほんの一部の例を示しています。

- カスタム DNS 名をマップする
- カスタム SSL 証明書をバインドする
- 継続的なデプロイを構成する
- スケールアップとスケールアウトを行う
- ユーザー認証を追加する

## <a name="next-steps"></a>次のステップ

事前作成されている [Web アプリの PowerShell スクリプト](app-service-powershell-samples.md)を調べます。
