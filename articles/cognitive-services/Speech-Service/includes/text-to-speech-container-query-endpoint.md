---
title: テキスト読み上げコンテナーのエンドポイントに対してクエリを実行する
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/31/2020
ms.author: eur
ms.openlocfilehash: e81b2bfb8781b3c8d14d182d8d6da14b48484953
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501082"
---
コンテナーには、[REST ベースのエンドポイント API](../rest-text-to-speech.md) が用意されています。 各種のプラットフォーム、フレームワーク、言語向けに、多数の[サンプル ソース コード プロジェクト](https://github.com/Azure-Samples/Cognitive-Speech-TTS)が提供されています。

標準またはニューラルのテキスト読み上げコンテナーでは、ダウンロードしたイメージ タグのロケールと音声を使用することになります。 たとえば、`latest` タグをダウンロードした場合、既定のロケールは `en-US` で、音声は `AriaNeural` になります。 そのうえで、`{VOICE_NAME}` 引数は [`en-US-AriaNeural`](../language-support.md#neural-voices) となります。 次のサンプル SSML をご覧ください。

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```
