---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 0a3e3d80e5b669884d19d5eb60260a31b1836de3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "128699171"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-web.md)]

> [!NOTE]
> この API は開発者向けにプレビューとして提供されており、寄せられたフィードバックにもとづいて変更される場合があります。 この API は運用環境で使用しないでください。 この API を使用するには、ACS Calling Web SDK の "ベータ" リリースを使用してください

通話転送は、コア `Call` API の拡張機能です。 まず、転送機能 API オブジェクトを取得する必要があります。

```js
const callTransferApi = call.api(Features.Transfer);
```

通話転送には、次の 3 者が関与します。

- "*転送者*": 転送要求を開始するユーザー。
- "*転送元*": 転送されるユーザー。
- "*転送先*": 転送先のユーザー。

転送は、こちらの手順に従います。

1. "*転送者*" と "*転送元*" の間に接続済みの通話が既に存在します。 "*転送者*" が、"*転送元*" から "*転送先*" への通話の転送を決定します。
1. "*転送者*" は `transfer` API を呼び出します。
1. "*転送元*" は、`transferRequested` イベントを使用して "*転送先*" への転送要求を `accept` するか、`reject` するかを決定します。
1. "*転送先*" は "*転送元*" が転送要求を受け入れる場合にのみ着信通話を受信します。

現在の通話を転送するには、`transfer` API を使用します。 `transfer` は、`disableForwardingAndUnanswered` フラグを設定できる省略可能な `transferCallOptions` を受け取ります。

- `disableForwardingAndUnanswered = false`: "*転送先*" が転送通話に応答しない場合、その転送は "*転送先*" の転送および未回答の設定に従います。
- `disableForwardingAndUnanswered = true`: "*転送先*" が転送通話に応答しない場合、転送の試行は終了します。

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

`transfer` API を使用すると、`transferStateChanged` と `transferRequested` のイベントにサブスクライブできます。 `transferRequested` イベントが `call` インスタンスから生成され、`transferStateChanged` イベントおよび転送の `state` と `error` が `transfer` インスタンスから生成されます。

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

"*転送元*" は、`transferRequested` イベントで "*転送者*" によって開始された転送要求を、`transferRequestedEventArgs` の `accept()` または `reject()` を使用して、受け入れるか拒否することができます。 `transferRequestedEventArgs` で、`targetParticipant` 情報と `accept` または `reject` メソッドにアクセスできます。

```js
// Transferee to accept the transfer request
callTransferApi.on('transferRequested', args => {
    args.accept();
});

// Transferee to reject the transfer request
callTransferApi.on('transferRequested', args => {
    args.reject();
});
```