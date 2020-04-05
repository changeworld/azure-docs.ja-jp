---
title: テキスト読み上げコンテナーのエンドポイントに対してクエリを実行する
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 8460ddca5cff2b3da540b5fa8cf66e0687892789
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "73491119"
---
コンテナーには、[REST ベースのエンドポイント API](../rest-text-to-speech.md) が用意されています。 各種のプラットフォーム、フレームワーク、言語向けに、多数の[サンプル ソース コード プロジェクト](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/)が提供されています。

"*標準のテキスト読み上げ*" コンテナーでは、ダウンロードしたイメージ タグのロケールと音声を使用することになります。 たとえば、`latest` タグをダウンロードした場合、既定のロケールは `en-US` で、音声は `JessaRUS` になります。 そのうえで、`{VOICE_NAME}` 引数は [`en-US-JessaRUS`](../language-support.md#standard-voices) となります。 次のサンプル SSML をご覧ください。

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

ただし、"*カスタムのテキスト読み上げ*" では、**カスタム音声ポータル**から[音声やモデル](https://aka.ms/custom-voice-portal)を取得する必要があります。 カスタム モデル名は、音声名と同義です。 **[トレーニング]** ページに移動し、 **引数として使用する**音声やモデル`{VOICE_NAME}`をコピーします。
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="カスタム音声モデル - 音声名":::

次のサンプル SSML をご覧ください。

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

いくつかのヘッダーとデータ ペイロードを指定して、HTTP POST 要求を作成してみましょう。 `{VOICE_NAME}` プレースホルダーは、実際の値に置き換えてください。

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

このコマンドは、次の操作を行います。

* `speech/synthesize/cognitiveservices/v1` エンドポイントの HTTP POST 要求を作成します。
* `Accept` の `audio/*` ヘッダーを指定します。
* `Content-Type` の `application/ssml+xml` ヘッダーを指定します。詳細については、[要求本文](../rest-text-to-speech.md#request-body)を参照してください。
* `X-Microsoft-OutputFormat` の `riff-16khz-16bit-mono-pcm` ヘッダーを指定します。その他のオプションについては、「[オーディオ出力](../rest-text-to-speech.md#audio-outputs)」を参照してください。
* [ が指定された](../speech-synthesis-markup.md)音声合成マークアップ言語 (SSML)`{VOICE_NAME}` 要求をエンドポイントに送信します。