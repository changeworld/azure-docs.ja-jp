---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 1773e22a54cc86e7736c91e4be757caa0250cbf7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421946"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>音声変換またはカスタム音声変換

コンテナーは、websocket ベースのクエリ エンドポイント API シリーズを提供します。これには、[Speech SDK](../index.yml) を介してアクセスします。 既定では、Speech SDK は、オンラインの音声サービスを使用します。 コンテナーを使用するには、初期化方法を変更する必要があります。

> [!TIP]
> Speech SDK とコンテナーを使用する場合、Azure Speech リソースの[サブスクリプション キーまたは認証ベアラー トークン](../rest-speech-to-text.md#authentication)を提供する必要はありません。

次の例を参照してください。

# <a name="c"></a>[C#](#tab/csharp)

この Azure クラウド初期化呼び出しを使用する方法を

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

次のようにコンテナー [ホスト](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)を使用する呼び出しに変更します。

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```
# <a name="python"></a>[Python](#tab/python)

この Azure クラウド初期化呼び出しを使用する方法を

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

次のようにコンテナー [ホスト](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)を使用する呼び出しに変更します。

```python
speech_config = speechsdk.SpeechConfig(
    host="ws://localhost:5000")
```

***
