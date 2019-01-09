---
title: Azure Media Services LiveEvent の種類 |Microsoft Docs
description: このアーティクルでは、LiveEvent の種類を比較する詳細な表を示します。
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
ms.date: 11/27/2018
ms.author: juliako
ms.openlocfilehash: 3ed38ce75e5ee7c9f05533d64b28171482bfec51
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53725934"
---
# <a name="liveevent-types-comparison"></a>LiveEvent タイプの比較

Azure Media Services において、[LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) にはライブ エンコードとパススルーの 2 種類があります。 

## <a name="types-comparison"></a>種類の比較 

次の表は、2 種類の LiveEvent の機能を比較したものです。

| 機能 | パススルー LiveEvent | 標準 LiveEvent |
| --- | --- | --- |
| シングル ビットレートの入力がクラウド内でマルチビットレートにエンコードされる |いいえ  |[はい] |
| コントリビューション フィードの最大ビデオ解像度 |4K (60 フレーム/秒で 4096x2160) |1080p (30 フレーム/秒 で 1920 x 1088)|
| コントリビューション フィードの推奨最大レイヤー|最大 12 個|1 つのオーディオ|
| 出力時の最大レイヤー| 入力と同じ|最大 7 個|
| コントリビューション フィードの最大集約帯域幅|60 Mbps|該当なし|
| コントリビューション内の各レイヤーにおける最大ビットレート |20 Mbps|20 Mbps|
| 複数言語オーディオ トラックのサポート|[はい]|いいえ |
| サポートされている入力ビデオ コーデック |H.264/AVC と H.265/HEVC|H.264/AVC|
| サポートされている出力ビデオ コーデック|入力と同じ|H.264/AVC|
| サポートされているビデオ ビット深度、入力、出力|HDR 10/HLG を含む最大 10 ビット|8 ビット|
| サポートされている入力オーディオ コーデック|AAC-LC、HE-AAC v1、HE-AAC v2|AAC-LC、HE-AAC v1、HE-AAC v2|
| サポートされている出力オーディオ コーデック|入力と同じ|AAC-LC|
| 出力ビデオの最大ビデオ解像度|入力と同じ|720p (30 フレーム/秒)|
| 入力プロトコル|RTMP、フラグメント化された MP4 (スムーズ ストリーミング)|RTMP、フラグメント化された MP4 (スムーズ ストリーミング)|
| 料金|[価格に関するページ](https://azure.microsoft.com/pricing/details/media-services/) を参照し、[ライブ ビデオ] タブをクリックしてください。|[価格に関するページ](https://azure.microsoft.com/pricing/details/media-services/) を参照し、[ライブ ビデオ] タブをクリックしてください。|
| 最長実行時間|24 時間 365 日 線形ライブ|24 時間 365 日|
| キャプション データから埋め込まれた CEA 608/708 をパス スルーする機能|[はい]|[はい]|
| スレートの挿入のサポート|いいえ |いいえ |
| API による広告信号のサポート| いいえ |いいえ |
| インバンド SCTE-35 メッセージによる広告信号のサポート|[はい]|[はい]|
| 投稿フィードでの短時間の停止から回復する機能|[はい]|なし (6 秒経過しても入力データがない場合、LiveEvent はスレートを開始します)|
| 均一でない入力 GOP のサポート|[はい]|いいえ。入力は固定 GOP 時間を持つ必要があります|
| 可変フレーム レートの入力のサポート|[はい]|なし。入力は固定フレーム レートにする必要があります。 たとえば、動きの大きなシーンでは、多少の変化は許容されます。 しかしコントリビューション フィードはフレームレートを (たとえば、15 フレーム/秒などに)ドロップすることはできません。|
| 入力フィードがなくなった場合の LiveEvent の自動停止|いいえ |12 時間後 (実行中の LiveOutput がない場合)|

## <a name="next-steps"></a>次の手順

[ライブ ストリーミングの概要](live-streaming-overview.md)
