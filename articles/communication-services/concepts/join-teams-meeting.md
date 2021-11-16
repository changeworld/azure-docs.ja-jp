---
title: Teams の会議の相互運用性
titleSuffix: An Azure Communication Services concept document
description: Teams の会議に参加する
author: tomkau
ms.author: tomkau
ms.date: 10/15/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.openlocfilehash: 74a39ff953c1ef8e549dbace0c7fc495976fae2a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325526"
---
# <a name="join-a-teams-meeting"></a>Teams 会議に参加する

> [!IMPORTANT]
> BYOI の相互運用性はパブリック プレビュー中であり、すべての Communication Services アプリケーションと Teams 組織で使用できます。
>
> プレビュー API および SDK は、サービス レベル アグリーメントなしで提供されるので、運用ワークロードには推奨されません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Communication Services を使用すると、ユーザーが Teams 会議に参加できるアプリケーションを作成できます。 これらのユーザーには [Standard ACS の価格](https://azure.microsoft.com/pricing/details/communication-services/)が適用されますが、相互運用性機能自体に追加料金は発生しません。 Bring Your Own Identity (BYOI) モデルでは、お客様がユーザー認証を制御し、アプリケーションのユーザーが Teams の会議に参加するために Teams のライセンスは必要ありません。 これは、ライセンスを持つ Teams ユーザー (医療プロバイダーや金融アドバイザーなど) とカスタム アプリケーションを使用する外部ユーザー (患者やクライアントなど) が仮想コンサルテーション エクスペリエンスに参加できる、企業-消費者間ソリューションに最適です。

Azure Communication Services SDK で Microsoft 365 Teams の ID を使用することもできます。 詳細については[こちら](./teams-interop.md)を参照してください。

現時点では、Azure Communication Services Calling SDK を使用して開始された通話に Teams ユーザーが参加することはできません。

## <a name="enabling-anonymous-meeting-join-in-your-teams-tenant"></a>Teams テナントで匿名の会議参加を有効にする

BYOI ユーザーが Teams の会議に参加すると、Teams Web アプリケーションを使用して匿名で Teams の会議に参加するユーザーと同様に、匿名の外部ユーザーとして扱われます。 BYOI ユーザーが Teams の会議に匿名ユーザーとして参加する機能は、既存の "匿名での会議への参加を許可する" 構成によって制御されます。これは、既存の Teams の匿名での会議への参加も制御します。 この設定は、[Teams の管理センター](https://admin.teams.microsoft.com/meetings/settings)で、または Teams の PowerShell コマンドレット [Set-CsTeamsMeetingConfiguration](/powershell/module/skype/set-csteamsmeetingconfiguration) を使用して更新できます。  

Azure Communication Services を使用して構築された、Teams ユーザーとの接続と通信を行うカスタム アプリケーションは、エンド ユーザーまたはボットから使用される可能性があります。また、アプリケーションの開発者が通信の一部として明示的に表示しない限り、Teams ユーザーに表示される方法に違いはありません。 カスタム アプリケーションでは、Teams の会議を保護するためにユーザー認証などのセキュリティ対策について考慮する必要があります。 匿名ユーザーを会議に参加させることによって生じるセキュリティへの影響に留意し、[Teams のセキュリティ ガイド](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings)を利用して、匿名ユーザーに提供する機能を構成してください。

## <a name="meeting-experience"></a>会議エクスペリエンス

Teams の匿名での会議への参加と同様に、アプリケーションには参加用の会議リンクが必要です。これは、Graph API または Microsoft Teams のカレンダーから取得できます。 Teams に表示される BYOI ユーザーの名前は、Communication Services Calling SDK を使用して構成でき、Azure Active Directory を使用して認証されていないことを Teams ユーザーに知らせるために、"外部" というラベルが付けられます。 最初の ACS ユーザーが Teams 会議に参加すると、Teams クライアントには、参加者のひとりがカスタム クライアントを使用しているために一部の機能が使用できない可能性があることを示すメッセージが表示されます。

会議の間に、Communication Services のユーザーは、Azure Communication Services SDK を使用して、基本的なオーディオ、ビデオ、画面共有、チャット機能を使用できます。 Communication Services のユーザーが会議を離れるか、会議が終了すると、新しいチャット メッセージの送受信はできなくなりますが、会議の間に送受信されたメッセージにはアクセスできます。 Communication Services の匿名ユーザーは、会議の参加者を追加したり削除したりすることはできません。また、会議の録画や文字起こしを始めることもできません。

Teams 会議に参加するために必要なデータフローに関するその他の情報は、「[クライアントとサーバーのアーキテクチャ](client-and-server-architecture.md)」ページに記載されています。 「[グループ通話のヒーロー サンプル](../samples/calling-hero-sample.md)」では、Web アプリケーションから Teams 会議に参加するためのサンプル コードを入手できます。

## <a name="privacy"></a>プライバシー
Azure Communication Services と Microsoft Teams の間の相互運用性により、アプリケーションとユーザーは、Teams の呼び出し、会議、チャットに参加できます。 アプリケーションの開発者は、Teams の呼び出しまたは会議で記録または文字起こしが有効になったら、そのことをユーザーに通知する責任があります。

記録または文字起こしが開始され、この事実をアプリケーションのユーザー インターフェイスを通じてリアルタイムでユーザーに通知する必要があることは、Azure Communication Services API によって開発者に示されます。 この義務に準拠していないことが原因で発生したすべてのコストと損害について、開発者は Microsoft の補償する必要があります。

## <a name="limitations-and-known-issues"></a>制限事項と既知の問題

- BYOI ユーザーは、Teams チャネルでスケジュールされている Teams 会議に参加し、音声と動画を使用できますが、チャネルのメンバーではないため、チャット メッセージを送受信することはできません。
- Microsoft Graph を使用して [Teams ミーティングの参加者の一覧を表示する](/graph/api/call-list-participants)と、Communication Services のユーザーに関する詳細は現在は含まれません。
- PowerPointユーザーに対して、プレゼンテーションCommunication Servicesされません。
- Teams の会議では最大 1,000 人の参加者がサポートされますが、現在の Azure Communication Services Calling SDK でサポートされる参加者は 350 人のみです。
- [Microsoft Teams のクラウド ビデオ相互運用性](/microsoftteams/cloud-video-interop)を使用すると、Communication Services のユーザーが画面を共有するときにいくつかのデバイスで問題が発生しています。
- 挙手、集合モード、ブレイクアウト ルームなどの機能は、Teams ユーザーのみが使用できます。
- 現在、Calling SDK では、Teams 会議のクローズ ドキャプションはサポートされていません。
- Communication Services ユーザーは [Teams ライブ イベント](/microsoftteams/teams-live-events/what-are-teams-live-events)に参加できません。
- Communication Services ユーザーが Teams ミーティングに参加しても、ボット用の [Teams アクティビティ ハンドラー イベント](/microsoftteams/platform/bots/bot-basics?tabs=csharp)は発生しません。

## <a name="next-steps"></a>次の手順

- [方法: Teams 会議に参加する](../how-tos/calling-sdk/teams-interoperability.md)
- [クイックスタート: BYOIと 呼び出しアプリを Teams 会議に参加させる](../quickstarts/voice-video-calling/get-started-teams-interop.md)
- [クイックスタート: byoi chat アプリを Teams 会議に参加させる](../quickstarts/chat/meeting-interop.md)
