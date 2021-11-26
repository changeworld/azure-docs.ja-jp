---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 974f42125aa65fb5b4a9ba334130e24e7214877b
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132590526"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

> [!WARNING]
> ACS Calling Android SDK のバージョン 1.1.0 およびベータ リリース バージョン 1.1.0-beta.1 までは、`isTranscriptionActive` および `addOnIsTranscriptionActiveChangedListener` は `Call` オブジェクトの一部になっています。 新しいベータ リリースでは、これらの API は、以下に説明するように `Call` の拡張機能として移動されています。

> [!NOTE]
> この API は開発者向けにプレビューとして提供されており、寄せられたフィードバックにもとづいて変更される場合があります。 この API は運用環境で使用しないでください。 この API を使用するには、ACS Calling Android SDK の "ベータ" リリースを使用してください

通話の文字起こしは、コア `Call` オブジェクトの拡張機能です。 まず、文字起こし機能オブジェクトを取得する必要があります。

```java
TranscriptionCallFeature callTranscriptionFeature = call.feature(Features.TRANSCRIPTION);
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
