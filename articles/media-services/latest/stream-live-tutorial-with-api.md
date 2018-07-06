---
title: .NET Core を使用して Azure Media Services v3 でライブ ストリーミングを行う | Microsoft Docs
description: このチュートリアルでは、.NET Core を使用して Media Services v3 でライブ ストリーミングを行う方法について順を追って説明します。
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/06/2018
ms.author: juliako
ms.openlocfilehash: 82ef04993b597778c808d649032603a0f3672e4c
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110332"
---
# <a name="stream-live-with-azure-media-services-v3-using-net-core"></a>.NET Core を使用して Azure Media Services v3 でライブ ストリーミングを行う

Media Services で、ライブ ストリーミング コンテンツのプロセスを担うのは [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) です。 LiveEvent は入力エンドポイントであり、その取り込み URL をライブ エンコーダーに対して指定します。 LiveEvent は、ライブ エンコーダーからライブ入力ストリームを受け取り、1 つまたは複数の [StreamingEndpoints](https://docs.microsoft.com/rest/api/media/streamingendpoints) を介してストリーミングできる状態にします。 また、ストリームはあらかじめプレビューし、確認したうえで処理、配信しますが、LiveEvent はその際に使用するプレビュー エンドポイント (プレビュー URL) も提供します このチュートリアルでは、.NET Core を使用してライブ イベントの**パススルー** タイプを作成、管理する方法について説明します。 

> [!NOTE]
> 先に進む前に、「[Live streaming with Media Services v3](live-streaming-overview.md)」(Media Services v3 によるライブ ストリーミング) を確認してください。 

このチュートリアルでは、次の操作方法について説明します。    

> [!div class="checklist"]
> * Media Services アカウントを作成する
> * Media Services API にアクセスする
> * サンプル アプリの構成
> * ライブ ストリーミングを実行するコードを確認する
> * [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) (http://ampdemo.azureedge.net) でイベントを視聴する
> * リソースのクリーンアップ

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

チュートリアルを完了するには次のものが必要です。

* Visual Studio Code または Visual Studio をインストールする
* イベントのブロードキャストに使用するカメラまたはデバイス (ラップトップなど)。
* カメラからの信号を Media Services ライブ ストリーミング サービスに送信されるストリームに変換するオンプレミスのライブ エンコーダー。 ストリームは **RTMP** または **Smooth Streaming** 形式である必要があります。

## <a name="download-the-sample"></a>サンプルのダウンロード

次のコマンドを使って、ストリーム配信の .NET サンプルが含まれる GitHub リポジトリを、お使いのコンピューターに複製します。  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

ライブ ストリーミングのサンプルは [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp) フォルダーにあります。

> [!IMPORTANT]
> このサンプルでは、各リソースに一意のサフィックスを使用します。 デバッグをキャンセルした場合、または完全に実行せずにアプリを終了した場合、アカウントに複数の LiveEvent が作成されます。 <br/>
> 実行中の LiveEvent を必ず停止してください。 そうしないと、**課金**が発生します。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-performs-live-streaming"></a>ライブ ストリーミングを実行するコードを確認する

このセクションでは、*MediaV3LiveApp* プロジェクトの [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) ファイルで定義されている関数を調べます。

サンプルをクリーンアップせずに複数回実行しても名前の競合が発生しないように、サンプルは各リソースに対して一意のサフィックスを作成します。

> [!IMPORTANT]
> このサンプルでは、各リソースに一意のサフィックスを使用します。 デバッグをキャンセルした場合、または完全に実行せずにアプリを終了した場合、アカウントに複数の LiveEvent が作成されます。 <br/>
> 実行中の LiveEvent を必ず停止してください。 そうしないと、**課金**が発生します。
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>.NET SDK で Media Services API の使用を開始する

.NET で Media Services API の使用を始めるには、**AzureMediaServicesClient** オブジェクトを作成する必要があります。 オブジェクトを作成するには、クライアントが Azure AD を使用して Azure に接続するために必要な資格情報を指定する必要があります。 この記事の最初に複製したコード内で、ローカル構成ファイルで指定された資格情報に基づいて、**GetCredentialsAsync** 関数が ServiceClientCredentials オブジェクトを作成します。  

```csharp
private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
{
    var credentials = await GetCredentialsAsync(config);

    return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
    {
        SubscriptionId = config.SubscriptionId,
    };
}
```

### <a name="create-a-live-event"></a>ライブ イベントを作成する

このセクションは、**パススルー** タイプの (LiveEventEncodingType が None に設定されている) LiveEvent を作成する方法を示します。 ライブ エンコーディングが有効になっている LiveEvent を作成する場合は、LiveEventEncodingType を Basic に設定します。 

ライブ イベントの作成時には、次を指定することをお勧めします。

* Media Services の場所 
* ライブ イベントのストリーミング プロトコル (現時点では、RTMP プロトコルと Smooth Streaming プロトコルがサポートされます)
       
    LiveEvent や、それに関連付けられた LiveOutputs の実行中は、プロトコル オプションを変更できません。 別のプロトコルが必要な場合は、ストリーミング プロトコルごとに別の LiveEvent を作成する必要があります。  
* 取り込みやプレビューの IP 制限。 この LiveEvent へのビデオの取り込みが許可される IP アドレスを定義できます。 許可される IP アドレスは、1 つの IP アドレス (例: '10.0.0.1')、IP アドレスと CIDR サブネット マスクを使用した IP 範囲 (例: '10.0.0.1/22')、IP アドレスとピリオドで区切られた 10 進数のサブネット マスクを使用した IP 範囲 (例: '10.0.0.1(255.255.252.0)') のいずれかの形で指定できます。
    
    IP アドレスが指定されておらず、規則の定義もない場合は、どの IP アドレスも許可されません。 すべての IP アドレスを許可するには、規則を作成し、0.0.0.0/0 に設定します。

イベントの作成中に、そのイベントを自動開始するように設定できます。 

```csharp
Console.WriteLine($"Creating a live event named {liveEventName}");
Console.WriteLine();

LiveEventPreview liveEventPreview = new LiveEventPreview
{
    AccessControl = new LiveEventPreviewAccessControl(
        ip: new IPAccessControl(
            allow: new IPRange[]
            {
                new IPRange (
                    name: "AllowAll",
                    address: "0.0.0.0",
                    subnetPrefixLength: 0
                )
            }
        )
    )
};

// This can sometimes take awhile. Be patient.
LiveEvent liveEvent = new LiveEvent(
    location: mediaService.Location, 
    description:"Sample LiveEvent for testing",
    vanityUrl:false,
    encoding: new LiveEventEncoding(
                // Set this to Basic to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                encodingType:LiveEventEncodingType.None, 
                presetName:null
            ),
    input: new LiveEventInput(LiveEventInputProtocol.RTMP), 
    preview: liveEventPreview,
    streamOptions: new List<StreamOptionsFlag?>()
    {
        // Set this to Default or Low Latency.
        // Low latency reduces the amount of buffering Media Services does.
        // Low latency can also reduce the stability of the live stream. 
        StreamOptionsFlag.Default
    }
);

Console.WriteLine($"Creating the LiveEvent, be patient this can take time...");
liveEvent = client.LiveEvents.Create(config.ResourceGroup, config.AccountName, liveEventName, liveEvent, autoStart:true);
```

### <a name="get-ingest-urls"></a>取り込み URL の取得

チャネルが作成されると、ライブ エンコーダーに提供する取り込み URL を取得できます。 エンコーダーは、これらの URL を使用して、ライブ ストリームを入力します。


```csharp
// Get the input endpoint to configure the on-premises encoder with
string ingestUrl = liveEvent.Input.Endpoints.First().Url;
Console.WriteLine($"The ingest url to configure the on-premises encoder with is:");
Console.WriteLine($"\t{ingestUrl}");
Console.WriteLine();
```

### <a name="get-the-preview-url"></a>プレビュー URL を取得する

previewEndpoint を使用して、エンコーダーからの入力が実際に受信されていることをプレビューおよび確認します。

> [!IMPORTANT]
> 続行する前に、ビデオがプレビュー URL に送信されていることを確認してください。

```sharp
string previewEndpoint = liveEvent.Preview.Endpoints.First().Url;
Console.WriteLine($"The preview url is:");
Console.WriteLine($"\t{previewEndpoint}");
Console.WriteLine();

Console.WriteLine($"Open the live preview in your browser and use the Azure Media Player to monitor the preview playback:");
Console.WriteLine($"\thttps://ampdemo.azureedge.net/?url={previewEndpoint}");
Console.WriteLine();
```

### <a name="create-and-manage-liveevents-and-liveoutputs"></a>LiveEvent と LiveOutput を作成して管理する

ストリームが LiveEvent に流れ始めると、資産、LiveOutput、および StreamingLocator を作成することにより、ストリーミング イベントを開始できます。 これにより、ストリームがアーカイブされ、StreamingEndpoint を介して視聴者がストリームを使用できるようになります。 

#### <a name="create-an-asset"></a>資産を作成する

LiveOutput が使用する資産を作成します。

```csharp
string assetName = "archiveAsset" + uniqueness;
Console.WriteLine($"Creating an asset named {assetName}");
Console.WriteLine();
Asset asset = client.Assets.CreateOrUpdate(config.ResourceGroup, config.AccountName, assetName, new Asset());
```

#### <a name="create-a-liveoutput"></a>LiveOutput を作成する

```csharp
string manifestName = "output";
string liveOutputName = "liveOutput" + uniqueness;
Console.WriteLine($"Creating a live output named {liveOutputName}");
Console.WriteLine();

LiveOutput liveOutput = new LiveOutput(assetName: asset.Name, manifestName: manifestName, archiveWindowLength: TimeSpan.FromMinutes(10));
liveOutput = client.LiveOutputs.Create(config.ResourceGroup, config.AccountName, liveEventName, liveOutputName, liveOutput);
```

#### <a name="create-a-streaminglocator"></a>StreamingLocator を作成する

> [!NOTE]
> Media Services アカウントの作成時に、**既定**のストリーミング エンドポイントが**停止**状態でアカウントに追加されます。 コンテンツのストリーミングを開始し、ダイナミック パッケージと動的暗号化を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが**実行中**状態である必要があります。 


```csharp
StreamingLocator locator = new StreamingLocator(assetName: assetName, streamingPolicyName: PredefinedStreamingPolicy.ClearStreamingOnly);
locator = client.StreamingLocators.Create(config.ResourceGroup, config.AccountName, streamingLocatorName, locator);

// Get the default Streaming Endpoint on the account
StreamingEndpoint streamingEndpoint = client.StreamingEndpoints.Get(config.ResourceGroup, config.AccountName, "default");

// If it's not running, Start it. 
if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
{
    Console.WriteLine("Streaming Endpoint was Stopped, restarting now..");
    client.StreamingEndpoints.Start(config.ResourceGroup, config.AccountName, "default");
}

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Media Services アカウント内のリソースをクリーンアップする

ストリーミング イベントが完了し、以前にプロビジョニングされたリソースをクリーンアップする場合は、次の手順に従います。

* エンコーダーからのストリームのプッシュを停止します。
* LiveEvent を停止します。 LiveEvent が停止すると、いかなる課金も発生しません。 もう一度開始する必要がある場合、取り込み URL は同一になるため、エンコーダーを再構成する必要はありません。
* ライブ イベントのアーカイブをオンデマンド ストリームとして提供し続けるのでない限り、StreamingEndpoint を停止できます。 LiveEvent が停止状態の場合は、いかなる課金も発生しません。

```csharp
private static void CleanupLiveEventAndOutput(IAzureMediaServicesClient client, string resourceGroup, string accountName, string liveEventName, string liveOutputName)
{
    // Delete the LiveOutput
    client.LiveOutputs.Delete(resourceGroup, accountName, liveEventName, liveOutputName);

    // Stop and delete the LiveEvent
    client.LiveEvents.Stop(resourceGroup, accountName, liveEventName);
    client.LiveEvents.Delete(resourceGroup, accountName, liveEventName);
}

private static void CleanupLocatorAssetAndStreamingEndpoint(IAzureMediaServicesClient client, string resourceGroup, string accountName, string streamingLocatorName, string assetName)
{
    // Delete the Streaming Locator
    client.StreamingLocators.Delete(resourceGroup, accountName, streamingLocatorName);

    // Delete the Archive Asset
    client.Assets.Delete(resourceGroup, accountName, assetName);
}

private static void CleanupAccount(IAzureMediaServicesClient client, string resourceGroup, string accountName)
{
    try{
        Console.WriteLine("Cleaning up the resources used, stopping the LiveEvent. This can take a few minutes to complete.");
        Console.WriteLine();

        var events = client.LiveEvents.List(resourceGroup, accountName);
        
        foreach (LiveEvent l in events)
        {
            if (l.Name == liveEventName){
                var outputs = client.LiveOutputs.List(resourceGroup, accountName, l.Name);

                foreach (LiveOutput o in outputs)
                {
                    client.LiveOutputs.Delete(resourceGroup, accountName, l.Name, o.Name);
                    Console.WriteLine($"LiveOutput: {o.Name} deleted from LiveEvent {l.Name}. The archived Asset and Streaming URLs are still retained for on-demand viewing.");
                }

                if (l.ResourceState == LiveEventResourceState.Running){
                    client.LiveEvents.Stop(resourceGroup, accountName, l.Name);
                    Console.WriteLine($"LiveEvent: {l.Name} Stopped.");
                    client.LiveEvents.Delete(resourceGroup, accountName, l.Name);
                    Console.WriteLine($"LiveEvent: {l.Name} Deleted.");
                    Console.WriteLine();
                }
            }
        }
    } 
    catch(ApiErrorException e)
    {
        Console.WriteLine("Hit ApiErrorException");
        Console.WriteLine($"\tCode: {e.Body.Error.Code}");
        Console.WriteLine($"\tCode: {e.Body.Error.Message}");
        Console.WriteLine();

    }
}
```        

## <a name="watch-the-event"></a>イベントの視聴

イベントを視聴するには、「[StreamingLocator を作成する](#create-a-streaminglocator)」に記述されたコードを実行したときに取得したストリーミング URL をコピーし、お好みのプレイヤーを使用します。 [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) (http://ampdemo.azureedge.net) を使用して、ストリームをテストできます。 

ライブ イベントが停止すると、イベントがオンデマンド コンテンツに自動的に変換されます。 イベントを停止して削除した後も、資産を削除していなければ、アーカイブ済みコンテンツをオンデマンドでのビデオとしてストリーミングできます。 イベントが資産を使用している場合は資産を削除できません。まずイベントを削除する必要があります。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで作成した Media Services アカウントとストレージ アカウントも含め、リソース グループ内のどのリソースも必要なくなった場合は、前に作成したリソース グループを削除します。 **CloudShell** ツールを使うことができます。

**CloudShell** で、次のコマンドを実行します。

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> LiveEvent を実行したままにすると課金が発生します。 プロジェクトやプログラムがクラッシュする、または何らかの理由で閉じられると、LiveEvent が課金状態で実行されたままになるため、ご注意ください。

## <a name="next-steps"></a>次の手順

[ファイルをストリーミングする](stream-files-tutorial-with-api.md)

