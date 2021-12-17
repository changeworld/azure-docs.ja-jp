---
title: Azure Video Analyzer for Media (旧称 Video Indexer) を使用してビデオのアップロードとインデックスの作成を行う
description: このトピックでは、Azure Video Analyzer for Media (旧称 Video Indexer) で API を使用して、ビデオのアップロードとインデックスの作成を行う方法を示します。
ms.service: azure-video-analyzer
ms.topic: article
ms.date: 11/15/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f2a9aa291937afcb55739cfdb4532d0a59f2bc9f
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519593"
---
# <a name="upload-and-index-your-videos"></a>ビデオのアップロードとインデックス作成  

ビデオがアップロードされると、Azure Video Analyzer for Media (旧称 Video Indexer) は (必要に応じて) ビデオをエンコードします (この記事で説明します)。 Video Analyzer for Media アカウントを作成するときに、無料試用アカウント (一定分数の無料インデックス作成を利用可能) または有料オプション (クォータによる制限なし) を選択できます。 無料試用アカウントで Video Analyzer for Media 使用する場合、Web サイト ユーザーは最大 600 分間の無料インデックス作成を利用でき、API ユーザーは最大 2,400 分間の無料インデックス作成を利用できます。 有料オプションでは、[ご使用の Azure サブスクリプションと Azure Media Services アカウントに接続される](connect-to-azure.md) Video Analyzer for Media アカウントを作成します。 インデックス作成にかかった時間 (分) に対して支払います。詳細については、「[Media Services の価格](https://azure.microsoft.com/pricing/details/media-services/)」を参照してください。

Video Analyzer for Media の API を用いてビデオをアップロードする場合、次のアップロードオプションが利用できます。 

* URL からビデオをアップロードする方法 (推奨) と、
* 要求本文のバイト配列としてビデオ ファイルを送信する方法です。
* [アセット ID](../../media-services/latest/assets-concept.md) (有料アカウントでのみサポート) を指定して、既存の Azure Media Services アセットを使用します。

この記事では、次のオプションを使用してビデオをアップロードし、インデックスを作成する方法について説明します。

* Video Analyzer for Media の Web サイト 
* Video Analyzer for Media の API 

## <a name="supported-file-formats-for-video-analyzer-for-media"></a>Video Analyzer for Media でサポートされるファイル形式

Video Analyzer for Media で使用できるファイル形式の一覧については、「[入力コンテナー/ファイル形式](../../media-services/latest/encode-media-encoder-standard-formats-reference.md)」を参照してください。

## <a name="video-files-storage"></a>ビデオ ファイルのストレージ

- 有料の Video Analyze for Media アカウントでは、ご利用の Azure サブスクリプションと Azure Media Services アカウントに接続される Video Analyzer for Media アカウントを作成します。 詳細については、「[Azure に接続された Video Analyzer for Media アカウントを作成する](connect-to-azure.md)」を参照してください。
- ビデオ ファイルは、Azure Media Services によって Azure storage に格納されます。 時間に制限はありません。
- ビデオおよびオーディオのファイルだけでなく、Video Analyzer for Media によってそれらから抽出されたメタデータや分析情報も削除することができます。 Video Analyzer for Media からファイルを削除すると、そのファイルとそのメタデータおよび分析情報は Video Analyzer for Media から完全に削除されます。 ただし、Azure ストレージに独自のバックアップ ソリューションを実装している場合、ファイルは Azure ストレージに残ります。
- アップロードが Video Analyzer for Media の Web サイトで行われるか、Upload API を使用して行われるかに関係なく、ビデオの永続性は同じです。
   
## <a name="upload-and-index-a-video-using-the-video-analyzer-for-media-website"></a>Video Analyzer for Media の Web サイトを使用してビデオのアップロードとインデックスの作成を行う

> [!NOTE]
> ビデオの名前は、80 文字以下にする必要があります。

1. [Video Analyzer for Media](https://www.videoindexer.ai/) Web サイトにサインインします。
1. ビデオをアップロードするには、 **[アップロード]** ボタンまたはリンクを押します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="アップロード":::
1. ビデオがアップロードされると、Video Analyzer for Media によってビデオのインデックス作成と分析が開始されます。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="アップロードの進行状況":::
1. Video Analyzer for Media で分析が完了すると、ビデオへのリンクとビデオの内容の簡単な説明が記載されたメールが届きます。 たとえば、人物、トピックス、OCR などが表示されます。

## <a name="upload-and-index-with-api"></a>API を使用したアップロードとインデックス作成

[Upload Video](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) API を使用して、URL に基づいてビデオのアップロードとインデックス作成を行います。 後述のコード サンプルには、バイト配列をアップロードする方法を示すコメント アウトされたコードが含まれています。 

### <a name="configurations-and-params"></a>構成とパラメーター

このセクションでは、いくつかの省略可能なパラメーターと、それらを設定する場合について説明します。 最新のパラメーター情報については、[ビデオのアップロード](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)の API に関するページを参照してください。

#### <a name="externalid"></a>externalID 

このパラメーターでは、ビデオに関連付けられる ID を指定できます。 この ID を、外部の "ビデオ コンテンツ管理" (VCM) システムの統合に適用できます。 指定した外部 ID を使用して、Video Analyzer for Media ポータル内にあるビデオを検索できます。

#### <a name="callbackurl"></a>callbackUrl

[!INCLUDE [callback url](./includes/callback-url.md)]

##### <a name="other-considerations"></a>その他の考慮事項

- Video Analyzer for Media では、元の URL で指定された既存のすべてのパラメーターが返されます。
- 指定される URL は、エンコードする必要があります。

#### <a name="indexingpreset"></a>indexingPreset

このパラメーターを使用して、オーディオ ファイルまたはビデオ ファイルに適用する AI バンドルを定義します。 このパラメーターは、インデックス作成プロセスの構成に使用されます。 次の値を指定できます。

- `AudioOnly` – オーディオのみを使用 (ビデオは無視) して、分析情報のインデックス作成と抽出を行います。
- `VideoOnly` – ビデオのみを使用 (オーディオは無視) して、分析情報のインデックス作成と抽出を行います。
- `Default` - オーディオとビデオの両方を使用して、分析情報のインデックス作成と抽出を行います。
- `DefaultWithNoiseReduction` - オーディオ ストリームにノイズ低減アルゴリズムを適用しながら、オーディオとビデオの両方から分析情報のインデックス作成と抽出を行います。

    `DefaultWithNoiseReduction` 値が既定のプリセット (非推奨) にマップされるようになりました。
- `BasicAudio` - オーディオのみ (文字起こし、翻訳、出力キャプションの書式設定、字幕などの基本的なオーディオ機能のみ) を使用 (ビデオは無視) して、分析情報のインデックス作成と抽出を行います。
- `AdvancedAudio` -標準のオーディオ分析に加え、高度なオーディオ機能 (オーディオ イベントの検出) など、オーディオのみを使用 (ビデオは無視) して、分析情報のインデックス作成と抽出を行います。
- `AdvancedVideo` - 標準のビデオ分析に加えて、高度なビデオ機能 (観察対象人物追跡) を含むビデオのみを使用 (オーディオは無視) して、分析情報のインデックス作成と抽出を行います。
- `AdvancedVideoAndAudio` - 高度なオーディオと高度なビデオ分析の両方を使用して、分析情報のインデックス作成と抽出を行います。 

> [!NOTE]
> 高度なプリセット (上記の一覧) には、パブリック プレビュー段階のモデルが含まれます。 これらのモデルが一般提供になると、価格に影響を及ぼす可能性があります。 

Video Indexer では、最大 2 つのオーディオ トラックがカバーされます。 ファイル内にこれより多いオーディオ トラックがある場合、それらは 1 つのトラックとして扱われます。<br/>
トラックに個別にインデックスを付ける場合は、関連するオーディオ ファイルを抽出し、それに `AudioOnly` とインデックスを付ける必要があります。

料金は、選択したインデックス作成オプションによって異なります。 詳細については、[Media Services の価格](https://azure.microsoft.com/pricing/details/media-services/)に関する記事を参照してください。

#### <a name="priority"></a>priority

ビデオには、優先度に従って、Video Analyzer for Media によってインデックスが付けられます。 インデックスの優先度を指定するには、**priority** パラメーターを使用します。 有効な値は、**Low**、**Normal** (既定)、**High** です。

**Priority** パラメーターは、有料アカウントだけでサポートされています。

#### <a name="streamingpreset"></a>streamingPreset

ビデオがアップロードされると、Video Analyzer for Media では、必要に応じてビデオがエンコードされます。 その後、インデックス作成とビデオの分析を行います。 Video Analyzer for Media が分析を完了すると、ビデオ ID を含む通知が表示されます。  

[Upload video](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) API または [Re-Index Video](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) API を使用するときの省略可能なパラメーターの 1 つに、`streamingPreset` があります。 `streamingPreset` を `Default`、`SingleBitrate`、または `AdaptiveBitrate` に設定すると、エンコード プロセスがトリガーされます。 インデックス作成ジョブとエンコード ジョブが完了すると、ビデオが公開され、ビデオをストリームできるようになります。 ビデオのストリーム元のストリーミング エンドポイントは、**実行中** 状態である必要があります。

SingleBitrate の場合、出力ごとに Standard Encoder コストが適用されます。 ビデオの高さが 720 以上の場合、Video Analyzer for Media によって 1280 x 720 としてエンコードされます。 それ以外の場合は、640 x 468 と指定されます。
既定の設定は、[コンテンツに対応したエンコード](../../media-services/latest/encode-content-aware-concept.md)です。

インデックス作成とエンコードのジョブを実行するには、[Video Analyzer for Media アカウントに接続されている Azure Media Services アカウント](connect-to-azure.md)に予約ユニットが必要です。 詳細については、[メディア処理のスケール設定](../../media-services/previous/media-services-scale-media-processing-overview.md)に関するページを参照してください。 これらはコンピューティング集中型のジョブであるため、ユニットの種類は S3 にすることを強くお勧めします。 RU の数によって、並列で実行できるジョブの最大数が定義されます。 ベースラインの推奨設定は、10 個の S3 RU です。 

ビデオのインデックス作成のみを行い、エンコードは行わない場合は、`streamingPreset` を `NoStreaming` に設定します。

#### <a name="videourl"></a>videoUrl

インデックスを作成するビデオ/音声ファイルの URL。 URL はメディア ファイルを示している必要があります (HTML ページはサポートされていません)。 このファイルは URI の一部として提供されるアクセス トークンで保護することができます。また、ファイルを提供するエンドポイントは TLS 1.2 以降を使用してセキュリティで保護する必要があります。 URL はエンコードする必要があります。 

`videoUrl` が指定されていない場合、Video Analyzer for Media はファイルが multipart/form 本文のコンテンツとして渡されると想定します。

### <a name="code-sample"></a>コード サンプル

次の C# コード スニペットは、Video Analyzer for Media のすべての API の使用方法を示しています。

### <a name="classic-account"></a>[クラシック アカウント名](#tab/With-classic-account/)

このコードをお使いの開発プラットフォームにコピーした後、次の 2 つのパラメーターを指定する必要があります。API Management 認証キーとビデオの URL。

* API キー – API キーは、個人の API Management サブスクリプション キーです。これにより、Video Analyzer for Media アカウントで操作を実行するためのアクセス トークンを取得できます。 

    API キーを取得するには、次のフローを実行します。

    * https://api-portal.videoindexer.ai/ に移動します
    * ログイン
    * **[製品]**  ->  **[承認]**  ->  **[[Authorization subscription]\(承認サブスクリプション\)]** に移動します。
    * **主キー** をコピーします。
* ビデオの URL – インデックスを作成するビデオ/音声ファイルの URL。 URL はメディア ファイルを示している必要があります (HTML ページはサポートされていません)。 このファイルは URI の一部として提供されるアクセス トークンで保護することができます。また、ファイルを提供するエンドポイントは TLS 1.2 以降を使用してセキュリティで保護する必要があります。 URL はエンコードする必要があります。

コード サンプルを正常に実行した結果には、分析情報ウィジェットの URL とプレーヤー ウィジェットの URL が含まれます。これにより、アップロードされた分析情報とビデオをそれぞれ調べることができます。 


```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var apiKey = "..."; // replace with API key taken from https://aka.ms/viapi

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
    
    // take the relevant account, here we simply take the first, 
    // you can also get the account via accounts.First(account => account.Id == <GUID>);
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Console.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =new byte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(new ByteArrayContent(buffer));

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

    // get the video ID from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Console.WriteLine("Uploaded");
    Console.WriteLine("Video ID:");
    Console.WriteLine(videoId);

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

        Console.WriteLine("");
        Console.WriteLine("State:");
        Console.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Console.WriteLine("");
            Console.WriteLine("Full JSON:");
            Console.WriteLine(videoGetIndexResult);
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
    Console.WriteLine("");
    Console.WriteLine("Search:");
    Console.WriteLine(searchResult);

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
    Console.WriteLine("Insights Widget url:");
    Console.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
     Console.WriteLine("");
     Console.WriteLine("Player Widget url:");
     Console.WriteLine(playerWidgetLink);
     Console.WriteLine("\nPress Enter to exit...");
     String line = Console.ReadLine();
     if (line == "enter")
     {
         System.Environment.Exit(0);
     }

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

### <a name="arm-account"></a>[ARM アカウント](#tab/with-arm-account-account/)

この C# プロジェクトを開発プラットフォームにコピーした後、次のことを実行する必要があります。 
1. Program.cs に移動し、```SubscriptionId``` にサブスクリプション ID を入力します。
2. Program.cs に移動し、```ResourceGroup``` にリソース グループを入力します。
3. Program.cs に移動し、```AccountName``` にアカウント名を入力します。
4. Program.cs に移動し、```VideoUrl``` にビデオ URL を入力します。
5. dotnet 6.0 がインストールされていることを確認します。 インストールされていない場合は、[インストールします](https://dotnet.microsoft.com/download/dotnet/6.0)。
6. Azure CLI がインストールされていることを確認します。 インストールされていない場合は、[インストールします](/cli/azure/install-azure-cli)。
7. ターミナルを開き、VideoIndexerArm フォルダーに移動します。
8. Azure にログインします。```az login --use-device```
9. プロジェクトをビルドします。```dotnet build```
10. プロジェクトを実行します。```dotnet run```

```csharp
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net5.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Azure.Identity" Version="1.4.1" />
    <PackageReference Include="Microsoft.Identity.Client" Version="4.36.2" />
  </ItemGroup>

</Project>
```

```csharp
using System;
using System.Collections.Generic;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text.Json;
using System.Text.Json.Serialization;
using System.Threading.Tasks;
using System.Web;
using Azure.Core;
using Azure.Identity;


namespace VideoIndexerArm
{
    public class Program
    {
        private const string AzureResourceManager = "https://management.azure.com";
        private const string SubscriptionId = ""; // Your azure subscription
        private const string ResourceGroup = ""; // Your resource group
        private const string AccountName = ""; // Your account name
        private const string VideoUrl = ""; // The video url you would like to index

        public static async Task Main(string[] args)
        {
            // Build Azure Video Analyzer for Media resource provider client that has access token throuhg ARM
            var videoIndexerResourceProviderClient = await VideoIndexerResourceProviderClient.BuildVideoIndexerResourceProviderClient();

            // Get account details
            var account = await videoIndexerResourceProviderClient.GetAccount();
            var accountId = account.Properties.Id;
            var accountLocation = account.Location;
            Console.WriteLine($"account id: {accountId}");
            Console.WriteLine($"account location: {accountLocation}");

            // Get account level access token for Azure Video Analyzer for Media 
            var accessTokenRequest = new AccessTokenRequest
            {
                PermissionType = AccessTokenPermission.Contributor,
                Scope = ArmAccessTokenScope.Account
            };

            var accessToken = await videoIndexerResourceProviderClient.GetAccessToken(accessTokenRequest);
            var apiUrl = "https://api.videoindexer.ai";
            System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;


            // Create the http client
            var handler = new HttpClientHandler();
            handler.AllowAutoRedirect = false;
            var client = new HttpClient(handler);

            // Upload a video
            var content = new MultipartFormDataContent();
            Console.WriteLine("Uploading...");
            // Get the video from URL

            // As an alternative to specifying video URL, you can upload a file.
            // Remove the videoUrl parameter from the query params below and add the following lines:
            // FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
            // byte[] buffer =new byte[video.Length];
            // video.Read(buffer, 0, buffer.Length);
            // content.Add(new ByteArrayContent(buffer));

            var queryParams = CreateQueryString(
                new Dictionary<string, string>()
                {
            {"accessToken", accessToken},
            {"name", "video sample"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", VideoUrl},
                });
            var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountLocation}/Accounts/{accountId}/Videos?{queryParams}", content);
            var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

            // Get the video ID from the upload result
            string videoId = JsonSerializer.Deserialize<Video>(uploadResult).Id;
            Console.WriteLine("Uploaded");
            Console.WriteLine("Video ID:");
            Console.WriteLine(videoId);

            // Wait for the video index to finish
            while (true)
            {
                await Task.Delay(10000);

                queryParams = CreateQueryString(
                    new Dictionary<string, string>()
                    {
                {"accessToken", accessToken},
                {"language", "English"},
                    });

                var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountLocation}/Accounts/{accountId}/Videos/{videoId}/Index?{queryParams}");
                var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

                string processingState = JsonSerializer.Deserialize<Video>(videoGetIndexResult).State;

                Console.WriteLine("");
                Console.WriteLine("State:");
                Console.WriteLine(processingState);

                // Job is finished
                if (processingState != "Uploaded" && processingState != "Processing")
                {
                    Console.WriteLine("");
                    Console.WriteLine("Full JSON:");
                    Console.WriteLine(videoGetIndexResult);
                    break;
                }
            }

            // Search for the video
            queryParams = CreateQueryString(
                new Dictionary<string, string>()
                {
            {"accessToken", accessToken},
            {"id", videoId},
                });

            var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountLocation}/Accounts/{accountId}/Videos/Search?{queryParams}");
            var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
            Console.WriteLine("");
            Console.WriteLine("Search:");
            Console.WriteLine(searchResult);

            // Get insights widget url
            queryParams = CreateQueryString(
                new Dictionary<string, string>()
                {
            {"accessToken", accessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
                });
            var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountLocation}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?{queryParams}");
            var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
            Console.WriteLine("Insights Widget url:");
            Console.WriteLine(insightsWidgetLink);

            // Get player widget url
            queryParams = CreateQueryString(
                new Dictionary<string, string>()
                {
            {"accessToken", accessToken},
                });
            var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountLocation}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?{queryParams}");
            var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
            Console.WriteLine("");
            Console.WriteLine("Player Widget url:");
            Console.WriteLine(playerWidgetLink);
            Console.WriteLine("\nPress Enter to exit...");
            String line = Console.ReadLine();
            if (line == "enter")
            {
                System.Environment.Exit(0);
            }

        }

        static string CreateQueryString(IDictionary<string, string> parameters)
        {
            var queryParameters = HttpUtility.ParseQueryString(string.Empty);
            foreach (var parameter in parameters)
            {
                queryParameters[parameter.Key] = parameter.Value;
            }

            return queryParameters.ToString();
        }

        public class VideoIndexerResourceProviderClient
        {
            private readonly string armAaccessToken;

            async public static Task<VideoIndexerResourceProviderClient> BuildVideoIndexerResourceProviderClient()
            {
                var tokenRequestContext = new TokenRequestContext(new[] { $"{AzureResourceManager}/.default" });
                var tokenRequestResult = await new DefaultAzureCredential().GetTokenAsync(tokenRequestContext);
                return new VideoIndexerResourceProviderClient(tokenRequestResult.Token);
            }
            public VideoIndexerResourceProviderClient(string armAaccessToken)
            {
                this.armAaccessToken = armAaccessToken;
            }

            public async Task<string> GetAccessToken(AccessTokenRequest accessTokenRequest)
            {
                Console.WriteLine($"Getting access token. {JsonSerializer.Serialize(accessTokenRequest)}");
                // Set the generateAccessToken (from video indexer) http request content
                var jsonRequestBody = JsonSerializer.Serialize(accessTokenRequest);
                var httpContent = new StringContent(jsonRequestBody, System.Text.Encoding.UTF8, "application/json");

                // Set request uri
                var requestUri = $"{AzureResourceManager}/subscriptions/{SubscriptionId}/resourcegroups/{ResourceGroup}/providers/Microsoft.VideoIndexer/accounts/{AccountName}/generateAccessToken?api-version=2021-08-16-preview";

                // Generate access token from video indexer
                var client = new HttpClient(new HttpClientHandler());
                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", armAaccessToken);
                var result = await client.PostAsync(requestUri, httpContent);
                var jsonResponseBody = await result.Content.ReadAsStringAsync();
                return JsonSerializer.Deserialize<GenerateAccessTokenResponse>(jsonResponseBody).AccessToken;
            }

            public async Task<Account> GetAccount()
            {

                Console.WriteLine($"Getting account.");
                // Set request uri
                var requestUri = $"{AzureResourceManager}/subscriptions/{SubscriptionId}/resourcegroups/{ResourceGroup}/providers/Microsoft.VideoIndexer/accounts/{AccountName}/?api-version=2021-08-16-preview";

                // Get account
                var client = new HttpClient(new HttpClientHandler());
                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", armAaccessToken);
                var result = await client.GetAsync(requestUri);
                var jsonResponseBody = await result.Content.ReadAsStringAsync();
                return JsonSerializer.Deserialize<Account>(jsonResponseBody);
            }
        }

        public class AccessTokenRequest
        {
            [JsonPropertyName("permissionType")]
            public AccessTokenPermission PermissionType { get; set; }

            [JsonPropertyName("scope")]
            public ArmAccessTokenScope Scope { get; set; }

            [JsonPropertyName("projectId")]
            public string ProjectId { get; set; }
            
            [JsonPropertyName("videoId")]
            public string VideoId { get; set; }
        }

        [JsonConverter(typeof(JsonStringEnumConverter))]
        public enum AccessTokenPermission
        {
            Reader,
            Contributor,
            MyAccessAdministrator,
            Owner,
        }

        [JsonConverter(typeof(JsonStringEnumConverter))]
        public enum ArmAccessTokenScope
        {
            Account,
            Project,
            Video
        }

        public class GenerateAccessTokenResponse
        {
            [JsonPropertyName("accessToken")]
            public string AccessToken { get; set; }

        }
        public class AccountProperties
        {
            [JsonPropertyName("accountId")]
            public string Id { get; set; }
        }

        public class Account
        {
            [JsonPropertyName("properties")]
            public AccountProperties Properties { get; set; }

            [JsonPropertyName("location")]
            public string Location { get; set; }

        }

        public class Video
        {
            [JsonPropertyName("id")]
            public string Id { get; set; }

            [JsonPropertyName("state")]
            public string State { get; set; }
        }
    }
}

```

### <a name="common-errors"></a>一般的なエラー

アップロード操作によって返される場合がある状態コードを次の表に示します。

|status code|ErrorType (応答本体内)|説明|
|---|---|---|
|409|VIDEO_INDEXING_IN_PROGRESS|指定されたアカウントで既に同じビデオの処理が進行中です。|
|400|VIDEO_ALREADY_FAILED|指定されたアカウントで 2 時間以内に同じビデオの処理に失敗しました。 API クライアントは、ビデオを再アップロードする前に少なくとも 2 時間待つ必要があります。|
|429||試用版アカウントでは、1 分あたり 5 回のアップロードが許可されます。 有料アカウントでは、1 分あたり 50 回のアップロードが許可されます。|

## <a name="uploading-considerations-and-limitations"></a>アップロードに関する考慮事項と制限事項
 
- ビデオの名前は、80 文字以下にする必要があります。
- URL に基づいてビデオをアップロードする場合 (推奨)、エンドポイントは TLS 1.2 (またはそれ以降) を使用してセキュリティで保護する必要があります。
- URL オプションでのアップロード サイズは、30 GB に制限されます。
- 要求 URL の長さは 6144 文字に制限されており、クエリ文字列の URL の長さは 4096 文字に制限されています。
- バイト配列オプションでのアップロード サイズは、2 GB に制限されます。
- バイト配列オプションでは 30 分後にタイムアウトします。
- `videoURL` パラメーターに指定する URL はエンコードする必要があります
- Media Services アセットのインデックス作成には、URL からのインデックス作成と同じ制限が適用されます。
- Video Analyzer for Media では、1 つのファイルの上限時間は 4 時間です。
- URL にアクセスできる必要があります (たとえば、パブリック URL)。 

    プライベート URL の場合は、要求でアクセス トークンが提供される必要があります。
- URL では、Web ページ (`www.youtube.com` へのリンクなど) ではなく、有効なメディア ファイルを指す必要があります。
- 有料アカウントでは、1 分あたり最大 50 個のムービーをアップロードできます。試用版アカウントでは、1 分あたり最大 5 個のムービーです。

> [!Tip]
> .NET Framework バージョン 4.6.2 以上を使用することをお勧めします。 これは、それ以前の .NET Framework では既定で TLS 1.2 に設定されていないためです。
>
> 以前の .NET Framework を使用する必要がある場合は、REST API の呼び出しを行う前に、コードに次の 1 行を追加します。  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="firewall"></a>ファイアウォール

[ファイアウォールの内側にあるストレージ アカウント](faq.yml#can-a-storage-account-connected-to-the-media-services-account-be-behind-a-firewall)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

[API によって生成された Azure Video Analyzer for Media の出力を調べる](video-indexer-output-json-v2.md)
