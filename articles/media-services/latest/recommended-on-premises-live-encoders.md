---
title: Media Services で推奨されるライブ ストリーミング エンコーダー - Azure | Microsoft Docs
description: Media Services で推奨されるライブ ストリーミング オンプレミス エンコーダーについて知る
services: media-services
keywords: エンコード;エンコーダー;メディア
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 02/10/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 60f9209129c75e329b283045d19b4b5140b40ec2
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2020
ms.locfileid: "78268188"
---
# <a name="recommended-on-premises-live-streaming-encoders"></a>推奨されるオンプレミス ライブ ストリーミング エンコーダー

Azure Media Services では、[ライブ イベント](https://docs.microsoft.com/rest/api/media/liveevents) (チャネル) は、ライブ ストリーミング コンテンツを処理するためのパイプラインを表します。 ライブ イベントは、次の 2 つの方法のいずれかでライブ入力ストリームを受信します。

* オンプレミスのライブ エンコーダーは、マルチビットレート RTMP またはスムーズ ストリーミング (フラグメント化 MP4) のストリームを、Media Services によるライブ エンコードの実行が無効な Live Event に送信します。 取り込んだストリームは、追加の処理なしで Live Event を通過します。 この方式は、 **パススルー**と呼ばれます。 ライブ エンコーダーの場合は、シングルビットレートのストリームではなく、マルチビットレートのストリームをパススルー ライブ イベントに送信して、クライアントへのアダプティブ ビットレート ストリーミングを可能にすることをお勧めします。 

    パススルー ライブ イベントにマルチビットレートのストリームを使用している場合は、再生側での予期しない動作を避けるために、異なるビットレートでのビデオの GOP サイズおよびビデオ フラグメントを同期する必要があります。

  > [!NOTE]
  > パススルー方式を使用することが、ライブ ストリーミングを行う最も経済的な方法です。
 
* オンプレミスのライブ エンコーダーでは、次のいずれかの形式で、Media Services によるライブ エンコードが有効な Live Event にシングル ビットレート ストリームが送信されます。RTMP またはスムーズ ストリーミング (フラグメント化 MP4)。 次に、受信したシングル ビットレート ストリームのマルチ ビットレート (アダプティブ) ビデオ ストリームへのライブ エンコードが Live Event で実行されます。

Media Services でのライブ エンコードの詳細については、[Media Services v3 でのライブ ストリーミング](live-streaming-overview.md)に関するページをご覧ください。

## <a name="encoder-requirements"></a>エンコーダーの要件

HTTPS または RTMPS プロトコルを使用する場合、エンコーダーで TLS 1.2 がサポートされている必要があります。

## <a name="live-encoders-that-output-rtmp"></a>RTMP を出力するライブ エンコーダー

Media Services では、RTMP を使用した、次のいずれかのライブ エンコーダーを出力として使用することを推奨しています。 サポートされる URL スキームは `rtmp://` または `rtmps://` です。

RTMP を使用してデータをストリーミングしている場合は、ファイアウォールまたはプロキシ、あるいはその両方の設定で、送信 TCP ポート 1935 と 1936 が開いていることを確認します。<br/><br/>
RTMP を使用してデータをストリーミングしている場合は、ファイアウォールまたはプロキシ、あるいはその両方の設定で、送信 TCP ポート 2935 と 2936 が開いていることを確認します。

> [!NOTE]
> RTMPS プロトコルを使用する場合、エンコーダーで TLS 1.2 がサポートされている必要があります。

- Adobe Flash Media Live Encoder 3.2
- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- Elemental Live (バージョン 2.14.15 以降)
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream Wirecast (TLS 1.2 の要件によりバージョン 13.0.2 以降)
- Telestream Wirecast S (RTMP のみがサポートされています)
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Hero 7 および Hero 8
- [Restream.io](https://restream.io/)

## <a name="live-encoders-that-output-fragmented-mp4"></a>フラグメント化 MP4 を出力するライブ エンコーダー

Media Services では、マルチビットレートのスムーズ ストリーミング (フラグメント化 MP4) を使用した、次のいずれかのライブ エンコーダーを出力として使用することを推奨しています。 サポートされる URL スキームは `http://` または `https://` です。

> [!NOTE]
> HTTPS プロトコルを使用する場合、エンコーダーで TLS 1.2 がサポートされている必要があります。

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live (TLS 1.2 の要件により 2.14.15 以降)
- Envivio 4Caster C4 Gen III 
- Imagine Communications Selenio MCP3
- Media Excel Hero Live と Hero 4K (UHD/HEVC)
- [Ffmpeg](https://www.ffmpeg.org)

> [!TIP]
>  複数の言語でライブ イベント (たとえば、英語のオーディオ トラックやスペイン語のオーディオ トラックなど) をストリーミングする場合は、パススルー ライブ イベントにライブ フィードを送信するように構成された Media Excel ライブ エンコーダーを利用して、これを実現できます。

## <a name="configuring-on-premises-live-encoder-settings"></a>オンプレミス ライブ エンコーダーの設定を構成する

ご利用のライブ イベントの種類に有効な設定に関する詳細については、「[ライブ イベントの種類の比較](live-event-types-comparison.md)」を参照してください。

### <a name="playback-requirements"></a>再生要件

コンテンツを再生するには、音声と映像の両方のストリームが存在する必要があります。 映像のみをストリーム再生することはできません。

### <a name="configuration-tips"></a>構成上のヒント

- 可能な限り、有線のインターネット接続を使用します。
- 帯域幅要件の目安は、ストリーミングのビットレートの 2 倍です。 必須ではありませんが、この単純なルールはネットワークの混雑による影響を軽減するのに役立ちます。
- ソフトウェア ベースのエンコーダーを使用する際は、不要なプログラムを終了します。
- プッシュの開始後にエンコーダーの構成を変更すると、イベントに悪影響を与えます。 構成を変更すると、イベントが不安定になる可能性があります。 
- 十分な時間をかけてイベントを設定してください。 高スケールのイベントの場合、イベントの 1 時間前に設定を開始することをお勧めします。

## <a name="see-also"></a>関連項目

[オンプレミス エンコーダー パートナーになる](become-on-premises-encoder-partner.md)

## <a name="next-steps"></a>次のステップ

[Media Services v3 を使用したライブ ストリーミング](live-streaming-overview.md)
