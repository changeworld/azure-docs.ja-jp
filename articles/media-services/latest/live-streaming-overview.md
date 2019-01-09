---
title: Azure Media Services を使用したライブ ストリーミングの概要 | Microsoft Docs
description: この記事では、Azure Media Services v3 を使用したライブ ストリーミングの概要を説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/26/2018
ms.author: juliako
ms.openlocfilehash: b51f2850a925fcd9daf3a07d8db66193555df0fa
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000256"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Azure Media Services v3 を使用したライブ ストリーミング

Azure Media Services では、Azure クラウドで顧客にライブ イベントを配信することができます。 Media Services でライブ イベントをストリーミングするには、以下が必要です。  

- ライブ イベントをキャプチャするために使用するカメラ。
- カメラ (またはノート PC などのデバイス) からの信号を Media Services に送信される投稿フィードに変換するライブ ビデオ エンコーダー。 投稿フィードでは、SCTE-35 マーカーなどの広告に関連する信号を含めることができます。
- コンテンツの取り込み、プレビュー、パッケージ、録画、暗号化、ライブ イベントのブロードキャスト (顧客のほか、CDN に配信して広域配信することもできる) を可能にする、Media Services のコンポーネント。

この記事では、Media Services でのライブ ストリーミングの概要を細部にわたって取り上げると共に、ガイダンス、関連する主要コンポーネントの図を紹介しています。

## <a name="overview-of-main-components"></a>主要コンポーネントの概要

Media Services でのオンデマンド ストリームまたはライブ ストリームを配信するためには、少なくとも 1 つの [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) が必要です。 Media Services アカウントの作成時に、**既定**の StreamingEndpoint が "**停止**" 状態でアカウントに追加されます。 視聴者にコンテンツをストリーミングする StreamingEndpoint を開始する必要があります。 既定の **StreamingEndpoint** を使用するか、必要な構成と CDN の設定を使用してカスタマイズした別の **StreamingEndpoint** を作成することができます。 複数の StreamingEndpoint を有効にしてそれぞれ別の CDN をターゲットとし、コンテンツ配信用の一意のホスト名を提供するように決定することができます。 

Media Services では、[LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) がライブ ビデオ フィードの取り込みと処理を担当します。 LiveEvent を作成するときに、リモートのエンコーダーからライブ信号を送信するために使用できる入力エンドポイントが作成されます。 リモート ライブ エンコーダーは、[RTMP](https://www.adobe.com/devnet/rtmp.html) または [スムーズ ストリーミング](https://msdn.microsoft.com/library/ff469518.aspx) (Fragmented MP4) プロトコルのいずれかを使用して、投稿フィードをその入力エンドポイントに送信します。  

**LiveEvent** が投稿フィードの受信を開始したら、ライブ ストリームを公開する前に、そのプレビュー エンドポイント (プレビュー URL) を使用して、ライブ ストリームが受信されていることをプレビューして検証できます。 プレビュー ストリームが良好であることを確認したら、その LiveEvent を使用して、1 つ以上の (事前に作成した) **StreamingEndpoints** を通した配信に使用できるライブ ストリームを作成できます。 これを実現するために、**LiveEvent** に新規 [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) を作成します。 

**LiveOutput** オブジェクトは、ライブ ストリームをキャッチして Media Services アカウントの資産に記録するテープ レコーダーのようなものです。 記録されたコンテンツは、アカウントに接続されている Azure Storage アカウントに保持され、資産リソースによって定義されたコンテナーに保持されます。  また、**LiveOuput** では、アーカイブ記録に保持されるストリームの量 (たとえば、クラウド DVR の容量) など、送信ライブ ストリームの一部のプロパティを制御することもできます。 ディスク上のアーカイブは、**LiveOutput** の **archiveWindowLength** プロパティで指定されているコンテンツ量のみを保持する循環アーカイブ「ウィンドウ」です。 このウィンドウの範囲外のコンテンツは、ストレージ コンテナーから自動的に破棄され、復旧できません。 1 つの LiveEvent に、アーカイブの長さと設定の異なる複数の LiveOutput (最大 3 つ) を作成できます。  

Media Services では、**ダイナミック パッケージ**を利用して、サービスに送信する投稿フィードからのライブ ストリームを [MPEG DASH、HLS、およびスムーズ ストリーミング形式](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) でプレビューしてブロードキャストできます。 視聴者は、HLS、DASH、またはスムーズ ストリーミングと互換性のある任意のプレーヤーを使用して、ライブ ストリームを再生できます。 これらいずれかのプロトコルでストリームを配信するために、Web またはモバイル アプリケーションの [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) を使用することができます。

Media Services では、Advanced Encryption Standard (AES-128) または主要な 3 つのデジタル著作権管理 (DRM) システム (Microsoft PlayReady、Google Widevine、および Apple FairPlay) によって動的に暗号化 (**動的暗号化**) されたコンテンツを配信できます。 Media Services では、承認されたクライアントに AES キーと DRM ライセンスを配信するためのサービスも提供しています。 Media Services でコンテンツを暗号化する方法の詳細については、「[コンテンツ保護の概要](content-protection-overview.md)」を参照してください。

必要であれば、動的フィルターを適用することもできます。動的フィルターを使用することで、プレーヤーに送信されるトラック数、形式、ビットレート、プレゼンテーションの時間枠を制御することができます。 

### <a name="new-capabilities-for-live-streaming-in-v3"></a>v3 のライブ ストリーミングの新機能

Media Services の v3 API を使用すると、次の新機能によるメリットが得られます。

- 新しい低待機時間モード。 詳しくは、「[待機時間](live-event-latency.md)」をご覧ください。
- RTMP サポートの強化 (安定性の向上およびソース エンコーダー サポートの強化)。
- RTMPS のセキュアな取り込み。<br/>LiveEvent を作成すると、4 つの取り込み URL を取得します。 4 つの取り込み URL はほとんど同じで。ストリーミング トークン (AppId) は同じですが、ポート番号の部分のみが異なります。 URL のうち 2 つは RTMPS のプライマリとバックアップです。   
- Media Services を使用してシングル ビットレートのコントリビューション フィードをマルチ ビットレートの出力ストリームにコード変換すると、最大 24 時間のライブ イベントをストリーミングできます。 

## <a name="liveevent-types"></a>LiveEvent の種類

[LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) には、パススルーとライブ エンコードの 2 種類があります。 

### <a name="pass-through"></a>パススルー

![パススルー](./media/live-streaming/pass-through.png)

パススルー LiveEvent を使用する場合は、オンプレミス ライブ エンコーダーを活用して、マルチ ビットレート ビデオ ストリームを生成し、(RTMP または Fragmented MP4 プロトコルを使用して) LiveEvent への投稿フィードとして送信します。 その後、LiveEvent は追加の処理なしで受信ビデオ ストリームを通過します。 このようなパススルー LiveEvent は、長時間実行されるライブ ストリームや 24 時間 365 日のリニア ライブ エンコード向けに最適化されています。 このタイプの LiveEvent を作成するときは、None (LiveEventEncodingType.None) を指定してください。

投稿フィードは最大 4K の解像度と 60 フレーム/秒のフレーム レートで、H.264/AVC または H.265/HEVC のいずれかのビデオ コーデックと AAC (AAC-LC、HE-AACv1、または HE-AACv2) オーディオ コーデックを使用して送信することができます。  詳細については、[LiveEvent タイプの比較と制限事項](live-event-types-comparison.md)についての記事を参照してください。

> [!NOTE]
> 長期にわたって複数のイベントを配信する場合で、かつオンプレミスのエンコーダーを既に導入済みである場合、ライブ ストリーミングの手段としてはパススルー方式が最も低コストです。 詳しくは、 [価格情報](https://azure.microsoft.com/pricing/details/media-services/) ページを参照してください。
> 

[MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126) のライブ例を参照してください。

### <a name="live-encoding"></a>ライブ エンコード  

![ライブ エンコード](./media/live-streaming/live-encoding.png)

Media Services によるライブ エンコードを使用する場合は、オンプレミス ライブ エンコーダーを、(RTMP または Fragmented MP4 プロトコルを使用して) LiveEvent への投稿フィードとしてシングル ビットレート ビデオを送信するように構成します。 LiveEvent は、受信シングル ビットレート ストリームを[マルチ ビットレート ビデオ ストリーム](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)にエンコードし、そのストリームを、MPEG-DASH、HLS、スムーズ ストリーミングなどのプロトコルを介して再生デバイスに配信できるようにします。 このタイプの LiveEvent を作成するときは、エンコードの種類に **Basic** (LiveEventEncodingType.Basic) を指定してください。

投稿フィードは最大 1080p の解像度と 30 フレーム/秒のフレーム レートで、H.264/AVC ビデオ コーデックと AAC (AAC-LC、HE-AACv1、または HE-AACv2) オーディオ コーデックを使用して送信することができます。 詳細については、[LiveEvent タイプの比較と制限事項](live-event-types-comparison.md)についての記事を参照してください。

## <a name="liveevent-types-comparison"></a>LiveEvent タイプの比較

2 つの LiveEvent タイプの機能の比較表は、[比較](live-event-types-comparison.md)についての記事に記載されています。

## <a name="liveoutput"></a>LiveOutput

[LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) では、記録するストリームの量 (たとえば、クラウド DVR の容量) や視聴者がライブ ストリームの視聴を開始することができるかどうかなど、送信ライブ ストリームのプロパティを制御することができます。 **LiveEvent** とその **LiveOutput** との関係は、従来のテレビの放送と似ており、チャンネル (**LiveEvent**) はビデオの一定のストリームを表し、録画 (**LiveOutput**) は特定の時間セグメント (たとえば、午後 6 時 30 分から午後 7 時 00 分までの夕方のニュース) を対象としています。 テレビはデジタル ビデオ レコーダー (DVR) を使用して録画することができます。それと同様の LiveEvent の機能は ArchiveWindowLength プロパティを介して管理されます。 これは、ISO-8601 形式で表した期間 (PTHH:MM:SS など) であり、DVR の容量を指定し、最短で 3 分から最長で 25 時間まで設定できます。


> [!NOTE]
> **LiveOutput** は作成すると開始され、削除されると停止します。 **LiveOutput** を削除しても、基になる**資産**と資産のコンテンツは削除されません。  

詳細については、「[クラウド DVR の使用](live-event-cloud-dvr.md)」を参照してください。

## <a name="streamingendpoint"></a>StreamingEndpoint

ストリームが **LiveEvent** に流れ始めると、**資産**、**LiveOutput**、および **StreamingLocator** を作成することにより、ストリーミング イベントを開始できます。 これにより、ストリームがアーカイブされ、[StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) を介して視聴者がストリームを使用できるようになります。

Media Services アカウントの作成時に、既定のストリーミング エンドポイントが "停止" 状態でアカウントに追加されます。 コンテンツのストリーミングを開始し、ダイナミック パッケージと動的暗号化を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが "実行中" 状態である必要があります。

## <a name="a-idbilling-liveevent-states-and-billing"></a><a id="billing" />LiveEvent の状態と課金

LiveEvent では、その状態が "**実行中**" に遷移するとすぐに課金が開始されます。 LiveEvent の課金を停止するには、LiveEvent を停止する必要があります。

詳細については、[状態と課金](live-event-states-billing.md)についてのページを参照してください。

## <a name="latency"></a>Latency

LiveEvent の待機時間の詳細については、[待機時間](live-event-latency.md)についてのページを参照してください。

## <a name="live-streaming-workflow"></a>ライブ ストリーミング ワークフロー

ライブ ストリーミング ワークフローの手順は次のとおりです。

1. LiveEvent を作成します。
2. 新しい資産オブジェクトを作成します。
3. LiveOutput を作成し、作成した資産の名前を使用します。
4. DRM でコンテンツを暗号化する場合は、ストリーミング ポリシーとコンテンツ キーを作成します。
5. DRM を使用しない場合、組み込みのストリーミング ポリシー タイプを使用してストリーミング ロケーターを作成します。
6. 使用する URL を返すためのパスをストリーミング ポリシーに列挙します (これらは決定論的です)。
7. ストリーミングするストリーミング エンドポイントのホスト名を取得します。 
8. 手順 6.の URL と、手順 7 のホスト名を組み合わせて、完全な URL を取得します。

詳細については、[ライブの .NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live) サンプルをベースとした[ライブ ストリーミング チュートリアル](stream-live-tutorial-with-api.md)を参照してください。

## <a name="next-steps"></a>次の手順

- [LiveEvent タイプの比較](live-event-types-comparison.md)
- [状態と課金](live-event-states-billing.md)
- [待機時間](live-event-latency.md)
