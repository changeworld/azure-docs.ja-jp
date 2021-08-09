---
title: Azure Web PubSub サービスのライブ デモ
description: Azure Web PubSub サービスのライブ デモを体験するためのクイックスタートです。
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: overview
ms.date: 04/26/2021
ms.openlocfilehash: e35ef66c038748e31ba218a56adb8111374d2339
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2021
ms.locfileid: "113219608"
---
# <a name="quickstart-get-started-with-chatroom-live-demo"></a>クイックスタート: チャットルーム ライブ デモを体験する

Azure Web PubSub サービスは、WebSocket とパブリッシュ-サブスクライブ パターンを使用して、リアルタイム メッセージング Web アプリケーションを作成するのに役立ちます。 [チャットルーム ライブ デモ](https://azure.github.io/azure-webpubsub/demos/clientpubsub.html)では、Azure Web PubSub から提供されるリアルタイム メッセージング機能を実演します。 このライブ デモでは、チャット グループに簡単に参加し、特定のグループにリアルタイム メッセージを送信できます。 

:::image type="content" source="media/quickstart-live-demo/chat-live-demo.gif" alt-text="チャットルーム ライブ デモを使用する。":::

このクイックスタートでは、ライブ デモを簡単に体験する方法について説明します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [create-instance-portal](includes/create-instance-portal.md)]

## <a name="get-started-with-the-chatroom-live-demo"></a>チャットルーム ライブ デモを始める

### <a name="get-client-url-with-a-temp-access-token"></a>一時アクセス トークンを使用してクライアントの URL を取得する

最初の手順として、Azure Web PubSub インスタンスからクライアント URL を取得する必要があります。 

- Azure portal に移動して、Azure Web PubSub インスタンスを見つけます。
- [`Key`]\(キー\) ブレードの [`Client URL Generator`]\(クライアント URL ジェネレーター\) に移動します。 
- 適切な [`Roles`]\(ロール\) として **[Send To Groups]\(グループへの送信\)** および **[Join/Leave Groups]\(グループへの参加と脱退\)** を設定します。
- [`Client Access URL`]\(クライアント アクセス URL\) を生成してコピーします。 

:::image type="content" source="media/quickstart-live-demo/generate-client-url.png" alt-text="クライアント URL 生成のスクリーンショット。":::

### <a name="try-the-live-demo"></a>ライブ デモを試す 

このライブ デモでは、グループへの参加とグループからの脱退、グループ メンバーへのメッセージの送信を簡単に行うことができます。 

- [チャットルーム ライブ デモ](https://azure.github.io/azure-webpubsub/demos/clientpubsub.html)を開き、`Client Access URL` を貼り付け、[接続] をクリックします。 

:::image type="content" source="media/quickstart-live-demo/paste-client-access-url.png" alt-text="ライブ デモでクライアント URL を貼り付けている画面のスクリーンショット。":::

> [!NOTE]
>  **クライアント アクセス URL** はポータルで提供される便利なツールであり、使用開始を簡単にします。このクライアント アクセス URL を使用して、簡易接続テストを行うこともできます。 独自のアプリケーションを記述するため、URL の生成に役立つよう、SDK を 4 か国語で提供しています。 

- 試しに、参加するグループやメッセージ送信先のグループを変えたときに、どのようなメッセージが届くかを見てみましょう。 次に例を示します。
    - 2 つのクライアントを同じグループに参加させます。 メッセージはグループのメンバーに配信されます。 
    - 2 つのクライアントを異なるグループに参加させます。 グループのメンバーではない場合、クライアントはメッセージを受信しません。 
- さらに、`Client Access URL` (クライアント アクセス URL) の生成時に [`Roles`] チェック ボックスをオフにして、グループへの参加またはグループへのメッセージ送信を行うとどうなるか見てみましょう。 次に例を示します。
    - `Send to Groups` アクセス許可をオフにします。 クライアントからはグループにメッセージを送信できません。 
    - `Join/Leave Groups` アクセス許可をオフにします。 クライアントはグループに参加できません。 

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、チャットルーム ライブ デモでリアルタイム メッセージング機能を学習しました。 これで、独自のアプリケーションの作成を始められます。 

> [!div class="nextstepaction"]
> [クイックスタート: Azure Web PubSub でメッセージを発行し、サブスクライブする](https://azure.github.io/azure-webpubsub/getting-started/publish-messages/js-publish-message)

> [!div class="nextstepaction"]
> [クイックスタート: Azure Web PubSub で簡単なチャットルームを作成する](https://azure.github.io/azure-webpubsub/getting-started/create-a-chat-app/js-handle-events)

> [!div class="nextstepaction"]
> [クイックスタート: Azure Functions と Azure Web PubSub サービスを使用してシンプルなサーバーレス チャット アプリケーションを作成する](./quickstart-serverless.md)

> [!div class="nextstepaction"]
> [Azure Web PubSub のサンプルをさらに確認する](https://github.com/Azure/azure-webpubsub/tree/main/samples)