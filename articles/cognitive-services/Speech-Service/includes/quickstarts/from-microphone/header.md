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
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 64f084f58ae4b12d92d6dee343a59dd56b1c351e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505565"
---
このクイックスタートでは、[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) を使用して、マイクからキャプチャされたオーディオ データから音声を対話形式で認識します。 いくつかの前提条件を満たすと、4 つのステップを実行するだけでマイクから音声を認識します。
> [!div class="checklist"]
> * サブスクリプション キーとリージョンから ````SpeechConfig```` オブジェクトを作成する。
> * 上記の ````SpeechConfig```` オブジェクトを使用して ````SpeechRecognizer```` オブジェクト作成する。
> * ````SpeechRecognizer```` オブジェクトを使用して、1 つの発話の認識プロセスを開始する。
> * 返された ````SpeechRecognitionResult```` を検査する。
