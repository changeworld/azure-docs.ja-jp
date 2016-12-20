---
title: "サンプルの使用"
description: "Power BI Embedded、SDK を使用して対話型の Power BI レポートをビジネス インテリジェンス アプリケーションに追加する"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: d8a9ef78-ad4e-4bc7-9711-89172dc5c548
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 10/04/2016
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: 830eb6627cae71f358b9790791b1d86f7c82c566
ms.openlocfilehash: 90207623273c3c8f959a07425e1bdf7324649067


---
# <a name="get-started-with-power-bi-embedded-sample"></a>Power BI Embedded の使用を開始するためのサンプル
**Microsoft Power BI Embedded**を使用すると、Web アプリケーションやモバイル アプリケーションに Power BI レポートをすぐに統合することができます。 この記事では、 **Power BI Embedded** の使用を開始するためのサンプルを紹介します。

先に進む前に、次のリソースを保存することができます。 これらのリソースは、サンプル アプリや独自のアプリに Power BI レポートを統合する際に役立ちます。

* [Sample dashboard web app](http://go.microsoft.com/fwlink/?LinkId=761493)
* [Power BI Embedded API reference](https://msdn.microsoft.com/library/mt711493.aspx)
* [Power BI Embedded .NET SDK (NuGet から入手可能) ](http://go.microsoft.com/fwlink/?LinkId=746472)

> [!NOTE]
> Power BI Embedded の使用を開始するためのサンプルを構成して実行するには、Azure サブスクリプションで少なくとも 1 つの **ワークスペース コレクション** 作成する必要があります。 Azure ポータルで **ワークスペース コレクション** を作成する方法については、 [Power BI Embedded の概要](power-bi-embedded-get-started.md)に関する記事をご覧ください。
>
>

## <a name="configure-the-sample-app"></a>サンプル アプリの構成
サンプル アプリの実行に必要なコンポーネントにアクセスするための Visual Studio 開発環境の設定手順を説明します。

1. GitHub でサンプル [Power BI Embedded - Integrate a report into a web app](http://go.microsoft.com/fwlink/?LinkId=761493) をダウンロードして解凍します。
2. Visual Studio で **PowerBI-embedded.sln** を開きます。 NuGet パッケージ マネージャー コンソールで **Update-Package** コマンドを実行して、このソリューションで使用するパッケージを更新する必要のある場合があります。
3. ソリューションをビルドします。
4. **ProvisionSample** コンソール アプリを実行します。 サンプルのコンソール アプリで、ワークスペースのプロビジョニングを行って、PBIX ファイルをインポートします。
5. 新しい**ワークスペース**をプロビジョニングするために、オプション 5 の **[Provision a new workspace in an existing workspace collection (既存のワークスペース コレクションに新しいワークスペースをプロビジョニングする)]** を選択します。

    ![](media\\powerbi-embedded-get-started-sample\\console-option-5.png)
6. **ワークスペース コレクション**名と**アクセス キー**を入力します。 これらは **Azure ポータル**で確認できます。 **アクセス キー**を取得する方法の詳細については、「Microsoft Power BI Embedded の概要」の「 [Power BI API のアクセス キーの表示](power-bi-embedded-get-started.md#view-power-bi-api-access-keys) 」をご覧ください。

    ![](media\\powerbi-embedded-get-started-sample\\azure-portal.png)
7. 新しく作成された **ワークスペース ID** を、この記事の後半で使用するためにコピーして保存します。 作成された**ワークスペース ID** は **Azure Portal** で確認できます。

    ![](media\\powerbi-embedded-get-started-sample\\workspace-id.png)
8. PBIX ファイルを**ワークスペース**にインポートするために、オプション **[6.Import PBIX Desktop file into an existing workspace]** を選択します。 PBIX ファイルがない場合は、[Retail Analysis Sample PBIX](http://go.microsoft.com/fwlink/?LinkID=780547) をダウンロードできます。
9. メッセージが表示されたら、分かりやすい **データセット**名を入力します。

次のように、応答が表示されます。

````
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> If your PBIX file contains any direct query connections, run option 7 to update the connection strings.
>
>

At this point, you have a Power BI PBIX report imported into your **Workspace**. Now, let's look at how to run the **Power BI Embedded** get started sample web app.

## Run the sample web app
The web app sample is a sample dashboard that renders reports imported into your **Workspace**. Here's how to configure the web app sample.

1. In the **PowerBI-embedded** Visual Studio solution, right click the **EmbedSample** web application, and choose **Set as StartUp project**.
2. In **web.config**, in the **EmbedSample** web application, edit the **appSettings**: **AccessKey**, **WorkspaceCollection** name, and **WorkspaceId**.

    ```
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Run the **EmbedSample** web application.

Once you run the **EmbedSample** web application, the left navigation panel should contain a **Reports** menu. To view the report you imported, expand **Reports**, and click a report. If you imported the [Retail Analysis Sample PBIX](http://go.microsoft.com/fwlink/?LinkID=780547), the sample web app would look like this:

![](media\\powerbi-embedded-get-started-sample\\power-bi-embedded-sample-left-nav.png)

After you click a report, the **EmbedSample** web application should look something this:

![](media\\powerbi-embedded-get-started-sample\\sample-web-app.png)

## Explore the sample code
The **Microsoft Power BI Embedded** sample is an example dashboard web app that shows you how to integrate **Power BI** reports into your app. It uses a Model-View-Controller (MVC) design pattern to demonstrate best practices. This section highlights parts of the sample code that you can explore within the **PowerBI-embedded** web app solution. The Model-View-Controller (MVC) pattern separates the modeling of the domain, the presentation, and the actions based on user input into three separate classes: Model, View, and Control. To learn more about MVC, see [Learn About ASP.NET](http://www.asp.net/mvc).

The **Microsoft Power BI Embedded** sample code is separated as follows. Each section includes the file name in the PowerBI-embedded.sln solution so that you can easily find the code in the sample.

> [!NOTE]
> This section is a summary of the sample code that shows how the code was written. To view the complete sample, please load the PowerBI-embedded.sln solution in Visual Studio.
>
>

### Model
The sample has a **ReportsViewModel** and **ReportViewModel**.

**ReportsViewModel.cs**: Represents Power BI Reports.

    public class ReportsViewModel
    {
        public List<Report> Reports { get; set; }
    }

**ReportViewModel.cs**: Represents a Power BI Report.

    public classReportViewModel
    {
        public IReport Report { get; set; }

        public string AccessToken { get; set; }
    }

### Connection string
The connection string must be in the following format:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

Using common server and database attributes will fail. For example: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### View
The **View** manages the display of Power BI **Reports** and a Power BI **Report**.

**Reports.cshtml**: Iterate over **Model.Reports** to create an **ActionLink**. The **ActionLink** is composed as follows:

| Part | Description |
| --- | --- |
| Title |Name of the Report. |
| QueryString |A link to the Report ID. |

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

Report.cshtml: Set the **Model.AccessToken**, and the Lambda expression for **PowerBIReportFor**.

    @model ReportViewModel

    ...

    <div class="side-body padding-top">
        @Html.PowerBIAccessToken(Model.AccessToken)
        @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
    </div>

### Controller
**DashboardController.cs**: Creates a PowerBIClient passing an **app token**. A JSON Web Token (JWT) is generated from the **Signing Key** to get the **Credentials**. The **Credentials** are used to create an instance of **PowerBIClient**. Once you have an instance of **PowerBIClient**, you can call GetReports() and GetReportsAsync().

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

### Integrate a report into your app
Once you have a **Report**, you use an **IFrame** to embed the Power BI **Report**. Here is a code snippet from  powerbi.js in the **Microsoft Power BI Embedded** sample.

![](media\\powerbi-embedded-get-started-sample\\power-bi-embedded-iframe-code.png)

## Filter reports embedded in your application
You can filter an embedded report using a URL syntax. To do this, you add a **$filter** query string parameter with an **eq** operator to your iFrame src url with the filter specified. Here is the filter query syntax:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [!NOTE]
> {tableName/fieldName} cannot include spaces or special characters. The {fieldValue} accepts a single categorical value.  
>
>

## See also
* [Common Microsoft Power BI Embedded scenarios](power-bi-embedded-scenarios.md)
* [Authenticating and authorizing in Power BI Embedded](power-bi-embedded-app-token-flow.md)



<!--HONumber=Nov16_HO3-->


