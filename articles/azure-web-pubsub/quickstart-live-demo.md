---
title: Azure Web PubSub サービスのライブ デモ
description: Azure Web PubSub サービスのライブ デモを体験するためのクイックスタートです。
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: overview
ms.date: 04/26/2021
ms.openlocfilehash: a516d215a971b2a6cb54d4e73305cc69ce0edf8c
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166959"
---
# <a name="quickstart-get-started-with-live-demo"></a>クイックスタート: ライブ デモを体験する

Azure Web PubSub サービスは、WebSocket とパブリッシュ-サブスクライブ パターンを使用して、リアルタイム メッセージング Web アプリケーションを作成するのに役立ちます。 このクイックスタートでは、ライブ デモを簡単に体験する方法について説明します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [create-instance-portal](includes/create-instance-portal.md)]

## <a name="get-started-with-the-live-demo"></a>ライブ デモを体験する

### <a name="get-client-url-with-a-temp-access-token"></a>一時アクセス トークンを使用してクライアントの URL を取得する

最初の手順として、Azure Web PubSub インスタンスからクライアント URL を取得する必要があります。 

- Azure portal に移動して、Azure Web PubSub インスタンスを見つけます。
- [`Key`]\(キー\) ブレードの [`Client URL Generator`]\(クライアント URL ジェネレーター\) に移動します。 
- 適切な [`Roles`]\(ロール\) として **[Send To Groups]\(グループへの送信\)** および **[Join/Leave Groups]\(グループへの参加と脱退\)** を設定します。
- [`Client Access URL`]\(クライアント アクセス URL\) を生成してコピーします。 

### <a name="try-the-live-demo"></a>ライブ デモを試す 

このライブ デモでは、グループへの参加とグループからの脱退、グループ メンバーへのメッセージの送信を簡単に行うことができます。 

- [Client Pub/Sub Demo (クライアント パブリッシュ-サブスクライブ デモ)](https://azure.github.io/azure-webpubsub/demos/clientpubsub.html) を開き、`Client Access URL` (クライアント アクセス URL) を貼り付けて接続します。 
- 試しに、参加するグループやメッセージ送信先のグループを変えたときに、どのようなメッセージが届くかを見てみましょう。
- さらに、`Client Access URL` (クライアント アクセス URL) の生成時に [`Roles`]\(ロール\) チェック ボックスをオフにして、グループへの参加と脱退、グループへのメッセージ送信を行うとどうなるかを見てみましょう。