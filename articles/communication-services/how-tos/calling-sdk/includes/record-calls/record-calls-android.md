---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: a12f5717d927e8358a503a50f314c6b80f577952
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129584968"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

## <a name="record-calls"></a>通話を記録する
> [!WARNING]
> ACS Calling Android SDK のバージョン 1.1.0 およびベータ リリース バージョン 1.1.0-beta.1 までは、`isRecordingActive` および `addOnIsRecordingActiveChangedListener` は `Call` オブジェクトの一部になっています。 新しいベータ リリースでは、これらの API は、以下に説明するように `Call` の拡張機能として移動されています。

> [!NOTE]
> この API は開発者向けにプレビューとして提供されており、寄せられたフィードバックにもとづいて変更される場合があります。 この API は運用環境で使用しないでください。 この API を使用するには、ACS Calling Android SDK の "ベータ" リリースを使用してください

通話記録は、コア `Call` API の拡張機能です。 まず、記録機能 API オブジェクトを取得する必要があります。

```java
RecordingCallFeature callRecordingFeature = call.api(Features.RECORDING);
```

次に、通話が記録されているかどうかを確認するために、`callRecordingFeature` の `isRecordingActive` プロパティを調べます。 `boolean` を返します。

```java
boolean isRecordingActive = callRecordingFeature.isRecordingActive();
```

また、変更の記録をサブスクライブすることもできます。

```java
private void handleCallOnIsRecordingChanged(PropertyChangedEvent args) {
    boolean isRecordingActive = callRecordingFeature.isRecordingActive();
}

callRecordingFeature.addOnIsRecordingActiveChangedListener(handleCallOnIsRecordingChanged);
```

アプリケーションから記録を開始する場合は、まず「[通話録音の概要](../../../../concepts/voice-video-calling/call-recording.md)」に従って通話記録を設定する手順を実行してください。

サーバーで通話録音を設定したら、Android アプリケーションで通話から `ServerCallId` 値を取得し、それをサーバーに送信して録音プロセスを開始する必要があります。 `ServerCallId` 値は、`getInfo()` を使用してクラス オブジェクト内に見つけることができる `CallInfo` クラスの `getServerCallId()` を使用して見つけることができます。

```java
try {
    String serverCallId = call.getInfo().getServerCallId().get();
    // Send serverCallId to your recording server to start the call recording.
} catch (ExecutionException | InterruptedException e) {

} catch (UnsupportedOperationException unsupportedOperationException) {

}
```

サーバーから録音が開始されると、イベント `handleCallOnIsRecordingChanged` がトリガーされ、`callRecordingFeature.isRecordingActive()` の値が `true` になります。

通話録音を停止する場合は、通話録音を開始するのと同じように、`ServerCallId` を取得し、これを録音サーバーに送信して、録音サーバーが通話録音を停止できるようにする必要があります。

```java
try {
    String serverCallId = call.getInfo().getServerCallId().get();
    // Send serverCallId to your recording server to stop the call recording.
} catch (ExecutionException | InterruptedException e) {

} catch (UnsupportedOperationException unsupportedOperationException) {

}
```

サーバーから録音が停止されると、イベント `handleCallOnIsRecordingChanged` がトリガーされ、`callRecordingFeature.isRecordingActive()` の値が `false` になります。
