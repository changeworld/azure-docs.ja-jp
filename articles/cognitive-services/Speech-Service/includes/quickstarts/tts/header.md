---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: e9f02f95693552180a0eed1550cc59d8f975416b
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961425"
---
このクイックスタートでは、[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) を使用して、テキストを合成音声に変換します。 テキスト読み上げサービスには、合成音声に関するオプションが数多く用意されています ([テキスト読み上げの言語サポート](../../../language-support.md#text-to-speech)に関するセクションを参照)。 いくつかの前提条件を満たすと、次の 4 つの手順だけで合成された音声を既定のスピーカーにレンダリングします。
> [!div class="checklist"]
> * サブスクリプション キーとリージョンから `SpeechConfig` オブジェクトを作成します。
> * 上記の `SpeechConfig` オブジェクトを使用して `SpeechSynthesizer` オブジェクトを作成します。
> * `SpeechSynthesizer` オブジェクトを使用してテキストを読み上げる
> * 返された `SpeechSynthesisResult` でエラーがないか確認する
