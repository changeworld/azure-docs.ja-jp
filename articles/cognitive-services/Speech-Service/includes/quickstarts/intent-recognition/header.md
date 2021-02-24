---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 4725a1a9cf2cb74655a37ac27a0a86f10d7f4bb9
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052930"
---
このクイックスタートでは、[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) と Language Understanding (LUIS) サービスを使用して、マイクによってキャプチャされたオーディオ データから意図を認識します。 具体的には、Speech SDK を使用して音声をキャプチャし、LUIS のあらかじめ構築されたドメインを使用してホーム オートメーションに対する意図 (照明のオン/オフなど) を特定します。 

いくつかの前提条件を満たすと、わずかな手順でマイクから音声を認識し、意図を特定することができます。

> [!div class="checklist"]
>
> * サブスクリプション キーとリージョンから `SpeechConfig` オブジェクトを作成します。
> * 上記の `SpeechConfig` オブジェクトを使用して `IntentRecognizer` オブジェクトを作成します。
> * `IntentRecognizer` オブジェクトを使用して、1 つの発話の認識プロセスを開始します。
> * 返された `IntentRecognitionResult` を検査します。

