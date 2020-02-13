---
title: Azure Media Services を使用してマルチビットレートのストリームを作成するときにオンプレミス エンコーダーを構成する | Microsoft Docs
description: このトピックには、ライブ イベントをキャプチャし、単一ビットレート ライブ ストリームを (Live Encoding が有効になっている) AMS チャネルに送信してさらに処理を行うために使用できるオンプレミス ライブ エンコーダーがリストされています。 このトピックには、リストされているエンコーダーを構成する方法を示すチュートリアルへのリンクがあります。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 0ec6f046-0841-4673-9057-883bdbc30d5c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 71a31228602ef161158eaa05c80d50f65de98a50
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133265"
---
# <a name="how-to-configure-on-premises-encoders-when-using-azure-media-services-to-create-multi-bitrate-streams"></a>Azure Media Services を使用してマルチビットレートのストリームを作成するときにオンプレミス エンコーダーを構成する方法
このトピックには、ライブ イベントをキャプチャし、単一ビットレート ライブ ストリームを (Live Encoding が有効になっている) AMS チャネルに送信してさらに処理を行うために使用できるオンプレミス ライブ エンコーダーがリストされています。 このトピックには、リストされているエンコーダーを構成する方法を示すチュートリアルへのリンクもあります。

> [!NOTE]
> RTMP を使用してデータをストリーミングしている場合は、ファイアウォールまたはプロキシ、あるいはその両方の設定で、送信 TCP ポート 1935 と 1936 が開いていることを確認します。

## <a name="haivision-kb-encoder"></a>Haivision KB エンコーダー
単一ビットレート ライブ ストリームが AMS チャネルに送信されるように [Haivision KB Encoder](https://www.haivision.com/products/kb-series/) エンコーダーを構成する方法については、[Haivision KB エンコーダーの構成](media-services-configure-kb-live-encoder.md)に関するページをご覧ください。

## <a name="telestream-wirecast"></a>Telestream Wirecast
単一ビットレート ライブ ストリームが AMS チャネルに送信されるように [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) エンコーダーを構成する方法については、[Wirecast の構成](media-services-configure-wirecast-live-encoder.md)に関するページをご覧ください。

## <a name="newtek-tricaster"></a>NewTek TriCaster
単一ビットレート ライブ ストリームが AMS チャネルに送信されるように [Tricaster](https://newtek.com/products/tricaster-40.html) エンコーダーを構成する方法については、[Tricaster の構成](media-services-configure-tricaster-live-encoder.md)に関するページをご覧ください。

## <a name="elemental-live"></a>Elemental Live
詳細については、[Elemental Live](https://www.elementaltechnologies.com/products/elemental-live) に関するページを参照してください。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>次のステップ

[Azure Media Services を使用して Live Encoding の実行が有効なチャネルを操作する](media-services-manage-live-encoder-enabled-channels.md)

