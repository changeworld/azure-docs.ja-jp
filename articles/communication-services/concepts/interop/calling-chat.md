---
title: Teams 通話とチャットの相互運用性
titleSuffix: An Azure Communication Services concept document
description: Teams 通話とチャットの相互運用性
author: tomkau
ms.author: tomkau
ms.date: 10/15/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.openlocfilehash: 10435eba4127fbb58d939bfac0710aa90e2c7b32
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846256"
---
# <a name="teams-interoperability-calling-and-chat"></a>Teams相互運用性: 呼び出しとチャット

> [!IMPORTANT]
> 通話とチャットの相互運用性はプライベート プレビュー段階であり、Azure Communication Services の早期導入者の数が制限されています。 このフォーム [を送信して](https://forms.office.com/r/F3WLqPjw0D) プレビューへの参加を要求できます。また、お客様のシナリオを確認し、プレビューへの参加を評価します。
>
> プライベート プレビュー Api および Sdk は、サービス レベル アグリーメントなしで提供されており、運用環境のワークロードには適しておらず、テストユーザーとテストデータでのみ使用してください。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
> 
> サポート、質問、またはフィードバックやレポートの問題を提供する場合は、[Teams 相互運用のアドホック通話とチャット チャネル](https://teams.microsoft.com/l/channel/19%3abfc7d5e0b883455e80c9509e60f908fb%40thread.tacv2/Teams%2520Interop%2520ad%2520hoc%2520calling%2520and%2520chat?groupId=d78f76f3-4229-4262-abfb-172587b7a6bb&tenantId=72f988bf-86f1-41af-91ab-2d7cd011db47)を使用してください。 Azure Communication Service TAP チームのメンバーである必要があります。

このプレビューの一環として、Azure Communication Services SDK を使用して、Bring Your Own IDENTITY (BYOI) ユーザーが 1:1 の呼び出しを開始したり、Teams ユーザーと 1:n チャットを開始したりできるアプリケーションを構築できます。 [Standard ACS 価格](https://azure.microsoft.com/pricing/details/communication-services/) は、これらのユーザーに適用されますが、相互運用性機能自体に追加料金はかかりません。



## <a name="enabling-calling-and-chat-interoperability-in-your-teams-tenant"></a>Teams テナントで通話とチャットの相互運用性を有効にする
Communication Services ユーザーと Teams テナント間で通話とチャットを有効にするには、新しい Teams PowerShell コマンドレット[CsTeamsAcsFederationConfiguration](/powershell/module/teams/set-csteamsacsfederationconfiguration)を使用します。 このコマンドレットは、プライベートプレビューの参加者のみが使用できます。 

Teams ユーザーと接続して通信するために Azure Communication Services を使用して構築されたカスタム アプリケーションは、エンド ユーザーまたはボットによって使用できます。また、アプリケーションの開発者が明示的に示す場合を限り、Teams ユーザーに対する表示方法に違いはありません。

Teams ユーザーと通話やチャットを開始するには、そのユーザーの Azure Active Directory (Azure AD) のオブジェクト ID が必要です。 これを取得するには、[Microsoft Graph API](/graph/api/resources/users) を使用します。また、[Azure AD Connect](../../../active-directory/hybrid/how-to-connect-sync-whatis.md) (または他のメカニズム) を使用してオンプレミスのディレクトリと Azure AD 間の同期を行っている場合は、オンプレミス ディレクトリからも取得できます。

## <a name="calling"></a>呼び出し
Calling SDK を使用すると、Communication Services ユーザーまたはエンドポイントは、Azure Active Directory (Azure AD) のオブジェクト ID で識別された Teams ユーザーと 1 対 1 の通話を開始できます。 他の Communication Services ユーザーを呼び出す既存のアプリケーションを簡単に変更して、代わりに Teams ユーザーを呼び出すことができます。
 
[通話の管理 - Azure Communication Services に関するハウツーガイド | Microsoft Docs](../../how-tos/calling-sdk/manage-calls.md?pivots=platform-web)

[communicationUserId](/javascript/api/@azure/communication-common/communicationuseridentifier?view=azure-node-latest#communicationUserId) を使用して別の Communication Services エンドポイントを呼び出します。
```js
const acsCallee = { communicationUserId: '<ACS User ID>' }
const call = callAgent.startCall([acsCallee]);
```

[microsoftTeamsUserId](/javascript/api/@azure/communication-common/microsoftteamsuseridentifier?view=azure-node-latest#microsoftTeamsUserId) を使用して Teams ユーザーを呼び出します。
```js
const teamsCallee = { microsoftTeamsUserId: '<Teams User AAD Object ID>' }
const call = callAgent.startCall([teamsCallee]);
```
 
**制限事項と既知の問題**
- Teams ユーザーは「TeamsOnly」モードである必要があります。 Skype for Business ユーザーが 1 対 1 の呼び出しを受信 Communication Services できない。
- グループ呼び出しへのエスカレーションはサポートされていません。
- Communication Services の記録は、1 対 1 の呼び出しでは使用できません。
- 通話転送、グループ通話の集荷、同化、ボイス メールなどの高度な通話ルーティング機能はサポートされていません。
- Teams ユーザーは、転送/転送 Communication Services としてユーザーを設定できない。
- Teams クライアントには、Communication Services ユーザーとの 1 対 1 の通話中に予期したとおりに動作しない機能が多数あります。
- サードパーティ製の [Teams 用デバイス](/MicrosoftTeams/devices/teams-ip-phones)と [Skype IP 電話](/skypeforbusiness/certification/devices-ip-phones)はサポートされていません。

## <a name="chat"></a>チャット
Chat SDK を使用すると、Communication Services ユーザーまたはエンドポイントは、Azure Active Directory (AAD) のオブジェクト ID で識別された Teams ユーザーとのグループ チャットを開始できます。 他の Communication Services ユーザーとのチャットを作成する既存のアプリケーションを簡単に変更し、代わりに Teams ユーザーとのチャットを作成できます。 Chat SDK を使用して Teams ユーザーを参加者として追加する方法の例を次に示します。 Chat SDK を使用してメッセージを送信する方法、参加者を管理する方法などについては、[クイックスタート](../../quickstarts/chat/get-started.md?pivots=programming-language-javascript)のページを参照してください。

Teams ユーザーとのチャットの作成:
```js
async function createChatThread() { 
const createChatThreadRequest = {  topic: "Hello, World!"  }; 
const createChatThreadOptions = {
    participants: [ { 
        id: { microsoftTeamsUserId: '<Teams User AAD Object ID>' }, 
        displayName: '<USER_DISPLAY_NAME>' }
    ] }; 
const createChatThreadResult = await chatClient.createChatThread( 
createChatThreadRequest, createChatThreadOptions ); 
const threadId = createChatThreadResult.chatThread.id; return threadId; }
```                                         

テストを簡単に行えるように、[こちら](https://github.com/Azure-Samples/communication-services-web-chat-hero/tree/teams-interop-chat-adhoc)にサンプル アプリを公開しました。 作業を開始するには、Communication Services リソースと、相互運用が有効になった Teams テナントでこのアプリを更新してください。 

**制限事項と既知の問題** </br>
プライベート プレビュー段階では、Communication Services ユーザーは、プレーンおよびリッチ テキスト メッセージ、入力インジケーター、確認メッセージ、リアルタイム通知の送受信など、Communication Services Chat SDK を使用してさまざまなアクションを実行できます。 ただし、Teams のチャット機能の多くはサポートされていません。 重要な動作と既知の問題を次に示します。
-   チャットを開始できるのは、Communication Services ユーザーのみである。 
-   Communication Services ユーザーは、gif、画像、またはファイルを送受信できない。 ファイルと画像へのリンクは共有できます。
-   Communication Services ユーザーはチャットを削除できる。 これにより、Teams ユーザーはチャット スレッドから削除され、Teams クライアントからのメッセージ履歴は非表示になります。
- 既知の問題: Communication Services ユーザーが参加者一覧に正しく表示されない。 現在、彼らは外部として表示されますが、ピープル カードの一貫性が損なわれる可能性があります。 
- 既知の問題: Teams アプリ内から、チャットを通話にエスカレートできない。 
- 既知の問題: Teams ユーザーによるメッセージの編集はサポートされていない。 

## <a name="privacy"></a>プライバシー
Azure Communication Services と Microsoft Teams の間の相互運用性により、アプリケーションとユーザーは、Teams の呼び出し、会議、チャットに参加できます。 アプリケーションの開発者は、Teams の呼び出しまたは会議で記録または文字起こしが有効になったら、そのことをユーザーに通知する責任があります。

記録または文字起こしが開始され、この事実をアプリケーションのユーザー インターフェイスを通じてリアルタイムでユーザーに通知する必要があることは、Azure Communication Services API によって開発者に示されます。 この義務に準拠していないことが原因で発生したすべてのコストと損害について、開発者は Microsoft の補償する必要があります。
