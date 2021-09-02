---
title: Azure Percept Audio のボタンと LED の状態
description: Azure Percept Audio のボタンと LED の状態について詳しく説明します
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 04ee60d6ebe9c84d77ea7e5ead140f7930b8cfe4
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224555"
---
# <a name="azure-percept-audio-button-and-led-states"></a>Azure Percept Audio のボタンと LED の状態

Azure Percept Audio デバイスのボタンと LED の状態については、次のガイダンスを参照してください。

## <a name="button-behavior"></a>ボタンの動作

ボタンを使用してデバイスの動作を制御します。

|ボタンの状態|動作|
|------------|----------|
|Mute|押すと、マイク アレイがミュートまたはミュート解除されます。 ボタンを押すと、そのイベントがトリガーされます。|
|PTT と PTS|キーワード スポッティング状態をバイパスし、コマンド リッスン状態をアクティブ化するには、PTT を押します。 もう一度押すと、エージェントのアクティブな会話が停止し、キーワード スポッティング状態に戻ります。 ボタンを押すと、そのイベントがトリガーされます。 PTS が機能するのは、エージェントが聞いたり、考えたりしているときではなく、エージェントが話しているときにボタンが押された場合のみです。|

## <a name="led-states"></a>LED の状態

LED インジケーターを使用して、デバイスの状態を把握します。

|LED|LED の状態|Ear SoM 状態|
|---|------------|----------------|
|L02|1 つが白色、静的オン|電源投入 |
|L02|1 つが白色、0.5 Hz 点滅|認証が進行中 |
|L01、L02、L03|3 つが青色、静的オン|キーワードの待機中|
|L01、L02、L03|LED アレイ点滅、20 fps |リッスン中または話し中|
|L01、L02、L03|LED アレイ レーシング、20 fps|処理中|
|L01、L02、L03|3 つが赤色、静的オン |Mute|

## <a name="understanding-ear-som-led-indicators"></a>Ear SoM LED インジケーターについて
LED インジケーターを使用して、デバイスの状態を把握できます。 通常、デバイスの電源がオンになり、モジュールの初期化が完了するまでに約 4～5 分かかります。 初期化の手順を終えると、次のことが確認できます。

1. 中央の白色 LED が点灯 (静止): デバイスの電源がオンです。
1. 中央の白色 LED が点灯 (点滅): 認証が進行中です。
1. 中央の白い LED が点灯 (静止): デバイスは認証されていますが、キーワードが構成されていません。
1. デモがデプロイされ、デバイスが使用できる状態になると、3 つの LED がすべて青色に変化します。


## <a name="troubleshooting-led-issues"></a>LED に関する問題のトラブルシューティング
- **中央の LED が白で点灯している場合** は、[テンプレートを使用して音声アシスタントを作成](./tutorial-no-code-speech.md)してみてください。
- **中央の LED が常に点滅している場合** は、認証の問題を示しています。 こちらのトラブルシューティング手順を試してください。
    1. USB-A とマイクロ USB の接続がセキュリティで保護されていることを確認します。 
    1. [音声モジュールが実行されている](./troubleshoot-audio-accessory-speech-module.md#checking-runtime-status-of-the-speech-module)かどうかを確認します。
    1. デバイスを再起動します
    1. [ログを収集](./troubleshoot-audio-accessory-speech-module.md#collecting-speech-module-logs)してサポート リクエストに添付します
    1. 開発キットで最新のソフトウェアが実行されているかどうかを確認し、利用可能な場合は更新プログラムを適用します。

## <a name="next-steps"></a>次のステップ

Azure Percept Audio デバイスのトラブルシューティングのヒントについては、こちらの[ガイド](./troubleshoot-audio-accessory-speech-module.md)を参照してください。