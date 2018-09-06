---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: ee50a104a75d3cd5ff958bd49a1ff7010c5d5083
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144391"
---
<!-- N.B. no header, language-agnostic -->

Microsoft Cognitive Services [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) は、アプリケーションで**音声翻訳**を使用する最も簡単な方法を提供します。
この SDK は、このサービスのすべての機能を提供します。 音声翻訳を実行するための基本的なプロセスには、次の手順が含まれます。

1. 音声ファクトリを作成し、音声サービス サブスクリプション キー (または認証トークン) と[リージョン](~/articles/cognitive-services/speech-service/regions.md)をパラメーターとして指定します。
   
1. 音声ファクトリから翻訳認識エンジンを作成します。 ソースおよびターゲットの翻訳言語を構成し、テキストと音声のどちらで出力するかを指定できます。 使用するオーディオ ソースに基づいて、翻訳認識エンジンにはさまざまな種類があります。

1. 必要に応じて、非同期操作のイベントを関連付けます。 暫定的結果、最終的結果、およびオプションのオーディオ出力の合成イベントが取得されると、認識エンジンによってイベント ハンドラーが呼び出されます。 それ以外の場合、アプリケーションは、最終的な文字起こしの結果のみを受け取ります。

1. 認識を開始します。 単発の翻訳の場合は、最初に認識された発話を返す `RecognizeAsync()` メソッドを使用します。 実行時間の長い翻訳の場合は、`StartContinuousRecognitionAsync()` メソッドを使用し、非同期の認識結果のイベントを関連付けます。

Speech SDK を使用する音声翻訳のシナリオでは、次のコード スニペットを参照してください。

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
