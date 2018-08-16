---
title: Cognitive Services Speech SDK のドキュメント
description: リリース ノート - 最新リリースの変更点
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/17/2018
ms.author: wolfma
ms.openlocfilehash: f346241e1d10d16eae08e389296f4be9149ec086
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502460"
---
# <a name="release-notes"></a>リリース ノート

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0: 2018-July リリース

**新機能**

* Android プラットフォーム (API 23: Android 6.0 Marshmallow 以降) をサポートします。
  [Android クイック スタート](quickstart-java-android.md)をチェックアウトします。
* Windows 上の .NET Standard 2.0 をサポートします。
  [.NET Core クイック スタート](quickstart-csharp-dotnetcore-windows.md)をチェックアウトします。
* 試験段階: Windows 上での UWP のサポート (バージョン 1709 以降)
  * 当社の [UWP クイック スタート](quickstart-csharp-uwp.md)をチェックアウトします。
  * 注: Speech SDK で構築された UWP アプリは、まだ Windows アプリ認定キット (WACK) に合格していません。
* 自動再接続を使用して、実行時間の長い認識をサポートします。

**機能の変更点**

* `StartContinuousRecognitionAsync()` は、実行時間の長い認識をサポートします。
* 認識結果には、認識されたテキストのオーディオの開始からのオフセットと期間 (どちらも単位はティック)、認識状態を表す追加の値 (`InitialSilenceTimeout` や `InitialBabbleTimeout` など) という多くのフィールドが含まれます。
* ファクトリ インスタンスを作成するための AuthorizationToken をサポートします。

**重大な変更**

* 認識イベント: NoMatch のイベントの種類は、エラー イベントにマージされました。
* C# での SpeechOutputFormat は、C++ との整合性を維持するために OutputFormat に名前が変更されました。
* `AudioInputStream` インターフェイスのいくつかのメソッドの戻り値の型が若干変更されました。
   * Java では、`read` メソッドが `int` の代わりに `long` を返すようになりました。
   * C# では、`Read` メソッドが `int` の代わりに `uint` を返すようになりました。
   * C++ では、`Read` および `GetFormat` メソッドが `int` の代わりに `size_t` を返すようになりました。
* C++: オーディオ入力ストリームのインスタンスを `shared_ptr` としてのみ渡すことができるようになりました。

**バグの修正**

* `RecognizeAsync()` がタイムアウトしたときの結果にある正しくない戻り値が修正されました。
* Windows 上のメディア ファンデーション ライブラリへの依存関係が削除されました。 この SDK は現在、コア オーディオ API を使用しています。
* ドキュメントの修正: サポートされるリージョンを説明するためのリージョン ページが追加されました。

**既知の問題**

* Android 用の Speech SDK では、翻訳のための音声合成の結果が報告されません。
  これは次のリリースで修正される予定です。

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
