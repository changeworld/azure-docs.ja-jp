---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: a4accb0846ec9cf5efc5ca5aa00c731edd41d4d4
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2018
ms.locfileid: "43143119"
---
<!-- N.B. no header, language-agnostic -->

Microsoft Cognitive Services [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) は、**音声から意図**を認識する方法を提供し、Cognitive Services [Language Understanding Service (LUIS)](https://www.luis.ai/home) によってサポートされています。

1. LUIS サブスクリプション キーと[リージョン](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition)をパラメーターとして使用して、音声ファクトリを作成します。 LUIS サブスクリプション キーは、サービス ドキュメント内で**エンドポイント キー**と呼ばれます。 LUIS 作成キーは使用できません  (このセクションの後の方の注を参照してください)。

1. 音声ファクトリから意図認識エンジンを取得します。 認識エンジンでは、デバイスの既定のマイク、オーディオ ストリーム、またはファイルの音声を使用できます。

1. **AppId** に基づいた言語理解モデルを入手します。 必要な意図を追加します。 

1. 必要に応じて、非同期操作のイベントを関連付けます。 暫定的結果および最終的結果 (意図を含む) が得られると、認識エンジンによってイベント ハンドラーが呼び出されます。 イベントを関連付けない場合、アプリケーションは、最終的な文字起こしの結果のみを受け取ります。

1. 意図認識を開始します。 コマンドまたはクエリの認識などの単発の認識の場合は、`RecognizeAsync()`メソッドを使用します。 このメソッドは、認識されている最初の発話を返します。 実行時間の長い認識では、`StartContinuousRecognitionAsync()` メソッドを使用します。 非同期認識結果のイベントを関連付けます。

Speech SDK を使用する意図認識のシナリオでは、次のコード スニペットを参照してください。 サンプルの値を、独自の LUIS サブスクリプション キー、[サブスクリプションのリージョン](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition)、および意図モデルの **AppId** で置き換えます。

> [!NOTE]
> Speech SDK でサポートされているその他のサービスとは対照的に、意図認識には特定のサブスクリプション キー (LUIS エンドポイント キー) が必要です。 意図認識技術については、[LUIS の Web サイト](https://www.luis.ai)を参照してください。 **エンドポイント キー**を取得する方法については、「[LUIS エンドポイント キーの作成](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-how-to-azure-subscription#create-luis-endpoint-key)」を参照してください。
