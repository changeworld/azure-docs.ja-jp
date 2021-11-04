---
author: eric-urban
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: eur
ms.openlocfilehash: 55cb81958e91a6c17e548eb02161154abba8aa66
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131507466"
---
このクイックスタートでは、[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) と Language Understanding (LUIS) サービスを使用して、マイクによってキャプチャされたオーディオ データから意図を認識します。 具体的には、Speech SDK を使用して音声をキャプチャし、LUIS のあらかじめ構築されたドメインを使用してホーム オートメーションに対する意図 (照明のオン/オフなど) を特定します。 

いくつかの前提条件を満たすと、わずかな手順でマイクから音声を認識し、意図を特定することができます。

> [!div class="checklist"]
>
> * サブスクリプション キーとリージョンから `SpeechConfig` オブジェクトを作成します。
> * 上記の `SpeechConfig` オブジェクトを使用して `IntentRecognizer` オブジェクトを作成します。
> * `IntentRecognizer` オブジェクトを使用して、1 つの発話の認識プロセスを開始します。
> * 返された `IntentRecognitionResult` を検査します。

