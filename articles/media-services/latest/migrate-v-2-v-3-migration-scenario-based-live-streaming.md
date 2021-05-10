---
title: Media Services のライブ ストリーミングの移行ガイダンス
description: この記事では、Azure Media Services v2 から v3 への最小移行に役立つライブ ストリーミングのシナリオベースのガイダンスを提供します。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: d021267c38f0043d2361b1a6392fbacd0634a164
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279699"
---
# <a name="live-streaming-scenario-based-migration-guidance"></a>ライブ ストリーミングのシナリオベースの移行ガイダンス

![移行ガイドのロゴ](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![移行の手順 2](./media/migration-guide/steps-4.svg)

Azure portal では、ライブ イベントの設定と管理がサポートされるようになりました。  V2 から V3 への移行をテストする際に、試してみることをお勧めします。

## <a name="test-the-v3-live-event-workflow"></a>V3 ライブ イベント ワークフローをテストする

> [!NOTE]
> v2 で作成されたチャネルとプログラム (v3 でライブ イベントとライブ出力にマップされる) を V3 API で管理します。 このガイダンスは、既存の V2 チャネルを停止できる都合の良いタイミングで、V3 のライブ イベントとライブ出力に切り替えるというものです。 現在のところ、24 時間 365 日実行されているライブ チャネルを新しい V3 ライブ イベントにシームレスに移行するためのサポートはありません。 そのため、メンテナンスのダウンタイムは、対象ユーザーとビジネスにとって最適な方法で調整する必要があります。

コンテンツを V2 から V3 に移行する前に、Media Services でライブ イベントを配信する新しい方法をテストします。 ここでは、移行のために使用および検討する必要がある V3 の機能を示します。

- エンコード用の新しい v3 [ライブ イベント](live-event-outputs-concept.md#live-events)を作成します。 [1080P および 720P エンコード プリセット](live-event-types-comparison-reference.md#system-presets)を有効にすることができます。
- プログラムの代わりに[ライブ出力](live-event-outputs-concept.md#live-outputs)エンティティを使用します
- [ストリーミング ロケーター](stream-streaming-locators-concept.md)を作成します。
- [HLS および DASH](encode-dynamic-packaging-concept.md) のライブ ストリーミングの必要性を考慮してください。
- ライブ イベントの高速起動が必要な場合は、新しい[スタンバイ モード](live-event-outputs-concept.md#standby-mode)機能を検討してください。
- ライブ イベントの進行中に、その文字起こしを行う場合は、新しい[ライブ文字起こし](live-event-live-transcription-how-to.md)機能を検討してください。
- より長いストリーミング期間が必要な場合は、v3 で 24 時間 365 日のライブ イベントを作成します。
- ライブ イベントを監視するには、[Event Grid](monitoring/monitor-events-portal-how-to.md) を使用します。

具体的な手順については、下の「ライブ イベントの概念、チュートリアル、およびハウツー ガイド」を参照してください。

## <a name="live-events-concepts-tutorials-and-how-to-guides"></a>ライブ イベントの概念、チュートリアル、およびハウツー ガイド

### <a name="concepts"></a>概念

- [Azure Media Services v3 を使用したライブ ストリーミング](stream-live-streaming-concept.md)
- [Media Services のライブ イベントとライブ出力](live-event-outputs-concept.md)
- [検証済みのオンプレミス ライブ ストリーミング エンコーダー](encode-recommended-on-premises-live-encoders.md)
- [タイムシフトとライブ出力を使用してオンデマンドのビデオ再生を作成する](live-event-cloud-dvr-time-how-to.md)
- [ライブ イベントでのライブ文字起こしの方法 (プレビュー)](live-event-live-transcription-how-to.md)
- [ライブ イベントの種類の比較](live-event-types-comparison-reference.md)
- [ライブ イベントの状態と課金](live-event-states-billing-concept.md)
- [ライブ イベントの低遅延設定](live-event-latency-reference.md)
- [Media Services ライブ イベント エラー コード](live-event-error-codes-reference.md)

### <a name="tutorials-and-quickstarts"></a>チュートリアルとクイック スタート

- [チュートリアル:Media Services を使用したライブ ストリーム配信](stream-live-tutorial-with-api.md)
- [OBS を使用して Azure Media Services のライブ ストリームを作成する](live-event-obs-quickstart.md)
- [クイック スタート:ポータルでコンテンツをアップロード、エンコード、およびストリーム配信する](asset-create-asset-upload-portal-quickstart.md)
- [クイックスタート:Wirecast を使用して Azure Media Services のライブ ストリームを作成する](live-event-wirecast-quickstart.md)

## <a name="samples"></a>サンプル

[V2 と V3 のコードをコード サンプルで比較する](migrate-v-2-v-3-migration-samples.md)こともできます。
