---
title: .NET 5.0 を使用して Media Services でライブ ストリーム配信を行う
titleSuffix: Azure Media Services
description: .NET 5.0 を使用してライブ イベントをストリーム配信する方法について説明します
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 06/13/2019
ms.author: inhenkel
ms.openlocfilehash: f10ef55a44aa917fd8f0fb3783dcd29284512d7e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732662"
---
# <a name="tutorial-stream-live-with-media-services-by-using-net-50"></a>チュートリアル: .NET 5.0 を使用して Media Services でライブ ストリーム配信を行う

Azure Media Services では、[ライブ イベント](/rest/api/media/liveevents)がライブ ストリーミング コンテンツの処理を受け持ちます。 ライブ イベントは入力エンドポイントであり、その取り込み URL をライブ エンコーダーに対して指定します。 ライブ イベントは、ライブ エンコーダーから入力ストリームを受け取り、1 つまたは複数の[ストリーミング エンドポイント](/rest/api/media/streamingendpoints)を介してストリーミングできる状態にします。 また、ストリームはあらかじめプレビューし、確認したうえで処理、配信しますが、ライブ イベントはその際に使用するプレビュー エンドポイント (プレビュー URL) も提供します。 

このチュートリアルでは、.NET 5.0 を使用してライブ イベントの *パススルー* タイプを作成する方法について説明します。 このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * サンプル アプリをダウンロードする。
> * ライブ ストリーミングを実行するコードを確認する。
> * [Media Player デモ サイト](https://ampdemo.azureedge.net)で [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) を使用してイベントを視聴する。
> * リソースをクリーンアップする。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> このチュートリアルでは [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent) の例を使用していますが、全体的な手順は [REST API](/rest/api/media/liveevents)、[CLI](/cli/azure/ams/live-event)、またはその他のサポートされている [SDK](media-services-apis-overview.md#sdks) で同じです。 

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のものが必要です。

- [Visual Studio Code for Windows/macOS/Linux](https://code.visualstudio.com/)、または [Visual Studio 2019 for Windows/Mac](https://visualstudio.microsoft.com/) をインストールします。
- [.NET 5.0 SDK](https://dotnet.microsoft.com/download) をインストールします。
- [Media Services アカウントを作成する](./account-create-how-to.md) **API アクセス** の詳細情報 (JSON 形式) をコピーするか、Media Services アカウントに接続するうえで必要な値をこのサンプルで使用する *.env* ファイルの書式に保存しておくようにしてください。
- [Azure CLI を使用した Azure Media Services API へのアクセス](./access-api-howto.md)に関するページの手順に従い、資格情報を保存します。 これらは、このサンプルで API にアクセスする際や、 *.env* ファイルの書式に入力する際に必要になります。 

ライブストリーミング ソフトウェアには、これらの追加項目が必要です。

- イベントのブロードキャストに使用するカメラまたはデバイス (ラップトップなど)。
- カメラ ストリームをエンコードし、Real-Time Messaging Protocol (RTMP) を介して Media Services ライブストリーミング サービスに送信する、オンプレミスのソフトウェア エンコーダー。 詳細については、「[おすすめのライブ ストリーミング エンコーダー](encode-recommended-on-premises-live-encoders.md)」を参照してください。 ストリームは RTMP または Smooth Streaming 形式である必要があります。  

  このサンプルは、Open Broadcaster Software (OBS) Studio を使用して、RTMP を取り込みエンドポイントにブロードキャストすることを前提としています。 [OBS Studio をインストール](https://obsproject.com/download)します。 

> [!TIP]
> 先に進む前に、「[Azure Media Services v3 を使用したライブ ストリーミング](stream-live-streaming-concept.md)」を確認してください。 

## <a name="download-and-configure-the-sample"></a>サンプルをダウンロードして構成する

次のコマンドを使用して、ライブストリーミングの .NET サンプルが含まれる GitHub リポジトリを自分のマシンにクローンします。  

```bash
git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
```

ライブストリーミングのサンプルは [Live](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live) フォルダーにあります。

[!INCLUDE [appsettings or .env file](./includes/note-appsettings-or-env-file.md)]

> [!IMPORTANT]
> このサンプルでは、各リソースに一意のサフィックスを使用します。 デバッグを取り消した場合、または完全に実行せずにアプリを終了した場合、アカウントに複数のライブ イベントが作成されます。
>
> 実行中のライブ イベントを必ず停止してください。 そうしないと、"*料金が発生*" します。

## <a name="examine-the-code-that-performs-live-streaming"></a>ライブ ストリーミングを実行するコードを確認する

このセクションでは、*LiveEventWithDVR* プロジェクトの [Authentication.cs](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Common_Utils/Authentication.cs) ファイル (Common_Utils フォルダー内) と [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/Program.cs) ファイルで定義されている関数を調べます。

サンプルをクリーンアップせずに複数回実行しても名前の競合が発生しないように、サンプルは各リソースに対して一意のサフィックスを作成します。


### <a name="start-using-media-services-apis-with-the-net-sdk"></a>.NET SDK で Media Services API の使用を開始する

Authentication.cs では、ローカル構成ファイル (appsettings.json または .env) で指定された資格情報を使用して `AzureMediaServicesClient` オブジェクトが作成されます。

`AzureMediaServicesClient` オブジェクトを使用すると、.NET で Media Services API の使用を開始できます。 オブジェクトを作成するには、クライアントが `GetCredentailsAsync` に実装されている Azure Active Directory を使用して Azure に接続するために必要な資格情報を指定する必要があります。 もう 1 つのオプションは対話型認証を使用する方法で、これは `GetCredentialsInteractiveAuthAsync` に実装されています。

[!code-csharp[Main](../../../media-services-v3-dotnet/Common_Utils/Authentication.cs#CreateMediaServicesClientAsync)]

この記事の冒頭で複製したコードの `GetCredentialsAsync` 関数により、ローカルの構成ファイル (*appsettings.json*) またはリポジトリのルートにある *.env* 環境変数ファイルの中で指定されている資格情報に基づいて `ServiceClientCredentials` オブジェクトが作成されます。

[!code-csharp[Main](../../../media-services-v3-dotnet/Common_Utils/Authentication.cs#GetCredentialsAsync)]

対話型認証の場合、`GetCredentialsInteractiveAuthAsync` 関数により、対話型認証と、ローカル構成ファイル (*appsettings.json*) またはリポジトリのルートにある *.env* 環境変数ファイルで指定された接続パラメーターに基づいて、`ServiceClientCredentials` オブジェクトが作成されます。 その場合、AADCLIENTID と AADSECRET は、構成ファイルまたは環境変数ファイルでは必要ではありません。

[!code-csharp[Main](../../../media-services-v3-dotnet/Common_Utils/Authentication.cs#GetCredentialsInteractiveAuthAsync)]


### <a name="create-a-live-event"></a>ライブ イベントを作成する

このセクションでは、"*パススルー*" タイプのライブ イベント (`LiveEventEncodingType` を `None` に設定) を作成する方法について説明します。 使用可能なライブ イベントの種類については、「[ライブ イベントの種類](live-event-outputs-concept.md#live-event-types)」のセクションを参照してください。 パススルーのほかには、720p または 1080p のアダプティブ ビットレート クラウド エンコードのためのライブ コード変換イベントを使用できます。
 
ライブ イベントを作成するときに、次の項目を指定したい場合もあります。

* **ライブ イベントの取り込みプロトコル**。 現時点では、RTMP、RTMPS、および Smooth Streaming プロトコルがサポートされています。 ライブ イベントや、それに関連付けられたライブ出力の実行中は、プロトコル オプションを変更できません。 別のプロトコルが必要な場合は、ストリーミング プロトコルごとに別のライブ イベントを作成します。 
* **取り込みやプレビューの IP 制限**。 このライブ イベントへのビデオの取り込みが許可される IP アドレスを定義できます。 許可される IP アドレスは、これらのいずれかとして指定できます。

  * 単一の IP アドレス (例: `10.0.0.1`)
  * IP アドレスと Classless Inter-Domain Routing (CIDR) サブネット マスクを使用する IP 範囲 (例: `10.0.0.1/22`)
  * IP アドレスとピリオド区切りのサブネット マスクを使用する IP 範囲 (例: `10.0.0.1(255.255.252.0)`)

  IP アドレスが指定されておらず、規則の定義もない場合は、どの IP アドレスも許可されません。 すべての IP アドレスを許可するには、規則を作成し、`0.0.0.0/0` に設定します。 IP アドレスの形式は、4 つの数字を含む IPv4 アドレスか CIDR アドレス範囲にする必要があります。  
* **イベントの作成時の自動開始**。 自動開始が `true` に設定されている場合、ライブ イベントは作成された後に開始されます。 つまり、ライブ イベントの実行が開始されるとすぐに課金が開始されます。 それ以上の課金を停止するには、ライブ イベント リソースの `Stop` を明示的に呼び出す必要があります。 詳細については、[ライブ イベントの状態と課金](live-event-states-billing-concept.md)に関するページを参照してください。

  各種のスタンバイ モードも利用できます。スタンバイ モードを使うと、実行中状態にすばやく移行できる低コストの "割り当て済み" 状態でライブ イベントを開始できます。 これは、ホット プールのように、ストリーマーにすばやくチャンネルを渡す必要がある局面で便利です。
* **静的ホスト名と一意の GUID**。 ハードウェアベースのライブ エンコーダーで取り込み URL の予測やメンテナンスをしやすくする場合には、`useStaticHostname` プロパティを `true` に設定します。 詳細については、「[ライブ イベントの取り込み URL](live-event-outputs-concept.md#live-event-ingest-urls)」のセクションを参照してください。

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>取り込み URL の取得

ライブ イベントの作成後、ライブ エンコーダーに提供する取り込み URL を取得できます。 エンコーダーは、これらの URL を使用して、ライブ ストリームを入力します。

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>プレビュー URL を取得する

プレビューして、エンコーダーからの入力が受信されていることを確認するには、`previewEndpoint` を使用します。

> [!IMPORTANT]
> 続行する前に、ビデオがプレビュー URL に送信されていることを確認してください。

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>ライブ イベントとライブ出力を作成して管理する

ストリームがライブ イベントに流れ始めたら、アセット、ライブ出力、およびストリーミング ロケーターを作成することにより、ストリーミング イベントを開始できます。 これにより、ストリームがアーカイブされ、ストリーミング エンドポイントを介して視聴者がストリームを使用できるようになります。

これらの概念を学ぶにあたっては、アセット オブジェクトというものを、かつてビデオ テープ レコーダーに挿入していたテープにたとえて考えてみるとわかりやすくなります。 ライブ出力は、テープ レコーダー マシンに相当します。 ライブ イベントは、マシンの背面に届くビデオ信号です。

まずは、ライブ イベントを作成することで、信号を作成します。 そのライブ イベントを開始し、エンコーダーを入力に接続するまで、この信号が流れることはありません。

"テープ" はいつでも作成できます。 これは、ライブ出力オブジェクト (このたとえでは "テープ レコーダー") に渡す空のアセットでしかありません。

"テープ レコーダー" も、いつでも作成できます。 ライブ出力の作成は、信号を流し始める前でも、後でも問題ありません。 スピードアップする必要がある場合は、信号を流し始める前に出力を作成すると役立つことがあります。

"テープ レコーダー" を停止するには、`LiveOutput` の `delete` を呼び出します。 この操作では、"テープ" (アセット) の "*コンテンツ*" は削除されません。 アセットは、明示的にそのアセット自体を対象とした `delete` を呼び出さない限り、その中に保存されているビデオ コンテンツともども、そのまま保持されます。 

次のセクションでは、アセットとライブ出力の作成手順を説明します。

#### <a name="create-an-asset"></a>アセットを作成する

ライブ出力で使用されるアセットを作成します。 これは、たとえでは、ライブ ビデオの信号を記録する "テープ" に相当します。 視聴者は、この仮想のテープからコンテンツをライブまたはオンデマンドで見ることができます。

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>ライブ出力を作成する

ライブ出力は、作成されると開始し、削除されると停止します。  ライブ出力を削除しても、基になるアセットやアセット内のコンテンツが削除されることはありません。 "テープ" を取り出しているようなものと考えてください。 記録があるアセットは、任意の期間、残しておくことができます。 取り出されると (つまり、ライブ出力が削除されると)、すぐにオンデマンド表示で利用できるようになります。

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>ストリーミング ロケーターを作成する

> [!NOTE]
> Media Services アカウントの作成時に、既定のストリーミング エンドポイントが停止状態でアカウントに追加されます。 コンテンツのストリーミングを開始し、[ダイナミック パッケージ](encode-dynamic-packaging-concept.md)と動的暗号化を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが実行中状態である必要があります。

ストリーミング ロケーターを使用してアセットを公開した場合、ライブ イベント (DVR ウィンドウの長さまで) は、ストリーミング ロケーターの有効期限またはストリーミング ロケーターを削除するまでの、どちらか早い方のタイミングまで引き続き表示できます。 このようにして、視聴者が仮想の "テープ" レコーディングをライブまたはオンデマンドで視聴できるようにします。 ライブ イベント、DVR ウィンドウ、またはレコーディングが完了した (ライブ出力が削除された) 場合のオンデマンド アセットの視聴には、同じ URL を使用できます。

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateStreamingLocator)]

```csharp

// Get the URL to stream the output
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

### <a name="clean-up-resources-in-your-media-services-account"></a>Media Services アカウント内のリソースをクリーンアップする

ストリーミング イベントが完了し、以前にプロビジョニングされたリソースをクリーンアップする場合は、次の手順を使用します。

1. エンコーダーからのストリームのプッシュを停止します。
1. ライブ イベントを停止します。 ライブ イベントが停止した後は、いかなる課金も発生しません。 もう一度開始する必要がある場合、取り込み URL は同一になるため、エンコーダーを再構成する必要はありません。
1. ライブ イベントのアーカイブをオンデマンド ストリームとして提供し続けるのでない限り、ストリーミング エンドポイントを停止してください。 ライブ イベントが停止状態の場合は、料金は発生しません。

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

## <a name="watch-the-event"></a>イベントの視聴

**Ctrl + F5** キーを押してコードを実行します。 これにより、ライブ イベントの視聴に使用できるストリーミング URL が出力されます。 取得したストリーミング URL をコピーしてストリーミング ロケーターを作成します。 任意のメディア プレーヤーを使用できます。 [Media Player デモ サイト](https://ampdemo.azureedge.net)でストリームをテストするには、[Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) を利用できます。

ライブ イベントが停止すると、イベントがオンデマンド コンテンツに自動的に変換されます。 イベントを停止して削除した後でも、アセットを削除しない限り、ユーザーはアーカイブされたコンテンツをビデオ オン デマンドとしてストリーム配信できます。 イベントがアセットを使用している場合は、アセットを削除できません。まず、イベントを削除する必要があります。

## <a name="clean-up-remaining-resources"></a>残りのリソースをクリーンアップする

このチュートリアルで作成した Media Services アカウントとストレージ アカウントも含め、リソース グループ内のどのリソースも必要なくなった場合は、前に作成したリソース グループを削除します。

次の CLI コマンドを実行します。

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> ライブ イベントを実行したままにすると課金が発生します。 プロジェクトまたはプログラムが応答を停止した場合、またはなんらかの理由で終了した場合、ライブ イベントが課金状態で実行されたままになる可能性があることに注意してください。

## <a name="ask-questions-give-feedback-get-updates"></a>質問、フィードバックの送信、最新情報の入手

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="next-steps"></a>次のステップ

[ファイルをストリーミングする](stream-files-tutorial-with-api.md)
 
