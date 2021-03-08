---
title: Azure Communication Services - 既知の問題
description: Azure Communication Services の既知の問題について説明します。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/03/2020
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: e9e4b747d9d0ab39a1d0ecef6cf45e4cc0f9e2c5
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628119"
---
# <a name="known-issues-azure-communication-services"></a>既知の問題:Azure Communication Services

この記事では、Azure Communication Services に関連する既知の問題について説明します。

## <a name="video-streaming-quality-on-chromeandroid"></a>Chrome/Android でのビデオ ストリーミングの品質 

Chrome/Android で、ビデオ ストリーミングのパフォーマンスが低下する場合があります。

### <a name="possible-causes"></a>考えられる原因
リモート ストリームの品質は、そのストリームを開始するために使用されたクライアント側レンダラーの解像度によって決まります。 受信者は、リモート ストリームをサブスクライブするときに、送信者のクライアント側レンダラーのディメンションに基づいて独自の解像度を決定します。

## <a name="bluetooth-headset-microphones-are-not-detected"></a>Bluetooth ヘッドセットのマイクが検出されない

Bluetooth ヘッドセットを Communication Services 通話に接続するときに問題が発生する場合があります。

### <a name="possible-causes"></a>考えられる原因
iOS には Bluetooth マイクを選択するオプションはありません。


## <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>ビデオ デバイスを繰り返し切り替えると、ビデオ ストリーミングが一時的に停止する場合がある

ビデオ デバイスを切り替えると、選択したデバイスからストリームが取得される間、ビデオ ストリームが一時停止する場合があります。

### <a name="possible-causes"></a>考えられる原因
メディア デバイスからのストリーミング、およびメディア デバイスの切り替えでは、コンピューティング処理の負荷が大きくなります。 頻繁に切り替えを行うと、パフォーマンスが低下する場合があります。 開発者は、1 つのデバイス ストリームを停止してから別のストリームを開始することをお勧めします。
