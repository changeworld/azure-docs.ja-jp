---
title: Azure Media Services ライブ イベントのエラーコード | Microsoft Docs
description: このアーティクルでは、ライブ イベントのエラーコードをリストにします。
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2020
ms.author: juliako
ms.openlocfilehash: bfbef771d33ad4d63ec8eaef83331e497d476071
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599688"
---
# <a name="media-services-live-event-error-codes"></a>Media Services ライブ イベント エラー コード

次の表は、[ライブ イベント](live-events-outputs-concept.md) エラー コードを示しています。

|エラー|説明|
|---|---| 
|MPE_INGEST_FRAMERATE_EXCEEDED|このエラーは、受信エンコーダーが、ライブ イベント/チャネルをエンコードするために 30 fps を超えるストリームを送信している場合に発生します。|
|MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED|このエラーは、受信エンコーダーが、ライブ イベント/チャネルをエンコードするために以下の解像度を超えるストリームを送信している場合に発生します。ライブ イベント/チャンネルのエンコード: 1920x1088、パススルー ライブ イベント/チャンネル: 4096 x 2160|

## <a name="see-also"></a>関連項目

[ストリーミング エンドポイント (配信元) エラー コード](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>次のステップ

[チュートリアル:Media Services を使用したライブ ストリーム配信](stream-live-tutorial-with-api.md)
