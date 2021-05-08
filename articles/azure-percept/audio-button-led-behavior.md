---
title: Azure Percept Audio のボタンと LED の動作
description: Azure Percept Audio のボタンと LED の状態について詳しく説明します
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: fa919acb527084d19ab88b2e7895d4e6ab0b72d3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105609073"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Azure Percept Audio のボタンと LED の動作

Azure Percept Audio デバイスのボタンと LED の状態については、次のガイダンスを参照してください。

## <a name="button-behavior"></a>ボタンの動作

ボタンを使用してデバイスの動作を制御します。

|ボタンの状態|動作|
|------------|----------|
|Mute|押すと、マイク アレイがミュートまたはミュート解除されます。 ボタンを押すと、そのイベントがトリガーされます。|
|PTT と PTS|キーワード スポッティング状態をバイパスし、コマンド リッスン状態をアクティブ化するには、PTT を押します。 もう一度押すと、エージェントのアクティブな会話が停止し、キーワード スポッティング状態に戻ります。 ボタンを押すと、そのイベントがトリガーされます。 PTS が機能するのは、エージェントが聞いたり、考えたりしているときではなく、エージェントが話しているときにボタンが押された場合のみです。|

## <a name="led-behavior"></a>LED の動作

LED インジケーターを使用して、デバイスの状態を把握します。

|LED|LED の状態|Ear SoM 状態|
|---|------------|----------------|
|L02|1 つが白色、静的オン|電源投入 |
|L02|1 つが白色、0.5 Hz 点滅|認証が進行中 |
|L01、L02、L03|3 つが青色、静的オン|キーワードの待機中|
|L01、L02、L03|LED アレイ点滅、20fps |リッスン中または話し中|
|L01、L02、L03|LED アレイ レーシング、20fps|処理中|
|L01、L02、L03|3 つが赤色、静的オン |Mute|

## <a name="next-steps"></a>次のステップ

Azure Percept Audio デバイスのトラブルシューティングのヒントについては、こちらの[ガイド](./troubleshoot-audio-accessory-speech-module.md)を参照してください。