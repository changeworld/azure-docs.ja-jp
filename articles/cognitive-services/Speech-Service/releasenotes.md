---
title: リリース ノート - Speech Services
titlesuffix: Azure Cognitive Services
description: Azure Speech Services に関する機能リリース、改善、バグ修正、既知の問題を時系列で掲載しています。
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 80cf0d9970eb7052eeb19fb297176453864202cf
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55205697"
---
# <a name="release-notes"></a>リリース ノート

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0:2018-December リリース

**新機能**

* Python
  * このリリースで、Python サポート (3.5 以降) のベータ版を使用できるようになりました。 詳細については、[こちらを参照](quickstart-python.md)してください。
* JavaScript
  * Speech SDK for JavaScript はオープンソースで提供されています。 ソース コードは [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js)から入手できます。
  * Node.js のサポートを開始しました。詳細については、[こちら](quickstart-js-node.md)を参照してください。
  * 音声セッションの長さの制限がなくなり、再接続は背後で自動的に実行されるようになります。
* Connection オブジェクト
  * 認識機能から、Connection オブジェクトにアクセスできます。 このオブジェクトを使用すると、サービスの接続を明示的に開始し、接続イベントと切断イベントをサブスクライブすることができます
    (これは JavaScript と Python からはまだ使用できません)。
* Ubuntu 18.04 のサポート。
* Android
  * APK 生成時の ProGuard サポートが有効になりました。

**機能強化**

* 内部スレッドの使用方法を改善し、スレッド、ロック、相互排他の数を減らしました。
* エラー報告や情報を改善しました。 一部のケースでは、エラー メッセージがまったく伝達されていませんでした。
* 最新のモジュールを使用するように JavaScript の開発依存関係を更新しました。

**バグの修正**

* RecognizeAsync の型の不一致によるメモリ リークを修正しました。
* 場合によっては、例外がリークしていました。
* 翻訳イベント引数のメモリ リークを修正しました。
* 長時間実行中のセッションでの再接続に関するロックの問題を修正しました。
* 翻訳が失敗した場合に最終的な結果が失われる可能性がある問題を修正しました。
* C#:メイン スレッドで非同期操作が待機されていない場合、非同期タスクが完了する前に認識機能が破棄される可能性がありました。
* Java:Java VM がクラッシュする原因となる問題を修正しました。
* Objective-C: RecognizingIntent ではなく RecognizedIntent が返されるという列挙型のマッピングを修正しました。
* JavaScript:SpeechConfig で既定の出力形式を 'simple' に設定します。
* JavaScript:JavaScript と他の言語の config オブジェクトのプロパティ間にある不整合を解消しました。

**サンプル**

* いくつかのサンプルを更新および修正しました (たとえば、翻訳のための出力音声など)。
* [サンプル リポジトリ](https://aka.ms/csspeech/samples)に Node.js サンプルを追加しました。

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**新機能**

* Android x86 または x64 のサポート。
* プロキシのサポート: SpeechConfig オブジェクトで、プロキシ情報 (ホスト名、ポート、ユーザー名、およびパスワード) を設定する関数を呼び出せるようになりました。 この機能は iOS ではまだ利用できません。
* 強化されたエラー コードとメッセージ。 認識でエラーが返された場合、これは既に `Reason` (取り消されたイベントの場合) または`CancellationDetails` (認識結果) を `Error` に設定します。 取り消されたイベントに、2 つのメンバー `ErrorCode` と `ErrorDetails` が含まれるようになりました。 サーバーによって、報告されたエラーと一緒に追加のエラー情報が返された場合、これを新しいメンバーで使用できるようになります。

**機能強化**

* 認識エンジンの構成に検証が追加され、エラー メッセージが追加されました。
* オーディオ ファイル中の長時間のサイレント状態の処理が強化されました。
* NuGet パッケージ: .NET Framework プロジェクトの場合、AnyCPU 構成でビルドされることを防ぎます。

**バグの修正**

* 認識エンジンで見つかったいくつかの例外を修正しました。 さらに例外がキャッチされ、キャンセル済みイベントに変換されます。
* プロパティ管理のメモリ リークを修正しました。
* オーディオ入力ファイルが認識エンジンをクラッシュする可能性があるバグを修正しました。
* セッションがイベントを停止した後でイベントが受信されることがあるバグを修正しました。
* スレッド処理の一部の競合状態を修正しました。
* クラッシュの原因となる iOS の互換性の問題を修正しました。
* Android のマイクのサポートのための安定性の向上。
* JavaScript の認識エンジンが認識言語を無視するバグを修正しました。
* JavaScript で (場合によっては) EndpointId の設定を妨げるバグを修正しました。
* JavaScript の AddIntent のパラメーターの順序を変更し、不足している AddIntent JavaScript 署名を追加します。

**サンプル**

* プルおよびプッシュ ストリームの使用のための C++ と C# のサンプルを[サンプル リポジトリ](https://aka.ms/csspeech/samples)に追加しました。

## <a name="speech-sdk-101"></a>Speech SDK 1.0.1

信頼性の向上とバグ修正

* 認識エンジンの破棄での競合状態によって致命的なエラーが発生する可能性を修正しました
* 未設定のプロパティによって致命的なエラーが発生する可能性を修正しました。
* 新しいエラーとパラメーターのチェックを追加しました。
* Objective-C: NSString での名前のオーバーライドによって致命的なエラーが発生する可能性を修正しました。
* Objective-C: API の可視性を調整しました
* JavaScript: イベントとそのペイロードに関して修正しました。
* ドキュメントの改善。

[サンプル リポジトリ](https://aka.ms/csspeech/samples)に、JavaScript の新しいサンプルを追加しました。

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0: 2018-September リリース

**新機能**

* iOS での Objective-C のサポート。 [iOS での Objective-C のクイック スタート](quickstart-objectivec-ios.md)に関するページをご覧ください。
* ブラウザーでの JavaScript のサポート。 [JavaScript のクイック スタート](quickstart-js-browser.md)に関するページをご覧ください。

**重大な変更**

* このリリースでは、重大な変更がいくつか行われています。
  詳しくは、[こちらのページ](https://aka.ms/csspeech/breakingchanges_1_0_0)をご覧ください。

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0: 2018-August リリース

**新機能**

* Speech SDK で構築された UWP アプリは、Windows アプリ認定キット (WACK) に合格できるようになりました。
  [UWP のクイック スタート](quickstart-csharp-uwp.md)に関するページをご覧ください。
* Linux (Ubuntu 16.04 x 64) 上の .NET Standard 2.0 のサポート。
* 試験段階: Windows (64 ビット) および Linux (Ubuntu 16.04 x 64) での Java 8 サポート。
  [Java ランタイム環境のクイック スタート](quickstart-java-jre.md)に関するページをご覧ください。

**機能の変更点**

* 接続エラーに関する追加エラーの詳細情報が公開されました。

**重大な変更**

* Java (Android) で、`SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` 関数にパス パラメーターが不要になりました。 サポートされているすべてのプラットフォームでパスが自動的に検出されるようになりました。
* Java および C# のプロパティ `EndpointUrl` の get-accessor が削除されました。

**バグの修正**

* Java で、翻訳認識エンジンの音声合成結果が実装されるようになりました。
* 非アクティブなスレッドの原因となったり、未使用の開いたソケット数の増加の原因となったりするバグが修正されました。
* 実行時間の長い認識が伝送の途中で終了する問題が修正されました。
* 認識エンジンのシャットダウン時の競合状態を修正しました。

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0: 2018-July リリース

**新機能**

* Android プラットフォーム (API 23: Android 6.0 Marshmallow 以降) をサポートします。 [Android クイック スタート](quickstart-java-android.md)をチェックアウトします。
* Windows 上の .NET Standard 2.0 をサポートします。 [.NET Core クイック スタート](quickstart-csharp-dotnetcore-windows.md)をチェックアウトします。
* 試験段階: Windows 上での UWP のサポート (バージョン 1709 以降)。
  * [UWP のクイック スタート](quickstart-csharp-uwp.md)に関するページをご覧ください。
  * 注:Speech SDK で構築された UWP アプリは、まだ Windows アプリ認定キット (WACK) に合格していません。
* 自動再接続を使用して、実行時間の長い認識をサポートします。

**機能の変更点**

* `StartContinuousRecognitionAsync()` は、実行時間の長い認識をサポートします。
* 認識結果に含まれるフィールドが増えました。 認識されたテキストのオーディオの開始からのオフセットと期間 (どちらも単位はティック)、および認識状態を表す追加の値 (`InitialSilenceTimeout` や `InitialBabbleTimeout` など) です。
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
* Windows 上のメディア ファンデーション ライブラリへの依存関係が削除されました。 SDK で Core Audio API が使用されるようになりました。
* ドキュメントの修正: サポートされるリージョンを説明するための[リージョン](regions.md) ページが追加されました。

**既知の問題**

* Android 用の Speech SDK では、翻訳のための音声合成の結果が報告されません。 この問題は次のリリースで修正される予定です。

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0: 2018-June リリース

**機能の変更点**

- AudioInputStream

  認識エンジンでは、オーディオ ソースとしてストリームを利用できるようになりました。 詳細については、関連する[ハウツー ガイド](how-to-use-audio-input-streams.md)を参照してください。

- 出力形式の詳細

  `SpeechRecognizer` を作成するときに、`Detailed` または `Simple` 出力形式を要求できます。 `DetailedSpeechRecognitionResult` には、信頼度スコア、認識されるテキスト、生の語彙形式、正規化形式、および不適切な内容がマスクされた正規化形式が含まれます。

**重大な変更**

- C# で `SpeechRecognitionResult.Text` から `SpeechRecognitionResult.RecognizedText` に変更されました。

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

このリリースは、Cognitive Services Speech SDK の最初のパブリック プレビュー リリースです。
