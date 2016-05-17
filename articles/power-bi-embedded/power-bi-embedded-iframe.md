<properties
   pageTitle="Microsoft Power BI Embedded プレビュー - IFrame を使用した Power BI レポートの埋め込み"
   description="Microsoft Power BI Embedded プレビュー - レポートをアプリに統合するための基本コード、Power BI Embedded アプリ トークンの認証方法、レポートの取得方法"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="05/06/2016"
   ms.author="derrickv"/>

# IFrame を使用した Power BI レポートの埋め込み
この記事では、**Power BI Embedded** REST API、アプリ トークン、IFrame、およびいくつかの JavaScript を使用して、アプリへのレポートの統合または埋め込みを行うための基本コードについて説明します。

「[Microsoft Power BI Embedded プレビューを使ってみる](power-bi-embedded-get-started.md)」では、レポート コンテンツ用の 1 つ以上の**ワークスペース**を保持する**ワークスペース コレクション**を構成する方法について説明します。その後、「[Microsoft Power BI Embedded の使用を開始するためのサンプル](power-bi-embedded-get-started-sample.md)」で、レポートを**ワークスペース**にインポートします。

この記事では、レポートをアプリに埋め込む手順について説明します。この記事の手順を実行するには、GitHub で [IFrame を使用したレポートの統合](https://github.com/Azure-Samples/power-bi-embedded-iframe)サンプルをダウンロードする必要があります。このサンプルは、レポートを統合するために必要となる C# と JavaScript の基本コードを示すことを目的とした、簡単な ASP.NET Web フォーム アプリです。レポートの統合にモデル ビュー コントローラー (MVC) 設計パターンを使用する、さらに高度なサンプルについては、GitHub の[ダッシュボード Web アプリのサンプル](http://go.microsoft.com/fwlink/?LinkId=761493)を参照してください。

それでは、**Power BI Embedded** レポートをアプリに統合する方法の説明を開始しましょう。

レポートを統合するための手順を次に示します。

- 手順 1. [ワークスペースでレポートを取得する](#GetReport)。この手順では、アクセス トークンを取得して [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx) REST 操作を呼び出すために、アプリ トークン フローを使用します。**Get Reports** リストからレポートを取得したら、**IFrame** 要素を使用してレポートをアプリに埋め込みます。
- 手順 2. [レポートをアプリに埋め込む](#EmbedReport)。この手順では、レポートの埋め込みトークン、いくつかの JavaScript、および IFrame を使用して、レポートを Web アプリに統合します (つまり、埋め込みます)。

サンプルを実行してレポートの統合方法を確認する場合は、GitHub で [IFrame を使用したレポートの統合](https://github.com/Azure-Samples/power-bi-embedded-iframe)サンプルをダウンロードし、次の 3 つの Web.Config 設定を構成します。

- **AccessKey**: **AccessKey** は、レポートの取得と埋め込みに使用される JSON Web トークン (JWT) を生成するために使用します。**AccessKey** を取得する方法については、「[Microsoft Power BI Embedded プレビューを使ってみる](power-bi-embedded-get-started.md)」を参照してください。
- **WorkspaceName**: **WorkspaceName** を取得する方法については、「[Microsoft Power BI Embedded プレビューを使ってみる](power-bi-embedded-get-started.md)」を参照してください。
- **WorkspaceId**: **WorkspaceId** を取得する方法については、「[Microsoft Power BI Embedded プレビューを使ってみる](power-bi-embedded-get-started.md)」を参照してください。

次のセクションでは、レポートの統合に必要なコードについて説明します。

<a name="GetReport"/>
## ワークスペースでレポートを取得する

レポートをアプリに統合するには、レポートの **ID** と **embedUrl** が必要です。レポートの **ID** と **embedUrl** を取得するには、[Get Reports](https://msdn.microsoft.com/library/mt711510.aspx) REST 操作を呼び出し、JSON リストからレポートを選択します。「[レポートをアプリに埋め込む](#EmbedReport)」では、レポートの **ID** と **embedUrl** を使用して、レポートをアプリに埋め込みます。

### レポートの JSON 応答を取得する
```
{
  "@odata.context":"https://api.powerbi.com/beta/collections/{WorkspaceName}/workspaces/{WorkspaceId}/$metadata#reports","value":[
    {
      "id":"804d3664-…-e71882055dba","name":"Import report sample","webUrl":"https://embedded.powerbi.com/reports/804d3664-...-e71882055dba","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=804d3664-...-e71882055dba"
    },{
      "id":"1d7cff58-…-380610e263a9","name":"Sample Report 2","webUrl":"https://embedded.powerbi.com/reports/1d7cff58-...-380610e263a9","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=1d7cff58-...-380610e263a9"
    }
  ]
}

```

[Get Reports](https://msdn.microsoft.com/library/mt711510.aspx) REST 操作を呼び出すには、アプリ トークンを使用します。アプリ トークン フローの詳細については、「[Power BI Embedded のアプリケーション トークン フローについて](power-bi-embedded-app-token-flow.md)」を参照してください。次のコードは、レポートの JSON リストを取得する方法を表しています。レポートを埋め込むには、「[レポートをアプリに埋め込む](#EmbedReport)」を参照してください。

```
protected void getReportsButton_Click(object sender, EventArgs e)
{
    //Get an app token to generate a JSON Web Token (JWT). An app token flow is a claims-based design pattern.
    //To learn how you can code an app token flow to generate a JWT, see the PowerBIToken class.
    var appToken = PowerBIToken.CreateDevToken(workspaceName, workspaceId);

    //After you get a PowerBIToken which has Claims including your WorkspaceName and WorkspaceID,
    //you generate JSON Web Token (JWT) . The Generate() method uses classes from System.IdentityModel.Tokens: SigningCredentials,
    //JwtSecurityToken, and JwtSecurityTokenHandler.
    string jwt = appToken.Generate(accessKey);

    //Set app token textbox to JWT string to show that the JWT was generated
    appTokenTextbox.Text = jwt;

    //Construct reports uri resource string
    var uri = String.Format("https://api.powerbi.com/beta/collections/{0}/workspaces/{1}/reports", workspaceName, workspaceId);

    //Configure reports request
    System.Net.WebRequest request = System.Net.WebRequest.Create(uri) as System.Net.HttpWebRequest;
    request.Method = "GET";
    request.ContentLength = 0;

    //Set the WebRequest header to AppToken, and jwt
    //Note the use of AppToken instead of Bearer
    request.Headers.Add("Authorization", String.Format("AppToken {0}", jwt));

    //Get reports response from request.GetResponse()
    using (var response = request.GetResponse() as System.Net.HttpWebResponse)
    {
        //Get reader from response stream
        using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
        {
            //Deserialize JSON string into PBIReports
            PBIReports PBIReports = JsonConvert.DeserializeObject<PBIReports>(reader.ReadToEnd());

            //Clear Textbox
            tb_reportsResult.Text = string.Empty;

            //Get each report
            foreach (PBIReport rpt in PBIReports.value)
            {
                tb_reportsResult.Text += String.Format("{0}\t{1}\t{2}\n", rpt.id, rpt.name, rpt.embedUrl);
            }
        }
    }
}
```

<a name="EmbedReport"/>
## レポートをアプリに埋め込む

レポートをアプリに埋め込むには、レポートの埋め込みトークンが必要です。このトークンは、**Power BI Embedded** REST 操作の呼び出しに使用されるアプリ トークンに似たものですが、REST リソースではなくレポート リソースに対して生成されます。レポートのアプリ トークンを取得するコードを次に示します。レポートのアプリ トークンを使用するには、「[レポートをアプリに埋め込む](#EmbedReportJS)」を参照してください。

<a name="EmbedReportToken"/>
### レポートのアプリ トークンを取得する

```
protected void getReportAppTokenButton_Click(object sender, EventArgs e)
{
    //Get an embed token for a report.
    var token = PowerBIToken.CreateReportEmbedToken(workspaceName, workspaceId, getReportAppTokenTextBox.Text);

    //After you get a PowerBIToken which has Claims including your WorkspaceName and WorkspaceID,
    //you generate JSON Web Token (JWT) . The Generate() method uses classes from System.IdentityModel.Tokens: SigningCredentials,
    //JwtSecurityToken, and JwtSecurityTokenHandler.
    string jwt = token.Generate(accessKey);

    //Set textbox to JWT string. The JavaScript embed code uses the embed jwt for a report.
    reportAppTokenTextbox.Text = jwt;
}
```

<a name="EmbedReportJS"/>
### レポートをアプリに埋め込む

**Power BI** レポートをアプリに埋め込むには、IFrame といくつかの JavaScript コードを使用します。IFrame の例と、レポートを埋め込むための JavaScript コードを次に示します。レポートを埋め込むためのサンプル コードすべてを確認するには、GitHub にある [IFrame を使用したレポートの統合](https://github.com/Azure-Samples/power-bi-embedded-iframe)サンプルを参照してください。

![Iframe](media\power-bi-embedded-integrate-report\Iframe.png)


```
window.onload = function () {
    // Client side click to embed a selected report.
    var el = document.getElementById("bEmbedReportAction");
    if (el.addEventListener) {
        el.addEventListener("click", updateEmbedReport, false);
    } else {
        el.attachEvent('onclick', updateEmbedReport);
    }

};

// Update embed report
function updateEmbedReport() {
    // Check if the embed url was selected
    var embedUrl = document.getElementById('tb_EmbedURL').value;

    // To load a report do the following:
    // 1: Set the url
    // 2: Add a onload handler to submit the auth token
    var iframe = document.getElementById('iFrameEmbedReport');
    iframe.src = embedUrl;
    iframe.onload = postActionLoadReport;
}

// Post the auth token to the iFrame.
function postActionLoadReport() {

    // Get the app token.
    accessToken = document.getElementById('MainContent_reportAppTokenTextbox').value;

    // Construct the push message structure
    var m = { action: "loadReport", accessToken: accessToken };
    message = JSON.stringify(m);

    // Push the message.
    iframe = document.getElementById('iFrameEmbedReport');
    iframe.contentWindow.postMessage(message, "*");
}
```

この記事では、**Power BI** レポートをアプリに統合するためのコードを紹介しました。アプリへのレポートの統合をすぐに開始するには、次に示すサンプルを GitHub でダウンロードしてください。

- [IFrame を使用したレポートの統合サンプル](https://github.com/Azure-Samples/power-bi-embedded-iframe)
- [Sample dashboard web app](http://go.microsoft.com/fwlink/?LinkId=761493)

## 関連項目
- [Get started with Microsoft Power BI Embedded Preview (Microsoft Power BI Embedded プレビューの使用を開始する)](power-bi-embedded-get-started.md)
- [Get started with Microsoft Power BI Embedded sample (Microsoft Power BI Embedded のサンプルの使用)](power-bi-embedded-get-started-sample.md)
- [System.IdentityModel.Tokens.SigningCredentials](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)
- [System.IdentityModel.Tokens.JwtSecurityToken](https://msdn.microsoft.com/library/system.identitymodel.tokens.jwtsecuritytoken.aspx)
- [System.IdentityModel.Tokens.JwtSecurityTokenHandler](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)
- [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx)

<!---HONumber=AcomDC_0511_2016-->