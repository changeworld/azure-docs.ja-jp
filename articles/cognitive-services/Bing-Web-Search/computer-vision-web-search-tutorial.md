---
title: Visual Search モバイル アプリのチュートリアル
description: Cognitive Services の Computer Vision API、Bing Web Search API、および Xamarin.Forms のクロスプラットフォーム フレームワークを使用して視覚検索を実装しているオープン ソース C# アプリケーション。
services: bing-web-search, computer-vision
author: Aristoddle
manager: bking
ms.service: cognitive-services
ms.devlang: csharp
ms.topic: article
ms.date: 06/22/2017
ms.author: t-jolan
ms.openlocfilehash: 54dabaeaad2e49ba7cc138636054bc3dfa4b8379
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374248"
---
# <a name="visual-search-mobile-app-tutorial"></a>Visual Search モバイル アプリのチュートリアル

## <a name="introduction"></a>はじめに  
このチュートリアルでは、[Computer Vision API](https://azure.microsoft.com/services/cognitive-services/computer-vision/) と [Bing Web Search API](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) エンドポイントについて説明し、それらを [Xamarin.Forms](https://developer.xamarin.com/guides/xamarin-forms/) と共に使用して基本的な視覚検索アプリケーションをビルドする方法を示します。  このチュートリアル全体では、以下のトピックを取り上げています。 

> [!div class="checklist"]
> * Xamarin.Forms アプリケーションを開発するための Visual Studio のセットアップ
> * [Xamarin メディア プラグイン](https://github.com/jamesmontemagno/MediaPlugin)を使用したイメージ データのキャプチャとインポート
> * イメージに含まれるテキストの Computer Vision API を使用した解析
> * Bing Web Search API への検索要求の送信
> * 両方の API からの JSON 応答の、[Json.NET](https://github.com/JamesNK/Newtonsoft.Json) を使用 (LINQ とモデル オブジェクトの逆シリアル化を使用) した解析
> * 視覚検索用のクロスプラット フォーム Xamarin.Forms ユーザー インターフェイスの作成 

## <a name="prerequisites"></a>前提条件

この例は、Xamarin.Forms を使用して、[Visual Studio 2017](https://www.visualstudio.com/downloads/) で開発されました。 Visual Studio 2017 の Community エディションを使用できます。 Visual Studio で Xamarin を使用することの詳細については、[Xamarin のドキュメント](https://developer.xamarin.com/guides/cross-platform/getting_started/)を参照してください。

このサンプルでは、以下の NuGet パッケージを使用しています。

> [!div class="checklist"]
> * [Xamarin メディア プラグイン](https://github.com/jamesmontemagno/MediaPlugin)
> * [Json.NET](https://github.com/JamesNK/Newtonsoft.Json)

アプリケーションでは、次の Cognitive Services APIs を使用しています。

> [!div class="checklist"]
> * [Bing Web Search API](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) 
> * [Computer Vision API](https://azure.microsoft.com/services/cognitive-services/computer-vision/)

これらの API 用の 30 日間試用版のキーを取得するには、「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/)」を参照してください。 商用利用を目的としたキーの取得の詳細については、[価格](https://azure.microsoft.com/pricing/calculator/)についてのページを参照してください。

## <a name="setup-for-development"></a>開発のためのセットアップ  

### <a name="installing-xamarin-on-windows"></a>Windows への Xamarin のインストール
Visual Studio 2017 のどのエディションがインストールされていても、Visual Studio インストーラーを開き、Visual Studio のインストールに関連付けられているハンバーガー メニューを選択し、**[変更]** を選択します。  

![Visual Studio インストーラー](./media/computer-vision-web-search-tutorial/visual-studio-installer.png) 

[モバイル & ゲーム] まで下にスクロールし、すでに有効になっていなければ **[.NET によるモバイル開発]** を有効にします。

![Xamarin.Forms が選択されている Visual Studio インストーラー](./media/computer-vision-web-search-tutorial/xamarin-forms-is-enabled.png)

最後に、ウィンドウ右下隅の **[変更]** をクリックして、Xamarin がインストールされるのを待ちます。

### <a name="installing-xamarin-on-macos"></a>macOS への Xamarin のインストール
Visual Studio for Mac には、Xamarin があらかじめパッケージ化されています。 インストールは必要ありません。

## <a name="building-and-running-the-app"></a>アプリのビルドと実行

Visual Search アプリケーションの Visual Studio ソリューション ファイル *(.sln)* は、[Cognitive Services を使用する Visual Search アプリ](https://azure.microsoft.com/resources/samples/cognitive-services-xamarin-forms-computer-vision-search/)のページからダウンロードできます。 ZIP アーカイブは、Web ブラウザーを使用してダウンロードし、GitHub からそれをワークステーションに複製するか、Visual Studio を使用してダウンロードすることができます。

サンプルを使用して作業を開始するには、Visual Studio で `VisualSearchApp.sln` を開きます。  必要なコンポーネントを初期化するのに、しばらく時間がかかります。

アプリケーションには、**Json.NET** と **Xamarin メディア プラグイン**という 2 つのサード パーティ製ライブラリが必要です。 これらのライブラリは、Visual Studio で NuGet パッケージ マネージャーを使用してインストールできます。 **[ツール] > [NuGet パッケージ マネージャー] > [ソリューションの NuGet パッケージの管理]** と選択するか、ソリューション エクスプローラーで目的のソリューションを右クリックし、コンテキスト メニューから **[NuGet パッケージの管理]** を選択します。

NuGet ウィンドウで、**Xamarin メディア プラグイン** (Xam.Plugin.Media) バージョン 2.6.2 と **Json.NET** (Newtonsoft.Json) 10.0.3 を検索してインストールします。 インストールするときにはすべてのプロジェクトを選択してください。

使用可能なすべてのプラットフォーム用にアプリケーションをビルドするには、**Ctrl キーと Shift キーを押しながら B キー**を押すか、リボン メニューの **[ビルド]** をクリックして **[ソリューションのビルド]** を選択します。  Windows システムから iOS 用のコードをコンパイルしてテストする場合は、[このガイド](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/)のヘルプを参照してください。

アプリケーションを実行する前に、ターゲットの構成、プラットフォーム、およびプロジェクトを選択する必要があります。  Xamarin.Forms アプリケーションは、Windows、Android、および iOS のネイティブ コードにコンパイルします。 このチュートリアルには、サンプルの Windows バージョンのスクリーン ショットが含まれていますが、機能的にはすべてのバージョンが同等です。 このガイドで使用されるデプロイ設定は次のとおりです。  

![Android フォン用にコンパイルするように構成された Visual Studio](./media/computer-vision-web-search-tutorial/configuration-selection.png)

ビルド プロセスが成功し、ターゲット プラットフォームを選択したら、ツールバーの **[開始]** ボタンをクリックするか、**F5** キーを押します。 Visual Studio は、ターゲット プラットフォームにソリューションをデプロイし、それを起動します。

[Add Keys](キーの追加) ページが表示されます。 (このページは `AddKeysPage.xaml` で定義されています。)  

![[Add Keys](キーの追加) ページのイメージ](./media/computer-vision-web-search-tutorial/add-keys-page.png)  

ここで、Computer Vision API と Bing Web Search API のキーを貼り付けます。 Computer Vision API では、エンドポイントがホストされているサーバーを選択する必要もあります。

> [!TIP]
> このページをスキップするには、この情報を、`App.xaml.cs` 内の適切な場所に入力します。 

アプリケーションは、テスト クエリを実行することによってキーを検証し、次に、[OCR Select](OCR の選択) ページを表示します (`OcrSelectPage.xaml` で定義)。
   
![[OCR Select](OCR の選択) ページのイメージ](./media/computer-vision-web-search-tutorial/ocr-select-page.png)  

この画面の上部で、認識するテキストが印刷であるか手書きであるかを選択できます。

> [!TIP]
> そこからテキストを認識しようとしているものはできるだけ水平に保持し、反射なく均等に光が当たっているようにします。 手書きの OCR が、文字フォントやその他の "意匠を凝らした" テキストよりもうまく機能する場合があることが判明していますす。

次に、**[Take Photo]**(写真を撮る) または **[Import Photo]**(写真をインポートする) をクリックして、デバイスのカメラを使って写真を撮るか、デバイスに保存されている写真を選択します。

写真を撮るか選択すると、イメージが Computer Vision API に渡されます。 [Words Found](見つかった単語) ページ (`OcrResultsPage.xaml` で定義) に、イメージ内で認識されたすべての単語が表示されます。

![[OCR Results](OCR の結果) ページのイメージ](./media/computer-vision-web-search-tutorial/ocr-results-page.png)  

> [!NOTE]
> これらの結果のために使用したイメージは、ソース リポジトリ内に、`SamplePhotos\TestImage.jpg` として含まれています。

[Words Found](見つかった単語) ページ で項目をクリックすると、[Web Results](Web の結果) ページ (`WebResultsPage.xaml`) が表示され、その検索の、Bing での上位の結果が表示されます。

![[Web Results](Web の結果) ページのイメージ](./media/computer-vision-web-search-tutorial/web-results-page.png)  
最後に、[Web Results](Web の結果) ページ上の項目を選択し、埋め込まれた WebView 内のリンクを開きます。 (または、戻って別の結果を選択します。)

![WebView ページのイメージ](./media/computer-vision-web-search-tutorial/web-view-page.png)  

ページは、任意の Web ブラウザーで操作する場合と同様に操作可能で、[Web Results](Web の結果) ページに戻ることもできます。 

## <a name="review-and-learn"></a>確認し学習する

これで Visual Search アプリにざっと目を通したので、2 つの Microsoft Cognitive Services APIs をどのように使用するかを詳しく探っていきましょう。  このサンプルを独自のアプリケーションの開始点として使用しているか、単に API の使用法として利用しているかを問わず、アプリケーションを画面ごとに操作して、どのように機能しているかを正確に調べることは価値があります。

### <a name="add-keys-page"></a>[Add Keys](キーの追加) ページ
[Add Keys](キーの追加) ページの UI は `AddKeysPage.xaml` で定義されていて、その主なロジックは `AddKeysPage.xaml.cs` で定義されています。 キーはテスト要求を行うことによって検証されるため、C# で Cognitive Services エンドポイントを使用する方法を確かめる良いタイミングです。  

この操作の基本的な構造は次のとおりです。 

1. *System.Net.Http* から *HttpResponseMessage* オブジェクトと *HttpClient* オブジェクトをインスタンス化します
2. (各エンドポイントの API リファレンスで定義されている) 必要なヘッダーをすべて *HttpClient* オブジェクトにアタッチします
3. 必要なパラメーターをすべて追加して、GET または POST 要求をデータと共にエンドポイント URI に送信します
4. 応答が成功したことを確認します
5. さらに処理を行うため、応答を次に渡します

Bing Search API キーの有効性をチェックする関数は、次に示した `CheckBingSearchKey()` です。

```csharp
async Task CheckBingSearchKey(object sender = null, EventArgs e = null)
{
    HttpResponseMessage response;
    HttpClient SearchApiClient = new HttpClient();

    SearchApiClient.DefaultRequestHeaders.Add(AppConstants.OcpApimSubscriptionKey, BingSearchKeyEntry.Text);

    try
    {
        response = await SearchApiClient.GetAsync(AppConstants.BingWebSearchApiUrl + "q=test");

        if (response.StatusCode != System.Net.HttpStatusCode.Unauthorized)
        {
            BingSearchKeyEntry.BackgroundColor = Color.Green;
            AppConstants.BingWebSearchApiKey = BingSearchKeyEntry.Text;
            bingSearchKeyWorks = true;
        }
        else
        {
            BingSearchKeyEntry.BackgroundColor = Color.Red;
            bingSearchKeyWorks = false;
        }
    }
    catch( Exception exception )
    {
        BingSearchKeyEntry.BackgroundColor = Color.Red;
        Console.WriteLine($"ERROR: {exception.Message}");
    }
}
```

類似の関数 `CheckComputerVisionKey()` は、Computer Vision キーを検証するために使用されます。

### <a name="ocr-select-page"></a>[OCR Select](OCR の選択) ページ

[OCR Select](OCR の選択) ページ (`OcrSelectPage.xaml`) には、2 つの重要な役割があります。 まず、ターゲットの写真に対して、どのような種類の OCR を実行するかを決定します。 次に、処理するイメージをキャプチャするか開くかを、ユーザーに選択させます。

クロスプラット フォーム アプリケーションでの 2 つ目のタスクは、プラットフォームごとに異なるコードが必要なため、従来のやり方ではやっかいなタスクです。 Xamarin メディア プラグインは、わずか数行のコードでそれを処理します。

次の関数には、写真キャプチャのために Xamarin メディア プラグインを使用する例が組み込まれています。  その中では、以下のことを行っています。

1. 現在のデバイスでカメラが使用できることを確認します
2. `StoreCameraMediaOptions` オブジェクトのインスタンスを作成し、キャプチャしたイメージを保存する場所を指定します
3. イメージをキャプチャして、イメージ データを含む `MediaFile` オブジェクトを取得します
4. `MediaFile` をバイト配列にアンパックします
5. さらに処理するため、バイト配列を返します

次に `TakePhoto()` を示します。これは、写真キャプチャのために Xamarin メディア プラグインを使用する関数です。  

```csharp
async Task<byte[]> TakePhoto()
{
    MediaFile photoMediaFile = null;
    byte[] photoByteArray = null;

    if (CrossMedia.Current.IsCameraAvailable)
    {
        var mediaOptions = new StoreCameraMediaOptions
        {
            PhotoSize = PhotoSize.Medium,
            AllowCropping = true,
            SaveToAlbum = true,
            Name = $"{DateTime.UtcNow}.jpg"
        };
        photoMediaFile = await CrossMedia.Current.TakePhotoAsync(mediaOptions);
        photoByteArray = MediaFileToByteArray(photoMediaFile);
    }
    else
    {
        await DisplayAlert("Error", "No camera found", "OK");
        Console.WriteLine($"ERROR: No camera found");
    }
    return photoByteArray;
}
```
写真インポート ユーティリティは、同様の方法で動作します。 どちらの機能の断片も `OcrSelectPage.xaml.cs` に含まれています。 
 
> [!NOTE]
> Handwritten OCR エンドポイントで処理できるのは、4 MB よりも小さい写真のみです。 ファイル サイズの問題を回避するため、`StoreCameraMediaOptions` オブジェクトに対してオプション `PhotoSize = PhotoSize.Medium` を設定して、写真の元のサイズを 50% に縮小しています。  ご利用のデバイスが非常に品質が高い写真を受け取り、エラーが発生している場合は、代わりに `PhotoSize = PhotoSize.Small` を試すことができます。

次は、*MediaFile* をバイト配列に変換するために使用されるユーティリティ関数です。

```csharp
byte[] MediaFileToByteArray(MediaFile photoMediaFile)
{
    using (var memStream = new MemoryStream())
    {
        photoMediaFile.GetStream().CopyTo(memStream);
        return memStream.ToArray();
    }
}
```

### <a name="ocr-results-page"></a>[OCR Results](OCR の結果) ページ

[OCR Results](OCR の結果) ページには、**Json.NET** [SelectToken メソッド](http://www.newtonsoft.com/json/help/html/SelectToken.htm)を使用してイメージから抽出されたテキストが表示されます。  2 つの OCR エンドポイントの動作は少し異なっているため、両方について説明すると有益です。  

Computer Vision API は、いくつかのサーバーの場所でのみホストされるため、そのエンドポイントは実行時に動的に構築される必要があります。 関数 `SetOcrLocation` (`AppConstants.cs` にある静的 *AppConstants* クラスの一部) は、URI エンドポイントの場所を設定します。 これは、[Add Keys](キーの追加) ページでのユーザーの選択に対応しています。または、`App.xaml.cs` で設定されている値を使用します。 

```csharp
public static void SetOcrLocation(string location)
{
    ComputerVisionApiOcrUrl = $"https://{location}.api.cognitive.microsoft.com/vision/v1.0/ocr?language=en&detectOrientation=true";
    ComputerVisionApiHandwritingUrl = $"https://{location}.api.cognitive.microsoft.com/vision/v1.0/recognizeText?handwriting=true";
}
```

これらの API 要求について詳しく見ていきましょう。 Computer Vision OCR API は、限定されない言語のテキストを解析可能ですが、ここでは結果を改善するために、英語のテキストを検索するよう指定します。 API によるテキストの方向の自動検出も行います。 固定の方向を使用すると解析結果が向上する可能性がありますが、方向の検出は、モバイル アプリケーションで便利な場合があります。

このエンドポイントで使用できるパラメーターについては、[印刷物の光学式文字認識の API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/56f91f2e778daf14a499e1fc)で詳しく学ぶことができます。  

Handwritten OCR API はまだプレビュー段階にあり、現在は英語のテキストでのみ機能します。  このため現在のパラメーターは、手書きのテキストを解析するかどうかを示すフラグだけとなっています。 手書き OCR の API では、機械で印刷したテキストと手書きテキストの両方を解析できますが、`handwriting=false` を指定すると、印刷したテキストに関してより優れた結果が得られます。 

このアプリケーションは英語で記述されており、このサンプルに対しては Handwritten OCR のみを使用し、ユーザーが何を認識するよう指定するかに従って `handwriting` フラグを設定することが可能でした。  わかりやすいように、両方のエンドポイントを使用しました。  

このエンドポイントで使用できるパラメーターについては、[手書きの光学式文字認識の API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/587f2c6a154055056008f200)で詳しく学ぶことができます。

ここで、これらの API を呼び出す関数を見てみましょう。

`FetchPrintedWordList()` は Print OCR エンドポイントを使用して、イメージに含まれる印刷されたテキストを解析します。 HTTP 要求は、[Add Keys](キーの追加) ページで使用された呼び出しと同様の構造に従ってキーを検証しますが、写真を送信する必要があります。 写真はクエリ文字列で渡すにはサイズが大きすぎるため、GET 要求ではなく HTTP POST 要求を使用する必要があります。 (メモリ内にバイト配列として存在する) 写真を `ByteArrayContent` オブジェクトにエンコードして、送信しようとしているデータの種類を定義するこのオブジェクトにヘッダーを追加する必要があります。 

指定できるコンテンツの種類については、[Computer Vision API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/587f2c6a154055056008f200)を参照してください。  

```csharp
async Task<ObservableCollection<string>> FetchPrintedWordList()
{
    ObservableCollection<string> wordList = new ObservableCollection<string>();
    if (photo != null)
    {
        HttpResponseMessage response = null;
        using (var content = new ByteArrayContent(photo))
        {
            // The media type of the body sent to the API. 
            // "application/octet-stream" defines an image represented 
            // as a byte array
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
            response = await visionApiClient.PostAsync(AppConstants.ComputerVisionApiOcrUrl, content);
        }

        string ResponseString = await response.Content.ReadAsStringAsync();
        JObject json = JObject.Parse(ResponseString);

        // Here, we pull down each "line" of text and then join it to 
        // make a string representing the entirety of each line.  
        // In the Handwritten endpoint, you are able to extract the 
        // "line" without any further processing.  If you would like 
        // to simply get a list of all extracted words,* you can do 
        // this with:
        // json.SelectTokens("$.regions[*].lines[*].words[*].text) 
        IEnumerable<JToken> lines = json.SelectTokens("$.regions[*].lines[*]");
        if (lines != null)
        {
            foreach (JToken line in lines)
            {
                IEnumerable<JToken> words = line.SelectTokens("$.words[*].text");
                if (words != null)
                {
                    wordList.Add(string.Join(" ", words.Select(x => x.ToString())));
                }
            }
        }
    }
    return wordList;
}
```
> [!TIP]
> どのように **Json.NET** [SelectToken メソッド](http://www.newtonsoft.com/json/help/html/SelectToken.htm)を使用して応答オブジェクトからテキストを抽出しているかに注目してください。 `SelectToken` は、JSON 応答の特定部分のみが必要な場合に最適で、その後、次の関数に渡すことができます。 コードの他の部分では、JSON 応答を `ModelObjects.cs` で定義されているモデル オブジェクトに逆シリアル化しています。

Print OCR 要求と Handwritten OCR 要求の主な違いは、Print OCR サービスでは認識されたテキストが HTTP 応答の一部として返されるのに対して、Handwritten OCR サービスには、情報を取得するために追加の要求が必要な点です。 Handwritten OCR の最初の要求は、状態として HTTP 202 を返します。これは、処理が始まったことを通知するだけです。 この応答には、取得可能になときには完了した応答を取得するためにクライアントがチェックする必要があるエンドポイントが含まれています。 そのすべてがどのように動作するかは、`FetchHandwrittenWordList()` を参照してください。

```csharp
async Task<ObservableCollection<string>> FetchHandwrittenWordList()
{
    ObservableCollection<string> wordList = new ObservableCollection<string>();
    if (photo != null)
    {
        // Make the POST request to the handwriting recognition URL
        HttpResponseMessage response = null;
        using (var content = new ByteArrayContent(photo))
        {
            // The media type of the body sent to the API. 
            // "application/octet-stream" defines an image represented 
            // as a byte array
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
            response = await visionApiClient.PostAsync(AppConstants.ComputerVisionApiHandwritingUrl, content);
        }

        // Fetch results
        IEnumerable<string> operationLocationValues;
        string statusUri = string.Empty;
        if (response.Headers.TryGetValues("Operation-Location", out operationLocationValues))
        {
            statusUri = operationLocationValues.FirstOrDefault();

            // Ping status URL, wait for processing to finish 
            JObject obj = await FetchResultFromStatusUri(statusUri);
            IEnumerable<JToken> strings = obj.SelectTokens("$.recognitionResult.lines[*].text");
            foreach (string s in strings)
            {
                wordList.Add((string)s);
            }
        }
    }
    return wordList;
}
```

`FetchResultFromStatusUri()` は、Handwriting OCR 処理の 2 番目の部分です。 結果が得られるか、関数がタイムアウトするまで、最初の応答のメタデータから抽出された URI に ping を実行します。この関数は、独自のスレッドで非同期的に呼び出されることが重要です。 そうしないと、このメソッドは、処理が完了するまでユーザー インターフェイスをロックすることになります。

```csharp
// Takes in the url to check for handwritten text parsing results, and pings it per second until processing is finished
// Returns the JObject holding data for a successful parse
async Task<JObject> FetchResultFromStatusUri(string statusUri)
{
    JObject obj = null;
    int timeoutcounter = 0;
    HttpResponseMessage response = await visionApiClient.GetAsync(statusUri);
    string responseString = await response.Content.ReadAsStringAsync();
    obj = JObject.Parse(responseString);
    while ((!((string)obj.SelectToken("status")).Equals("Succeeded")) && (timeoutcounter++ < 60))
    {
        await Task.Delay(1000);
        response = await visionApiClient.GetAsync(statusUri);
        responseString = await response.Content.ReadAsStringAsync();
        obj = JObject.Parse(responseString);
    } 
    return obj;
}
```

### <a name="web-results-page"></a>[Web Results](Web の結果) ページ
ユーザーが [OCR Results](OCR の結果) ページに表示された検索用語を選択すると、[Web Results](Web の結果) ページに移動します。  ここで [Bing Web Search API](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) 要求を作成し、それをサービスのエンドポイントに送信して、**Json.NET** [DeserializeObject](http://www.newtonsoft.com/json/help/html/DeserializeObject.htm) メソッドを使用して JSON 応答を逆シリアル化します。  

```csharp
async Task<WebResultsList> GetQueryResults()
{
    // URL-encode the query term
    var queryString = System.Net.WebUtility.UrlEncode(queryTerm);

    // Here we encode the URL that will be used for the GET request to 
    // find query results.  Its arguments are as follows:
    // 
    // - [count=20] This sets the number of webpage objects returned at 
    //   "$.webpages" in the JSON response.  Currently, the API asks for 
    //   20 webpages in the response
    //
    // - [mkt=en-US] This sets the market where the results come from.
    //   Currently, the API looks for english results based in the 
    //   United States.
    //
    // - [q=queryString] This sets the string queried using the Search 
    //   API.   
    //
    // - [responseFilter=Webpages] This filters the response to only 
    //   include Webpage results.  This tag can take a comma seperated 
    //   list of response types that you are looking for.  If left 
    //   blank, all responses (webPages, News, Videos, etc) are 
    //   returned.
    //
    // - [setLang=en] This sets the languge for user interface strings. 
    //   To learn more about UI strings, check the Web Search API 
    //   reference.
    //
    // - [API Reference] https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference
    string uri = AppConstants.BingWebSearchApiUrl + $"count=20&mkt=en-US&q={queryString}&responseFilter=Webpages&setLang=en";

    // Make the HTTP Request
    WebResultsList webResults = null;
    HttpResponseMessage httpResponseMessage = await searchApiClient.GetAsync(uri);
    var responseContentString = await httpResponseMessage.Content.ReadAsStringAsync();
    JObject json = JObject.Parse(responseContentString);
    JToken resultBlock = json.SelectToken("$.webPages");
    if (resultBlock != null)
    {
        webResults = JsonConvert.DeserializeObject<WebResultsList>(resultBlock.ToString());
    }
    return webResults;
}
```

Bing Web Search API は、ユーザーが何を求めているかに関する情報をできるだけ多く提供する場合に、最適に機能します。 特に、`mkt` パラメーターと `setLang` パラメーターをほぼ常に使用し (ここでは英語 (米国) に設定)、ユーザーの場所と優先言語を識別します。

> [!NOTE]
> Microsoft は、ソース コードが理解しやすいように、Bing Web Search クエリをシンプルにしておきました。  実際のアプリケーションでは、結果の改善のため、HTTP 要求に次のヘッダーを追加する必要があります。 
> * User-Agent  
> * X-MSEdge-ClientID  
> * X-Search-ClientIP  
> * X-Search-Location  
>
> これらのヘッダー値の詳細については、[Bing Web Search API のリファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers)を参照してください

## <a name="next-steps"></a>次の手順
Microsoft Cognitive Services では、このアプリケーションに容易に統合できる追加のサービスが多数提供されています。  たとえば、次のことが可能です。

* [Bing Entity Search](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) を追加して Web の検索結果を拡張する
* Bing Web Search の代わりに [Bing Custom Search](https://azure.microsoft.com/services/cognitive-services/bing-custom-search/) を取り入れる
* [Bing Image Search](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) に備わる、イメージに関するコグニティブな分析情報の機能を使用して、キャプチャされたイメージについての詳細を確認し、Web で類似したイメージを見つける
* [Bing Spell Check](https://azure.microsoft.com/services/cognitive-services/spell-check/) 使用して、解析されたテキストの質をさらに向上させる
* [Microsoft Translator](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) を統合して、抽出されたテキストを異なる言語で表示する
* [Cognitive Services ポータル](https://azure.microsoft.com/services/cognitive-services/)にあるその他のサービスを混在させたり組み合わせたりする。その可能性は無限大です。
