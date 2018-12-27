---
title: 音声テキスト変換の使用 | Microsoft Docs
description: 音声サービスで音声テキスト変換を使用する方法について説明します。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 26cecedfc3ad2d472b9686e25054fe08253cee77
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39068524"
---
# <a name="use-speech-to-text-in-the-speech-service"></a>音声サービスで音声テキスト変換を使用する

お使いのアプリケーションで**音声テキスト変換**を使用するには、2 つの方法があります。

| 方法 | 説明 |
|-|-|
| [SDK](speech-sdk.md) | C/C++、C#、および Java 開発者向けの最も簡単な方法 |
| [REST](rest-apis.md) | HTTP POST 要求を使用して短い発話を認識する | 

## <a name="using-the-sdk"></a>SDK を使用する

[Speech SDK](speech-sdk.md) では、アプリケーションのすべての機能と共に**音声テキスト変換**を使用する最も簡単な方法を提供しています。

1. 音声ファクトリを作成し、音声サービス サブスクリプション キーと[リージョン](regions.md)または承認トークンを指定します。 この時点で、独自の音声認識モデルの認識言語やカスタム エンドポイントなどのオプションを構成することもできます。

2. ファクトリから認識エンジンを取得します。 3 種類の認識エンジンを利用できます。 認識エンジンの種類ごとに、デバイスの既定のマイク、オーディオ ストリーム、またはファイルの音声を使用できます。

    認識エンジン | 関数
    -|-
    音声認識エンジン|音声のテキスト文字起こしを提供します
    意図認識エンジン|認識した後に、[LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/) を介して話者の意図を派生させます\*
    翻訳認識エンジン|書き起こされたテキストを別の言語に翻訳します ([音声翻訳](how-to-translate-speech.md)に関するページを参照)

    \* "*意図を認識するには、意図認識エンジンの音声ファクトリを作成するときに、別の LUIS サブスクリプション キーを使用する必要があります。*"
    
4. 必要に応じて、非同期操作のイベントを関連付けます。 暫定的結果および最終的結果が得られると、認識エンジンによってイベント ハンドラーが呼び出されます。 それ以外の場合、アプリケーションは、最終的な文字起こしの結果を受け取ります。

5. 認識を開始します。
   単発の認識の場合は、コマンドまたはクエリの認識と同様に、認識されている最初の発話を返す `RecognizeAsync()` を使用します。
   実行時間の長い認識の場合は、文字起こしと同様に、`StartContinuousRecognitionAsync()` を使用し、非同期の認識結果のイベントを関連付けます。

### <a name="sdk-samples"></a>SDK のサンプル

最新のサンプル セットについては、[Cognitive Services Speech SDK のサンプル GitHub リポジトリ](https://aka.ms/csspeech/samples)を参照してください。

## <a name="using-the-rest-api"></a>REST API の使用

この REST API は、SDK でサポートされていない言語を使用している場合に、音声を認識する最も簡単な方法です。 サービス エンドポイントに HTTP POST 要求を行い、要求の本文で発話全体を渡します。 認識されたテキストが含まれる応答を受信します。

> [!NOTE]
> REST API を使用する場合、発話は 15 秒以下に制限されます。

**Speech to Text** REST API の詳細については、[REST API](rest-apis.md#speech-to-text) に関するページをご覧ください。 動作を確認するには、GitHub から [REST API のサンプル](https://github.com/Azure-Samples/SpeechToText-REST)をダウンロードします。

## <a name="next-steps"></a>次の手順

- [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
- [C++ で音声を認識する方法](quickstart-cpp-windows.md)
- [C# で音声を認識する方法](quickstart-csharp-dotnet-windows.md)
- [Java で音声を認識する方法](quickstart-java-android.md)
