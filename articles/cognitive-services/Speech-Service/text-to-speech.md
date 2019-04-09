---
title: Azure Speech Services によるテキスト読み上げ
titleSuffix: Azure Cognitive Services
description: Azure Speech Services のテキスト読み上げは、REST ベースのサービスです。このサービスを使用すると、アプリケーション、ツール、デバイスは、テキストを人間のような自然な合成音声に変換できます。 音声は、標準音声およびニューラル音声から選択できますが、製品やブランドに固有のカスタム音声を独自に作成することもできます。 標準音声は、45 を超える言語およびロケールで 75 種類以上が用意されています。ニューラル音声は、4 つの言語およびロケールで 5 種類が用意されています。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 1edc2587ef8680f60082bf6271b73d30184f331b
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521246"
---
# <a name="what-is-text-to-speech"></a>テキスト読み上げの概要

Azure Speech Services のテキスト読み上げは、REST ベースのサービスです。このサービスを使用すると、アプリケーション、ツール、デバイスは、テキストを人間のような自然な合成音声に変換できます。 音声は、標準音声およびニューラル音声から選択できますが、製品やブランドに固有のカスタム音声を独自に作成することもできます。 標準音声は、45 を超える言語およびロケールで 75 種類以上が用意されています。ニューラル音声は、4 つの言語およびロケールで 5 種類が用意されています。 詳細については、[サポートされる言語](language-support.md#text-to-speech)に関するページを参照してください。

テキスト読み上げテクノロジを使用すると、コンテンツ作成者は、ユーザーとさまざまな方法で対話できます。 テキスト読み上げでは、ユーザーが音声を使ってコンテンツと対話できるため、アクセシビリティが向上します。 視覚障害や学習障害をお持ちの方も、車の運転中にナビゲーション情報が必要な方も、テキスト読み上げを使用することにより、これまでのエクスペリエンスを向上させることができます。 テキスト読み上げは、音声ボットや仮想アシスタントにも役立つアドオンです。

### <a name="neural-voices"></a>ニューラル音声

ニューラル音声を使用すると、チャットボットや仮想アシスタントとの対話をより自然で魅力的なものにできます。また、電子書籍などのデジタル テキストをオーディオブックに変換したり、カーナビゲーション システムを強化したりすることもできます。 ニューラル音声では、人間のような自然な韻律と明瞭な発音により、ユーザーが AI システムと対話する際のリスニング疲労が大幅に軽減されます。 ニューラル音声の詳細については、[サポートされている言語](language-support.md#text-to-speech)に関するページを参照してください。

### <a name="custom-voices"></a>カスタム音声

音声をカスタマイズすることで、ブランド独自の認識性の高い音声を作成できます。 カスタム音声フォントを作成するには、スタジオで録音を行い、関連するスクリプトをトレーニング データとしてアップロードします。 サービスは、録音に合わせて調整された一意の音声モデルを作成します。 このカスタム音声フォントを使用して、音声を合成できます。 詳細については、[カスタム音声](how-to-customize-voice-font.md)に関するページを参照してください。

## <a name="core-features"></a>コア機能

以下の表は、テキスト読み上げのコア機能を示しています。

| ユース ケース | SDK | REST |
|----------|-----|------|
| テキストを音声に変換する。 | いいえ  | はい |
| 音声適応のためのデータセットをアップロードする。 | いいえ  | はい\* |
| 音声フォント モデルを作成および管理する。 | いいえ  | はい\* |
| デプロイする音声フォントを作成および管理する。 | いいえ  | はい\* |
| 音声フォント テストを作成および管理する。 | いいえ  | はい\* |
| サブスクリプションを管理する。 | いいえ  | はい\* |

\**これらのサービスは、cris.ai エンドポイントを使用して提供されます。[Swagger リファレンス](https://westus.cris.ai/swagger/ui/index)に関するページを参照してください。*

> [!NOTE]
> テキスト読み上げエンドポイントでは、スロットリングが実装され、要求が 5 秒あたり 25 個に制限されます。 スロットリングが実行されると、その旨がメッセージ ヘッダーを介して通知されます。

## <a name="get-started-with-text-to-speech"></a>テキスト読み上げを使ってみる

10 分もかからずにコードを実行できるように設計されたクイック スタートが用意されています。 以下の表は、テキスト読み上げのクイック スタートを言語別に整理して示したものです。

| クイック スタート | プラットフォーム | API リファレンス |
|------------|----------|---------------|
| [C#、.NET Core](quickstart-dotnet-text-to-speech.md) | Windows、macOS、Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Window、macOS、Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Window、macOS、Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>サンプル コード

テキスト読み上げのサンプル コードは、GitHub 上で入手できます。 これらのサンプルに含まれるテキスト読み上げ会話は、人気の高いプログラミング言語で作成されています。

* [テキスト読み上げのサンプル (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>リファレンス ドキュメント

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: 音声テキスト変換](rest-speech-to-text.md)
* [REST API: テキスト読み上げ](rest-text-to-speech.md)
* [REST API: 一括文字起こしとカスタマイズ](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>次の手順

* [無料の Speech Services サブスクリプションを取得する](get-started.md)
* [カスタム音声フォントを作成する](how-to-customize-voice-font.md)
