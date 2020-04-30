---
title: Long Audio API (プレビュー) - 音声サービス
titleSuffix: Azure Cognitive Services
description: 長い形式のテキスト読み上げ (オーディオ ブックなど) の非同期合成のために Long Audio API がどのように設計されているか説明します。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: trbye
ms.openlocfilehash: b7cca314ec59e46cf17751b1aec28b5c3ea029ed
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "81401072"
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
* [バイト オーダー マーク (BOM) 付きの UTF-8](https://www.w3.org/International/questions/qa-utf8-bom.en#bom) としてエンコードされている
* 単一のファイルであり、zip ではない
* プレーンテキストの場合は 400 文字以上、または SSML テキストの場合は 400 文字以上の[課金対象文字](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note)を含み、1 万段落未満である
  * プレーンテキストの場合は、**Enter/Return** を押すことで各段落が区切られます - [プレーンテキストの入力例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)を参照してください
  * SSML テキストの場合、SSML の各要素は段落と見なされます。 SSML の要素は、異なる段落で区切る必要があります - [SSML テキスト入力の例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)を参照してください
> [!NOTE]
> 中国語 (大陸)、中国語 (香港)、中国語 (台湾)、日本語、および韓国語の場合、1 ワードが 2 文字としてカウントされます。 

## <a name="submit-synthesis-requests"></a>合成要求の送信

入力コンテンツを準備した後、[長い形式のオーディオ合成のクイック スタート](https://aka.ms/long-audio-python)に関する記事に従って、要求を送信します。 複数の入力ファイルがある場合は、複数の要求を送信する必要があります。 次のような注意すべきいくつかの制限事項があります。 
* クライアントは、各 Azure サブスクリプション アカウントに対して 1 秒あたり最大 5 個の要求をサーバーに送信できます。 制限を超えると、クライアントはエラー コード 429 (要求が多すぎます) を受け取ります。 1 秒あたりの要求の数を減らしてください
* サーバーは、各 Azure サブスクリプション アカウントに対して最大 120 個の要求を実行およびキューに登録できます。 制限を超えた場合、サーバーはエラー コード 429 (要求が多すぎます) を返します。 いくつかの要求が完了するまで、新しい要求を送信しないでお待ちください
* サーバーは、各 Azure サブスクリプション アカウントごとに最大 2 万個の要求を保持します。 制限を超えた場合は、新しい要求を送信する前に、いくつかの要求を削除してください

## <a name="audio-output-formats"></a>音声出力形式

柔軟なオーディオ出力形式をサポートしています。 段落ごとにオーディオ出力を生成したり、'concatenateResult' パラメーターを設定して複数のオーディオを 1 つの出力に連結したりすることができます。 Long Audio API では、次の音声出力形式がサポートされています。

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

## <a name="quickstarts"></a>クイックスタート

Long Audio API を正常に実行できるように作られたクイック スタートがあります。 以下の表は、Long Audio API のクイック スタートを言語別に整理して示したものです。

* [クイック スタート: Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>サンプル コード
Long Audio API のサンプル コードは、GitHub で入手できます。

* [サンプル コード: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [サンプル コード: C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [サンプル コード: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
