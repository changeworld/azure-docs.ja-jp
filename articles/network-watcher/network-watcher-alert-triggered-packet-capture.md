---
title: "パケット キャプチャを使用して Azure Functions によるプロアクティブなネットワーク監視を実行する | Microsoft Docs"
description: "この記事では、Azure Network Watcher を使用して、アラートでトリガーされるパケット キャプチャを作成する方法について説明します"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 6c7a0402a913c836f3248fa6f2f9b27cbf2b0d04
ms.openlocfilehash: a23d569451d2cc776b2e8fc84ec4d01259f74c63
ms.lasthandoff: 02/23/2017


---
# <a name="use-packet-capture-to-do-proactive-network-monitoring-with-azure-functions"></a>パケット キャプチャを使用して Azure Functions によるプロアクティブなネットワーク監視を実行する

Network Watcher のパケット キャプチャを使用すると、仮想マシンとの間で送受信されるトラフィックを追跡するキャプチャ セッションを作成できます。 キャプチャ ファイルには、監視したいトラフィックのみを追跡するために定義したフィルターを設定できます。 このデータは、ストレージ BLOB に格納されるか、ローカルのゲスト マシンに格納されます。 この機能は、Azure Functions と同様に他の自動化のシナリオからリモートで開始できます。 パケット キャプチャを使用すると、定義したネットワークの異常に基づいて、プロアクティブなキャプチャを実行できます。 その他の用途には、ネットワーク統計の収集、ネットワークへの侵入に関する情報の取得などがあり、クライアントとサーバーの間で行われる通信のデバッグなどに役立ちます。

Azure でデプロイされたリソースは、24 時間 365 日実行されています。 その間、お客様とそのスタッフがすべてのリソースの状態を常に監視し続けることはできません。 もし午前 2 時に問題が発生したら、どのように対応すればよいでしょうか。

Azure エコシステム内部から Network Watcher、アラート、関数を使用すれば、問題を解決するためのデータとツールを使ってネットワークに存在する問題にプロアクティブに対応できます。

## <a name="before-you-begin"></a>開始する前に

この例では、VM が送信している TCP セグメントが普段よりも多い場合にアラートが届くようにします。 TCP セグメントが例として使用されていますが、任意のアラート条件を使用することができます。 アラートを受け取ったら、マシンを通常の通信状態に戻すための措置を取れるように、パケット レベルのデータを取得して通信量が増加した理由を把握する必要があります。
このシナリオでは、Network Watcher の既存のインスタンスがあり、有効な仮想マシンが含まれたリソース グループが使用されることを前提としています。

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="scenario"></a>シナリオ

このプロセスを自動化するには、問題が発生した際にトリガーされる VM のアラートと、Network Watcher を呼び出す Azure 関数を作成して接続します。

このシナリオで、以下の作業を行います。

* パケット キャプチャを開始する Azure 関数を作成する
* 仮想マシンのアラート ルールを作成する
* Azure 関数を呼び出すアラート ルールを構成する

## <a name="creating-an-azure-function-and-overview"></a>Azure 関数の作成と概要

最初の手順では、アラートを処理してパケット キャプチャを作成する Azure 関数を作成します。 

次の一覧は、実行されるワークフローの概要です。

1. アラートが VM でトリガーされる。
1. アラートが webhook 経由で Azure 関数を呼び出す。
1. Azure 関数がアラートを処理し、Network Watcher のパケット キャプチャ セッションを開始する。
1. パケット キャプチャが VM で実行され、トラフィックが収集される。 
1. キャプチャ ファイルが確認と診断のためにストレージ アカウントにアップロードされる。 

Azure 関数の作成は、「[初めての Azure 関数の作成](../azure-functions/functions-create-first-azure-function.md)」の手順に従ってポータルで実行できます。 この例では、種類が HttpTrigger-CSharp の関数を使用しました。 

> [!NOTE]
> Azure 関数では他の言語を使用できますが、試験段階であり、PowerShell と Python のように完全にはサポートされていない可能性があります。

## <a name="processing-the-alert-and-starting-a-packet-capture-session"></a>アラートの処理とパケット キャプチャ セッションの開始

次に、Azure 関数の内部から Network Watcher を呼び出します。 この関数の実装は要件によって異なりますが、 コードのフローは一般的に次のようになります。

1. 入力パラメーターを処理する
2. 既存のパケット キャプチャの有無を照会し、制限を確認して、名前の競合を解決する
3. 適切なパラメーターを設定してパケット キャプチャを作成する
4. 完了するまでパケット キャプチャを定期的にポーリングする
5. パケット キャプチャ セッションの完了をユーザーに通知する

次の例は、Azure 関数で使用できる C# です。 サブスクリプション、クライアント ID、テナント ID、クライアント シークレットの値は置き換える必要があります。

```CSharp
using System.Net;
using Newtonsoft;
using Newtonsoft.Json;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.Azure.Management.ResourceManager.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

public static TraceWriter log;
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter twlog)
{
    log = twlog;
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    HttpContent requestContent = req.Content;
    string jsonContent = requestContent.ReadAsStringAsync().Result;
    
    //Deserialize json input
    WebhookInputParameters inParams = JsonConvert.DeserializeObject<WebhookInputParameters>(jsonContent);

    log.Info($@"subscriptionId: {inParams.subscriptionId}  
                networkWatcherResourceGroup: {inParams.networkWatcherResourceGroup} 
                networkWatcherName: {inParams.networkWatcherName} 
                packetCaptureName: {inParams.packetCaptureName} 
                storageID: {inParams.storageID} 
                timeLimit: {inParams.timeLimit} 
                targetVM: {inParams.targetVM}");

    //Get JWT Token
    string token = GetAuthorizationToken();
    
    //Create URI and Delete existing Packet Capture if it exists
    string endpoint = @"https://management.azure.com";
    string PacketCaptureRequestURI = $@"{endpoint}/subscriptions/{inParams.subscriptionId}/resourceGroups/{inParams.networkWatcherResourceGroup}/providers/Microsoft.Network/networkWatchers/{inParams.networkWatcherName}/packetCaptures/{inParams.packetCaptureName}?api-version=2016-03-30";

    //Delete Packet Capture
    HttpWebRequest packetCaptureDelete = Request(PacketCaptureRequestURI, token, "Delete");
    //Errors from delete (404 not found) indicate that the packet capture does not exist already
    HttpWebResponse pcDeleteResponse = getHttpResponse(packetCaptureDelete);

    //Create Packet Capture Request Body
    PacketCaptureRequestBody pcrb = new PacketCaptureRequestBody();
    pcrb.properties.timeLimitInSeconds = inParams.timeLimit;
    pcrb.properties.target = inParams.targetVM;
    pcrb.properties.storageLocation.storageId = inParams.storageID;

    //serialize PacketCaptureRequestBody
    var jSetting = new JsonSerializerSettings();
    jSetting.NullValueHandling = NullValueHandling.Ignore;
    jSetting.Formatting = Formatting.Indented;
    string pcRequestBody = JsonConvert.SerializeObject(pcrb, jSetting);
    
    //Create Packet Capture via PUT 
    HttpWebRequest packetCaptureCreate = Request(PacketCaptureRequestURI, token, "Put", pcRequestBody);
    HttpWebResponse pcPostResponse = getHttpResponse(packetCaptureCreate);
    
    //if packet capture creation successful return to user created storagePath
    if (!(pcPostResponse == null) && pcPostResponse.StatusCode == HttpStatusCode.Created)
    {
        string pcPostResponseLocation = getHeaderContent(pcPostResponse, "Location") ?? "Header null";
        string pcPostResponseContent = httpWebResponseContent(pcPostResponse) ?? "Response Content null";

        //Deserialize response body into object
        PacketCaptureResponseBody pcrbObj = JsonConvert.DeserializeObject<PacketCaptureResponseBody>(pcPostResponseContent);
        return req.CreateResponse(HttpStatusCode.OK, $"Packet Capture successfully created and will upload to StoragePath: {pcrbObj.properties.storageLocation.storagePath}");
    }
    return req.CreateResponse(HttpStatusCode.OK, "Error creating packet capture");
}

// Creates and returns the HTTPWebRequest
public static HttpWebRequest Request(string requestURI, string token, string requestType, string requestContent = null)
{
    
    var httpWebRequest = (HttpWebRequest)WebRequest.Create(requestURI);
    httpWebRequest.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
    httpWebRequest.ContentType = "application/json";
    httpWebRequest.Method = requestType;
    if (requestContent != null)
    {
        httpWebRequest.ContentLength = (requestContent != null) ? requestContent.Length : 0;
    }
    log.Info("Sending Request: " + requestURI + (requestContent ?? ""));

    //ensure requestType is in uppercase
    requestType = requestType.ToUpper();
    if (requestType.Equals("PUT") || requestType.Equals("POST"))
    {
        using (var requestStream = new StreamWriter(httpWebRequest.GetRequestStream()))
        {
            requestStream.Write(requestContent);
        }
    }
    log.Info("Request Sent");
    return httpWebRequest;
}

public static HttpWebResponse getHttpResponse(HttpWebRequest httpWebRequest)
{
    HttpWebResponse httpWebResponse = null;
    try
    {
        httpWebResponse = (HttpWebResponse)httpWebRequest.GetResponse();
    }
    catch (Exception ex)
    {
        log.Info("Error from " + ex.Message, "HttpWebResponsexeption");
    }
    return httpWebResponse;
}

public static string httpWebResponseContent(HttpWebResponse httpWebResponse)
{
    string content = null;
    using (StreamReader reader = new StreamReader(httpWebResponse.GetResponseStream()))
    {
        content = reader.ReadToEnd();
    }
    return content;
}

public static string getHeaderContent(HttpWebResponse httpWebResponse, string param)
{
    log.Info((int)httpWebResponse.StatusCode + "\tStatus Description: " + httpWebResponse.StatusDescription + "\r\n Headers: " + httpWebResponse.Headers);
    return httpWebResponse.Headers.Get(param);
}

//Gets the JWT Token to be able to make REST calls
public static string GetAuthorizationToken()
{
    var tenantId = "<insert tenant id>"; 
    var clientId = "<insert client id>"; 
    var secret = "<insert client secret>";
    var subscriptionId = "<insert subscription id>"; 

    string authContextURL = "https://login.windows.net/" + tenantId;
    var authenticationContext = new AuthenticationContext(authContextURL);
    var credential = new ClientCredential(clientId, secret);
    var result = authenticationContext.AcquireToken(resource: "https://management.azure.com/", clientCredential: credential);
    if (result == null)
    {
        throw new InvalidOperationException("Failed to obtain the JWT token");
    }
    return result.AccessToken;
}

public class WebhookInputParameters
{
    public string subscriptionId { get; set; }
    public string networkWatcherResourceGroup { get; set; }
    public string networkWatcherName { get; set; }
    public string packetCaptureName { get; set; }
    public string storageID { get; set; }
    public int timeLimit { get; set; }
    public string targetVM { get; set; }
}

public class PacketCaptureRequestBody
{
    public PacketCaptureRequestBody()
    {
        properties = new Properties();
    }
    public Properties properties { get; set; }
}

public class StorageLocation
{
    public string storageId { get; set; }
    public string storagePath { get; set; }
    public string filePath { get; set; }
}

public class Filter
{
    public string protocol { get; set; }
    public string localIP { get; set; }
    public string localPort { get; set; }
    public string remoteIP { get; set; }
    public string remotePort { get; set; }
}

public class Properties
{
    public Properties()
    {
        filters = new List<Filter>();
        storageLocation = new StorageLocation();
    }

    public string target { get; set; }
    public int bytestToCapturePerPacket { get; set; }
    public int totalBytesPerSession { get; set; }
    public int timeLimitInSeconds { get; set; }

    public StorageLocation storageLocation { get; set; }
    public List<Filter> filters { get; set; }
}

public class PacketCaptureResponseBody
{
    public PacketCaptureResponseBody()
    {
        properties = new PacketCaptureResponseProperties();
    }
    public string name { get; set; }
    public string id { get; set; }
    public string etag { get; set; }
    public PacketCaptureResponseProperties properties { get; set; }
}

public class PacketCaptureResponseProperties
{
    public PacketCaptureResponseProperties()
    {
        filters = new List<Filter>();
        storageLocation = new StorageLocation();
    }
    public string provisioningState { get; set; }
    public string target { get; set; }
    public int bytesToCapturePerPacket { get; set; }
    public int totalBytesPerSession { get; set; }
    public int timeLimitInSeconds { get; set; }
    public StorageLocation storageLocation { get; set; }
    public List<Filter> filters { get; set; }
    public string captureStartTime { get; set; }
    public string packetCaptureStatus { get; set; }
    public List<object> packetCaptureError { get; set; }
}
``` 

関数を作成したら、その関数に関連付けられた URL を呼び出すようにアラートを構成する必要があります。 この値を取得するには、関数アプリから関数の URL をコピーします。

![関数 URL の検索][2]

webhook POST 要求のペイロードでカスタム プロパティが必要な場合、「[Azure メトリック アラートでの webhook の構成](../monitoring-and-diagnostics/insights-webhooks-alerts.md)」を参照してください。

## <a name="configure-an-alert-on-a-vm"></a>VM のアラートの構成

アラートは、特定のメトリックが割り当て済みのしきい値を超えた場合に個々のユーザーに通知が届くように、構成することができます。 この例のアラートは、送信された TCP セグメントに基づいていますが、多数の他のメトリックを使用してトリガーできます。 この例では、webhook を呼び出して関数を呼び出すようにアラートを構成します。

### <a name="create-the-alert-rule"></a>アラート ルールの作成

既存の仮想マシンに移動してアラート ルールを追加します。 アラートの構成に関する詳細なドキュメントは、[Azure Portal を使用した Azure サービス用のアラートの作成](../monitoring-and-diagnostics/insights-alerts-portal.md)に関するページにあります。 

![仮想マシンへの VM アラート ルールの追加][1]

> [!NOTE]
> 既定では、一部のメトリックは有効になっていません。 追加のメトリックを有効にする方法の詳細については、「[監視と診断の有効化](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md)」を参照してください。

最後に、前の手順で取得した URL をアラートの [webhook] ボックスに貼り付けます。 **[OK]** をクリックして、アラート ルールを保存します。

![アラート ルールへの URL の貼り付け][3]

## <a name="downloading-and-viewing-the-capture-file"></a>キャプチャ ファイルのダウンロードと表示

キャプチャをストレージ アカウントに保存したら、ポータルまたはプログラムを使用してキャプチャ ファイルをダウンロードできます。 キャプチャ ファイルがローカルに格納されている場合は、仮想マシンにログインして取得します。 

Azure ストレージ アカウントからファイルをダウンロードする方法については、「[.NET を使用して Azure BLOB ストレージを使用する](../storage/storage-dotnet-how-to-use-blobs.md)」を参照してください。 使用できるツールとして他にストレージ エクスプローラーがあります。 ストレージ エクスプローラーの詳細については、[ストレージ エクスプローラー](http://storageexplorer.com/)に関するページを参照してください。

ダウンロードしたキャプチャは、**.cap** ファイルを読み取れる任意のツールを使って表示できます。 そのようなツールを&2; つ、次のリンクで紹介します。

[Microsoft Message Analyzer](https://technet.microsoft.com/en-us/library/jj649776.aspx)  
[Wireshark](https://www.wireshark.org/)  

## <a name="next-steps"></a>次のステップ

パケット キャプチャを表示する方法については、[Wireshark によるパケット キャプチャ分析](network-watcher-alert-triggered-packet-capture.md)に関するページを参照してください。

[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png

