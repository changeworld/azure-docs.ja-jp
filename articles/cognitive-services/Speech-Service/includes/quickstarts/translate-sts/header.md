---
title: クイック スタート:音声間で翻訳する - Speech サービス
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
ms.openlocfilehash: 805193c08e57ddc18dfca0a78c6b58cf895baec1
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74817400"
---
このクイックスタートでは、[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) を使用して、ある言語の音声を別の言語の音声に対話的に翻訳します。 いくつかの前提条件を満たすと、次の 6 つの手順だけで音声間翻訳を行います。
> [!div class="checklist"]
> * サブスクリプション キーとリージョンから ````SpeechTranslationConfig```` オブジェクトを作成します。
> * ````SpeechTranslationConfig```` オブジェクトを更新して、ソースとターゲットの言語を指定する
> * ````SpeechTranslationConfig```` オブジェクトを更新して、音声出力の音声名を指定する
> * 上記の ````SpeechTranslationConfig```` オブジェクトを使用して ````TranslationRecognizer```` オブジェクトを作成します。
> * ````TranslationRecognizer```` オブジェクトを使用して、1 つの発話の認識プロセスを開始します。
> * 返された ````TranslationRecognitionResult```` を検査します。
