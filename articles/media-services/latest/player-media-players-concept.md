---
title: Media Services 用のメディア プレーヤーの概要
description: Azure Media Services ではどのメディア プレーヤーを使用できますか。 現時点で、Azure Media Player、Shaka、および Video.js。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 3/08/2021
ms.openlocfilehash: 43d0a8ee82a84a57be7c8ded9e7f5afc172bd9d6
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281404"
---
# <a name="media-players-for-media-services"></a>Media Services 用のメディア プレーヤー

Media Services では、複数のメディア プレーヤーを使用できます。

## <a name="azure-media-player"></a>Azure Media Player

Azure Media Player は、さまざまなブラウザーやデバイスに対応するビデオ プレーヤーです。 Azure Media Player では、HTML5、Media Source Extensions (MSE)、Encrypted Media Extensions (EME) といった業界標準を使用して、強化されたアダプティブ ストリーミングを提供します。 デバイスやブラウザーでこれらの標準を使用できない場合、Azure Media Player は Flash や Silverlight をフォールバック テクノロジとして使用します。 使用される再生テクノロジを問わず、開発者は統一された JavaScript インターフェイスを使用して API にアクセスできます。 Azure Media Services によって提供されるコンテンツは、追加の作業を必要とすることなくさまざまなデバイスやブラウザーで再生できます。

[Azure Media Player のドキュメント](../azure-media-player/azure-media-player-overview.md)を参照してください。

## <a name="shaka"></a>Shaka

Shaka プレーヤーは、アダプティブ メディア用のオープンソースの JavaScript ライブラリです。 ブラウザーでアダプティブ メディア形式 (DASH や HLS など) を再生できます。プラグインや Flash は使用されません。 代わりに、Shaka プレーヤーでは、オープン Web 標準の Media Source Extensions と Encrypted Media Extensions が使用されています。

「[Azure Media Services で Shaka プレーヤーを使用する方法](player-shaka-player-how-to.md)」を参照してください。

## <a name="videojs"></a>Video.js

Video.js は、ブラウザーでアダプティブ メディア形式 (DASH、HLS など) を再生するビデオ プレーヤーです。 Video.js では、オープン Web 標準の MediaSource Extensions と Encrypted Media Extensions が使用されています。 デスクトップおよびモバイル デバイスでのビデオ再生がサポートされます。 公式ドキュメントについては、https://docs.videojs.com/ をご覧ください。

「[Azure Media Services で Video.js プレーヤーを使用する方法](player-how-to-video-js-player.md)」を参照してください。


## <a name="next-steps"></a>次のステップ ##

- [Azure Media Player クイックスタート](../azure-media-player/azure-media-player-quickstart.md)