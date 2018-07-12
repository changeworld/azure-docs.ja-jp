---
title: Azure Media Services を使用したライブ ストリーミングの概要 | Microsoft Docs
description: このトピックでは、Azure Media Services v3 を使用したライブ ストリーミングの概要を説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/06/2018
ms.author: juliako
ms.openlocfilehash: e9ecf1ba3022ca057fa09bad2413aa19d902ae23
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972181"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Azure Media Services v3 を使用したライブ ストリーミング

Azure Media Services を使用してライブ ストリーミング イベントを配信する場合、通常、次のコンポーネントが必要になります。

* イベントのブロードキャストに使用されるカメラ。
* カメラ (またはノート PC などのデバイス) からの信号を Media Services ライブ ストリーミング サービスに送信されるストリームに変換するライブ ビデオ エンコーダー。 信号には、広告挿入用の SCTE-35 と広告キューを含めることもできます。 
* Media Services ライブ ストリーミングは、コンテンツの取り込み、プレビュー、パッケージ、録画、暗号化、配信に対応するサービスです。コンテンツは顧客に配信できるほか、CDN に配信して広域配信することもできます。

この記事では、Media Services でのライブ ストリーミングの概要を細部にわたって取り上げると共に、関連する主要コンポーネントの図を紹介しています。

## <a name="overview-of-main-components"></a>主要コンポーネントの概要

Media Services では、[LiveEvents](https://docs.microsoft.com/rest/api/media/liveevents) がライブ ストリーミング コンテンツの処理をつかさどります。 LiveEvent は入力エンドポイントであり、その取り込み URL をオンプレミスのライブ エンコーダーに対して指定します。 LiveEvent は、ライブ エンコーダーから RTMP 形式または Smooth Streaming 形式でライブ入力ストリームを受け取り、1 つまたは複数の [StreamingEndpoints](https://docs.microsoft.com/rest/api/media/streamingendpoints) を介してストリーム配信できる状態にします。 ライブ ストリームの公開、レコーディング、DVR ウィンドウ設定は、[LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) を通じて制御することができます。 また、ストリームはあらかじめプレビューし、確認したうえで処理、配信しますが、LiveEvent はその際に使用するプレビュー エンドポイント (プレビュー URL) も提供します。 

Media Services には**ダイナミック パッケージ**機能があり、MPEG DASH、HLS、Smooth Streaming ストリーミング形式でコンテンツをプレビューして配信することができます。つまり、これらのストリーミング形式に手動で再パッケージ化する必要がありません。 HLS、DASH、Smooth のいずれかと互換性があれば、どのようなプレーヤーでも再生することができます。 [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) を使用して、ストリームをテストすることもできます。

Media Services では、Advanced Encryption Standard (AES-128) または主要な 3 つのデジタル著作権管理 (DRM) システム (Microsoft PlayReady、Google Widevine、および Apple FairPlay) によって動的に暗号化 (**動的暗号化**) されたコンテンツを配信できます。 Media Services では、承認されたクライアントに AES キーと DRM (PlayReady、Widevine、FairPlay) ライセンスを配信するためのサービスも提供しています。

必要であれば、**動的フィルター**を適用することもできます。動的フィルターを使用することで、プレーヤーに送信されるトラック数、形式、ビットレートを制御することができます。 Media Services は広告の挿入にも対応しています。


## <a name="liveevent-types"></a>LiveEvent の種類

[LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) には、ライブ エンコードとパススルーの 2 種類があります。 

### <a name="live-encoding-with-media-services"></a>Media Services でのライブ エンコード

![ライブ エンコード](./media/live-streaming/live-encoding.png)

オンプレミスのライブ エンコーダーは、RTMP か Smooth Streaming (Fragmented MP4) プロトコルで、シングル ビットレート ストリームを Media Services によるライブ エンコードが有効な LiveEvent に送信します。 次に、受信したシングル ビットレート ストリームのマルチ ビットレート (アダプティブ) ビデオ ストリームへのライブ エンコードが LiveEvent で実行されます。 Media Services は、要求に応じて、ストリームを顧客に配信します。

このタイプの LiveEvent を作成するときは、**Basic** (LiveEventEncodingType.Basic) を指定してください。

### <a name="pass-through"></a>パススルー

![パススルー](./media/live-streaming/pass-through.png)

パススルーは、オンプレミスのライブ エンコーダーを使用した長時間実行されるライブ ストリームや24 時間 365 日のリニア ライブ エンコード向けに最適化されています。 オンプレミスのエンコーダーは、マルチビットレート **RTMP** または **Smooth Streaming** (Fragmented MP4) を、**パススルー**配信用に設定された LiveEvent に送信します。 **パススルー**配信とは、取り込んだストリームが、追加の処理なしで **LiveEvent** を通過することをいいます。 

パススルー LiveEvent は最大 4K 解像度に対応しているほか、Smooth Streaming 取り込みプロトコルと併用すれば HEVC パススルーにも対応します。 

このタイプの LiveEvent を作成するときは、**None** (LiveEventEncodingType.None) を指定してください。

> [!NOTE]
> 長期にわたって複数のイベントを配信する場合で、かつオンプレミスのエンコーダーを既に導入済みである場合、ライブ ストリーミングの手段としてはパススルー方式が最も低コストです。 詳しくは、 [価格情報](https://azure.microsoft.com/pricing/details/media-services/) ページを参照してください。
> 

## <a name="liveevent-types-comparison"></a>LiveEvent タイプの比較 

次の表は、2 種類の LiveEvent の機能を比較したものです。

| Feature | パススルー LiveEvent | Basic LiveEvent |
| --- | --- | --- |
| シングル ビットレートの入力がクラウド内でマルチビットレートにエンコードされる |いいえ  |[はい] |
| 最大解像度、層の数 |4Kp30  |720p、6 層、30 fps |
| 入力プロトコル |RTMP、スムーズ ストリーミング |RTMP、スムーズ ストリーミング |
| 料金 |[価格に関するページ](https://azure.microsoft.com/pricing/details/media-services/) を参照し、[ライブ ビデオ] タブをクリックしてください。 |[価格に関するページ](https://azure.microsoft.com/pricing/details/media-services/) |
| 最長実行時間 |24 時間 365 日 |24 時間 365 日 |
| スレートの挿入のサポート |いいえ  |[はい] |
| API による広告信号のサポート|いいえ  |[はい] |
| インバンド SCTE35 による広告信号のサポート|[はい] |[はい] |
| パススルー CEA 608/708 キャプション |[はい] |[はい] |
| 投稿フィードでの短時間の停止から回復する機能 |[はい] |なし (6 秒経過しても入力データがない場合、LiveEvent はスレートを開始します)|
| 均一でない入力 GOP のサポート |[はい] |いいえ – 入力は固定の 2 秒の GOP である必要があります |
| 可変フレーム レートの入力のサポート |[はい] |なし。入力は固定フレーム レートにする必要があります。<br/>たとえば、動きの大きなシーンでは、多少の変化は許容されます。 しかし、エンコーダーは 10 フレーム/秒に下げることはできません。 |
| 入力フィードがなくなった場合の LiveEvent の自動停止 |いいえ  |12 時間後 (実行中の LiveOutput がない場合) |

## <a name="liveevent-states"></a>LiveEvent の状態 

現在の LiveEvent の状態。 指定できる値は、次のとおりです。

|State|説明|
|---|---|
|**Stopped**| これは、LiveEvent 作成後の初期状態です (自動開始が選択された場合を除く)。この状態では、課金は行われません。 この状態で、LiveEvent のプロパティを更新できますが、ストリーミングは許可されていません。|
|**開始中**| LiveEvent を開始しています。 この状態では、課金は行われません。 この状態の場合、更新やストリーミングはできません。 エラーが発生した場合は、LiveEvent は Stopped 状態に戻ります。|
|**実行中**| LiveEvent は、ライブ ストリームを処理できます。 課金が始まります。 課金されないようにするには、LiveEvent を停止する必要があります。|
|**停止中**| LiveEvent を停止しています。 この遷移状態では、課金は行われません。 この状態の場合、更新やストリーミングはできません。|
|**削除中**。| LiveEvent を削除しています。 この遷移状態では、課金は行われません。 この状態の場合、更新やストリーミングはできません。|

## <a name="liveoutput"></a>LiveOutput

ライブ ストリームの公開、レコーディング、DVR ウィンドウ設定は、[LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) を通じて制御することができます。 LiveEvent と LiveOutput の関係は、従来のメディアと似ています。チャネル (LiveEvent) が絶えずコンテンツのストリームを配信するのに対し、プログラム (LiveOutput) は、その LiveEvent 上で決まった時間に生じるイベントです。
録画されたコンテンツの保持時間は、LiveOutput の **ArchiveWindowLength** プロパティで設定できます。 **ArchiveWindowLength** は、アーカイブ ウィンドウの長さを ISO 8601 形式で表した期間です (Digital Video Recorder または DVR)。 この値は、最小 5 分から最大 25 時間までの範囲で設定できます。 

クライアントが現在のライブ位置からさかのぼってシークできる最長時間も **ArchiveWindowLength** によって決まります。 LiveOutput は、指定された時間の長さまでは放送できますが、アーカイブ ウィンドウの長さを過ぎたコンテンツは絶えず破棄されていきます。 さらに、このプロパティの値によって、クライアント マニフェストが肥大した場合の最大サイズも決まります。

各 LiveOutput は[アセット](https://docs.microsoft.com/rest/api/media/assets)に関連付けられ、Media Services アカウントにアタッチされた Azure Storage 内のコンテナーにデータを記録します。 LiveOutput を公開するには、関連付けられた資産の [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) を作成する必要があります。 このロケーターを作成すると、ストリーミング URL を構築してクライアントに提供できます。

LiveEvent は、最大 3 つの同時実行 LiveOutput をサポートするので、同じ受信ストリームのアーカイブを複数作成できます。 これにより、1 つのイベントのさまざまな部分を必要に応じて発行したりアーカイブしたりできます。 たとえばビジネス要件として、24 時間 365 日のライブ リニア フィードを配信する一方で、顧客が見逃したコンテンツをオンデマンド視聴できるよう、終日プログラムの "レコーディング" を作成するとします。  このシナリオではまず、顧客がプライマリ ライブ ストリームとして受信するためのプライマリ LiveOutput を、1 時間未満の短いアーカイブ ウィンドウで作成します。 この LiveOutput の StreamingLocator を作成し、アプリケーションまたは Web サイトに "ライブ" フィードとして発行することになります。  フィードの実行中、同時実行される 2 つ目の LiveOutput を、番組の開始 (または後でトリミングしやすいよう 5 分前) の時点でプログラムから作成できます。この 2 つ目の LiveOutput は、プログラムまたはイベントの終了から 5 分後に停止でき、その後、新しい StreamingLocator を作成して、このプログラムをオンデマンド アセットとしてアプリケーションのカタログに発行することができます。  オンデマンドとしてすぐに共有したい他のプログラム範囲やハイライトについても、このプロセスを繰り返します。このプロセスはすべて、1 つ目の LiveOutput の "ライブ" フィードがリニア フィードを配信し続ける最中に行うことができます。  また、プログラム間の "継ぎ目" として念のために確保しておいたアーカイブの先頭と末尾は、動的フィルター機能を使って LiveOutput からトリミングすることにより、コンテンツの開始と終了の精度を高めることができます。 アーカイブ済みのコンテンツを[変換機能](https://docs.microsoft.com/rest/api/media/transforms)に送り、エンコーディングやフレーム アキュレートなサブクリップ処理を通じて、他のサービスへのシンジケーションとして使用するさまざまな出力形式を得ることも可能です。

## <a name="streamingendpoint"></a>StreamingEndpoint

ストリームが LiveEvent に流れ始めると、資産、LiveOutput、および StreamingLocator を作成することにより、ストリーミング イベントを開始できます。 これにより、ストリームがアーカイブされ、[StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) を介して視聴者がストリームを使用できるようになります。

Media Services アカウントの作成時に、既定のストリーミング エンドポイントが "停止" 状態でアカウントに追加されます。 コンテンツのストリーミングを開始し、ダイナミック パッケージと動的暗号化を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが "実行中" 状態である必要があります。

## <a name="billing"></a>課金

LiveEvent では、その状態が "実行中" に遷移するとすぐに課金が開始されます。 LiveEvent の課金を停止するには、LiveEvent を "停止" する必要があります。

> [!NOTE]
> [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) の **LiveEventEncodingType** を Basic に設定した場合、入力フィードがなくなり、実行中の LiveOutputs がなくなってから 12 時間後に、まだ "実行中" 状態の LiveEvent があれば、それらは Media Services によって自動的に停止されます。 ただし、LiveEvent が "実行中" 状態だった期間については課金されます。
>

次の表は、LiveEvent の状態と課金モードとの対応を示しています。

| LiveEvent の状態 | 課金対象 |
| --- | --- |
| 開始中 |いいえ (遷移状態) |
| 実行中 |はい |
| 停止中 |いいえ (遷移状態) |
| 停止済み |いいえ  |

## <a name="next-steps"></a>次の手順

[ライブ ストリーミングのチュートリアル](stream-live-tutorial-with-api.md)
