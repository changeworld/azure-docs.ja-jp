---
title: クイック スタート:マイクから音声を認識する - Speech サービス
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: ab51fe0323ea23c16cb52aa7a0570f8d51d40b2f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467573"
---
このクイックスタートでは、[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) を使用して、音声をマイク入力から対話形式で認識し、キャプチャしたオーディオからテキスト文字起こしを取得します。 会話の文字起こしなどの一般的な認識タスクのために、この機能をアプリやデバイスに簡単に統合できます。 また、Bot Framework を Speech SDK と共に使用して音声アシスタントを構築するなど、より複雑な統合にも使用できます。

いくつかの前提条件を満たすと、4 つのステップを実行するだけでマイクから音声を認識します。

> [!div class="checklist"]
> * サブスクリプション キーとリージョンから `SpeechConfig` オブジェクトを作成します。
> * 上記の `SpeechConfig` オブジェクトを使用して `SpeechRecognizer` オブジェクトを作成します。
> * `SpeechRecognizer` オブジェクトを使用して、1 つの発話の認識プロセスを開始します。
> * 返された `SpeechRecognitionResult` を検査します。
