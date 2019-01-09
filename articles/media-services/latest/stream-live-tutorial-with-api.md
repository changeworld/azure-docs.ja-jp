---
title: Azure Media Services v3 によるライブ ストリーミング | Microsoft Docs
description: このチュートリアルでは、.NET Core を使用して Media Services v3 でライブ ストリーミングを行う方法について順を追って説明します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 12/28/2018
ms.author: juliako
ms.openlocfilehash: 858c062c2b3d61b38247e323bf70d2768d33b257
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969337"
---
# <a name="tutorial-stream-live-with-media-services-v3-using-apis"></a>チュートリアル:API を使用した Media Services v3 によるライブ ストリーミング

Azure Media Services では、[LiveEvents](https://docs.microsoft.com/rest/api/media/liveevents) がライブ ストリーミング コンテンツの処理を受け持ちます。 LiveEvent は入力エンドポイントであり、その取り込み URL をライブ エンコーダーに対して指定します。 LiveEvent は、ライブ エンコーダーからライブ入力ストリームを受け取り、1 つまたは複数の [StreamingEndpoints](https://docs.microsoft.com/rest/api/media/streamingendpoints) を介してストリーミングできる状態にします。 また、ストリームはあらかじめプレビューし、確認したうえで処理、配信しますが、LiveEvent はその際に使用するプレビュー エンドポイント (プレビュー URL) も提供します このチュートリアルでは、.NET Core を使用してライブ イベントの**パススルー** タイプを作成、管理する方法について説明します。 

> [!NOTE]
> 先に進む前に、「[Live streaming with Media Services v3](live-streaming-overview.md)」(Media Services v3 によるライブ ストリーミング) を確認してください。 

このチュートリアルでは、次の操作方法について説明します。    

> [!div class="checklist"]
> * Media Services API にアクセスする
> * サンプル アプリの構成
> * ライブ ストリーミングを実行するコードを確認する
> * [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) (http://ampdemo.azureedge.net) でイベントを視聴する
> * リソースのクリーンアップ

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

チュートリアルを完了するには次のものが必要です。

- Visual Studio Code または Visual Studio をインストールします。
- CLI をローカルにインストールして使用します。この記事では、Azure CLI バージョン 2.0 以降が必要です。 お使いのバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

    現在、一部の [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) コマンドが Azure Cloud Shell では正常に動作しません。 CLI はローカルで使用することをお勧めします。

- [Media Services アカウントを作成する](create-account-cli-how-to.md)

    リソース グループ名および Media Services アカウント名として使用した値を覚えておいてください。

- イベントのブロードキャストに使用するカメラまたはデバイス (ラップトップなど)。
- カメラからの信号を Media Services ライブ ストリーミング サービスに送信されるストリームに変換するオンプレミスのライブ エンコーダー。 ストリームは **RTMP** または **Smooth Streaming** 形式である必要があります。

## <a name="download-the-sample"></a>サンプルのダウンロード

次のコマンドを使って、ストリーム配信の .NET サンプルが含まれる GitHub リポジトリを、お使いのコンピューターに複製します。  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

ライブ ストリーミングのサンプルは [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp) フォルダーにあります。

> [!IMPORTANT]
> このサンプルでは、各リソースに一意のサフィックスを使用します。 デバッグをキャンセルした場合、または完全に実行せずにアプリを終了した場合、アカウントに複数の LiveEvent が作成されます。 <br/>
> 実行中の LiveEvent を必ず停止してください。 そうしないと、**課金**が発生します。

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-performs-live-streaming"></a>ライブ ストリーミングを実行するコードを確認する

このセクションでは、*MediaV3LiveApp* プロジェクトの [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) ファイルで定義されている関数を調べます。

サンプルをクリーンアップせずに複数回実行しても名前の競合が発生しないように、サンプルは各リソースに対して一意のサフィックスを作成します。

> [!IMPORTANT]
> このサンプルでは、各リソースに一意のサフィックスを使用します。 デバッグをキャンセルした場合、または完全に実行せずにアプリを終了した場合、アカウントに複数の LiveEvent が作成されます。 <br/>
> 実行中の LiveEvent を必ず停止してください。 そうしないと、**課金**が発生します。
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>.NET SDK で Media Services API の使用を開始する

.NET で Media Services API の使用を始めるには、**AzureMediaServicesClient** オブジェクトを作成する必要があります。 オブジェクトを作成するには、クライアントが Azure AD を使用して Azure に接続するために必要な資格情報を指定する必要があります。 この記事の最初に複製したコード内で、ローカル構成ファイルで指定された資格情報に基づいて、**GetCredentialsAsync** 関数が ServiceClientCredentials オブジェクトを作成します。 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>ライブ イベントを作成する

このセクションは、**パススルー** タイプの (LiveEventEncodingType が None に設定されている) LiveEvent を作成する方法を示します。 ライブ エンコードが有効になっている LiveEvent を作成する場合は、LiveEventEncodingType を Standard に設定します。 

ライブ イベントの作成時には、次を指定することをお勧めします。

* Media Services の場所 
* ライブ イベントのストリーミング プロトコル (現時点では、RTMP プロトコルと Smooth Streaming プロトコルがサポートされます)
       
    LiveEvent や、それに関連付けられた LiveOutputs の実行中は、プロトコル オプションを変更できません。 別のプロトコルが必要な場合は、ストリーミング プロトコルごとに別の LiveEvent を作成する必要があります。  
* 取り込みやプレビューの IP 制限。 この LiveEvent へのビデオの取り込みが許可される IP アドレスを定義できます。 許可される IP アドレスは、1 つの IP アドレス (例: '10.0.0.1')、IP アドレスと CIDR サブネット マスクを使用した IP 範囲 (例: '10.0.0.1/22')、IP アドレスとピリオドで区切られた 10 進数のサブネット マスクを使用した IP 範囲 (例: '10.0.0.1(255.255.252.0)') のいずれかの形で指定できます。
    
    IP アドレスが指定されておらず、規則の定義もない場合は、どの IP アドレスも許可されません。 すべての IP アドレスを許可するには、規則を作成し、0.0.0.0/0 に設定します。

イベントの作成中に、そのイベントを自動開始するように設定できます。 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>取り込み URL の取得

LiveEvent が作成されると、ライブ エンコーダーに提供する取り込み URL を取得できます。 エンコーダーは、これらの URL を使用して、ライブ ストリームを入力します。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>プレビュー URL を取得する

previewEndpoint を使用して、エンコーダーからの入力が実際に受信されていることをプレビューおよび確認します。

> [!IMPORTANT]
> 続行する前に、ビデオがプレビュー URL に送信されていることを確認してください。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-liveevents-and-liveoutputs"></a>LiveEvent と LiveOutput を作成して管理する

ストリームが LiveEvent に流れ始めると、資産、LiveOutput、および StreamingLocator を作成することにより、ストリーミング イベントを開始できます。 これにより、ストリームがアーカイブされ、StreamingEndpoint を介して視聴者がストリームを使用できるようになります。 

#### <a name="create-an-asset"></a>資産を作成する

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

LiveOutput が使用する資産を作成します。

#### <a name="create-a-liveoutput"></a>LiveOutput を作成する

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaminglocator"></a>StreamingLocator を作成する

> [!NOTE]
> Media Services アカウントの作成時に、**既定**のストリーミング エンドポイントが**停止**状態でアカウントに追加されます。 コンテンツのストリーミングを開始し、ダイナミック パッケージと動的暗号化を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが**実行中**状態である必要があります。 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateStreamingLocator)]

```csharp

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

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupAccount)]   

## <a name="watch-the-event"></a>イベントの視聴

イベントを視聴するには、「[StreamingLocator を作成する](#create-a-streaminglocator)」に記述されたコードを実行したときに取得したストリーミング URL をコピーし、お好みのプレイヤーを使用します。 [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) (http://ampdemo.azureedge.net) を使用して、ストリームをテストできます。 

ライブ イベントが停止すると、イベントがオンデマンド コンテンツに自動的に変換されます。 イベントを停止して削除した後も、アセットを削除していなければ、アーカイブ済みコンテンツをオンデマンドでのビデオとしてストリーミングできます。 イベントがアセットを使用している場合はアセットを削除できません。まずイベントを削除する必要があります。  

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで作成した Media Services アカウントとストレージ アカウントも含め、リソース グループ内のどのリソースも必要なくなった場合は、前に作成したリソース グループを削除します。

次の CLI コマンドを実行します。

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> LiveEvent を実行したままにすると課金が発生します。 プロジェクトやプログラムがクラッシュする、または何らかの理由で閉じられると、LiveEvent が課金状態で実行されたままになるため、ご注意ください。

## <a name="next-steps"></a>次の手順

[ファイルをストリーミングする](stream-files-tutorial-with-api.md)

