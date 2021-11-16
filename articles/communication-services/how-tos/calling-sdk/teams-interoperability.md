---
title: Teams 会議に参加する
titleSuffix: An Azure Communication Services how-to guide
description: Azure Communication Services SDK を使用して Teams 会議に参加します。
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
ms.openlocfilehash: 0b2469def75cc54efc9efced2c25587b466f4cf0
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063629"
---
# <a name="join-a-teams-meeting"></a>Teams 会議に参加する

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

Azure Communication Services SDK を使用して、ユーザーを通常の Microsoft Teams 会議に参加させることができます。 その方法を見ていきましょう。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- デプロイ済みの Communication Services リソース。 [Communication Services リソースを作成します](../../quickstarts/create-communication-resource.md)。
- 通話クライアントを有効にするためのユーザー アクセス トークン。 詳細については、[アクセス トークンの作成と管理](../../quickstarts/access-tokens.md)に関する記事を参照してください。
- 省略可能: クイックスタートを完了して、[アプリケーションに音声通話を追加します](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

> [!NOTE]
> この API は開発者向けにプレビューとして提供されており、寄せられたフィードバックにもとづいて変更される場合があります。 この API は運用環境で使用しないでください。 この API を使用するには、ACS Calling Web SDK の "ベータ" リリースを使用してください

Teams 会議に参加するには、`join` メソッドを使用し、会議リンクまたは会議の座標を渡します。

会議リンクを使用して参加する:

```js
const locator = { meetingLink: '<MEETING_LINK>'}
const call = callAgent.join(locator);
```

会議の座標を使用して参加する:

```js
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

会議 ID を使用して参加する (これは現在、制限付きのプレビュー段階です):

```js
const locator = { meetingId: '<MEETING_ID>'}
const call = callAgent.join(locator);
```

## <a name="next-steps"></a>次のステップ
- [通話の管理方法を参照する](./manage-calls.md)
- [ビデオの管理方法を参照する](./manage-video.md)
- [通話の転送方法を参照する](./transfer-calls.md)
