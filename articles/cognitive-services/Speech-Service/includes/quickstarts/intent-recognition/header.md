---
title: クイック スタート:音声、意図、エンティティを認識する - Speech サービス
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 1/02/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a833d39ab91cd803f066d707306a6ff648d37e8f
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660521"
---
このクイックスタートでは、[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) と Language Understanding (LUIS) サービスを使用して、マイクによってキャプチャされたオーディオ データから意図を認識します。 具体的には、Speech SDK を使用して音声をキャプチャし、LUIS のあらかじめ構築されたドメインを使用してホーム オートメーションに対する意図 (照明のオン/オフなど) を特定します。 

いくつかの前提条件を満たすと、わずかな手順でマイクから音声を認識し、意図を特定することができます。

> [!div class="checklist"]
>
> * サブスクリプション キーとリージョンから ````SpeechConfig```` オブジェクトを作成します。
> * 上記の ````SpeechConfig```` オブジェクトを使用して ````IntentRecognizer```` オブジェクトを作成します。
> * ````IntentRecognizer```` オブジェクトを使用して、1 つの発話の認識プロセスを開始します。
> * 返された ````IntentRecognitionResult```` を検査します。
