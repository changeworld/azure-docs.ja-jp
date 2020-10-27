---
title: クイックスタート - Teams の会議に参加する
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: dd75e5e97e5dca898ba10e91528782861fb949ec
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114590"
---
## <a name="prerequisites"></a>前提条件

- 実用的な [Communication Services 通話アプリ](../getting-started-with-calling.md)。
- [Teams のデプロイ](https://docs.microsoft.com/deployoffice/teams-install)。

## <a name="enable-teams-interoperability"></a>Teams の相互運用性を有効にする

Teams の相互運用性機能は、現在プライベート プレビュー段階です。 ご利用の Communication Services リソースに関して、この機能を有効にする場合は、次の情報を添えて、メール ([acsfeedback@microsoft.com](mailto:acsfeedback@microsoft.com)) でご連絡ください。

1. Communication Services リソースがある Azure サブスクリプションのサブスクリプション ID。
2. Teams のテナント ID。 この情報を入手する方法としては、[チームへのリンクを取得して共有](https://support.microsoft.com/office/create-a-link-or-a-code-for-joining-a-team-11b0de3b-9288-4cb4-bc49-795e7028296f#:~:text=Create%20a%20link%20If%20you%E2%80%99re%20a%20team%20owner%2C,link%20into%20any%20browser%20to%20join%20the%20team)するのが最も簡単です。

この機能を使用するには、両方のエンティティの所有組織のメンバーである必要があります。

## <a name="add-the-teams-ui-controls"></a>Teams の UI コントロールを追加する

HTML 内に新しいテキスト ボックスとボタンを追加します。 テキスト ボックスは、Teams の会議のコンテキストを入力する目的で、また、ボタンは、指定した会議に参加する目的で使用されます。

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px;"
    />
    <input 
      id="teams-id-input"
      type="text"
      placeholder="Teams meeting context"
      style="margin-bottom:1em; width: 300px;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
         <button id="meeting-button" type="button" disabled="false">
        Join Teams Meeting
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>Teams の UI コントロールを有効にする

これで、指定された Teams の会議に参加するためのコードに対し、 **[Join Teams Meeting]\(Teams の会議に参加\)** ボタンをバインドすることができます。

```javascript
meetingButton.addEventListener("click", () => {
    
    // join with meeting link
    call = callAgent.join({meetingLink: 'MEETING_LINK'}, {});

     // join with meeting coordinates
     call = callAgent.join({
        threadId: 'CHAT_THREAD_ID',
        organizerId: 'ORGANIZER_ID',
        tenantId: 'TENANT_ID',
        messageId: 'MESSAGE_ID'
    }, {})
    
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
    meetingButton.disabled = true;
});
```

## <a name="get-the-meeting-context"></a>会議のコンテキストを取得する

Teams のコンテキストは、Graph API を使用して取得できます。 この点については、[Graph のドキュメント](https://docs.microsoft.com/graph/api/onlinemeeting-createorget?view=graph-rest-beta&tabs=http)で詳しく説明されています。

必要な会議情報は、会議の招待状自体にある **[Join Meeting]\(会議に参加\)** の URL から取得することもできます。

## <a name="run-the-code"></a>コードの実行

Webpack ユーザーは、`webpack-dev-server` を使用してアプリをビルドし、実行することができます。 ローカルの Web サーバーにアプリケーション ホストをバンドルするには、次のコマンドを実行します。

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

ブラウザーを開き、http://localhost:8080/ に移動します。 次のように表示されます。

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="完成した JavaScript アプリケーションのスクリーンショット。":::

Communication Services アプリケーション内から Teams の会議に参加するには、Teams のコンテキストをテキスト ボックスに挿入し、 *[Join Teams Meeting]\(Teams の会議に参加\)* を押します。

