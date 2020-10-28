---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: cba789b6a40dd23309bb94289b187209893908e3
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2020
ms.locfileid: "92116662"
---
コンテナーは、websocket ベースのクエリ エンドポイント API シリーズを提供します。これには、[Speech SDK](../index.yml) を介してアクセスします。 既定では、Speech SDK は、オンラインの音声サービスを使用します。 コンテナーを使用するには、初期化方法を変更する必要があります。

> [!TIP]
> Speech SDK とコンテナーを使用する場合、Azure Speech リソースの[サブスクリプション キーまたは認証ベアラー トークン](../rest-speech-to-text.md#authentication)を提供する必要はありません。

次の例を参照してください。

# <a name="c"></a>[C#](#tab/csharp)

この Azure クラウド初期化呼び出しを使用する方法を

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

コンテナー [ホスト](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)でこの呼び出しを使用する方法に変更します。

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

コンテナー [エンドポイント](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)でこの呼び出しを使用する方法に変更します。

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/conversation/cognitiveservices/v1"
```

---
