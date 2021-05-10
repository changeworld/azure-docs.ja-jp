---
title: テキスト読み上げの概要 - 音声サービス
titleSuffix: Azure Cognitive Services
description: 音声サービスのテキスト読み上げ機能を使用すると、アプリケーション、ツール、またはデバイスでテキストを人間のような自然な合成音声に変換できます。 この記事は、テキスト読み上げサービスの利点と機能の概要になっています。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: テキスト読み上げ
ms.openlocfilehash: 73e37fde4b3c2dd1aeb6ab171c3726f1b4353949
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106090"
---
# <a name="what-is-text-to-speech"></a>テキスト読み上げの概要

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

この概要では、お使いのアプリケーション、ツール、デバイスでテキストを人間のような合成音声に変換することを可能にするテキスト読み上げの利点と機能について説明します。 音声は、人間のようなニューラル音声から選択できますが、製品やブランドに固有のカスタム音声を作成することもできます。 サポートされている音声、言語、ロケールの一覧については、[サポートされている言語](language-support.md#text-to-speech)に関する記事をご覧ください。

このドキュメントには、次の種類の記事が含まれています。

* **クイックスタート** は、サービスへの要求の実行方法を説明する概要手順です。
* **攻略ガイド** には、より具体的またはカスタマイズした方法でサービスを使用するための手順が記載されています。
* **概念** では、サービスの機能と特徴について詳しく説明します。
* **チュートリアル** はより長文のガイドであり、より広範なビジネス ソリューションの 1 コンポーネントとしてこのサービスを使用する方法を示すものです。

> [!NOTE]
> Bing Speech は、2019 年 10 月 15 日に使用が停止されました。 アプリケーション、ツール、または製品で Bing Speech API または Custom Speech をご使用の方に向けて、Speech Service への移行に役立つガイドを作成しました。
> - [Bing Speech から Speech Service に移行する](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>コア機能

* 音声合成 - [Speech SDK](./get-started-text-to-speech.md) または [REST API](rest-text-to-speech.md) を使用して、標準音声、ニューラル音声、またはカスタム音声を使ってテキストを音声に変換します。

* 長い音声の非同期合成 - [Long Audio API](long-audio-api.md) を使用して、10 分以上のテキスト読み上げファイル (オーディオ ブックや講義など) を非同期に合成します。 Speech SDK または Speech-to-Text REST API を使用して実行される合成とは異なり、応答はリアルタイムで返されません。 要求が非同期に送信され、応答がポーリングされること、および合成音声がサービスから利用可能になったときにダウンロードされることを想定しています。 カスタム ニューラル音声のみがサポートされています。

* ニューラル音声 - ディープ ニューラル ネットワークを使用して、話し言葉のアクセントとイントネーションに関する従来の音声合成の限界を克服します。 韻律予測と音声合成が同時に行われるため、より滑らかで自然な音声出力が得られます。 ニューラル音声を使用すると、チャットボットや音声アシスタントとの対話をより自然で魅力的なものにすることができます。また、電子書籍などのデジタル テキストをオーディオブックに変換したり、カーナビゲーション システムを強化したりすることもできます。 ニューラル音声では、人間のような自然な韻律と明瞭な発音により、ユーザーが AI システムとやりとりする際のリスニング疲労が大幅に軽減されます。 ニューラル音声の一覧については、[サポートされている言語](language-support.md#text-to-speech)に関する記事をご覧ください。

* SSML で話し方を調整する - 音声合成マークアップ言語 (SSML) は、音声テキスト変換の出力をカスタマイズするために使用される XML ベースのマークアップ言語です。 SSML を使用して、ピッチの調整、一時停止の追加、発音の改善、読み上げ速度の調整、音量の上げ下げ、単一のドキュメントへの複数の音声の割り当てを行うことができます。 話し方の調整については、[方法](speech-synthesis-markup.md)に関するページを参照してください。

* 口形素 - [口形素](how-to-speech-synthesis-viseme.md)は、特定の音素を生成するときの唇、顎、舌の位置などの観察されたスピーチにおける主要な姿勢です。 口形素は、音声と音素との強い相関関係を持っています。 Speech SDK で口形素イベントを使用すると、顔のアニメーション データを生成できます。これは、読唇時のコミュニケーション、教育、エンターテイメント、カスタマー サービスの顔をアニメーションで表現するために使用できます。

> [!NOTE]
> Viseme イベントは現在、`en-US-AriaNeural` 音声でのみサポートされています。

## <a name="get-started"></a>はじめに

テキスト読み上げの使用を開始するには、[クイックスタート](get-started-text-to-speech.md)を参照してください。 テキスト読み上げサービスは、[Speech SDK](speech-sdk.md)、[REST API](rest-text-to-speech.md)、および [Speech CLI](spx-overview.md) を介して利用できます。

## <a name="sample-code"></a>サンプル コード

テキスト読み上げのサンプル コードは、GitHub 上で入手できます。 これらのサンプルに含まれるテキスト読み上げ会話は、人気の高いプログラミング言語で作成されています。

- [テキスト読み上げのサンプル (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [テキスト読み上げのサンプル (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customization"></a>カスタマイズ

ニューラル音声に加え、製品またはブランドに固有のカスタム音声を作成し、微調整できます。 作業を始めるにあたって必要なのは、少数のオーディオ ファイルと関連する文字起こしだけです。 詳細については、「[Get started with Custom Voice (カスタム音声の概要)](how-to-custom-voice.md)」をご覧ください。

## <a name="pricing-note"></a>価格に関する注意

テキスト読み上げサービスを使用している場合、句読点を含めて、文字が音声に変換されるごとに課金されます。 SSML ドキュメント自体は課金対象外ですが、テキストが音声に変換される方法を調整するために使用される省略可能な要素 (音素やピッチなど) は、課金対象の文字としてカウントされます。 課金対象の一覧を次に示します。

- 要求の SSML 本文でテキスト読み上げサービスに渡されたテキスト
- `<speak>` と `<voice>` タグを除く、SSML 形式の要求本文のテキスト フィールド内のすべてのアークアップ
- 文字、句読点、スペース、タブ、マークアップ、すべての空白文字
- Unicode で定義されているすべてのコード ポイント

詳細については、[価格](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)に関するページを参照してください。

> [!IMPORTANT]
> 中国語、日本語、韓国語の言語文字はそれぞれ、2 文字としてカウントされ課金されます。

## <a name="reference-docs"></a>リファレンス ドキュメント

- [Speech SDK](speech-sdk.md)
- [REST API: テキスト読み上げ](rest-text-to-speech.md)

## <a name="next-steps"></a>次のステップ

- [無料の音声サービス サブスクリプションを取得する](overview.md#try-the-speech-service-for-free)
- [Speech SDK を取得する](speech-sdk.md)
