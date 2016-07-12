<properties
   pageTitle="Get started with sample (サンプルの使用を開始する)"
   description="Power BI Embedded、SDK を使用して対話型の Power BI レポートをビジネス インテリジェンス アプリケーションに追加する"
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="06/28/2016"
   ms.author="owend"/>

# Microsoft Power BI Embedded の使用を開始するためのサンプル

**Microsoft Power BI Embedded プレビュー**を利用して、Web アプリケーションやモバイル アプリケーションに Power BI レポートを統合することができます。このため、ユーザーのためにデータを視覚化するカスタム ソリューションを作成する必要はありません。次のリソースは、アプリへの Power BI レポートの統合を開始するのに役立ちます。

 -	[Sample dashboard web app](http://go.microsoft.com/fwlink/?LinkId=761493)
 -	[Power BI Embedded API reference](https://msdn.microsoft.com/library/mt711493.aspx)
 -	[Power BI Embedded .NET SDK (NuGet から入手可能)](http://go.microsoft.com/fwlink/?LinkId=746472)

この記事では、**Power BI Embedded** の使用を開始するためのサンプルについて説明します。サンプル Web アプリを実行できるように、サンプル アプリの構成を始めましょう。

> [AZURE.NOTE] Azure サブスクリプションに少なくとも 1 つの**ワークスペース コレクション**作成してから、Power BI Embedded の使用を開始するためのサンプルを構成して実行する必要があります。 Azure ポータルで**ワークスペース コレクション**を作成する方法については、「[Getting Started with Power BI Embedded Preview (Power BI Embedded プレビューの使用を開始する)](power-bi-embedded-get-started.md)」をご覧ください。

## サンプル アプリの構成

サンプル アプリの実行に必要なプレビュー コンポーネントにアクセスするための Visual Studio 開発環境の設定手順を以下に説明します。

1. GitHub でサンプル [Power BI Embedded - Integrate a report into a web app](http://go.microsoft.com/fwlink/?LinkId=761493) をダウンロードして解凍します。

2. Visual Studio で **PowerBI-embedded.sln** を開きます。

3. ソリューションをビルドします。

4. **ProvisionSample** コンソール アプリを実行します。サンプルのコンソール アプリで、ワークスペースのプロビジョニングを行って、PBIX ファイルをインポートします。

5. 新しい**ワークスペース**のプロビジョニングを行うには、オプション **[5.Provision a new workspace in an existing workspace collection]** を選択します。

    ![](media\powerbi-embedded-get-started-sample\console-option-5.png)

6. **ワークスペース コレクション**名と**アクセス キー**を入力します。これらを **Azure ポータル**から入手できます。**アクセス キー**を取得する方法については、「Get started with Microsoft Power BI Embedded Preview (Microsoft Power BI Embedded プレビューの使用を開始する)」の「[View Power BI API Access Keys (Power BI API のアクセス キーの表示)](power-bi-embedded-get-started-sample.md#view-access-keys)」 をご覧ください。

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

7. 新しく作成された**ワークスペース ID** をこの記事の後半で使用するためにコピーして保存します。作成された**ワークスペース ID** は **Azure ポータル**で確認できます。

    ![](media\powerbi-embedded-get-started-sample\workspace-id.png)

8. PBIX ファイルを**ワークスペース**にインポートするには、オプション **[6.Import PBIX Desktop file into an existing workspace]** を選択します。PBIX ファイルがない場合は、[Retail Analysis Sample PBIX](http://go.microsoft.com/fwlink/?LinkID=780547) をダウンロードできます。

9. メッセージが表示されたら、分かりやすい**データセット**の名前を入力します。

次のように、応答が表示されます。

````
Checking import state... Publishing
Checking import state... Succeeded
```

> [AZURE.NOTE] PBIX ファイルに直接クエリ接続が含まれている場合は、接続文字列を更新するオプション 7 を実行します。

この時点で、Power BI PBIX レポートは**ワークスペース**にインポートされています。次のセクションには、**Power BI Embedded** の使用を開始するためのサンプル Web アプリを実行する方法が記載されています。 次のセクションでは、サンプル Web アプリを実行する方法を学習します。

## サンプル Web アプリの実行

サンプル Web アプリは、インポートされたレポートを**ワークスペース**にレンダリングするサンプル ダッシュ ボードです。

サンプル Web アプリを構成する方法は以下のとおりです。

1. **PowerBI-embedded** Visual Studio ソリューションで **EmbedSample** Web アプリケーションを右クリックして、**[Set as StartUp project]** を選択します。
2. **EmbedSample** Web アプリケーションの **web.config** で、**[appSettings]** の **[AccessKey]**、**[WorkspaceCollection]** の名前、および **[WorkspaceId]** を編集します。

    ```
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. **EmbedSample** Web アプリケーションを実行します。

**EmbedSample** Web アプリケーションを実行すると、左側のナビゲーション パネルに**[レポート]** メニューが表示されます。インポートしたレポートを表示するには、**[レポート]** を展開して、レポートをクリックします。[Retail Analysis Sample PBIX](http://go.microsoft.com/fwlink/?LinkID=780547) をインポートした場合、サンプル Web アプリは次のように表示されます。

![](media\powerbi-embedded-get-started-sample\power-bi-embedded-sample-left-nav.png)

レポートをクリックすると、**EmbedSample** Web アプリケーションは次のように表示されます。

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)

次のセクションでは、**Power BI Embedded** のサンプル コードを検証します。

## サンプル コードの検証
**Microsoft Power BI Embedded** プレビューのサンプルは、**Power BI** レポートをアプリに統合する方法を説明するダッシュボード Web アプリの例です。このサンプルは、モデル ビュー コント ローラー (MVC) デザイン パターンを使用して、ベスト プラクティスを説明します。このセクションでは、**PowerBI-embedded** Web アプリ ソリューション内で検証できるサンプル コードの部分を説明します。モデル ビュー コント ローラー (MVC) パターンは、ユーザー入力に基づいて、ドメイン、プレゼンテーション、およびアクションのモデリングをモデル、ビュー、およびコントロールという 3 つの個別クラスに分割します。MVC の詳細については、「[Learn About ASP.NET (ASP.NET について)](http://www.asp.net/mvc)」をご覧ください。

**Microsoft Power BI Embedded** プレビューのサンプル コードは次のように区切られます。サンプル内のコードを簡単に見つけることができるように、各セクションには PowerBI-embedded.sln ソリューションのファイル名が含まれます。

> [AZURE.NOTE] このセクションは、コードの作成方法を説明するサンプル コードの概要です。このサンプルの説明は一般公開 (GA) への移行後に拡大されます。完全なサンプルを表示するには、Visual Studio で PowerBI-embedded.sln ソリューションを読み込んでください。

### モデル
サンプルには、**ReportsViewModel** と **ReportViewModel** があります。

**ReportsViewModel.cs**: 複数の Power BI レポートを表します。

    public class ReportsViewModel
    {
        public List<Report> Reports { get; set; }
    }

**ReportViewModel.cs**： 1 つの Power BI レポートを表します。

    public classReportViewModel
    {
        public IReport Report { get; set; }

        public string AccessToken { get; set; }
    }

### 表示
**ビュー**は複数の Power BI **レポート** と 1 つの Power BI **レポート**の表示を制御します。

**Reports.cshtml**: **ActionLink** を作成するために **Model.Reports** で繰り返します。 **ActionLink** は次のように構成されています。

|パート|説明
|---|---
|タイトル| レポートの名前。
|QueryString| レポート ID へのリンク。

    <div id="reports-nav" class="panel-collapse collapse">
        <div class="panel-body">
            <ul class="nav navbar-nav">
                @foreach (var report in Model.Reports)
                {
                    var reportClass = Request.QueryString["reportId"] == report.Id ? "active" : "";
                    <li class="@reportClass">
                        @Html.ActionLink(report.Name, "Report", new { reportId = report.Id })
                    </li>
                }
            </ul>
        </div>
    </div>

Report.cshtml: **Model.AccessToken**、および **PowerBIReportFor** のラムダ式を設定します。

    @model ReportViewModel

    ...

    <div class="side-body padding-top">
        @Html.PowerBIAccessToken(Model.AccessToken)
        @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
    </div>

### Controller

**DashboardController.cs**: **アプリ トークン**を渡す PowerBIClient を作成します。JSON Web トークン (JWT) は、**資格情報**を取得するために**署名キー**から生成されます。 **資格情報** は **PowerBIClient** のインスタンスの作成に使用されます。**アプリ トークン**の詳細については、「[How does app token flow work? (アプリ トークンのフローの動作)](#key-flow)」をご覧ください。**PowerBIClient** のインスタンスを作成したら、GetReports() と GetReportsAsync() を呼び出すことができます。

CreatePowerBIClient()

    private IPowerBIClient CreatePowerBIClient(PowerBIToken token)
    {
        var jwt = token.Generate(accessKey);
        var credentials = new TokenCredentials(jwt, "AppToken");
        var client = new PowerBIClient(credentials)
        {
            BaseUri = new Uri(apiUrl)
        };

        return client;
    }

ActionResult Reports()

    public ActionResult Reports()
    {
        var devToken = PowerBIToken.CreateDevToken(this.workspaceCollection, this.workspaceId);
        using (var client = this.CreatePowerBIClient(devToken))
        {
            var reportsResponse = client.Reports.GetReports(this.workspaceCollection, this.workspaceId);

            var viewModel = new ReportsViewModel
            {
                Reports = reportsResponse.Value.ToList()
            };

            return PartialView(viewModel);
        }
    }


Task<ActionResult> Report(string reportId)

    public async Task<ActionResult> Report(string reportId)
    {
        var devToken = PowerBIToken.CreateDevToken(this.workspaceCollection, this.workspaceId);
        using (var client = this.CreatePowerBIClient(devToken))
        {
            var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
            var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
            var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, Guid.Parse(report.Id));

            var viewModel = new ReportViewModel
            {
                Report = report,
                AccessToken = embedToken.Generate(this.accessKey)
            };

            return View(viewModel);
        }
    }

### アプリへのレポートの統合

**レポート**を作成したら、**IFrame** を使用して Power BI **レポート**を埋め込みます。**Microsoft Power BI Embedded** プレビュー サンプルの powerbi.js からのコード スニペットを次に示します。

![](media\powerbi-embedded-get-started-sample\power-bi-embedded-iframe-code.png)


## アプリケーションに埋め込まれたレポートのフィルター

URL 構文を使用して、埋め込みレポートをフィルター処理できます。そのためには、フィルターを指定して、**eq** 演算子を使って **$filter** クエリ文字列パラメーターを iFrame src url に追加します。フィルター クエリ構文を次に示します。

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [AZURE.NOTE] {tableName/fieldName} には、スペースや特殊文字を含めることはできません。{fieldValue} は単一のカテゴリ値を受け入れます。


## 関連項目

- [Microsoft Power BI Embedded とは](power-bi-embedded-what-is-power-bi-embedded.md)
- [Common Microsoft Power BI Embedded Preview scenarios (Microsoft Power BI Embedded のプレビューの一般的なシナリオ)](power-bi-embedded-scenarios.md)
- [Get started with Microsoft Power BI Embedded Preview (Microsoft Power BI Embedded プレビューの使用を開始する)](power-bi-embedded-get-started.md)
- [About app token flow in Power BI Embedded (Power BI Embedded のアプリ トークン フローについて)](power-bi-embedded-app-token-flow.md)

<!---HONumber=AcomDC_0629_2016-->