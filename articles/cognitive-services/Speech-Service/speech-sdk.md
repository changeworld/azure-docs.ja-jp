---
title: Speech SDK について - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech ソフトウェア開発キット (SDK) には Speech サービスの各種機能が多数含まれており、音声認識対応アプリケーションの開発が容易になります。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: f663c9af8c352d0170c633fe76b3fbc5268aad1e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400025"
---
# <a name="about-the-speech-sdk"></a>Speech SDK について

Speech ソフトウェア開発キット (SDK) には Speech サービスの各種機能が多数含まれており、音声認識対応アプリケーションを開発できます。 Speech SDK は、多くのプログラミング言語と、すべてのプラットフォームで使用できます。

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>シナリオの機能

Speech SDK は Speech サービスの機能が多数含まれていますが、すべてではありません。 Speech SDK の機能は、多くの場合、シナリオに関連づけられています。 Speech SDK は、ローカル デバイス、ファイル、Azure blob ストレージ、さらに入力ストリームと出力ストリームを使用して、リアルタイムのシナリオと非リアルタイムのシナリオの両方に適しています。 Speech SDK でシナリオを実現できない場合は、別の REST API を探してください。

### <a name="speech-to-text"></a>音声テキスト変換

[音声テキスト変換](speech-to-text.md) (*音声認識*とも呼ばれます) ではオーディオ ストリームからテキストへの文字起こしが行われ、アプリケーション、ツール、またはデバイスでそのテキストを使用したり表示したりできます。 音声テキスト変換を [Language Understanding (LUIS)](../luis/index.yml) で使用し、文字起こしされた音声からユーザーの意図を抽出して、音声コマンドで対応します。 [Speech Translation](speech-translation.md) を使用すると、音声入力を 1 回の通話で別の言語に変換できます。 詳細については、「[音声テキスト変換の基本](speech-to-text-basics.md)」を参照してください。

### <a name="text-to-speech"></a>テキスト読み上げ

[テキスト読み上げ](text-to-speech.md) (*音声合成*とも呼ばれます) は、テキストを人間に似た合成音声に変換します。 入力テキストは、文字列リテラルか、[音声合成マーク付け言語 (SSML)](speech-synthesis-markup.md) を使用したものです。 標準またはニューラルな音声の詳細については、「[テキスト読み上げ言語と音声サポート](language-support.md#text-to-speech)」を参照してください。

### <a name="voice-assistants"></a>音声アシスタント

Speech SDK を使用した音声アシスタントにより、開発者はアプリケーションやエクスペリエンス向けに自然で人間のような会話型インターフェイスを作成できます。 音声アシスタント サービスは、デバイスとアシスタントの間で高速かつ信頼性の高い対話機能を提供します。 実装では、Bot Framework の Direct Line Speech チャネルまたは統合カスタム コマンド (プレビュー) サービスを使用してタスクを完了します。 さらに、音声アシスタントは、[Custom Voice Portal](https://aka.ms/customvoice) を使用して作成し、独自の音声エクスペリエンスを創造できます。

#### <a name="keyword-spotting"></a>キーワード認識

Speech SDK では、[キーワード認識](speech-devices-sdk-create-kws.md)の概念をサポートしています。 キーワード認識では、音声でキーワードを識別した後、キーワードを聞くとアクションが行われます。 たとえば、「やあ、Cortana」という音声により、Cortana アシスタントがアクティブになります。

### <a name="meeting-scenarios"></a>ミーティングのシナリオ

Speech SDK は、単一のデバイスか複数デバイスの会話かに関わらず、ミーティングのシナリオの文字起こしに最適です。

#### <a name="conversation-transcription"></a>会話の文字起こし

[会話の文字起こし](conversation-transcription.md)を使用すると、リアルタイム (および非同期) の音声認識、話者識別、各話者に対する文の属性が有効になります (*ダイアライゼーション*とも呼ばれます)。 話者を区別することができるため、対面会議の文字起こしに最適です。

#### <a name="multi-device-conversation"></a>マルチデバイスの会話

[マルチデバイスの会話](multi-device-conversation.md)を使用して、会話の中で複数のデバイスまたはクライアントを接続し、音声ベースまたはテキストベースのメッセージを送信し、文字起こしや翻訳を容易に実現します。

### <a name="custom--agent-scenarios"></a>カスタム/エージェントのシナリオ

Speech SDK は、テレフォニー データが生成されるコール センターのシナリオでの文字起こしにも使用できます。

#### <a name="call-center-transcription"></a>コール センターの文字起こし

[コール センターの文字起こし](call-center-transcription.md)は、対話型音声応答 (IVR) のような様々なシステムに由来する大量のテレフォニー データを文字起こしする一般的な音声テキスト変換シナリオです。 Speech サービスの最新の音声認識モデルはそのような電話データの文字起こしに優れており、人間には理解が難しいデータにも対応しています。

### <a name="codec-compressed-audio-input"></a>コーデック圧縮音声入力

いくつかの Speech SDK プログラミング言語では、コーデック圧縮音声入力ストリームをサポートしています。 詳細については、「<a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">圧縮音声入力形式を使用する<span class="docon docon-navigate-external x-hidden-focus"></span></a>」を参照してください。

## <a name="rest-api"></a>REST API

Speech SDK には音声サービスの多くの機能が含まれていますが、一部のシナリオでは REST API を使用した方がよいかもしれません。

### <a name="batch-transcription"></a>バッチ文字起こし

[バッチ文字起こし](batch-transcription.md)では、大量のデータの音声からテキストへの文字起こしを非同期で行うことができます。 バッチ文字起こしは、REST API でのみ可能です。 音声からテキストへの変換に加え、一括音声テキスト変換は、ダイアライゼーションやセンチメント分析にも対応します。

## <a name="customization"></a>カスタマイズ

Speech サービスは、音声からテキスト、テキストから音声、音声翻訳に至るまで、既定のモデルを使用して優れた機能を提供します。 場合によっては、独自のユース ケースに合わせて、ベースラインのパフォーマンスを向上させた方がよいかもしれません。 Speech サービスには、簡単に使用できるさまざまなコードなしのカスタマイズ ツールがあり、独自のデータに基づいてカスタム モデルを使用して競争力を得られます。 これらのモデルは、ユーザーとその組織だけが使用できます。

### <a name="custom-speech-to-text"></a>カスタム音声変換

独自環境での認識と文字起こしに音声テキスト変換を使用する場合は、カスタムの音響、言語、発音モデルを作成してトレーニングし、周囲の雑音や業界固有の語彙に対応できます。 コードを使用しない Custom Speech モデルの作成と管理は、[Custom Speech Portal](https://aka.ms/customspeech) を通じて行うことができます。 Custom Speech モデルが発行されると、Speech SDK で利用できるようになります。

### <a name="custom-text-to-speech"></a>カスタム テキスト読み上げ

Custom Voice とも呼ばれるカスタム テキスト読み上げは、ブランド用に認識性の高い固有の音声を作成できる、一連のオンライン ツールです。 コードを使用しない Custom Voice モデルの作成と管理は、[Custom Voice Portal](https://aka.ms/customvoice) を通じて行うことができます。 Custom Voice モデルが発行されると、Speech SDK で利用できるようになります。

## <a name="get-the-speech-sdk"></a>Speech SDK を取得する

# <a name="windows"></a>[Windows](#tab/windows)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-windows.md)]

# <a name="linux"></a>[Linux](#tab/linux)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-linux.md)]

# <a name="ios"></a>[iOS](#tab/ios)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-ios.md)]

# <a name="macos"></a>[macOS](#tab/macos)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-macos.md)]

# <a name="android"></a>[Android](#tab/android)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-android.md)]

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[!INCLUDE [Get the Node.js Speech SDK](includes/get-speech-sdk-nodejs.md)]

# <a name="browser"></a>[ブラウザー](#tab/browser)

[!INCLUDE [Get the Browser Speech SDK](includes/get-speech-sdk-browser.md)]

---

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

[!INCLUDE [Sample source code](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>次のステップ

* [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
* [C# で音声を認識する方法を確認する](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
