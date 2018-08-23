---
title: Azure Video Indexer でビデオのアップロードとインデックス作成を行う | Microsoft Docs
description: このトピックでは、API を使用して Azure Video Indexer でビデオのアップロードとインデックス作成を行う方法を示します。
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 08/17/2018
ms.author: juliako
ms.openlocfilehash: 8a9409c46cac8397bc449c586374729a4d864036
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "41929716"
---
# <a name="upload-and-index-your-videos"></a>ビデオのアップロードとインデックス作成  

この記事では、[Upload video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) API を使用して Azure Video Indexer でビデオのアップロードとインデックス作成を行う方法を示します。 また、API の出力を変更および処理するために API で設定できるいくつかのパラメーターについても説明します。

> [!Note]
> Video Indexer アカウントを作成する場合、無料試用アカウント (一定分数の無料インデックス作成を利用可能) または有料オプション (クォータによる制限がありません) を選択できます。 <br/>無料試用アカウントで Video Indexer 使用すると、Web サイト ユーザーは最大 600 分間の無料インデックス作成、API ユーザーは最大 2,400 分間の無料インデックス作成を利用できます。 <br/>有料オプションでは、[Azure サブスクリプションと Azure Media Services アカウント](connect-to-azure.md)に接続する Video Indexer アカウントを作成します。 Media アカウント関連の料金と同様に、インデックス作成時間 (分単位) の料金がかかります。 

## <a name="configurations-and-params"></a>構成とパラメーター

このセクションでは、いくつかの省略可能なパラメーターと、それらを設定する場合について説明します。

### <a name="externalid"></a>externalID 

このパラメーターでは、ビデオに関連付けられる ID を指定できます。 この ID を、外部の "ビデオ コンテンツ管理" (VCM) システムの統合に適用できます。 指定した外部 ID を使用して、Video Indexer ポータル内にあるビデオを検索できます。

### <a name="indexingpreset"></a>indexingPreset

未加工の録画または外部の録画に背景ノイズが入っている場合は、このパラメーターを使用します。 このパラメーターは、インデックス作成プロセスの構成に使用されます。 次の値を指定できます。

- `Default` – オーディオとビデオの両方を使用して、分析情報のインデックス作成と抽出を行います
- `AudioOnly` – オーディオのみを使用 (ビデオは無視) して、分析情報のインデックス作成と抽出を行います
- `DefaultWithNoiseReduction` – オーディオ ストリームにノイズ低減アルゴリズムを適用しながら、オーディオとビデオの両方からインデックス作成と抽出を行います

料金は、選択したインデックス作成オプションによって異なります。  

### <a name="callbackurl"></a>callbackUrl

インデックス作成が完了したときに通知する POST URL です。 Video Indexer は、これに id と state という 2 つのクエリ文字列を追加します。 たとえば、コールバック URL が 'https://test.com/notifyme?projectName=MyProject' の場合、通知は追加のパラメーターを含めた 'https://test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed' に送信されます。

Video Indexer の呼び出しを POST する前に、さらに多くのパラメーターを URL を追加することもできます。これらのパラメーターはコールバックに含まれます。 後でコードのクエリ文字列を解析して、クエリ文字列に指定されているすべてのパラメーター (最初に URL に追加したデータと、Video Indexer によって提供された情報) を取得できます。 

### <a name="streamingpreset"></a>streamingPreset

ビデオがアップロードされると、Video Indexer は必要に応じてビデオをエンコードします。 その後、インデックス作成とビデオの分析を行います。 Video Indexer が分析を完了すると、ビデオ ID を含んだ通知が送信されます。  

[Upload video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) API または [Re-Index Video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) API を使用するときの省略可能なパラメーターの 1 つに、`streamingPreset` があります。 `streamingPreset` を `Default`、`SingleBitrate`、または `AdaptiveBitrate` に設定すると、エンコード プロセスがトリガーされます。 インデックス作成ジョブとエンコード ジョブが完了すると、ビデオが公開され、ビデオをストリームできるようになります。 ビデオのストリーム元のストリーミング エンドポイントは、**実行中**状態である必要があります。

インデックス作成ジョブとエンコード ジョブを実行するには、[Video Indexer アカウントに接続されている Azure Media Services アカウント](connect-to-azure.md)に予約ユニットが必要です。 詳細については、[メディア処理のスケール設定](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview)に関するページを参照してください。 これらはコンピューティング集中型のジョブであるため、ユニットの種類は S3 にすることを強くお勧めします。 RU の数によって、並列で実行できるジョブの最大数が定義されます。 ベースラインの推奨設定は、10 個の S3 RU です。 

ビデオのインデックス作成のみを行い、エンコードは行わない場合は、`streamingPreset` を `NoStreaming` に設定します。

## <a name="code-sample"></a>サンプル コード

次の C# コード スニペットは、すべての Video Indexer API の使用方法を示しています。

```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var location = "westus2";
    var apiKey = "...";

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    // take the relevant account, here we simply take the first
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Debug.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =newbyte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(newByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video id from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Debug.WriteLine("Uploaded");
    Debug.WriteLine("Video ID:");
    Debug.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

        Debug.WriteLine("");
        Debug.WriteLine("State:");
        Debug.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Debug.WriteLine("");
            Debug.WriteLine("Full JSON:");
            Debug.WriteLine(videoGetIndexResult);
            break;
        }
    }

    // search for the video
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Debug.WriteLine("");
    Debug.WriteLine("Search:");
    Debug.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Debug.WriteLine("Insights Widget url:");
    Debug.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
    Debug.WriteLine("");
    Debug.WriteLine("Player Widget url:");
    Debug.WriteLine(playerWidgetLink);
}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```



## <a name="next-steps"></a>次の手順

[v2 API によって生成される Azure Video Indexer の出力を調べる](video-indexer-output-json-v2.md)
