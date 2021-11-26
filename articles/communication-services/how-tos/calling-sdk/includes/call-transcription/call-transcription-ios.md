---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: e895050eb2f3d0c298bf0f127395aff0cee4b52c
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132590655"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

> [!WARNING]
> ACS Calling iOS SDK のバージョン 1.1.0 およびベータ リリース バージョン 1.1.0-beta.1 までは、`isTranscriptionActive` は `Call` オブジェクトの一部になっていて、`didChangeTranscriptionState` は `CallDelegate` デリゲートの一部になっています。 新しいベータ リリースでは、これらの API は、以下に説明するように `Call` の拡張機能として移動されています。
> [!NOTE]
> この API は開発者向けにプレビューとして提供されており、寄せられたフィードバックにもとづいて変更される場合があります。 この API は運用環境で使用しないでください。 この API を使用するには、ACS Calling iOS SDK の "ベータ" リリースを使用してください

通話の文字起こしは、コア `Call` オブジェクトの拡張機能です。 まず、文字起こし機能オブジェクトを取得する必要があります。

```swift
let callTranscriptionFeature = call.feature(Features.transcription)
```

次に、通話が文字起こしされているかどうかを確認するために、`callTranscriptionFeature` の `isTranscriptionActive` プロパティを調べます。 `Bool` を返します。

```swift
let isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive;
```

イベント `didChangeTranscriptionState` を使用してクラスに `TranscriptionCallFeatureDelegate` 文字起こしを実装することにより、文字起こしの変更をサブスクライブすることもできます。

```swift
callTranscriptionFeature.delegate = self

// didChangeTranscriptionState is a member of TranscriptionCallFeatureDelegate
public func transcriptionCallFeature(_ transcriptionCallFeature: TranscriptionCallFeature, didChangeTranscriptionState args: PropertyChangedEventArgs) {
    let isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive
}
```
