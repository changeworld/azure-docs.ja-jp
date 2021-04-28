---
title: テキスト読み上げコンテナーのエンドポイントに対してクエリを実行する
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 4571acfc46d9b336be23eaa3287dce5696c707b2
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2021
ms.locfileid: "107930230"
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
