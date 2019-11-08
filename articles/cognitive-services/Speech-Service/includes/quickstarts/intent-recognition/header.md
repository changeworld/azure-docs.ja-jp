---
title: クイック スタート:音声、意図、エンティティを認識する - Speech Service
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 3b50ba8f2c3d21fb5bb0ab2c26cedb6bda0fab16
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505965"
---
このクイックスタートでは、[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) を使用して、マイクからキャプチャされたオーディオ データの音声を対話形式で認識します。 いくつかの前提条件を満たすと、4 つのステップを実行するだけでマイクから音声を認識します。
> [!div class="checklist"]
> * サブスクリプション キーとリージョンから ````SpeechConfig```` オブジェクトを作成します。
> * 上記の ````SpeechConfig```` オブジェクトを使用して ````IntentRecognizer```` オブジェクトを作成します。
> * ````IntentRecognizer```` オブジェクトを使用して、1 つの発話の認識プロセスを開始します。
> * 返された ````IntentRecognitionResult```` を検査します。