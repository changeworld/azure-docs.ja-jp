---
title: 音声アシスタント - 音声サービス
titleSuffix: Azure Cognitive Services
description: Speech SDK (ソフトウェア開発キット) を使用した音声アシスタントの特徴、機能、制限の概要。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 9219c9a72ce9e1cfba3504b0b8e16ade77f8a5e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369898"
---
# <a name="what-is-a-voice-assistant"></a>音声アシスタントとは

音声サービスを使用した音声アシスタントにより、開発者は、そのアプリケーションとエクスペリエンスのための自然で人間のような会話型インターフェイスを作成できます。

音声アシスタント サービスは、(1) Bot Framework の Direct Line Speech チャネルまたは (2) 統合カスタム コマンド (プレビュー) サービスを使用してタスクを遂行するアシスタントの実装とデバイスとの間に、高速で信頼性の高い対話を実現します。

アプリケーションは、Speech SDK (Software Development Kit) を使用して音声アシスタント サービスに接続します。

   ![音声アシスタントのオーケストレーション サービス フローの概念図](media/voice-assistants/overview.png "音声アシスタントのフロー")

## <a name="choosing-an-assistant-solution"></a>アシスタント ソリューションを選択する

音声アシスタントを作成する際に最初に行うことは、その要件を明らかにすることです。 音声サービスには、アシスタントの対話を精巧に作り上げるためのさまざまな補完的ソリューションが用意されています。 Bot Framework の [Direct Line Speech](direct-line-speech.md) チャンネルの柔軟性と多機能性が求められるケースであれ、単純なシナリオ向けに[カスタム コマンド (プレビュー)](custom-commands.md) の簡潔さが求められるケースであれ、適切なツールを選ぶことがその第一歩となります。

| 目的 | 候補となるソリューション | 例 |
|-------------------|------------------|----------------|
|堅牢なスキルの連携と完全なデプロイ制御を使用した制約のない会話 | Bot Framework の [Direct Line Speech](direct-line-speech.md) チャンネル | <ul><li>"I need to go to Seattle"</li><li>"What kind of pizza can I order?"</li></ul>
|作成とホスティングが簡素化されたコマンド アンド コントロールまたはタスク指向の会話 | [カスタム コマンド (プレビュー)](custom-commands.md) | <ul><li>"Turn on the overhead light"</li><li>"Make it 5 degrees warmer"</ul>

アシスタントで扱う処理がまだ決まっていない場合には、既定の最適な選択肢として [Direct Line Speech](direct-line-speech.md) をお勧めします。 [仮想アシスタント ソリューションとエンタープライズ テンプレート](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)や [QnA Maker サービス](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview)を使用して、一般的なパターンを作成したり既存のナレッジ ソースを使用したりするなど、豊富なツール群と作成支援機能が統合されています。

[カスタム コマンド (プレビュー)](custom-commands.md) は、作成とホスティングに関して、自然言語によるコマンド アンド コントロールのシナリオに特化した効率的なエクスペリエンスが特徴です。

   ![アシスタント ソリューションの比較](media/voice-assistants/assistant-solution-comparison.png "アシスタント ソリューションの比較")

## <a name="core-features"></a>コア機能

アシスタントの対話を作成する手段として [Direct Line Speech](direct-line-speech.md) と[カスタム コマンド (プレビュー)](custom-commands.md) のどちらを選んだとしても、その豊富なカスタマイズ機能を使用して、ブランド、製品、パーソナリティに合わせてアシスタントをカスタマイズできます。

| カテゴリ | 特徴 |
|----------|----------|
|[カスタム キーワード](speech-devices-sdk-create-kws.md) | ユーザーは、"Hey Contoso" などのカスタム キーワードを使用してアシスタントとの会話を始めることができます。 これは、アプリが Speech SDK のカスタム キーワード エンジンを使用して行います。カスタム キーワード エンジンは、[ここで生成できる](speech-devices-sdk-create-kws.md)カスタム キーワードを使用して構成できます。 音声アシスタントは、サービス側のキーワード検証を使用することで、(デバイス単体の場合と比べて) キーワード アクティブ化の正確性を高めることができます。
|[音声テキスト変換](speech-to-text.md) | 音声アシスタントは、音声サービスの[音声変換](speech-to-text.md)を使用してリアルタイム オーディオを認識されたテキストに変換します。 このテキストは文字起こしされているため、アシスタントの実装とクライアント アプリケーションのどちらからでも利用できます。
|[テキスト読み上げ](text-to-speech.md) | アシスタントからのテキスト応答は、音声サービスの[テキスト読み上げ](text-to-speech.md)を使用して合成されます。 この合成は、クライアント アプリケーションでオーディオ ストリームとして利用できるようになります。 Microsoft では、独自のカスタム高品質ニューラル TTS 音声を構築してブランドに音声を加える機能を提供しています。 詳細については、[こちらからお問い合わせください](mailto:mstts@microsoft.com)。

## <a name="getting-started-with-voice-assistants"></a>音声アシスタントの使用を開始する

10 分もかからずにコードを実行できるように設計されたクイック スタートが用意されています。 以下の表は、音声アシスタントのクイックスタートの一覧を言語別に整理して示しています。

| クイック スタート | プラットフォーム | API リファレンス |
|------------|----------|---------------|
| C#、UWP | Windows | [[参照]](https://aka.ms/csspeech/csharpref) |
| Java | Windows、macOS、Linux | [[参照]](https://aka.ms/csspeech/javaref) |
| Java | Android | [[参照]](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>サンプル コード

音声アシスタントを作成するためのサンプル コードは GitHub から入手できます。 これらのサンプルは、いくつかの一般的なプログラミング言語でアシスタントに接続するためのクライアント アプリケーションに対応しています。

* [音声アシスタントのサンプル (SDK)](https://aka.ms/csspeech/samples)
* [チュートリアル:Speech SDK を使用してアシスタントを音声対応にする (C#)](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>チュートリアル

[Speech SDK および Direct Line Speech チャネルを使用して、アシスタントを音声対応にする](tutorial-voice-enable-your-bot-speech-sdk.md)方法についてのチュートリアルです。

## <a name="customization"></a>カスタマイズ

音声サービスを使用して構築された音声アシスタントでは、[音声変換](speech-to-text.md)、[テキスト読み上げ](text-to-speech.md)、および[カスタム キーワードの選択](speech-devices-sdk-create-kws.md)に利用できるさまざまなカスタム オプションを使用できます。

> [!NOTE]
> カスタマイズのオプションは、言語やロケールによって異なります ([サポートされる言語](supported-languages.md)に関するページを参照してください)。

## <a name="reference-docs"></a>リファレンス ドキュメント

* [Speech SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>次のステップ

* [Speech サービスのサブスクリプション キーを無料で取得する](get-started.md)
* [Speech SDK を取得する](speech-sdk.md)
* [カスタム コマンド (プレビュー) について詳しく学習する](custom-commands.md)
* [Direct Line Speech について詳しく学習する](direct-line-speech.md)
