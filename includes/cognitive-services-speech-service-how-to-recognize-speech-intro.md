---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: 1103e5a217ca4972cc1c983a7ad0d07b0797e9e9
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2018
ms.locfileid: "43129351"
---
<!-- N.B. no header, no intents here, language-agnostic -->

Cognitive Services [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) では、アプリケーションのすべての機能と共に**音声テキスト変換**を使用する最も簡単な方法を提供しています。

1. 音声ファクトリを作成し、音声サービス サブスクリプション キー (または認証トークン) と[リージョン](~/articles/cognitive-services/speech-service/regions.md)をパラメーターとして指定します。 非標準のサービス エンドポイントを指定するカスタム エンドポイントを指定することもできます。

1. 音声ファクトリから音声認識エンジンを取得します。 入力言語と出力形式を構成することができます。 認識エンジンでは、デバイスの既定のマイク、オーディオ ストリーム、またはファイルの音声を使用できます。

1. 必要に応じて、非同期操作のイベントを関連付けます。 暫定的結果および最終的結果が得られると、認識エンジンによってイベント ハンドラーが呼び出されます。 それ以外の場合、アプリケーションは、最終的な文字起こしの結果のみを受け取ります。

1. 認識を開始します。 コマンドまたはクエリの認識などの単発の認識の場合は、`RecognizeAsync()`メソッドを使用します。 このメソッドは、認識されている最初の発話を返します。 文字起こしのような実行時間の長い認識では、`StartContinuousRecognitionAsync()` メソッドを使用します。 非同期認識結果のイベントを関連付けます。

Speech SDK を使用する音声認識のシナリオでは、次のコード スニペットを参照してください。

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
