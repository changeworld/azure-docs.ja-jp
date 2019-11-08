---
title: クイック スタート:音声間翻訳 - Speech Service
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 77b060a5caf9865a1296cd6644cf0c51e3253a0e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504845"
---
このクイックスタートでは、[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) を使用して、ある言語の音声を別の言語の音声に対話的に翻訳します。 いくつかの前提条件を満たすと、次の 6 つの手順だけで音声間翻訳を行います。
> [!div class="checklist"]
> * サブスクリプション キーとリージョンから ````SpeechTranslationConfig```` オブジェクトを作成します。
> * ````SpeechTranslationConfig```` オブジェクトを更新して、ソースとターゲットの言語を指定する
> * ````SpeechTranslationConfig```` オブジェクトを更新して、音声出力の音声名を指定する
> * 上記の ````SpeechTranslationConfig```` オブジェクトを使用して ````TranslationRecognizer```` オブジェクトを作成します。
> * ````TranslationRecognizer```` オブジェクトを使用して、1 つの発話の認識プロセスを開始します。
> * 返された ````TranslationRecognitionResult```` を検査します。
