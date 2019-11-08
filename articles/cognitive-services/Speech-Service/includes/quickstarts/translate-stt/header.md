---
title: クイック スタート:音声変換の翻訳 - Speech Service
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
ms.openlocfilehash: c45e75038d1731c933ccf8bf26f9de573e409292
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504741"
---
このクイックスタートでは、[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) を使用して、ある言語の音声を別の言語のテキストに対話的に翻訳します。 いくつかの前提条件を満たすと、次の 5 つのステップを実行するだけで音声変換を翻訳します。
> [!div class="checklist"]
> * サブスクリプション キーとリージョンから ````SpeechConfig```` オブジェクトを作成します。
> * ````SpeechConfig```` オブジェクトを更新して、ソースとターゲットの言語を指定します。
> * 上記の ````SpeechConfig```` オブジェクトを使用して ````TranslationRecognizer```` オブジェクト作成します。
> * ````TranslationRecognizer```` オブジェクトを使用して、1 つの発話の認識プロセスを開始します。
> * 返された ````TranslationRecognitionResult```` を検査します。
