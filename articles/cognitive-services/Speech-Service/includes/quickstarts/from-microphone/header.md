---
title: クイック スタート:マイクから音声を認識する - Speech Service
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: bee3b64ece3faac6258fe4d017f12833b12e370d
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2019
ms.locfileid: "74536364"
---
このクイックスタートでは、[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) を使用して、音声をマイク入力から対話形式で認識し、キャプチャしたオーディオからテキスト文字起こしを取得します。 会話の文字起こしなどの一般的な認識タスクのために、この機能をアプリやデバイスに簡単に統合できます。 また、Bot Framework を Speech SDK と共に使用して音声アシスタントを構築するなど、より複雑な統合にも使用できます。

いくつかの前提条件を満たすと、4 つのステップを実行するだけでマイクから音声を認識します。

> [!div class="checklist"]
> * サブスクリプション キーとリージョンから `SpeechConfig` オブジェクトを作成する。
> * 上記の `SpeechConfig` オブジェクトを使用して `SpeechRecognizer` オブジェクト作成する。
> * `SpeechRecognizer` オブジェクトを使用して、1 つの発話の認識プロセスを開始します。
> * 返された `SpeechRecognitionResult` を検査します。
