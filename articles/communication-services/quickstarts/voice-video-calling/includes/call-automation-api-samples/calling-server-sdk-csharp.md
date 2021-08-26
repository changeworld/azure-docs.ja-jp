---
title: インクルード ファイル
description: C#
services: azure-communication-services
author: ravithanneeru
manager: joseys
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: joseys
ms.openlocfilehash: 1dae62ae0ab1fa03dd4c5e1128699cd43a241984
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801740"
---
## <a name="prerequisites"></a>前提条件

開始する前に、必ず次のことを行ってください。

- アクティブなサブスクリプションがある Azure アカウントを作成します。 詳細については、[アカウントの無料作成](https://azure.microsoft.com/free/)に関するページを参照してください。
- [Visual Studio (2019 以上)](https://visualstudio.microsoft.com/vs/)。
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download/dotnet-framework/net472) (使用する Visual Studio インスタンス (32 または 64 ビット) に対応するバージョンを必ずインストールしてください)。
- Azure Communication Services リソースを作成します。 詳細については、[Azure Communication Services リソースの作成](../../../create-communication-resource.md)に関するページを参照してください。 このサンプルで使用するリソースの **接続文字列** を記録する必要があります。
- 新しい Azure Communication Services リソースの電話番号を取得します。 詳細については、[電話番号の取得](../../../telephony-sms/get-phone-number.md?pivots=platform-azp)に関するページを参照してください。
- [ngrok](https://www.ngrok.com/download) をダウンロードしてインストールします。 サンプルはローカルで実行されるため、ngrok によってすべてのイベントの受信が有効にされます。
- (省略可能) アプリケーションで再生されるカスタム メッセージを生成するための Azure Speech リソースを作成します。 [こちらのガイダンス](../../../../../cognitive-services/speech-service/overview.md#try-the-speech-service-for-free)に従ってリソースを作成します。

> [!NOTE]
> このクイックスタートの最終的なコードは [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/OutboundCallReminder) にあります。 このサンプルでは、Microsoft Cognitive Services Speech SDK が使用されます。 Azure Cognitive Services Speech SDK をダウンロードすることで、その[ライセンス](https://aka.ms/csspeech/license201809)に同意したものと見なされます。

## <a name="object-model"></a>オブジェクト モデル

C# 用 Azure Communication Services Calling Server SDK が備える主な機能のいくつかは、次のクラスにより処理されます。

| 名前                                  | 説明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallingServerClient | このクラスは通話機能に必要です。 Communication Services リソースの接続文字列を使用して CallingServerClient のインスタンスを作成し、それを使って通話の開始と終了、音声の再生と取り消し、参加者の追加と削除を行います。 |
| CommunicationIdentityClient | このクラスは、通信ユーザー ID を作成または削除するために必要です。 |

## <a name="create-a-call-client"></a>通話クライアントを作成する

通話クライアントを作成するには、Communication Services の接続文字列を使用し、それを通話クライアント オブジェクトに渡します。

```csharp
this.CallClient = new CallingServerClient("<Connection_String>");
```

## <a name="create-user-identity"></a>ユーザー ID を作成する

Communication Services リソースの接続文字列を使用して `CommunicationIdentityClient` オブジェクトを作成し、次に `CreateUserAsync` メソッドを使用してソース ユーザー ID を作成します。

```csharp
var client = new CommunicationIdentityClient("<Connection_String>");
var user = await client.CreateUserAsync().ConfigureAwait(false);
```

## <a name="delete-user-identity"></a>ユーザー ID を削除する

`CommunicationIdentityClient` オブジェクトの `DeleteUserAsync` メソッドを使用して、ユーザー ID を削除します。

```csharp
var client = new CommunicationIdentityClient("<Connection_String>");
await client.DeleteUserAsync(new CommunicationUserIdentifier(source)).ConfigureAwait(false);
```

## <a name="create-a-call"></a>通話を作成する

`CallingServerClient` オブジェクトの `CreateCallConnectionAsync` メソッドを使用して、通話を開始します。 `CreateCallConnectionAsync` メソッドのパラメーターを次に示します。
- `source` は、`CommunicationIdentifier` 型の発信者のソース ユーザー ID です。
- `targets` は、`IEnumerable<CommunicationIdentifier>` 型のターゲットID の一覧です。
- `options` は、`CreateCallOptions` 型の通話のオプションです。 `CreateCallOptions` メソッドのパラメーターを次に示します。

    - `callbackUri` は、アプリケーションのコールバック URI です。
    - `requestedModalities` は、要求通話のモダリティです。 種類は `IEnumerable<MediaType>` です。 値は、Audio または Video です。
    - `requestedCallEvents` は、要求されたコールバック イベントです。 これは `IEnumerable<EventSubscriptionType>` 型です。 `EventSubscriptionType.ParticipantsUpdated` と `EventSubscriptionType.DtmfReceived` イベントをサブスクライブするには、このパラメーターを使用します。

- `cancellationToken` は、キャンセル トークンです。 種類は `CancellationToken` です。

```csharp
var source = new CommunicationUserIdentifier(callConfiguration.SourceIdentity);
var target = new PhoneNumberIdentifier(targetPhoneNumber);
var createCallOption = new CreateCallOptions(
    new Uri(callConfiguration.AppCallbackUrl),
    new List<MediaType> { MediaType.Audio },
    new List<EventSubscriptionType> { EventSubscriptionType.ParticipantsUpdated, EventSubscriptionType.DtmfReceived }
    );
createCallOption.AlternateCallerId = new PhoneNumberIdentifier(callConfiguration.SourcePhoneNumber);

Logger.LogMessage(Logger.MessageType.INFORMATION, "Performing CreateCall operation");
var call = await callClient.CreateCallConnectionAsync(source,
    new List<CommunicationIdentifier>() { target },
    createCallOption, reportCancellationToken)
    .ConfigureAwait(false);
```

`CreateCallConnectionAsync` メソッドでは `CallConnection` オブジェクトが返されます。これは、音声を再生する、音声を取り消す、電話を切るなどの他の通話操作に使用できます。

## <a name="play-audio"></a>オーディオの再生

通話接続が作成されると、`CallConnection` オブジェクトの `PlayAudioAsync` メソッドを使用して、被発信者に対する音声メッセージを再生できます。 `PlayAudioAsync` メソッドでは、次のパラメーターがサポートされています。

- `options` (必須) は、音声の再生オプションです。 種類は `PlayAudioOptions` です。 `PlayAudioOptions` オブジェクトでは、次のパラメーターがサポートされています。
    - `AudioFileUri` (必須) は、再生されるメッセージが含まれている音声ファイルの URI です。
    - `OperationContext` (必須) は、要求コンテキストの一意の ID です。
    - `Loop` (省略可能) は、音声メッセージを繰り返すには、true に設定します。
- `cancellationToken` は、キャンセル トークンです。 種類は `CancellationToken` です。

```csharp
// Preparing data for request
var playAudioRequest = new PlayAudioOptions()
{
    AudioFileUri = new Uri(callConfiguration.AudioFileUrl),
    OperationContext = Guid.NewGuid().ToString(),
    Loop = true,
};

Logger.LogMessage(Logger.MessageType.INFORMATION, "Performing PlayAudio operation");
var response = await callConnection.PlayAudioAsync(playAudioRequest, reportCancellationToken).ConfigureAwait(false);
```

`PlayAudioAsync` メソッドでは `PlayAudioResponse` が返されます。これを使用して、`OperationStatus` を取得できます。 `OperationStatus` の値は次のとおりです: `NotStarted`、`Running`、`Completed`、または `Failed`。

```csharp
Logger.LogMessage(Logger.MessageType.INFORMATION, $"Play Audio state: {response.Value.Status}");
```

## <a name="add-a-participant-to-the-call"></a>通話に参加者を追加する

`CallConnection` オブジェクトの `AddParticipantAsync` メソッドを使用して、通話に参加者を追加します。 `AddParticipantAsync` メソッドでは、次のパラメーターがサポートされています。

- `participant` は、`CommunicationUserIdentifier` または `PhoneNumberIdentifier` のいずれかの型の参加者識別子です。
- `alternateCallerId` は、ソース発信者 ID です。
- `operationContext` は、要求コンテキストの一意の ID です。
- `cancellationToken` (省略可能) は、キャンセル トークンです。


```csharp
// Preparing data for request
var operationContext = Guid.NewGuid().ToString();

if (identifierKind == CommunicationIdentifierKind.UserIdentity)
{
    var response = await callConnection.AddParticipantAsync(new CommunicationUserIdentifier(addedParticipant), null, operationContext).ConfigureAwait(false);
    Logger.LogMessage(Logger.MessageType.INFORMATION, $"PlayAudioAsync response --> {response}");
}
else if (identifierKind == CommunicationIdentifierKind.PhoneIdentity)
{
    var response = await callConnection.AddParticipantAsync(new PhoneNumberIdentifier(addedParticipant), callConfiguration.SourcePhoneNumber, operationContext).ConfigureAwait(false);
    Logger.LogMessage(Logger.MessageType.INFORMATION, $"PlayAudioAsync response --> {response}");
}
```

## <a name="cancel-media-processing"></a>メディア処理を取り消す

`CallConnection` オブジェクトの `CancelAllMediaOperationsAsync` メソッドを使用して、音声の再生操作を取り消します。 `CancelAllMediaOperationsAsync` メソッドでサポートされているパラメーターを次に示します。

- `operationContext` は、要求コンテキストの一意の ID です。
- `reportCancellationToken` は、キャンセル トークンです。 種類は `CancellationToken` です。

```csharp
var operationContext = Guid.NewGuid().ToString();
var response = await callConnection.CancelAllMediaOperationsAsync(operationContext, reportCancellationToken).ConfigureAwait(false);
```

## <a name="hang-up-the-call"></a>電話を切る

`CallConnection` オブジェクトの `HangupAsync` メソッドを使用して、電話を切ります。 `HangupAsync` メソッドでサポートされているパラメーターを次に示します。

- `reportCancellationToken` は、キャンセル トークンです。 種類は `CancellationToken` です。

```csharp
var hangupResponse = await callConnection.HangupAsync(reportCancellationToken).ConfigureAwait(false);
```