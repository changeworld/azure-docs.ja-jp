---
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/08/2021
ms.openlocfilehash: 1cd52fc7930407317d426b3e2615202dce37e860
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751183"
---
## <a name="get-the-client-url-with-a-temp-access-token"></a>一時アクセス トークンを使用してクライアントの URL を取得する

Azure portal には、迅速なテストおよび検証のために一時 URL を生成する単純なクライアント URL ジェネレーターが用意されています。 このツールを使用して、一時クライアント アクセス URL を取得し、インスタンスに接続してみましょう。

- Azure portal に移動して、Azure Web PubSub インスタンスを見つけます。
- [`Key`]\(キー\) ブレードの [`Client URL Generator`]\(クライアント URL ジェネレーター\) に移動します。 
- 適切な [`Roles`]\(ロール\) として **[Send To Groups]\(グループへの送信\)** および **[Join/Leave Groups]\(グループへの参加と脱退\)** を設定します。
- [`Client Access URL`]\(クライアント アクセス URL\) を生成してコピーします。 

:::image type="content" source="../media/quickstart-live-demo/generate-client-url.png" alt-text="クライアント URL 生成のスクリーンショット。":::

## <a name="try-the-instance-with-an-online-demo"></a>オンライン デモでインスタンスを試す

このライブ デモでは、グループへの参加とグループからの脱退、グループ メンバーへのメッセージの送信を簡単に行うことができます。

> [!div class="nextstepaction"]
> [デモを開く](https://azure.github.io/azure-webpubsub/demos/clientpubsub.html)

> [!NOTE]
>  **クライアント アクセス URL** はポータルで提供される便利なツールであり、使用開始を簡単にします。このクライアント アクセス URL を使用して、簡易接続テストを行うこともできます。 独自のアプリケーションを記述するため、URL の生成に役立つよう、SDK を 4 か国語で提供しています。 

- 試しに、参加するグループやメッセージ送信先のグループを変えたときに、どのようなメッセージが届くかを見てみましょう。 次に例を示します。
    - 2 つのクライアントを同じグループに参加させます。 メッセージはグループのメンバーに配信されます。 
    - 2 つのクライアントを異なるグループに参加させます。 グループのメンバーではない場合、クライアントはメッセージを受信しません。 
- さらに、`Client Access URL` (クライアント アクセス URL) の生成時に [`Roles`] チェック ボックスをオフにして、グループへの参加またはグループへのメッセージ送信を行うとどうなるか見てみましょう。 次に例を示します。
    - `Send to Groups` アクセス許可をオフにします。 クライアントからはグループにメッセージを送信できません。 
    - `Join/Leave Groups` アクセス許可をオフにします。 クライアントはグループに参加できません。 
