---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: dapine
ms.openlocfilehash: d59a55a9b6d57d90ac6ae893b05e1064c11eb58b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900423"
---
このクイックスタートでは、[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) と Language Understanding (LUIS) サービスを使用して、マイクによってキャプチャされたオーディオ データから意図を認識します。 具体的には、Speech SDK を使用して音声をキャプチャし、LUIS のあらかじめ構築されたドメインを使用してホーム オートメーションに対する意図 (照明のオン/オフなど) を特定します。 

いくつかの前提条件を満たすと、わずかな手順でマイクから音声を認識し、意図を特定することができます。

> [!div class="checklist"]
>
> * サブスクリプション キーとリージョンから `SpeechConfig` オブジェクトを作成します。
> * 上記の `IntentRecognizer` オブジェクトを使用して `SpeechConfig` オブジェクトを作成します。
> * `IntentRecognizer` オブジェクトを使用して、1 つの発話の認識プロセスを開始します。
> * 返された `IntentRecognitionResult` を検査します。
