---
title: 音声変換コンテナー エンドポイントのクエリを実行する
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: a55cf5ea6aa696d0cf0cdd619dc22839d748d83c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491134"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>音声変換またはカスタム音声変換

コンテナーは、websocket ベースのクエリ エンドポイント API シリーズを提供します。これには、[Speech SDK](../index.md) を介してアクセスします。 既定では、Speech SDK は、オンラインの音声サービスを使用します。 コンテナーを使用するには、初期化方法を変更する必要があります。

> [!TIP]
> Speech SDK とコンテナーを使用する場合、Azure Speech リソースの[サブスクリプション キーまたは認証ベアラー トークン](../rest-speech-to-text.md#authentication)を提供する必要はありません。

次の例を参照してください。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

この Azure クラウド初期化呼び出しを使用する方法を

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

次のようにコンテナー [エンドポイント](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet)を使用する呼び出しに変更します。

```csharp
var config = SpeechConfig.FromEndpoint(
    new Uri("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1"));
```
# <a name="pythontabpython"></a>[Python](#tab/python)

この Azure クラウド初期化呼び出しを使用する方法を

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

次のようにコンテナー [エンドポイント](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)を使用する呼び出しに変更します。

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

***
