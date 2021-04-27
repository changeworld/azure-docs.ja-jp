---
title: Media Services v3 Node.js によるライブ ストリーム配信
titleSuffix: Azure Media Services
description: Node.js を使用してライブ ストリーム配信を行う方法について説明します。
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
ms.openlocfilehash: 749d2fc845f036a2802c80c161b3fc8c171c2555
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730206"
---
# <a name="tutorial-stream-live-with-media-services-using-nodejs-and-typescript"></a>チュートリアル: Node.js と TypeScript を使用して Media Services でライブ ストリーム配信を行う

> [!NOTE]
> このチュートリアルでは Node.js の例を使用していますが、全体的な手順は [REST API](/rest/api/media/liveevents)、[CLI](/cli/azure/ams/live-event)、またはその他サポートされている [SDK](media-services-apis-overview.md#sdks) でも同じです。 

Azure Media Services では、[ライブ イベント](/rest/api/media/liveevents)がライブ ストリーミング コンテンツの処理を受け持ちます。 ライブ イベントは入力エンドポイントであり、その取り込み URL をライブ エンコーダーに対して指定します。 ライブ イベントは、ライブ エンコーダーからライブ入力ストリームを受け取り、1 つまたは複数の[ストリーミング エンドポイント](/rest/api/media/streamingendpoints)を介してストリーミングできる状態にします。 また、ストリームはあらかじめプレビューし、確認したうえで処理、配信しますが、ライブ イベントはその際に使用するプレビュー エンドポイント (プレビュー URL) も提供します。 このチュートリアルでは、Node.js を使用して、**パススルー** タイプのライブ イベントを作成し、[OBS Studio](https://obsproject.com/download) を使用してそこにライブ ストリームを配信する方法について説明します。

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> * このトピックで説明されているサンプル コードをダウンロードする。
> * ライブ ストリーミングを構成および実行するコードを確認する。
> * [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) ([https://ampdemo.azureedge.net](https://ampdemo.azureedge.net)) でイベントを視聴する。
> * リソースをクリーンアップする。


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

チュートリアルを完了するには以下が必要です。

- [Node.js](https://nodejs.org/en/download/)
- [TypeScript](https://www.typescriptlang.org/) をインストールする
- [Media Services アカウントを作成する](./create-account-howto.md)<br/>Media Services アカウント名、ストレージ名、およびリソース名として使用した値を覚えておいてください。
- 「[Azure CLI で Azure Media Services API にアクセスする](./access-api-howto.md)」の手順に従い、資格情報を保存します。 この API にアクセスして環境変数ファイルを構成するには、これらを使用する必要があります。
- Node.js クライアント SDK の使い方を把握するために、あらかじめ [Node.js を使用した構成と接続](./configure-connect-nodejs-howto.md)の攻略ガイドを参照しておいてください。
- Visual Studio Code または Visual Studio をインストールします。
- TypeScript 言語をサポートするように、[Visual Studio Code 環境を設定する](https://code.visualstudio.com/Docs/languages/typescript)。

## <a name="additional-settings-for-live-streaming-software"></a>ライブ ストリーミング ソフトウェアの追加設定

- イベントのブロードキャストに使用するカメラまたはデバイス (ラップトップなど)。
- カメラ ストリームをエンコードし、RTMP プロトコルを使って Media Services のライブ ストリーミング サービスに送信するオンプレミスのソフトウェア エンコーダー ([お勧めのオンプレミスのライブ エンコーダー](encode-recommended-on-premises-live-encoders.md)に関するページを参照してください)。 ストリームは **RTMP** または **Smooth Streaming** 形式である必要があります。  
- このサンプルには、シンプルに作業を開始するという観点から、最初は無料の [Open Broadcaster Software OBS Studio](https://obsproject.com/download) のようなソフトウェア エンコーダーを使用することをお勧めします。

このサンプルは、OBS Studio を使用して、RTMP を取り込みエンドポイントにブロードキャストすることを前提としています。 最初に OBS Studio をインストールします。
OBS Studio では、次のエンコード設定を使用します。

- エンコーダー: NVIDIA NVENC (使用可能な場合) または x264
- レート制御: CBR
- ビットレート: 2500 Kbps (または自分のノート PC に適した値)
- キーフレーム間隔: 2 秒 (低遅延の場合は 1 秒)  
- プリセット: Low-latency Quality または Performance (NVENC の場合)。x264 を使用している場合は "veryfast"
- プロファイル: 高
- GPU: 0 (自動)
- 最大 B フレーム: 2

> [!TIP]
> 先に進む前に、「[Live streaming with Media Services v3](stream-live-streaming-concept.md)」(Media Services v3 によるライブ ストリーミング) を確認してください。

## <a name="download-and-configure-the-sample"></a>サンプルをダウンロードして構成する

次のコマンドを使用して、ライブ ストリーミングの Node.js サンプルが含まれる次の GitHub リポジトリを自分のマシンにクローンします。  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

ライブ ストリーミングのサンプルは [Live](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live) フォルダーにあります。

[AMSv3Samples](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples) フォルダーにある "sample.env" という名前のファイルを ".env" という名前の新しいファイルにコピーし、[Azure CLI を使用した Azure Media Services API へのアクセス](./access-api-howto.md)に関する記事で収集した環境変数設定を保存します。
このファイルがコード サンプルと正しく連動するように、".env" の先頭に "ドット" (.) が含まれていることを確認してください。

この [.env ファイル](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/sample.env)には、AAD アプリケーションのキーとシークレットと共に、Media Services アカウントへの SDK アクセスを認証するために必要なアカウント名とサブスクリプション情報が含まれています。 .gitignore ファイルは、フォークしたリポジトリにこのファイルが公開されないように既に構成されています。 これらの資格情報はアカウントにとって重要なシークレットであるため、漏洩しないようにしてください。

> [!IMPORTANT]
> このサンプルでは、各リソースに一意のサフィックスを使用します。 デバッグをキャンセルした場合、または完全に実行せずにアプリを終了した場合、アカウントに複数のライブ イベントが作成されます。 <br/>実行中のライブ イベントを必ず停止してください。 そうしないと、**料金が発生** します。 このプログラムを実行して最後まで完了すると、リソースが自動的にクリーンアップされます。 プログラムがクラッシュした場合や、誤ってデバッガーを停止したりプログラムの実行を中断したりした場合は、ポータルを再度確認して、実行中またはスタンバイ状態のライブ イベントが残っていないことを確認する必要があります。ライブ イベントが残っていると不要な料金が発生します。

## <a name="examine-the-typescript-code-for-live-streaming"></a>ライブ ストリーミング用の TypeScript コードを調べる

このセクションでは、*Live* プロジェクトの [index.ts](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/Live/index.ts) ファイルで定義されている関数を調べます。

サンプルをクリーンアップせずに複数回実行しても名前の競合が発生しないように、サンプルは各リソースに対して一意のサフィックスを作成します。

### <a name="start-using-media-services-sdk-for-nodejs-with-typescript"></a>TypeScript で Node.js 用 Media Services SDK の使用を開始する

Node.js で Media Services API シリーズの使用を開始するには、まず npm パッケージ マネージャーを使用して [@azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices) SDK モジュールを追加する必要があります。

```bash
npm install @azure/arm-mediaservices
```

package.json では、これが既に構成されているため、*npm install* を実行するだけでモジュールと依存関係を読み込むことができます。

1. **コマンド プロンプト** を開いて、サンプルのディレクトリに移動します。
1. AMSv3Samples フォルダーに移動します。

    ```bash
    cd AMSv3Samples
    ```

1. *packages.json* ファイルで使用されているパッケージをインストールします。

    ```bash
    npm install 
    ```

1. *AMSv3Samples* フォルダーから Visual Studio Code を起動します ( *.vscode* フォルダーと *tsconfig.json* ファイルが格納されているフォルダーから起動するために必要)。

    ```bash
    cd ..
    code .
    ```

*Live* のフォルダーを開き、Visual Studio Code エディターで *index.ts* ファイルを開きます。
*index.ts* ファイルで F5 キーを押してデバッガーを起動します。

### <a name="create-the-media-services-client"></a>Media Services クライアントを作成する

次のコード スニペットは、Node.js で Media Services クライアントを作成する方法を示しています。
このコードでは、最初に AzureMediaServicesOptions の **longRunningOperationRetryTimeout** プロパティを 2 秒に設定して、Azure Resource Management エンドポイント上の実行時間の長い操作の状態をポーリングする際にかかる時間を短縮しています。  ライブ イベントに対する操作のほとんどは非同期で、完了するまでに時間がかかることがあるため、SDK のこのポーリング間隔を既定値の 30 秒から小さくし、ライブ イベントの作成、開始、停止など、すべて非同期呼び出しとなる主要な操作を完了するためにかかる時間を短縮する必要があります。 ほとんどのユース ケース シナリオで推奨される値は 2 秒です。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>ライブ イベントを作成する

このセクションは、**パススルー** タイプの (LiveEventEncodingType が None に設定されている) ライブ イベントを作成する方法を示します。 他の使用可能なライブ イベントの種類の詳細については、「[ライブ イベントの種類](live-event-outputs-concept.md#live-event-types)」を参照してください。 パススルーのほかには、720p または 1080p のアダプティブ ビットレート クラウド エンコードのためのライブ コード変換ライブ イベントを使用できます。
 
ライブ イベントを作成するときは、次のことを指定します。

* ライブ イベントの取り込みプロトコル (現時点では、RTMP(S) プロトコルと Smooth Streaming プロトコルがサポートされます)。<br/>ライブ イベントや、それに関連付けられたライブ出力の実行中は、プロトコル オプションを変更できません。 別のプロトコルが必要な場合は、ストリーミング プロトコルごとに別のライブ イベントを作成する必要があります。  
* 取り込みやプレビューの IP 制限。 このライブ イベントへのビデオの取り込みが許可される IP アドレスを定義できます。 許可される IP アドレスは、1 つの IP アドレス (例: '10.0.0.1')、IP アドレスと CIDR サブネット マスクを使用した IP 範囲 (例: '10.0.0.1/22')、IP アドレスとピリオドで区切られた 10 進数のサブネット マスクを使用した IP 範囲 (例: '10.0.0.1(255.255.252.0)') のいずれかの形で指定できます。<br/>IP アドレスが指定されておらず、規則の定義もない場合は、どの IP アドレスも許可されません。 すべての IP アドレスを許可するには、規則を作成し、0.0.0.0/0 に設定します。<br/>IP アドレスの形式は、4 つの数字を含む IpV4 アドレスか CIDR アドレス範囲にする必要があります。
* イベントの作成時に、そのイベントを自動開始するように設定できます。 <br/>自動開始が true に設定されている場合、ライブ イベントは作成された後に開始されます。 つまり、ライブ イベントの実行が開始されるとすぐに課金が開始されます。 それ以上の課金を停止するには、ライブ イベント リソースの Stop を明示的に呼び出す必要があります。 詳細については、[ライブ イベントの状態と課金](live-event-states-billing-concept.md)に関するページを参照してください。
このほか、各種のスタンバイ モードも利用できます。スタンバイ モードを使うと、"実行中" 状態にすばやく移行できる低コストの "割り当て済み" 状態でライブ イベントを開始できます。 これは、ホット プールのように、ストリーマーにすばやくチャンネルを渡す必要がある局面で便利です。
* ハードウェア ベースのライブ エンコーダーで取り込み URL の予測やメンテナンスをしやすくするには、"useStaticHostname" プロパティを true に設定すると共に、"accessToken" で一意のカスタム GUID を使用します。 詳細については、「[ライブ イベントの取り込み URL](live-event-outputs-concept.md#live-event-ingest-urls)」を参照してください。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveEvent)]

### <a name="create-an-asset-to-record-and-archive-the-live-event"></a>ライブ イベントを記録およびアーカイブするためのアセットを作成する

このコード ブロックでは、ライブ イベントのアーカイブを記録するための "テープ" として使用する空のアセットを作成します。
ここに挙げた概念を学ぶにあたっては、"アセット" オブジェクトというものを、かつてビデオ テープ レコーダーに挿入していたテープにたとえて考えてみるのが良いでしょう。 "ライブ出力" は、テープ レコーダー マシンに相当します。 "ライブ イベント" は、マシンの背面に届くビデオ信号です。

アセット、つまり "テープ" はいつでも作成できることに注意してください。 これは、ライブ出力オブジェクト (この比喩で言うならテープ レコーダー) に渡す空の "アセット" でしかありません。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateAsset)]

### <a name="create-the-live-output"></a>ライブ出力を作成する

このセクションでは、アセット名を入力として使用するライブ出力を作成し、ライブ イベントの記録先を指定します。 また、レコーディングに使用するタイムシフト (DVR) ウィンドウを設定します。
このサンプル コードは、1 時間のタイムシフト ウィンドウを設定する方法を示しています。 これにより、クライアントは、イベントの過去 1 時間のいずれの場所からでも再生できます。  さらに、過去 1 時間のライブ イベントだけがアーカイブに残ります。 必要に応じて、これを最大 25 時間まで延長できます。  また、公開時に URL パスで HLS および DASH マニフェストを使用して、出力マニフェストの名前付けを制御できることにも注意してください。

ここでは "テープ レコーダー" にたとえられているライブ出力も、いつでも作成できます。 つまり、ライブ出力の作成は、信号を流し始める前でも、後でも問題ありません。 スピードアップする必要がある場合は、信号を流し始める前に作成すると役立つことがよくあります。

ライブ出力は作成すると開始され、削除されると停止します。  ライブ出力を削除しても、基になるアセットやアセット内のコンテンツが削除されることはありません。 テープを取り出しているようなものと考えてください。 アセットとそこに記録されている内容は、好きな期間だけ残しておくことができます。アセットを取り出すと (つまり、ライブ出力を削除すると)、そのアセットはすぐに、オンデマンドで視聴できる状態になります。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveOutput)]


### <a name="get-ingest-urls"></a>取り込み URL の取得

ライブ イベントが作成されると、ライブ エンコーダーに提供する取り込み URL を取得できます。 このエンコーダーは、これらの URL を使用して、RTMP プロトコルを使用するライブ ストリームを入力します。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetIngestURL)]

### <a name="get-the-preview-url"></a>プレビュー URL を取得する

previewEndpoint を使用して、エンコーダーからの入力が実際に受信されていることをプレビューおよび確認します。

> [!IMPORTANT]
> 続行する前に、ビデオがプレビュー URL に送信されていることを確認してください。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetPreviewURL)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>ライブ イベントとライブ出力を作成して管理する

ストリームがライブ イベントに流れるようになったら、クライアント プレーヤーで使用するストリーミング ロケーターを公開することによって、ストリーミング イベントを開始できます。 これにより、ストリーミング エンドポイントを介して視聴者がこれを使用できるようになります。

まずは、信号となる "ライブ イベント" を作成します。  この信号は、ライブ イベントを開始し、エンコーダーを入力に接続するまで流れることはありません。

"テープ レコーダー" を停止するには、LiveOutput を対象とした delete を呼び出します。 実際には、これによってテープ "アセット" 上のアーカイブの **コンテンツ** が削除されるわけではなく、"テープ レコーダー" が削除されてアーカイブが停止するだけです。 アセットは、明示的にそのアセット自体を対象とした delete を呼び出さないかぎり、その中に保存されているビデオ コンテンツともども、そのまま保持されます。 liveOutput を削除するとすぐに、"アセット" の記録済みコンテンツは、既に公開されている任意のストリーミング ロケーター URL を介して引き続き再生できます。 アーカイブされたコンテンツをユーザーが再生できないようにする場合は、まずアセットからすべてのロケーターを削除し、配信に CDN を使用している場合は URL パスの CDN キャッシュもフラッシュする必要があります。 そうしないと、CDN の標準的な有効期限設定 (最大 72 時間) の間、コンテンツが CDN のキャッシュに存在することになります。

#### <a name="create-a-streaming-locator-to-publish-hls-and-dash-manifests"></a>ストリーミング ロケーターを作成して HLS および DASH マニフェストを公開する

> [!NOTE]
> Media Services アカウントの作成時に、**既定の** ストリーミング エンドポイントが **停止** 状態でアカウントに追加されます。 コンテンツのストリーミングを開始し、[ダイナミック パッケージ](encode-dynamic-packaging-concept.md)と動的暗号化を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが **実行中** 状態である必要があります。

ストリーミング ロケーターを使用してアセットを公開した場合、ライブ イベント (DVR ウィンドウの長さまで) は、ストリーミング ロケーターの有効期限またはストリーミング ロケーターを削除するまでの、どちらか早い方のタイミングまで引き続き表示できます。 このようにして、視聴者が仮想の "テープ" レコーディングをライブまたはオンデマンドで視聴できるようにします。 ライブ イベント、DVR ウィンドウ、またはレコーディングが完了した (ライブ出力が削除された) 場合のオンデマンド アセットの視聴には、同じ URL を使用できます。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateStreamingLocator)]

#### <a name="build-the-paths-to-the-hls-and-dash-manifests"></a>HLS および DASH マニフェストへのパスを作成する

このサンプルの BuildManifestPaths メソッドは、さまざまなクライアントやプレーヤー フレームワークへの DASH および HLS 配信に使用するストリーミング パスを確定的に作成する方法を示しています。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#BuildManifestPaths)]

## <a name="watch-the-event"></a>イベントの視聴

イベントを監視するには、「ストリーミング ロケーターを作成する」で説明されているコードを実行したときに表示されたストリーミング URL をコピーします。 任意のメディア プレーヤーを使用できます。 [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) は、https://ampdemo.azureedge.net でのストリームをテストするために使用できます。

ライブ イベントが停止すると、イベントがオンデマンド コンテンツに自動的に変換されます。 イベントを停止して削除した後でも、アセットを削除しない限り、ユーザーはアーカイブされたコンテンツをビデオ オン デマンドとしてストリーム配信できます。 イベントがアセットを使用している場合はアセットを削除できません。まずイベントを削除する必要があります。

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Media Services アカウント内のリソースをクリーンアップする

アプリケーションを最後まで実行すると、"cleanUpResources" という名前の関数で、使用されているすべてのリソースが自動的にクリーンアップされます。 アプリケーションまたはデバッガーの実行が完了していることを確認してください。そうしないと、リソースがリークしたり、自分のアカウントに実行中のライブ イベントが残ったりする可能性があります。 Azure portal を再度確認して、Media Services アカウント内のすべてのリソースがクリーンアップされていることを確認してください。  

詳細については、サンプル コードの **cleanUpResources** メソッドを参照してください。

> [!IMPORTANT]
> ライブ イベントを実行したままにすると課金が発生します。 プロジェクトやプログラムがクラッシュする、またはなんらかの理由で閉じられると、ライブ イベントが課金状態で実行されたままになるため、ご注意ください。

## <a name="ask-questions-give-feedback-get-updates"></a>質問、フィードバックの送信、最新情報の入手

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Azure での Node.js に関するその他の開発者向けドキュメント

- [JavaScript および Node.js 開発者向けの Azure](/azure/developer/javascript/)
- [@azure/azure-sdk-for-js GitHub リポジトリにおける Media Services のソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Node.js 開発者向けの Azure パッケージのドキュメント](/javascript/api/overview/azure/)


## <a name="next-steps"></a>次のステップ

[ファイルをストリーミングする](stream-files-tutorial-with-api.md)