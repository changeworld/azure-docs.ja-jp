---
title: ライブ イベントとライブ出力の概念
description: このトピックでは、Azure Media Services v3 のライブ イベントとライブ出力の概要について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 10/23/2020
ms.author: inhenkel
ms.openlocfilehash: 44ab9e4ff83fec2ddfbd1cb44f503298d12789d1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766301"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>Media Services のライブ イベントとライブ出力

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services では、Azure クラウドで顧客にライブ イベントを配信できます。 Media Services v3 でライブ ストリーミング イベントを設定するには、この記事で説明されている概念を理解する必要があります。

> [!TIP]
> Media Services v2 API から移行するお客様の場合、**ライブ イベント** エンティティは v2 にある **Channel** に置き換わり、**ライブ出力** は **program** に置き換わります。

## <a name="live-events"></a>ライブ イベント

[ライブ イベント](/rest/api/media/liveevents)は、ライブ ビデオ フィードの取り込みと処理を担当します。 ライブ イベントを作成すると、リモート エンコーダーからライブ シグナルを送信するために使用できるプライマリおよびセカンダリ入力エンドポイントが作成されます。 リモート ライブ エンコーダーは、[RTMP](https://www.adobe.com/devnet/rtmp.html) または [Smooth Streaming](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) (Fragmented MP4) 入力プロトコルを使用して、その入力エンドポイントに投稿フィードを送信します。 RTMP 取り込みプロトコルの場合は、コンテンツを平文で (`rtmp://`)、またはワイヤ上で安全に暗号化して (`rtmps://`) 送信できます。 Smooth Streaming 取り込みプロトコルでサポートされる URL スキームは `http://` と `https://` です。  

## <a name="live-event-types"></a>ライブ イベントの種類

[ライブ イベント](/rest/api/media/liveevents)は、*パススルー* (オンプレミスのライブ エンコーダーによって複数のビットレート ストリームが送信される) または *ライブ エンコード* (オンプレミスのライブ エンコーダーによってシングル ビットレート ストリームが送信される) のいずれかに設定できます。 これらの種類は、[LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype) を使って作成中に設定されます。

* **LiveEventEncodingType.None**:オンプレミス ライブ エンコーダーは、マルチ ビットレート ストリームを送信します。 取り込まれたストリームは、追加の処理なしでライブ イベントを通過します。 パススルー モードとも呼ばれます。
* **LiveEventEncodingType.Standard**:オンプレミス ライブ エンコーダーは、ライブ イベントにシングル ビットレート ストリームを送信し、Media Services がマルチ ビットレート ストリームを作成します。 投稿フィードの解像度が 720p 以上である場合、**Default720p** プリセットは 6 つの解像度とビットレートのペアのセットをエンコードします。
* **LiveEventEncodingType.Premium1080p**:オンプレミス ライブ エンコーダーは、ライブ イベントにシングル ビットレート ストリームを送信し、Media Services がマルチ ビットレート ストリームを作成します。 Default1080p プリセットは、解像度とビットレートのペアの出力セットを指定します。

### <a name="pass-through"></a>パススルー

![パススルー ライブ イベントと Media Services サンプル図](./media/live-streaming/pass-through.svg)

パススルー **ライブ イベント** を使用する場合は、オンプレミス ライブ エンコーダーを活用して、マルチ ビットレート ビデオ ストリームを生成し、(RTMP または Fragmented MP4 プロトコルを使用して) ライブ イベントへの投稿フィードとして送信します。 その後、ライブ イベントは追加の処理なしで受信ビデオ ストリームを通過します。 このようなパススルー ライブ イベントは、長時間実行されるライブ ストリームや 24 時間 365 日のリニア ライブ エンコード向けに最適化されています。 この種類のライブ イベントを作成するときは、None (LiveEventEncodingType.None) を指定してください。

投稿フィードは最大 4K の解像度と 60 フレーム/秒のフレーム レートで、H.264/AVC または H.265/HEVC のいずれかのビデオ コーデックと AAC (AAC-LC、HE-AACv1、または HE-AACv2) オーディオ コーデックを使用して送信することができます。 詳細については、[ライブ イベントの種類の比較](live-event-types-comparison-reference.md)に関するページを参照してください。

> [!NOTE]
> 長期にわたって複数のイベントを配信する場合で、かつオンプレミスのエンコーダーを既に導入済みである場合、ライブ ストリーミングの手段としてはパススルー方式が最も低コストです。 [価格](https://azure.microsoft.com/pricing/details/media-services/)の詳細を参照してください。
>

[DVR を使用したライブ イベント](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/4a436376e77bad57d6cbfdc02d7df6c615334574/Live/LiveEventWithDVR/Program.cs#L214)で、パススルー ライブ イベントの作成に関する .NET コードの例を参照してください。

### <a name="live-encoding"></a>ライブ エンコード  

![ライブ エンコードと Media Services サンプル図](./media/live-streaming/live-encoding.svg)

Media Services によるライブ エンコードを使用する場合は、オンプレミス ライブ エンコーダーを、(RTMP または Fragmented MP4 プロトコルを使用して) ライブ イベントへの投稿フィードとしてシングル ビットレート ビデオを送信するように構成します。 次に、その受信シングル ビットレート ストリームを[マルチ ビットレート ビデオ ストリーム](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)にエンコードするようにライブ イベントを設定し、その出力を MPEG-DASH、HLS、Smooth Streaming などのプロトコル経由で再生デバイスに配信できるようにします。

ライブ エンコードを使用すると、H.264/AVC ビデオ コーデックと AAC (AAC-LC、HE-AACv1、または HE-AACv2) オーディオ コーデックを使用して、30 フレーム/秒のフレーム レートで最大 1080p の解像度のみで投稿フィードを送信できます。 パススルー ライブ イベントは、60 フレーム/秒で最大 4K の解像度をサポートできることに注意してください。 詳細については、[ライブ イベントの種類の比較](live-event-types-comparison-reference.md)に関するページを参照してください。

ライブ エンコーダーからの出力に含まれている解像度とビットレートは、プリセットによって決定されます。 **Standard** ライブ エンコーダー (LiveEventEncodingType.Standard) を使用している場合、*Default720p* プリセットは、3.5 Mbps での 720p から 200 kbps での 192p までの 6 つの解像度とビットレートのペアのセットを指定します。 それ以外で **Premium1080p** ライブ エンコーダー (LiveEventEncodingType.Premium1080p) を使用している場合、*Default1080p* プリセットは、3.5 Mbps での 1080p から 200 kbps での 180p までの 6 つの解像度とビットレートのペアのセットを指定します。 詳細については、[システムのプリセット](live-event-types-comparison-reference.md#system-presets)に関するページを参照してください。

> [!NOTE]
> ライブ エンコード プリセットをカスタマイズする必要がある場合は、Azure portal 経由でサポート チケットを開きます。 目的の解像度とビットレートを指定します。 720p (Standard ライブ エンコーダーのプリセットを要求する場合) または 1080p (Premium1080p ライブ エンコーダーのプリセットを要求する場合) に 1 つのレイヤーと、多くとも 6 つのレイヤーしか存在しないことを確認してください。

## <a name="creating-live-events"></a>ライブ イベントの作成

### <a name="options"></a>Options

ライブ イベントの作成時には次のオプションを指定できます。

* ライブイベントに名前と説明を指定できます。
* クラウド エンコードには、パススルー (クラウド エンコードなし)、Standard (最大 720p)、Premium (最大 1080p) が含まれます。 Standard と Premium のエンコードでは、エンコードされたビデオのストレッチ モードを選択できます。
  * なし: エンコード プリセットに指定された出力解像度に厳密に従います。入力ビデオのピクセル縦横比やディスプレイの縦横比は考慮されません。
  * AutoSize:出力解像度をオーバーライドし、それをパディングなしでディスプレイの入力の縦横比に合わせて変更します。 たとえば入力が 1920 x 1080 であるとき、エンコード プリセットによって 1280 x 1280 が要求された場合、プリセットの値がオーバーライドされ、出力は 1280 x 720 となり、入力の縦横比 (16:9) が維持されます。
  * AutoFit:出力におけるアクティブなビデオ領域に入力と同じ縦横比を確保しつつ、出力解像度を遵守するために、出力を (レターボックスまたはピラーボックスで) パディングします。 例として、入力が 1920 x 1080 であるとき、エンコード プリセットによって 1280 x 1280 が要求された場合、出力は 1280 x 1280 となりますが、縦横比が 16:9 である 1280 x 720 の四角形を挟むように、幅 280 ピクセルのピラー ボックス領域が左右に追加されます。
* ストリーミング プロトコル (現時点では、RTMP プロトコルと Smooth Streaming プロトコルがサポートされます)。 ライブ イベントや、それに関連付けられたライブ出力の実行中は、プロトコル オプションを変更できません。 別のプロトコルが必要な場合は、ストリーミング プロトコルごとに別のライブ イベントを作成します。
* 入力 ID。ライブ イベント入力ストリームのグローバル一意識別子です。
* 静的ホスト名プレフィックス (このケースでは、ランダムな 128 ビットの 16 進文字列が使用されます)。ライブ イベント名を使用するか、カスタム名を使用します。  顧客名の使用を選択した場合、この値はカスタム ホスト名プレフィックスになります。
* 入力キー フレームの間隔を設定することで、ライブ ブロードキャストと再生の間のエンドツーエンドの待機時間を減らすことができます。これは、HLS 出力の各メディア セグメントの期間 (秒単位) です。 値は、0.5 から 20 秒の範囲の 0 以外の整数である必要があります。  入力キー フレーム間隔と出力キー フレーム間隔の *どちらも* 設定されていない場合は、値の既定値は 2 秒です。 キー フレームの間隔は、パススルー イベントでのみ許可されます。
* イベントの作成時に、それを自動開始するように設定できます。 自動開始が true に設定されている場合、ライブ イベントは作成された後に開始されます。 ライブ イベントの実行が開始されるとすぐに課金が開始されます。 それ以上の課金を停止するには、ライブ イベント リソースの Stop を明示的に呼び出す必要があります。 または、ストリーミングを開始する準備ができたら、イベントを開始できます。

> [!NOTE]
> 最大フレームレートは、Standard と Premium の両方のエンコードで 30 fps です。

## <a name="standby-mode"></a>StandBy モード

ライブ イベントを作成する際に、それを StandBy モードに設定できます。 イベントが StandBy モードの間は、説明、静的ホスト名プレフィックス、入力およびプレビュー アクセスの制限設定を編集できます。  StandBy モードはまだ課金対象モードですが、ライブ ストリームを開始する場合とは異なる価格設定になります。

詳細については、[ライブ イベントの状態と課金](live-event-states-billing-concept.md)に関するページを参照してください。

* 取り込みやプレビューの IP 制限。 このライブ イベントへのビデオの取り込みが許可される IP アドレスを定義できます。 許可される IP アドレスは、1 つの IP アドレス (例: '10.0.0.1')、IP アドレスと CIDR サブネット マスクを使用した IP 範囲 (例: '10.0.0.1/22')、IP アドレスとピリオドで区切られた 10 進数のサブネット マスクを使用した IP 範囲 (例: '10.0.0.1(255.255.252.0)') のいずれかの形で指定できます。
<br/><br/>
IP アドレスが指定されておらず、規則の定義もない場合は、どの IP アドレスも許可されません。 すべての IP アドレスを許可するには、規則を作成し、0.0.0.0/0 に設定します。<br/>IP アドレスの形式は、4 つの数字を含む IpV4 アドレスか CIDR アドレス範囲にする必要があります。
<br/><br/>
自分のファイアウォールで特定の IP を有効にする場合、またはライブ イベントへの入力を Azure IP アドレスに制限する場合は、[Azure Datacenter IP のアドレス範囲](https://www.microsoft.com/download/details.aspx?id=41653)に関するページから JSON ファイルをダウンロードします。 このファイルについて詳しくは、ページの「**詳細**」セクションを選択してください。

* イベントを作成するときに、ライブの文字起こしを有効にすることを選択できます。 既定では、ライブの文字起こしは無効になっています。 ライブの文字起こしの詳細については、「[ライブ文字起こし](live-event-live-transcription-how-to.md)」を参照してください。

### <a name="naming-rules"></a>名前付け規則

* ライブ イベントの名前は最大 32 文字です。
* 名前は、次の [regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) パターン `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$` に従う必要があります。

[ストリーミング エンドポイントの命名規則](stream-streaming-endpoint-concept.md#naming-convention)に関するページも参照してください。

> [!TIP]
> ライブ イベント名の一意性を保証するには、GUID を生成してから、すべてのハイフンと中かっこを削除します (存在する場合)。 文字列はすべてのライブ イベントで一意であり、長さは 32 であることが保証されています。

## <a name="live-event-ingest-urls"></a>ライブ イベントの取り込み URL

ライブ イベントが作成されると、ライブ オンプレミス エンコーダーに提供する取り込み URL を取得できます。 ライブ エンコーダーは、これらの URL を使用して、ライブ ストリームを入力します。 詳細については、「[おすすめのライブ ストリーミング エンコーダー](encode-recommended-on-premises-live-encoders.md)」を参照してください。

>[!NOTE]
> 2020-05-01 API リリース現在、"バニティ" URL は静的ホスト名 (useStaticHostname: true) と呼ばれています。


> [!NOTE]
> ハードウェア エンコーダーのセットアップで使用するために取り込み URL を静的で予測可能にするには、**useStaticHostname** プロパティを true に設定し、各作成時に **accessToken** プロパティを同じ GUID に設定します。 

### <a name="example-liveevent-and-liveeventinput-configuration-settings-for-a-static-non-random-ingest-rtmp-url"></a>静的な (ランダムでない) 取り込み RTMP URL の LiveEvent と LiveEventInput の構成設定の例。

```csharp
             LiveEvent liveEvent = new LiveEvent(
                    location: mediaService.Location,
                    description: "Sample LiveEvent from .NET SDK sample",
                    // Set useStaticHostname to true to make the ingest and preview URL host name the same. 
                    // This can slow things down a bit. 
                    useStaticHostname: true,

                    // 1) Set up the input settings for the Live event...
                    input: new LiveEventInput(
                        streamingProtocol: LiveEventInputProtocol.RTMP,  // options are RTMP or Smooth Streaming ingest format.
                                                                         // This sets a static access token for use on the ingest path. 
                                                                         // Combining this with useStaticHostname:true will give you the same ingest URL on every creation.
                                                                         // This is helpful when you only want to enter the URL into a single encoder one time for this Live Event name
                        accessToken: "acf7b6ef-8a37-425f-b8fc-51c2d6a5a86a",  // Use this value when you want to make sure the ingest URL is static and always the same. If omitted, the service will generate a random GUID value.
                        accessControl: liveEventInputAccess, // controls the IP restriction for the source encoder.
                        keyFrameIntervalDuration: "PT2S" // Set this to match the ingest encoder's settings
                    ),
```

* 非静的ホスト名

    **LiveEvent** を作成するとき、非静的ホスト名は Media Services v3 の既定のモードです。 ライブ イベントの割り当ては少し速くなりますが、ライブ エンコード ハードウェアまたはソフトウェアに必要な取り込み URL はランダムになります。 ライブ イベントを停止または開始すると、URL は変わります。 非静的ホスト名は、エンド ユーザーがライブ イベントを非常に迅速に取得する必要があるアプリを使用し、動的な取り込み URL を持つことが問題でないようなストリーミングを行う場合にのみ役立ちます。

    ライブ イベントが作成される前にクライアント アプリが取り込み URL を事前に生成する必要がない場合は、ライブ イベント用のアクセス トークンが Media Services で自動生成されるようにします。

* 静的ホスト名 

    静的ホスト名モードは、特定のライブ イベントの作成や停止/開始時に変更されることのない RTMP 取り込み URL を使用してライブ エンコード ハードウェアまたはソフトウェアを事前に構成する大半の演算子で推奨されています。 これらの演算子は、時間が経過しても変化しない、予測的な RTMP 取り込み URL を必要としています。 これは、BlackMagic Atem Mini Pro などのハードウェア エンコード デバイス、または同様のハードウェア エンコードや製作ツールの構成設定に静的な RTMP 取り込み URL をプッシュする必要がある場合にも非常に便利です。 

    > [!NOTE]
    > Azure portal では、静的ホスト名 URL は "*静的ホスト名プレフィックス*" と呼ばれます。

    API でこのモードを指定するには、作成時に `useStaticHostName` を `true` に設定します (既定値は `false`)。 `useStaticHostname` が true に設定されている場合、`hostnamePrefix` は、ライブ イベント プレビューおよび取り込みエンドポイントに割り当てられたホスト名の最初の部分を指定します。 最終的なホスト名は、このプレフィックス、メディア サービス アカウント名、および Azure Media Services データ センターの短いコードの組み合わせになります。

    URL でのランダム トークンを避けるため、作成時に自身のアクセス トークン (`LiveEventInput.accessToken`) を渡す必要もあります。  このアクセス トークンは、有効な GUID 文字列 (ハイフン付きまたはなし) である必要があります。 モードはいったん設定されると、更新できません。

    アクセス トークンは、Azure リージョンと Media Services アカウント内で一意である必要があります。 アプリで静的ホスト名の取り込み URL を使用する必要がある場合は、リージョン、Media Services アカウント、ライブ イベントの特定の組み合わせで使用するために、常に新しい GUID インスタンスを作成することをお勧めします。

    静的ホスト名 URL を有効にしたり、アクセス トークンを有効な GUID (たとえば `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`) に設定したりするには、次の API を使用します。  

    |Language|静的ホスト名 URL を有効にする|アクセス トークンを設定する|
    |---|---|---|
    |REST|[properties.useStaticHostname](/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.useStaticHostname](/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--use-static-hostname](/cli/azure/ams/live-event#az_ams_live_event_create)|[--access-token](/cli/azure/ams/live-event#optional-parameters)|
    |.NET|[LiveEvent.useStaticHostname](/dotnet/api/microsoft.azure.management.media.models.liveevent.usestatichostname?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Management_Media_Models_LiveEvent_UseStaticHostname)|[LiveEventInput.AccessToken](/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|

### <a name="live-ingest-url-naming-rules"></a>ライブ取り込み URL の名前付け規則

* 以下の "*ランダム*" な文字列は 128 ビットの 16 進数です (32 文字の 0-9 a-f で構成されています)。
* "*アクセス トークン*": 静的ホスト名設定を使用するときに設定する有効な GUID 文字列。 たとえば、「 `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"` 」のように入力します。
* *ストリーム名*:特定の接続のためのストリーム名を示します。 ストリーム名の値は通常、使用するライブ エンコーダーによって追加されます。 接続を説明するために任意の名前 ("video1_audio1"、"video2_audio1"、"stream" など) を使用するようにライブ エンコーダーを構成できます。

#### <a name="non-static-hostname-ingest-url"></a>非静的ホスト名の取り込み URL

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>スムーズ ストリーミング

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="static-hostname-ingest-url"></a>静的ホスト名の取り込み URL

次のパスでは、`<live-event-name>` は、イベントに指定された名前、またはライブ イベントの作成で使用されるカスタム名のいずれかを意味します。

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>スムーズ ストリーミング

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>ライブ イベント プレビュー URL

ライブ イベントが投稿フィードの受信を開始したら、ライブ ストリームを公開する前に、そのプレビュー エンドポイントを使用して、ライブ ストリームが受信されていることをプレビューして検証できます。 プレビュー ストリームが良好であることを確認したら、ライブ イベントを使用して、そのライブ ストリームを 1 つ以上の (事前に作成された) ストリーミング エンドポイント経由で配信可能にすることができます。 これを実現するために、ライブ イベントに新しい[ライブ出力](/rest/api/media/liveoutputs)を作成します。

> [!IMPORTANT]
> 続行する前に、ビデオがプレビュー URL に送信されていることを確認してください。

## <a name="live-event-long-running-operations"></a>ライブイベントの長時間にわたって実行される操作

詳細については、「[長時間にわたって実行される操作](media-services-apis-overview.md#long-running-operations)」を参照してください。

## <a name="live-outputs"></a>ライブ出力

ストリームがライブ イベントに流れ始めると、[アセット](/rest/api/media/assets)、[ライブ出力](/rest/api/media/liveoutputs)、[ストリーミング ロケーター](/rest/api/media/streaminglocators)を作成することにより、ストリーミング イベントを開始できます。 ライブ出力により、ストリームがアーカイブされ、[ストリーミング エンドポイント](/rest/api/media/streamingendpoints)を介して視聴者がストリームを使用できるようになります。  

ライブ出力の詳細については、「[クラウド DVR の使用](live-event-cloud-dvr-time-how-to.md)」を参照してください。

## <a name="live-event-output-questions"></a>ライブ イベント出力の質問

[ライブ イベント出力の質問](questions-collection.md#live-streaming)に関する記事をご覧ください。
