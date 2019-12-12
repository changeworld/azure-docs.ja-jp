---
title: Long Audio API (プレビュー) - 音声サービス
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: ed00a9df46660cc6bfb4ec5fd9a93c80f5d6653e
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815330"
---
# <a name="long-audio-api-preview"></a>Long Audio API (プレビュー)

Long Audio API は、長い形式のテキスト読み上げ (オーディオ ブックなど) の非同期合成のために設計されています。 この API は、合成された音声をリアルタイムで返しません。代わりに、応答をポーリングし、サービスから提供されている出力を使用することを想定しています。 Speech SDK によって使用されるテキスト読み上げ API とは異なり、Long Audio API は、10 分よりも長い合成された音声を作成できるため、パブリッシャーと音声コンテンツ プラットフォームには最適です。

Long Audio API のその他の利点は次のとおりです。

* サービスによって返される合成音声は、再現性の高い音声出力を保証するニューラル音声を使用します。
* リアルタイム応答はサポートされていないため、音声エンドポイントをデプロイする必要はありません。

> [!NOTE]
> 現在、Long Audio API では、[カスタム ニューラル音声](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices)のみをサポートしています。

## <a name="workflow"></a>ワークフロー

通常、Long Audio API を使用する場合は、合成するテキスト ファイルを送信し、状態をポーリングして、状態が正常である場合は音声出力をダウンロードできます。

次の図は、ワークフローの概要を示しています。

![Long Audio API のワークフロー図](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>合成用にコンテンツを準備する

テキスト ファイルを準備するときは、次のことを確認してください。

* プレーンテキスト (.txt) または SSML テキスト (.txt) のいずれかである
  * プレーンテキストの場合は、**Enter/Return** を押すことで各段落が区切られます - [プレーンテキストの入力例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)を参照してください
  * SSML テキストの場合、SSML の各要素は段落と見なされます。 SSML の要素は、さまざまな段落で区切る必要があります - [SSML テキスト入力の例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)を参照してください。 言語コードについては、「[音声合成マークアップ言語 (SSML)](speech-synthesis-markup.md)」を参照してください
* [バイト オーダー マーク (BOM) 付きの UTF-8](https://www.w3.org/International/questions/qa-utf8-bom.en#bom) としてエンコードされている
* 10,000 を超える文字または 50 を超える段落が含まれている
* 単一のファイルであり、zip ではない

## <a name="audio-output-formats"></a>音声出力形式

Long Audio API では、次の音声出力形式がサポートされています。

> [!NOTE]
> 既定の音声形式は、riff-16khz-16bit-mono-pcm です。

* riff-8khz-16bit-mono-pcm
* riff-16khz-16bit-mono-pcm
* riff-24khz-16bit-mono-pcm
* riff-48khz-16bit-mono-pcm
* audio-16khz-32kbitrate-mono-mp3
* audio-16khz-64kbitrate-mono-mp3
* audio-16khz-128kbitrate-mono-mp3
* audio-24khz-48kbitrate-mono-mp3
* audio-24khz-96kbitrate-mono-mp3
* audio-24khz-160kbitrate-mono-mp3

## <a name="quickstarts"></a>クイック スタート

Long Audio API を正常に実行できるように作られたクイック スタートがあります。 以下の表は、Long Audio API のクイック スタートを言語別に整理して示したものです。

* [クイック スタート:Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>サンプル コード
Long Audio API のサンプル コードは、GitHub で入手できます。

* [サンプル コード: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [サンプル コード: C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [サンプル コード: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
