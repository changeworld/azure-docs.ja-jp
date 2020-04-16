---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: 36243fd5db76a4706318f41b3e2cb3f557c17189
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400758"
---
このクイックスタートでは、[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) を使用して、音声をマイク入力から対話形式で認識し、キャプチャしたオーディオからテキスト文字起こしを取得します。 会話の文字起こしなどの一般的な認識タスクのために、この機能をアプリやデバイスに簡単に統合できます。 また、Bot Framework を Speech SDK と共に使用して音声アシスタントを構築するなど、より複雑な統合にも使用できます。

いくつかの前提条件を満たすと、4 つのステップを実行するだけでマイクから音声を認識します。

> [!div class="checklist"]
> * サブスクリプション キーとリージョンから `SpeechConfig` オブジェクトを作成します。
> * 上記の `SpeechConfig` オブジェクトを使用して `SpeechRecognizer` オブジェクトを作成します。
> * `SpeechRecognizer` オブジェクトを使用して、1 つの発話の認識プロセスを開始します。
> * 返された `SpeechRecognitionResult` を検査します。
