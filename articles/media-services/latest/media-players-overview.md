---
title: Media Services 用のメディア プレーヤーの概要
description: Azure Media Services ではどのメディア プレーヤーを使用できますか。 現時点で、Azure Media Player、Shaka、および Video.js。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 3/08/2021
ms.openlocfilehash: 1a1d415b374818d9a51c87e78e7ac422fa374bc5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510405"
---
# <a name="media-players-for-media-services"></a>Media Services 用のメディア プレーヤー

Media Services では、複数のメディア プレーヤーを使用できます。

## <a name="azure-media-player"></a>Azure Media Player

Azure Media Player は、さまざまなブラウザーやデバイスに対応するビデオ プレーヤーです。 Azure Media Player では、HTML5、Media Source Extensions (MSE)、Encrypted Media Extensions (EME) といった業界標準を使用して、強化されたアダプティブ ストリーミングを提供します。 デバイスやブラウザーでこれらの標準を使用できない場合、Azure Media Player は Flash や Silverlight をフォールバック テクノロジとして使用します。 使用される再生テクノロジを問わず、開発者は統一された JavaScript インターフェイスを使用して API にアクセスできます。 Azure Media Services によって提供されるコンテンツは、追加の作業を必要とすることなくさまざまなデバイスやブラウザーで再生できます。

[Azure Media Player のドキュメント](https://docs.microsoft.com/azure/media-services/azure-media-player/azure-media-player-overview)を参照してください。

## <a name="shaka"></a>Shaka

Shaka プレーヤーは、アダプティブ メディア用のオープンソースの JavaScript ライブラリです。 ブラウザーでアダプティブ メディア形式 (DASH や HLS など) を再生できます。プラグインや Flash は使用されません。 代わりに、Shaka プレーヤーでは、オープン Web 標準の Media Source Extensions と Encrypted Media Extensions が使用されています。

「[Azure Media Services で Shaka プレーヤーを使用する方法](how-to-shaka-player.md)」を参照してください。

## <a name="videojs"></a>Video.js

Video.js は、ブラウザーでアダプティブ メディア形式 (DASH、HLS など) を再生するビデオ プレーヤーです。 Video.js では、オープン Web 標準の MediaSource Extensions と Encrypted Media Extensions が使用されています。 デスクトップおよびモバイル デバイスでのビデオ再生がサポートされます。 公式ドキュメントについては、https://docs.videojs.com/ をご覧ください。

「[Azure Media Services で Video.js プレーヤーを使用する方法](how-to-video-js-player.md)」を参照してください。


## <a name="next-steps"></a>次のステップ ##

- [Azure Media Player クイックスタート](../azure-media-player/azure-media-player-quickstart.md)
