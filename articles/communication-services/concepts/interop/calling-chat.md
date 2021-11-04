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
ms.openlocfilehash: 328bfff2366efb416cbd41452a821256f675d0e2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131078695"
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

Teams ユーザーと通話やチャットを開始するには、そのユーザーの Azure Active Directory (AAD) のオブジェクト ID が必要です。 これを取得するには[Microsoft Graph API](/graph/api/resources/users) を使用するか、 [Azure AD Connect](../../../active-directory/hybrid/how-to-connect-sync-whatis.md) (または他のメカニズム) を使用してオンプレミスのディレクトリと AAD の間で同期する場合があります。

## <a name="calling"></a>呼び出し
Calling SDK を使用すると、Communication Services ユーザーまたはエンドポイントは、Teams ユーザー (Azure Active Directory (AAD) オブジェクト ID で識別される 1 対 1 の呼び出しを開始できます。 他の Communication Services ユーザーを呼び出す既存のアプリケーションを簡単に変更して、代わりに Teams ユーザーを呼び出すことができます。
 
[通話の管理 - Azure Communication Services に関するハウツーガイド | Microsoft Docs](../../how-tos/calling-sdk/manage-calls.md?pivots=platform-web)

別の ACS ユーザーの呼び出し:
```js
const acsCallee = { communicationUserId: <'ACS_USER_ID>' }
const call = callAgent.startCall([acsCallee]);
```

Teams ユーザーの呼び出し:
```js
const teamsCallee = { microsoftTeamsUserId: '<Teams User AAD Object ID>' }
const call = callAgent.startCall([teamsCallee]);
```
 
**制限事項と既知の問題**
- Teams ユーザーは「TeamsOnly」モードである必要があります。 Skype for Business ユーザーが 1 対 1 の呼び出しを受信 Communication Services できない。
- グループ呼び出しへのエスカレーションはサポートされていません。
- 通話履歴に Communication Services ユーザーが正しく表示されない
- Communication Services の記録は、1 対 1 の呼び出しでは使用できません。
- 通話転送、グループ通話の集荷、同化、ボイス メールなどの高度な通話ルーティング機能はサポートされていません。
- Teams ユーザーは、転送/転送 Communication Services としてユーザーを設定できない。
- LyncIpPhone フォークはサポートされていません。

## <a name="chat"></a>チャット
Chat SDK を使用すると、Communication Services ユーザーまたはエンドポイントは、Teams ユーザー (Azure Active Directory (AAD) オブジェクト ID で識別される 1:n チャットを開始できます。 他の Communication Services ユーザーとのチャットを作成する既存のアプリケーションを簡単に変更し、代わりに Teams ユーザーとのチャットを作成することができます。
                                            
[クイック スタート:チャットをアプリに追加する](../../quickstarts/chat/get-started.md?pivots=programming-language-javascript)

Teams ユーザーとのチャットの作成:
```js
async function createChatThread() { 
const createChatThreadRequest = {  topic: "Hello, World!"  }; 
const createChatThreadOptions = {
    participants: [ { 
        id: { microsoftTeamsUserId: '<TEAMS_USER_ID>' }, 
        displayName: '<USER_DISPLAY_NAME>' }
    ] }; 
const createChatThreadResult = await chatClient.createChatThread( 
createChatThreadRequest, createChatThreadOptions ); 
const threadId = createChatThreadResult.chatThread.id; return threadId; }
```                                         

**サポートされる機能**
-   送信/受信メッセージ (種類: テキスト、リッチテキスト、絵文字) 
-   Communication Services ユーザーは、送信されたメッセージを編集できます
-   送信済みメッセージの削除
-   リアルタイム通知を受け取る (現在、ACS でサポートされているスレッドおよびメッセージ関連イベント)
-   送信および入力インジケーターを受信する
-   開封確認メッセージを送信および受信する
-   参加者の追加とメッセージ履歴の共有: Teams ユーザーは Teams ユーザーのみを追加できます。 Communication Services ユーザーは、Teams と Communication Services ユーザーを追加できます。
-   チャットから既存の参加者を削除する
-   チャットを離れる
-   チャット トピックを更新する
-   Communication Services ユーザーはチャットを削除できます。


**制限事項と既知の問題**
- Teams ユーザーによるメッセージの編集に失敗します。
- Communication Services によってスレッドが削除されると、Teams ユーザーのメッセージ履歴が削除され、Teams ユーザーがスレッドから削除されます。
- 外部ユーザー用の Teams クライアント UI に整合性がありません。
- Teams のクライアントを使用して、チャット参加者との通話を開始することはできません


## <a name="privacy"></a>プライバシー
Azure Communication Services と Microsoft Teams の間の相互運用性により、アプリケーションとユーザーは、Teams の呼び出し、会議、チャットに参加できます。 アプリケーションの開発者は、Teams の呼び出しまたは会議で記録または文字起こしが有効になったら、そのことをユーザーに通知する責任があります。

記録または文字起こしが開始され、この事実をアプリケーションのユーザー インターフェイスを通じてリアルタイムでユーザーに通知する必要があることは、Azure Communication Services API によって開発者に示されます。 この義務に準拠していないことが原因で発生したすべてのコストと損害について、開発者は Microsoft の補償する必要があります。
