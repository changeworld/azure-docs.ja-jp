---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 7c739c65e638657cffbfade0c2a491cdefd6b626
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "128699174"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-web.md)]

### <a name="record-calls"></a>通話を記録する
> [!NOTE]
> この API は開発者向けにプレビューとして提供されており、寄せられたフィードバックにもとづいて変更される場合があります。 この API は運用環境で使用しないでください。 この API を使用するには、ACS Calling Web SDK の "ベータ" リリースを使用してください

通話記録は、コア `Call` API の拡張機能です。 まず、記録機能 API オブジェクトを取得する必要があります。

```js
const callRecordingApi = call.api(Features.Recording);
```

次に、通話が記録されているかどうかを確認するために、`callRecordingApi` の `isRecordingActive` プロパティを調べます。 `Boolean` を返します。

```js
const isRecordingActive = callRecordingApi.isRecordingActive;
```

また、変更の記録をサブスクライブすることもできます。

```js
const isRecordingActiveChangedHandler = () => {
    console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);
```