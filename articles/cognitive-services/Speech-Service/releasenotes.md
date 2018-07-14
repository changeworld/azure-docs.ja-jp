---
title: Cognitive Services Speech SDK のドキュメント | Microsoft Docs
description: リリース ノート - 最新リリースの変更点
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 0b1559d288380cf3d0c180a225278cc13d22a5d0
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2018
ms.locfileid: "35378998"
---
# <a name="release-notes"></a>リリース ノート

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0: 2018-June リリース

**機能の変更点**

- AudioInputStream

  認識エンジンでは、オーディオ ソースとしてストリームを利用できるようになりました。 詳細については、関連する[ハウツー ガイド](how-to-use-audio-input-streams.md)を参照してください。

- 出力形式の詳細

  `SpeechRecognizer` を作成する一方で、`Detailed` または `Simple` 出力形式を要求できます。 `DetailedSpeechRecognitionResult` には、信頼度スコア、認識されるテキスト、生の語彙形式、正規化形式、および不適切な内容がマスクされた正規化形式が含まれます。

**重大な変更**

- C# では `SpeechRecognitionResult.Text` から `SpeechRecognitionResult.RecognizedText` に変更されます。

**バグの修正**

- シャットダウン中に USP レイヤーで発生する可能性のあるコールバックの問題を修正しました。

- 認識エンジンでオーディオ入力ファイルが使用されると、必要以上に長くファイル ハンドルが保持されていました。

- メッセージ ポンプと認識エンジンの間の複数のデッドロックが削除されました。

- サービスからの応答がタイムアウトすると、結果は `NoMatch` になります。

- Windows のメディア ファンデーション ライブラリは、遅延読み込みされます。 このライブラリは、マイク入力の場合のみ必要です。

- オーディオ データのアップロードの速度が、元の音声速度の約 2 倍に制限されます。

- Windows では、C# .NET アセンブリには厳密な名前が指定されるようになりました。

- ドキュメントの修正: `Region` は、認識エンジンを作成するための必須情報です。

他のサンプルも追加されており、常に更新されます。 最新のサンプル セットについては、[Speech SDK のサンプルの GitHub リポジトリ](https://aka.ms/csspeech/samples)を参照してください。

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733: 2018-May リリース

Cognitive Services Speech SDK の最初のパブリック プレビュー リリースです。
