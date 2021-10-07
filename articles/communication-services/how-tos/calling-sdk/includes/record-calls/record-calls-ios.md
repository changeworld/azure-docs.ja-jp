---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 029e4ce2f699eb66f0807f47cc80a893052f7a57
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129584934"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

## <a name="record-calls"></a>通話を記録する
> [!WARNING]
> ACS Calling iOS SDK のバージョン 1.1.0 およびベータ リリース バージョン 1.1.0-beta.1 までは、`isRecordingActive` は `Call` オブジェクトの一部になっていて、`didChangeRecordingState` は `CallDelegate` デリゲートの一部になっています。 新しいベータ リリースでは、これらの API は、以下に説明するように `Call` の拡張機能として移動されています。
> [!NOTE]
> この API は開発者向けにプレビューとして提供されており、寄せられたフィードバックにもとづいて変更される場合があります。 この API は運用環境で使用しないでください。 この API を使用するには、ACS Calling iOS SDK の "ベータ" リリースを使用してください

通話記録は、コア `Call` API の拡張機能です。 まず、記録機能 API オブジェクトを取得する必要があります。

```swift
let callRecordingFeature = call.api(Features.recording)
```

次に、通話が記録されているかどうかを確認するために、`callRecordingFeature` の `isRecordingActive` プロパティを調べます。 `Bool` を返します。

```swift
let isRecordingActive = callRecordingFeature.isRecordingActive;
```

イベント `didChangeRecordingState` を使用してクラスに `RecordingCallFeatureDelegate` デリゲートを実装することにより、録音の変更をサブスクライブすることもできます。

```swift
callRecordingFeature.delegate = self

// didChangeRecordingState is a member of RecordingCallFeatureDelegate
public func recordingCallFeature(_ recordingCallFeature: RecordingCallFeature, didChangeRecordingState args: PropertyChangedEventArgs) {
    let isRecordingActive = recordingFeature.isRecordingActive
}
```

アプリケーションから記録を開始する場合は、まず「[通話録音の概要](../../../../concepts/voice-video-calling/call-recording.md)」に従って通話記録を設定する手順を実行してください。

サーバーで通話録音を設定したら、iOS アプリケーションで通話から `ServerCallId` 値を取得し、それをサーバーに送信して録音プロセスを開始する必要があります。 `ServerCallId` 値は、`getInfo()` を使用してクラス オブジェクト内に見つけることができる `CallInfo` クラスの `getServerCallId()` を使用して見つけることができます。

```swift
// Send serverCallId to your recording server to start the call recording.
let serverCallId = call.info.getServerCallId(){ (serverId, error) in }
```

サーバーから録音が開始されると、イベント `didChangeRecordingState` がトリガーされ、`recordingFeature.isRecordingActive` の値が `true` になります。

通話録音を停止する場合は、通話録音を開始するのと同じように、`ServerCallId` を取得し、これを録音サーバーに送信して、録音サーバーが通話録音を停止できるようにする必要があります。

```swift
// Send serverCallId to your recording server to stop the call recording.
let serverCallId = call.info.getServerCallId(){ (serverId, error) in }
```

サーバーから録音が停止されると、イベント `didChangeRecordingState` がトリガーされ、`recordingFeature.isRecordingActive` の値が `false` になります。
