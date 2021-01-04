---
title: クイックスタート - Teams の会議に参加する
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: c191da32444c3eb0315373780c8037f1b45be423
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2020
ms.locfileid: "96993028"
---
## <a name="prerequisites"></a>前提条件

- 実用的な [Communication Services 通話アプリ](../getting-started-with-calling.md)。
- [Teams のデプロイ](/deployoffice/teams-install)。

## <a name="enable-teams-interoperability"></a>Teams の相互運用性を有効にする

Teams の相互運用性機能は、現在プライベート プレビュー段階です。 ご利用の Communication Services リソースに関して、この機能を有効にする場合は、次の情報を添えて、メール ([acsfeedback@microsoft.com](mailto:acsfeedback@microsoft.com)) でご連絡ください。

1. Communication Services リソースがある Azure サブスクリプションのサブスクリプション ID。
2. Teams のテナント ID。 この情報を入手する方法としては、[チームへのリンクを取得して共有](https://support.microsoft.com/office/create-a-link-or-a-code-for-joining-a-team-11b0de3b-9288-4cb4-bc49-795e7028296f)するのが最も簡単です。

この機能を使用するには、両方のエンティティの所有組織のメンバーである必要があります。

## <a name="add-the-teams-ui-controls"></a>Teams の UI コントロールを追加する

index.html のコードを次のスニペットに置き換えます。
テキスト ボックスは、Teams の会議のコンテキストを入力する目的で、また、ボタンは、指定した会議に参加する目的で使用されます。

```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - Calling Sample</title>
</head>
<body>
    <h4>Azure Communication Services</h4>
    <h1>Teams meeting join quickstart</h1>
    <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
    <div>
        <button id="join-meeting-button" type="button" disabled="false">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
    </div>
    <script src="./bundle.js"></script>
</body>

</html>
```

## <a name="enable-the-teams-ui-controls"></a>Teams の UI コントロールを有効にする

client.js ファイルの内容を次のスニペットに置き換えます。

```javascript
import { CallClient } from "@azure/communication-calling";
import { AzureCommunicationUserCredential } from '@azure/communication-common';

let call;
let callAgent;
const meetingLinkInput = document.getElementById('teams-link-input');
const hangUpButton = document.getElementById('hang-up-button');
const teamsMeetingJoinButton = document.getElementById('join-meeting-button');
const callStateElement = document.getElementById('call-state');

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationUserCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential);
    teamsMeetingJoinButton.disabled = false;
}
init();

hangUpButton.addEventListener("click", async () => {
    // end the current call
    await call.hangUp();
  
    // toggle button states
    hangUpButton.disabled = true;
    teamsMeetingJoinButton.disabled = false;
    callStateElement.innerText = '-';
  });

teamsMeetingJoinButton.addEventListener("click", () => {
    
    // set display name in the meeting
    callAgent.updateDisplayName('ACS user');
    
    // join with meeting link
    call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
    call.on('callStateChanged', () => {
        callStateElement.innerText = call.state;
    })
    // toggle button states
    hangUpButton.disabled = false;
    teamsMeetingJoinButton.disabled = true;
});
```

## <a name="get-the-teams-meeting-link"></a>Teams 会議のリンクを取得する

Teams 会議のリンクは、Graph API を使用して取得できます。 この点については、[Graph のドキュメント](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta)で詳しく説明されています。
Communication Services 通話 SDK は、Teams 会議のフル リンクを受け入れます。 このリンクは、`onlineMeeting` リソースの一部として返され、[`joinWebUrl` プロパティ](/graph/api/resources/onlinemeeting?view=graph-rest-beta)からアクセスできます。必要な会議情報は、Teams 会議の招待自体に含まれる **[会議に参加]** の URL から取得することもできます。

## <a name="run-the-code"></a>コードの実行

Webpack ユーザーは、`webpack-dev-server` を使用してアプリをビルドし、実行することができます。 ローカルの Web サーバーにアプリケーション ホストをバンドルするには、次のコマンドを実行します。

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

ブラウザーを開き、http://localhost:8080/ に移動します。 次のように表示されます。

:::image type="content" source="../media/javascript/acs-join-teams-meeting-quickstart.PNG" alt-text="完成した JavaScript アプリケーションのスクリーンショット。":::

Communication Services アプリケーション内から Teams の会議に参加するには、Teams のコンテキストをテキスト ボックスに挿入し、 *[Join Teams Meeting]\(Teams の会議に参加\)* を押します。
