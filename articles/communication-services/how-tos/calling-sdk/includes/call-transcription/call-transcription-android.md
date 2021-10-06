---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: b880f8a8f84247c14a0f1e81577d9e252b2b78c8
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129584928"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

> [!WARNING]
> ACS Calling Android SDK のバージョン 1.1.0 およびベータ リリース バージョン 1.1.0-beta.1 までは、`isTranscriptionActive` および `addOnIsTranscriptionActiveChangedListener` は `Call` オブジェクトの一部になっています。 新しいベータ リリースでは、これらの API は、以下に説明するように `Call` の拡張機能として移動されています。

> [!NOTE]
> この API は開発者向けにプレビューとして提供されており、寄せられたフィードバックにもとづいて変更される場合があります。 この API は運用環境で使用しないでください。 この API を使用するには、ACS Calling Android SDK の "ベータ" リリースを使用してください

通話の文字起こしは、コア `Call` API の拡張機能です。 まず、文字起こし機能 API オブジェクトを取得する必要があります。

```java
TranscriptionCallFeature callTranscriptionFeature = call.api(Features.TRANSCRIPTION);
```

次に、通話が文字起こしされているかどうかを確認するために、`callTranscriptionFeature` の `isTranscriptionActive` プロパティを調べます。 `boolean` を返します。

```java
boolean isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive();
```

文字起こしの変更をサブスクライブすることもできます。

```java
private void handleCallOnIsTranscriptionChanged(PropertyChangedEvent args) {
    boolean isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive();
}

callTranscriptionFeature.addOnIsTranscriptionActiveChangedListener(handleCallOnIsTranscriptionChanged);
```
