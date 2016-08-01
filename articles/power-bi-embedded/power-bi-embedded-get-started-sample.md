<properties
   pageTitle="サンプルの使用"
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
   ms.date="07/14/2016"
   ms.author="owend"/>

# Power BI Embedded の使用を開始するためのサンプル

**Microsoft Power BI Embedded** を使用すると、Web アプリケーションやモバイル アプリケーションに Power BI レポートをすぐに統合することができます。この記事では、**Power BI Embedded** の使用を開始するためのサンプルを紹介します。

先に進む前に、次のリソースを保存することができます。これらのリソースは、サンプル アプリや独自のアプリに Power BI レポートを統合する際に役立ちます。

 -	[Sample dashboard web app](http://go.microsoft.com/fwlink/?LinkId=761493)
 -	[Power BI Embedded API reference](https://msdn.microsoft.com/library/mt711493.aspx)
 -	[Power BI Embedded .NET SDK (NuGet から入手可能)](http://go.microsoft.com/fwlink/?LinkId=746472)



> [AZURE.NOTE] Azure サブスクリプションに少なくとも 1 つの**ワークスペース コレクション**作成してから、Power BI Embedded の使用を開始するためのサンプルを構成して実行する必要があります。 Azure ポータルで**ワークスペース コレクション**を作成する方法については、[Power BI Embedded の概要](power-bi-embedded-get-started.md)に関する記事をご覧ください。

## サンプル アプリの構成

サンプル アプリの実行に必要なコンポーネントにアクセスするための Visual Studio 開発環境の設定手順を説明します。

1. GitHub でサンプル [Power BI Embedded - Integrate a report into a web app](http://go.microsoft.com/fwlink/?LinkId=761493) をダウンロードして解凍します。

2. Visual Studio で **PowerBI-embedded.sln** を開きます。

3. ソリューションをビルドします。

4. **ProvisionSample** コンソール アプリを実行します。サンプルのコンソール アプリで、ワークスペースのプロビジョニングを行って、PBIX ファイルをインポートします。

5. 新しい**ワークスペース**をプロビジョニングするには、オプション 5 の **[Provision a new workspace in an existing workspace collection (既存のワークスペース コレクションに新しいワークスペースをプロビジョニングする)]** を選択します。

    ![](media\powerbi-embedded-get-started-sample\console-option-5.png)

6. **ワークスペース コレクション**名と**アクセス キー**を入力します。これらを **Azure ポータル**から入手できます。**アクセス キー**を取得する方法の詳細については、「Get started with Microsoft Power BI Embedded (Microsoft Power BI Embedded の概要)」の「[View Power BI API Access Keys (Power BI API のアクセス キーの表示)](power-bi-embedded-get-started-sample.md#view-access-keys)」をご覧ください。

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

この時点で、Power BI PBIX レポートは**ワークスペース**にインポートされています。次に、**Power BI Embedded** の使用を開始するためのサンプル Web アプリを実行する方法を説明します。

## サンプル Web アプリの実行

サンプル Web アプリは、インポートされたレポートを**ワークスペース**にレンダリングするサンプル ダッシュ ボードです。サンプル Web アプリを構成する方法は以下のとおりです。

1. **PowerBI-embedded** Visual Studio ソリューションで、**EmbedSample** Web アプリケーションを右クリックし、**[スタートアップ プロジェクトに設定]** を選択します。
2. **EmbedSample** Web アプリケーションの **web.config** で、**appSettings** の **AccessKey**、**WorkspaceCollection** の名前、**WorkspaceId** を編集します。

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

レポートをクリックすると、**EmbedSample** Web アプリケーションが次のように表示されます。

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)


## サンプル コードの検証
**Microsoft Power BI Embedded** のサンプルは、**Power BI** レポートをアプリに統合する方法を示すダッシュボード Web アプリの例です。このサンプルは、モデル ビュー コント ローラー (MVC) デザイン パターンを使用して、ベスト プラクティスを説明します。このセクションでは、**PowerBI-embedded** Web アプリ ソリューション内で検証できるサンプル コードの部分を説明します。モデル ビュー コント ローラー (MVC) パターンは、ユーザー入力に基づいて、ドメイン、プレゼンテーション、およびアクションのモデリングをモデル、ビュー、およびコントロールという 3 つの個別クラスに分割します。MVC の詳細については、「[Learn About ASP.NET (ASP.NET について)](http://www.asp.net/mvc)」をご覧ください。

**Microsoft Power BI Embedded** のサンプル コードは以下のように区切られています。サンプル内のコードを簡単に見つけることができるように、各セクションには PowerBI-embedded.sln ソリューションのファイル名が含まれます。

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
**ビュー**では、複数の Power BI **レポート** と 1 つの Power BI **レポート**の表示を管理します。

**Reports.cshtml**: **ActionLink** を作成するために **Model.Reports** を反復処理します。**ActionLink** は次のように構成されています。

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

Report.cshtml: **Model.AccessToken** と、**PowerBIReportFor** のラムダ式を設定します。

    @model ReportViewModel

    ...

    <div class="side-body padding-top">
        @Html.PowerBIAccessToken(Model.AccessToken)
        @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
    </div>

### Controller

**DashboardController.cs**: **アプリ トークン**を渡す PowerBIClient を作成します。JSON Web トークン (JWT) は、**資格情報**を取得するために**署名キー**から生成されます。**資格情報**は、**PowerBIClient** のインスタンスの作成に使用されます。**PowerBIClient** のインスタンスを作成したら、GetReports() と GetReportsAsync() を呼び出すことができます。

CreatePowerBIClient()

    private IPowerBIClient CreatePowerBIClient()
    {
        var credentials = new TokenCredentials(accessKey, "AppKey");
        var client = new PowerBIClient(credentials)
        {
            BaseUri = new Uri(apiUrl)
        };

        return client;
    }

ActionResult Reports()

    public ActionResult Reports()
    {
        using (var client = this.CreatePowerBIClient())
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
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
            var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
            var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

            var viewModel = new ReportViewModel
            {
                Report = report,
                AccessToken = embedToken.Generate(this.accessKey)
            };

            return View(viewModel);
        }
    }

### アプリへのレポートの統合

**レポート**を作成したら、**IFrame** を使用して Power BI **レポート**を埋め込みます。**Microsoft Power BI Embedded** のサンプルの powerbi.js のコード スニペットを次に示します。

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

- [Common Microsoft Power BI Embedded scenarios (Microsoft Power BI Embedded の一般的なシナリオ)](power-bi-embedded-scenarios.md)
- [Power BI Embedded での認証と承認](power-bi-embedded-app-token-flow.md)

<!---HONumber=AcomDC_0720_2016-->