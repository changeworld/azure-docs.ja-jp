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
ms.openlocfilehash: 0e18fd0c52fd4090477599f53cd0ef0bc05855f2
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587342"
---
# <a name="long-audio-api-preview"></a>Long Audio API (プレビュー)

Long Audio API は、長い形式のテキスト読み上げ (オーディオ ブック、ニュース記事、ドキュメントなど) の非同期合成のために設計されています。 この API は、合成された音声をリアルタイムで返しません。代わりに、応答をポーリングし、サービスから提供されている出力を使用することを想定しています。 Speech SDK によって使用されるテキスト読み上げ API とは異なり、Long Audio API は、10 分よりも長い合成された音声を作成できるため、パブリッシャーと音声コンテンツ プラットフォームには最適です。

Long Audio API のその他の利点は次のとおりです。

* サービスによって返される合成音声では、最適なニューラル音声が使用されます。
* リアルタイム バッチ モードによる音声合成は行われないため、音声エンドポイントをデプロイする必要はありません。

> [!NOTE]
> Long Audio API で、[パブリック ニューラル音声](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices)と[カスタム ニューラル音声](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices)の両方がサポートされるようになりました。

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

入力コンテンツを準備した後、[長い形式のオーディオ合成のクイック スタート](https://aka.ms/long-audio-python)に関する記事に従って、要求を送信します。 複数の入力ファイルがある場合は、複数の要求を送信する必要があります。 

**HTTP 状態**コードによって、一般的なエラーが示されます。

| API | HTTP 状態コード | 説明 | 提案 |
|-----|------------------|-------------|----------|
| 作成 | 400 | 音声合成はこのリージョンでは有効になっていません。 | 音声サブスクリプション キーをサポートされているリージョンに変更してください。 |
|        | 400 | このリージョンでは **Standard** 音声サブスクリプションのみが有効です。 | 音声サブスクリプション キーを "Standard" 価格レベルに変更してください。 |
|        | 400 | Azure アカウントの要求数の上限である 20,000 個を超えています。 新しい要求を送信する前に、いくつかの要求を削除してください。 | サーバーでは、Azure アカウントごとに最大 20,000 個の要求が保持されます。 新しい要求を送信する前に、いくつかの要求を削除してください。 |
|        | 400 | 音声合成ではこのモデルを使用することはできません : {modelID}。 | {modelID} の状態が正しいことを確認してください。 |
|        | 400 | 要求のリージョンがこのモデルのリージョンと一致しません: {modelID}。 | {modelID} のリージョンが要求のリージョンと一致することを確認してください。 |
|        | 400 | 音声合成では、バイト順マーカー付きのUTF-8 エンコードのテキスト ファイルのみがサポートされます。 | 入力ファイルが、バイト順マーカー付きの UTF-8 エンコードになっていることを確認します。 |
|        | 400 | 音声合成要求では、有効な SSML 入力のみが許可されます。 | 入力 SSML 式が正しいことを確認してください。 |
|        | 400 | 入力ファイル内に音声名 {voiceName} が見つかりません。 | 入力 SSML の音声名がモデル ID と一致しません。 |
|        | 400 | 入力ファイル内の段落の量は、10,000 未満である必要があります。 | ファイル内の段落が 10,000 未満であることを確認してください。 |
|        | 400 | 入力ファイルの文字数は 400 文字を越えている必要があります。 | 入力ファイルが 400 文字を超えていることを確認してください。 |
|        | 404 | 音声合成定義内に宣言されたモデルが見つかりません: {modelID}。 | {modelID} が正しいことを確認してください。 |
|        | 429 | アクティブな音声合成の制限を超えています。 いくつかの要求が完了するまでお待ちください。 | サーバーでは、Azure アカウントごとに最大 120 個の要求を実行およびキューに登録できます。 いくつかの要求が完了するまで、新しい要求を送信しないでお待ちください。 |
| All       | 429 | 要求が多すぎます。 | クライアントでは、Azure アカウントごとに 1 秒あたり最大 5 個の要求をサーバーに送信することが許可されています。 1 秒あたりの要求の数を減らしてください |
| 削除    | 400 | その音声合成タスクはまだ使用中です。 | 削除できるのは、**完了した**要求、または**失敗した要求**のみです。 |
| GetByID   | 404 | 指定されたエンティティが見つかりません。 | 合成 ID が正しいことを確認してください。 |

## <a name="regions-and-endpoints"></a>リージョンとエンドポイント

Long Audio API は、一意のエンドポイントを持つ複数のリージョンで使用できます。

| リージョン | エンドポイント |
|--------|----------|
| オーストラリア東部 | `https://australiaeast.customvoice.api.speech.microsoft.com` |
| カナダ中部 | `https://canadacentral.customvoice.api.speech.microsoft.com` |
| 米国東部 | `https://eastus.customvoice.api.speech.microsoft.com` |
| インド中部 | `https://centralindia.customvoice.api.speech.microsoft.com` |
| 米国中南部 | `https://southcentralus.customvoice.api.speech.microsoft.com` |
| 東南アジア | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| 英国南部 | `https://uksouth.customvoice.api.speech.microsoft.com` |
| 西ヨーロッパ | `https://westeurope.customvoice.api.speech.microsoft.com` |
| 米国西部 2 | `https://westus2.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>音声出力形式

柔軟なオーディオ出力形式をサポートしています。 段落ごとにオーディオ出力を生成したり、'concatenateResult' パラメーターを設定して複数のオーディオ出力を単一の出力に連結したりできます。 Long Audio API では、次の音声出力形式がサポートされています。

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
