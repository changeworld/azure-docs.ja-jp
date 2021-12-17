---
title: Node.js と TypeScript を使用して Media Services でライブ ストリーム配信を行う
titleSuffix: Azure Media Services
description: Node.js、TypeScript、および OBS Studio を使用してライブ イベントをストリーム配信する方法について説明します。
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
ms.custom: mvc, devx-track-nodejs
ms.date: 04/15/2021
ms.author: inhenkel
ms.openlocfilehash: 2b553548111c732ca778c439232e949b0abb4707
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129388605"
---
# <a name="tutorial-stream-live-with-media-services-by-using-nodejs-and-typescript"></a>チュートリアル: Node.js と TypeScript を使用して Media Services でライブ ストリーム配信を行う

Azure Media Services では、[ライブ イベント](/rest/api/media/liveevents)がライブ ストリーミング コンテンツの処理を受け持ちます。 ライブ イベントは入力エンドポイントであり、その取り込み URL をライブ エンコーダーに対して指定します。 ライブ イベントは、ライブ エンコーダーから入力ストリームを受け取り、1 つまたは複数の[ストリーミング エンドポイント](/rest/api/media/streamingendpoints)を介してストリーミングできる状態にします。 また、ストリームはあらかじめプレビューし、確認したうえで処理、配信しますが、ライブ イベントはその際に使用するプレビュー エンドポイント (プレビュー URL) も提供します。 

このチュートリアルでは、Node.js と TypeScript を使用して、"*パススルー*" タイプのライブ イベントを作成し、[OBS Studio](https://obsproject.com/download) を使用してそこにライブ ストリームを配信する方法について説明します。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * サンプル コードをダウンロードする。
> * ライブ ストリーミングを構成および実行するコードを確認する。
> * [Media Player デモ サイト](https://ampdemo.azureedge.net)で [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) を使用してイベントを視聴する。
> * リソースをクリーンアップする。


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> このチュートリアルでは Node.js の例を使用していますが、全体的な手順は [REST API](/rest/api/media/liveevents)、[CLI](/cli/azure/ams/live-event)、またはその他サポートされている [SDK](media-services-apis-overview.md#sdks) でも同じです。 

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のものが必要です。

- [Node.js](https://nodejs.org/en/download/) をインストールします。
- [TypeScript](https://www.typescriptlang.org/) をインストールします。
- [Media Services アカウントを作成する](./create-account-howto.md) リソース グループ名および Media Services アカウント名として使用した値を覚えておいてください。
- [Azure CLI を使用した Azure Media Services API へのアクセス](./access-api-howto.md)に関するページの手順に従い、資格情報を保存します。 API にアクセスして環境変数ファイルを構成するには、これらが必要になります。
- Node.js クライアント SDK の使い方を把握するために、[Node.js を使用した構成と接続](./configure-connect-nodejs-howto.md)に関する記事を参照してください。
- Visual Studio Code または Visual Studio をインストールします。
- TypeScript 言語をサポートするように、[Visual Studio Code 環境を設定](https://code.visualstudio.com/Docs/languages/typescript)します。

ライブストリーミング ソフトウェアには、これらの追加項目が必要です。

- イベントのブロードキャストに使用するカメラまたはデバイス (ラップトップなど)。
- カメラ ストリームをエンコードし、Real-Time Messaging Protocol (RTMP) を介して Media Services ライブストリーミング サービスに送信する、オンプレミスのソフトウェア エンコーダー。 詳細については、「[おすすめのライブ ストリーミング エンコーダー](encode-recommended-on-premises-live-encoders.md)」を参照してください。 ストリームは RTMP または Smooth Streaming 形式である必要があります。

  このサンプルは、Open Broadcaster Software (OBS) Studio を使用して、RTMP を取り込みエンドポイントにブロードキャストすることを前提としています。 [OBS Studio をインストール](https://obsproject.com/download)します。 

  OBS Studio では、次のエンコード設定を使用します。

  - エンコーダー: NVIDIA NVENC (使用可能な場合) または x264
  - レート制御: CBR
  - ビット レート: 2,500 Kbps (または自分のコンピューターに適した値)
  - キーフレーム間隔: 2 秒 (低遅延の場合は 1 秒)  
  - プリセット: Low-latency Quality または Performance (NVENC の場合)。x264 を使用している場合は "veryfast"
  - プロファイル: 高
  - GPU: 0 (自動)
  - 最大 B フレーム: 2

> [!TIP]
> 先に進む前に、「[Azure Media Services v3 を使用したライブ ストリーミング](stream-live-streaming-concept.md)」を確認してください。

## <a name="download-and-configure-the-sample"></a>サンプルをダウンロードして構成する

次のコマンドを使用して、ライブストリーミングの Node.js サンプルが含まれる GitHub リポジトリを自分のマシンにクローンします。  

```bash
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

ライブストリーミングのサンプルは [Live](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live) フォルダーにあります。

[AMSv3Samples](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples) フォルダーにある *sample.env* という名前のファイルを *.env* という名前の新しいファイルにコピーし、[Azure CLI を使用した Azure Media Services API へのアクセス](./access-api-howto.md)に関する記事で収集した環境変数設定を保存します。
このファイルがコード サンプルと正しく連動するように、ファイル名の "env" の前にドット (.) が含まれていることを確認してください。

[.env ファイル](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/sample.env)には、Azure Active Directory (Azure AD) アプリケーションのキーおよびシークレットが含まれています。 また、Media Services アカウントへの SDK アクセスを認証するために必要なアカウント名とサブスクリプション情報も含まれています。 *.gitignore* ファイルは、フォークしたリポジトリにこのファイルが公開されないように既に構成されています。 これらの資格情報はアカウントにとって重要なシークレットであるため、漏洩しないようにしてください。

> [!IMPORTANT]
> このサンプルでは、各リソースに一意のサフィックスを使用します。 デバッグを取り消した場合、または完全に実行せずにアプリを終了した場合、アカウントに複数のライブ イベントが作成されます。 
>
> 実行中のライブ イベントを必ず停止してください。 そうしないと、"*料金が発生*" します。 リソースが自動的にクリーンアップされるように、このプログラムを最後まで実行します。 プログラムが停止した場合や、誤ってデバッガーを停止したりプログラムの実行を中断したりした場合は、ポータルを再度確認して、実行中またはスタンバイ状態のライブ イベントが残っていないことを確認する必要があります。ライブ イベントが残っていると不要な料金が発生します。

## <a name="examine-the-typescript-code-for-live-streaming"></a>ライブ ストリーミング用の TypeScript コードを調べる

このセクションでは、*Live* プロジェクトの [index.ts](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/Live/index.ts) ファイルで定義されている関数を調べます。

サンプルをクリーンアップせずに複数回実行しても名前の競合が発生しないように、サンプルは各リソースに対して一意のサフィックスを作成します。

### <a name="start-using-the-media-services-sdk-for-nodejs-with-typescript"></a>TypeScript で Node.js 用 Media Services SDK の使用を開始する

Node.js で Media Services API の使用を開始するには、まず npm パッケージ マネージャーを使用して [@azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices) SDK モジュールを追加する必要があります。

```bash
npm install @azure/arm-mediaservices
```

*package.json* ファイルでは、これは既に構成されています。 モジュールと依存関係を読み込むには、単に `npm install` を実行します。

1. コマンド プロンプトを開いて、サンプルのディレクトリに移動します。
1. *AMSv3Samples* フォルダーに移動します。

    ```bash
    cd AMSv3Samples
    ```

1. *packages.json* ファイルで使用されているパッケージをインストールします。

    ```bash
    npm install 
    ```

1. *AMSv3Samples* フォルダーから Visual Studio Code を開きます ( *.vscode* フォルダーと *tsconfig.json* ファイルが格納されているフォルダーから開始するために必要)。

    ```bash
    cd ..
    code .
    ```

*Live* のフォルダーを開き、Visual Studio Code エディターで *index.ts* ファイルを開きます。

*index.ts* ファイル内にいるときに、F5 キーを押してデバッガーを開きます。

### <a name="create-the-media-services-client"></a>Media Services クライアントを作成する

次のコード スニペットは、Node.js で Media Services クライアントを作成する方法を示しています。

このコードでは、`AzureMediaServicesOptions` の `longRunningOperationRetryTimeout` プロパティを、既定値である 30 秒から 2 秒に変更しています。 この変更により、Azure Resource Manager エンドポイントで実行時間の長い操作の状態をポーリングするためにかかる時間が短縮されます。 これにより、ライブ イベントの作成、開始、停止などの主要な操作 (すべて非同期呼び出し) を完了するための時間が短縮されます。 ほとんどのシナリオで、値を 2 秒にすることをお勧めします。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>ライブ イベントを作成する

このセクションでは、基本 "*パススルー*" タイプのライブ イベント (`LiveEventEncodingType` を `PassthroughBasic` に設定) を作成する方法について説明します。 使用可能なライブ イベントの種類については、「[ライブ イベントの種類](live-event-outputs-concept.md#live-event-types)」のセクションを参照してください。 基本または標準のパススルーのほかには、720p または 1080p のアダプティブ ビットレート クラウド エンコードのためのライブ エンコーディング イベントを使用できます。
 
ライブ イベントを作成するときに、次の項目を指定したい場合もあります。

* **ライブ イベントの取り込みプロトコル**。 現時点では、RTMP、RTMPS、および Smooth Streaming プロトコルがサポートされています。 ライブ イベントや、それに関連付けられたライブ出力の実行中は、プロトコル オプションを変更できません。 別のプロトコルが必要な場合は、ストリーミング プロトコルごとに別のライブ イベントを作成します。  
* **取り込みやプレビューの IP 制限**。 このライブ イベントへのビデオの取り込みが許可される IP アドレスを定義できます。 許可される IP アドレスは、これらのいずれかとして指定できます。

  * 単一の IP アドレス (例: `10.0.0.1`)
  * IP アドレスと Classless Inter-Domain Routing (CIDR) サブネット マスクを使用する IP 範囲 (例: `10.0.0.1/22`)
  * IP アドレスとピリオド区切りのサブネット マスクを使用する IP 範囲 (例: `10.0.0.1(255.255.252.0)`)

  IP アドレスが指定されておらず、規則の定義もない場合は、どの IP アドレスも許可されません。 すべての IP アドレスを許可するには、規則を作成し、`0.0.0.0/0` に設定します。 IP アドレスの形式は、4 つの数字を含む IPv4 アドレスか CIDR アドレス範囲にする必要があります。
* **イベントの作成時の自動開始**。 自動開始が `true` に設定されている場合、ライブ イベントは作成された後に開始されます。 つまり、ライブ イベントの実行が開始されるとすぐに課金が開始されます。 それ以上の課金を停止するには、ライブ イベント リソースの `Stop` を明示的に呼び出す必要があります。 詳細については、[ライブ イベントの状態と課金](live-event-states-billing-concept.md)に関するページを参照してください。

  各種のスタンバイ モードも利用できます。スタンバイ モードを使うと、実行中状態にすばやく移行できる低コストの "割り当て済み" 状態でライブ イベントを開始できます。 これは、ホット プールのように、ストリーマーにすばやくチャンネルを渡す必要がある局面で便利です。
* **静的ホスト名と一意の GUID**。 ハードウェアベースのライブ エンコーダーで取り込み URL の予測やメンテナンスをしやすくする場合には、`useStaticHostname` プロパティを `true` に設定します。 `accessToken` の場合は、カスタムの一意の GUID を使用します。 詳細については、「[ライブ イベントの取り込み URL](live-event-outputs-concept.md#live-event-ingest-urls)」のセクションを参照してください。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveEvent)]

### <a name="create-an-asset-to-record-and-archive-the-live-event"></a>ライブ イベントを記録およびアーカイブするためのアセットを作成する

次のコード ブロックでは、ライブ イベントのアーカイブを記録するための "テープ" として使用する空のアセットを作成します。

これらの概念を学ぶにあたっては、アセット オブジェクトというものを、かつてビデオ テープ レコーダーに挿入していたテープにたとえて考えてみるとわかりやすくなります。 ライブ出力は、テープ レコーダー マシンに相当します。 ライブ イベントは、マシンの背面に届くビデオ信号です。

アセット、つまり "テープ" は、いつでも作成できることに注意してください。 空のアセットを、ライブ出力オブジェクト (このたとえでは "テープ レコーダー") に渡します。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateAsset)]

### <a name="create-the-live-output"></a>ライブ出力を作成する

このセクションでは、アセット名を入力として使用するライブ出力を作成し、ライブ イベントの記録先を指定します。 また、レコーディングに使用するタイムシフト (DVR) ウィンドウを設定します。

このサンプル コードは、1 時間のタイムシフト ウィンドウを設定する方法を示しています。 このウィンドウにより、クライアントは、イベントの過去 1 時間の何でも再生できます。 さらに、過去 1 時間のライブ イベントだけがアーカイブに残ります。 必要に応じて、このウィンドウを最大 25 時間まで延長できます。  また、HTTP ライブ ストリーミング (HLS) および Dynamic Adaptive Streaming over HTTP (DASH) マニフェストが使用する出力マニフェストの名前付けを、公開時に URL パスで制御できることにも注意してください。

ライブ出力 (たとえでは "テープ レコーダー") も、いつでも作成できます。 ライブ出力の作成は、信号を流し始める前でも、後でも問題ありません。 スピードアップする必要がある場合は、信号を流し始める前に出力を作成すると役立つことがよくあります。

ライブ出力は、作成されると開始し、削除されると停止します。  ライブ出力を削除しても、基になるアセットやアセット内のコンテンツが削除されることはありません。 "テープ" を取り出しているようなものと考えてください。 記録があるアセットは、任意の期間、残しておくことができます。 取り出されると (つまり、ライブ出力が削除されると)、すぐにオンデマンド表示で利用できるようになります。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveOutput)]


### <a name="get-ingest-urls"></a>取り込み URL の取得

ライブ イベントの作成後、ライブ エンコーダーに提供する取り込み URL を取得できます。 エンコーダーは、これらの URL を使用し、RTMP プロトコルを使用して、ライブ ストリームを入力します。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetIngestURL)]

### <a name="get-the-preview-url"></a>プレビュー URL を取得する

プレビューして、エンコーダーからの入力が受信されていることを確認するには、`previewEndpoint` を使用します。

> [!IMPORTANT]
> 続行する前に、ビデオがプレビュー URL に送信されていることを確認してください。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetPreviewURL)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>ライブ イベントとライブ出力を作成して管理する

ストリームがライブ イベントに流れるようになったら、クライアント プレーヤーで使用するストリーミング ロケーターを公開することによって、ストリーミング イベントを開始できます。 これにより、ストリーミング エンドポイントを介して視聴者がこれを使用できるようになります。

まずは、ライブ イベントを作成することで、信号を作成します。 そのライブ イベントを開始し、エンコーダーを入力に接続するまで、この信号が流れることはありません。

"テープ レコーダー" を停止するには、`LiveOutput` の `delete` を呼び出します。 この操作では、"テープ" (アセット) 上のアーカイブの "*コンテンツ*" は削除されません。 "テープ レコーダー" のみが削除され、アーカイブ処理が停止されます。 アセットは、明示的にそのアセット自体を対象とした `delete` を呼び出さない限り、その中に保存されているビデオ コンテンツともども、そのまま保持されます。 `LiveOutput` を削除するとすぐに、アセットの記録済みコンテンツは、公開されている任意のストリーミング ロケーター URL を介して引き続き再生できます。 

アーカイブされたコンテンツをクライアントが再生できないようにするには、まず、アセットからすべてのロケーターを削除する必要があります。 配信に CDN を使用している場合は、URL パス上のコンテンツ配信ネットワーク (CDN) キャッシュもフラッシュします。 そうしないと、CDN の標準の有効期限設定 (最大 72 時間) の間、コンテンツが CDN のキャッシュに存在することになります。

#### <a name="create-a-streaming-locator-to-publish-hls-and-dash-manifests"></a>ストリーミング ロケーターを作成して HLS および DASH マニフェストを公開する

> [!NOTE]
> Media Services アカウントの作成時に、既定のストリーミング エンドポイントが停止状態でアカウントに追加されます。 コンテンツのストリーミングを開始し、[ダイナミック パッケージ](encode-dynamic-packaging-concept.md)と動的暗号化を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが実行中状態である必要があります。

ストリーミング ロケーターを使用してアセットを公開した場合、ライブ イベント (DVR ウィンドウの長さまで) は、ストリーミング ロケーターの有効期限またはストリーミング ロケーターを削除するまでの、どちらか早い方のタイミングまで引き続き表示できます。 このようにして、視聴者が仮想の "テープ" レコーディングをライブまたはオンデマンドで視聴できるようにします。 ライブ イベント、DVR ウィンドウ、またはレコーディングが完了した (ライブ出力が削除された) 場合のオンデマンド アセットの視聴には、同じ URL を使用できます。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateStreamingLocator)]

#### <a name="build-the-paths-to-the-hls-and-dash-manifests"></a>HLS および DASH マニフェストへのパスを作成する

このサンプルの `BuildManifestPaths` メソッドは、さまざまなクライアントやプレーヤー フレームワークへの HLS 配信または DASH 配信に使用するストリーミング パスを確定的に作成する方法を示しています。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#BuildManifestPaths)]

## <a name="watch-the-event"></a>イベントの視聴

イベントを視聴するには、ストリーミング ロケーターを作成するコードを実行したときに取得したストリーミング URL をコピーします。 任意のメディア プレーヤーを使用できます。 [Media Player デモ サイト](https://ampdemo.azureedge.net)でストリームをテストするには、[Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) を利用できます。

ライブ イベントが停止すると、イベントがオンデマンド コンテンツに自動的に変換されます。 イベントを停止して削除した後でも、アセットを削除しない限り、ユーザーはアーカイブされたコンテンツをビデオ オン デマンドとしてストリーム配信できます。 イベントがアセットを使用している場合は、アセットを削除できません。まず、イベントを削除する必要があります。

## <a name="clean-up-resources-in-your-media-services-account"></a>Media Services アカウント内のリソースをクリーンアップする

アプリケーションを最後まで実行すると、`cleanUpResources` 関数で、使用されているすべてのリソースが自動的にクリーンアップされます。 アプリケーションまたはデバッガーの実行が完了していることを確認してください。そうしないと、リソースがリークしたり、自分のアカウントに実行中のライブ イベントが残ったりする可能性があります。 Azure portal を再度確認して、Media Services アカウント内のすべてのリソースがクリーンアップされていることを確認してください。 

詳細については、サンプル コードの `cleanUpResources` メソッドを参照してください。

> [!IMPORTANT]
> ライブ イベントを実行したままにすると課金が発生します。 プロジェクトまたはプログラムが応答を停止した場合、またはなんらかの理由で終了した場合、ライブ イベントが課金状態で実行されたままになる可能性があることに注意してください。

## <a name="ask-questions-give-feedback-get-updates"></a>質問、フィードバックの送信、最新情報の入手

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Azure での Node.js に関するその他の開発者向けドキュメント

- [JavaScript および Node.js 開発者向けの Azure](/azure/developer/javascript/)
- [@azure/azure-sdk-for-jsGitHub リポジトリ](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)における Media Services のソース コード
- [Node.js 開発者向けの Azure パッケージのドキュメント](/javascript/api/overview/azure/)


## <a name="next-steps"></a>次のステップ

[ファイルをストリーミングする](stream-files-tutorial-with-api.md)