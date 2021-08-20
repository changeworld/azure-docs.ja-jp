---
title: Teams の会議の相互運用性
titleSuffix: An Azure Communication Services concept document
description: Teams の会議に参加する
author: chpalm
manager: chpalm
services: azure-communication-services
ms.author: chpalm
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e884079bc159dbbc76d6443dc0e095c4d8f596ab
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2021
ms.locfileid: "114668098"
---
# <a name="teams-interoperability"></a>Teams の相互運用性

> [!IMPORTANT]
> BYOI 相互運用性はパブリック プレビュー段階であり、リクエストに応じて広範囲で利用できます。 [チームのテナント相互運用性](../concepts/teams-interop.md)を有効または無効にするには、[このフォーム](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u)を完成させてください。
>
> Microsoft 365 によって認証される相互運用性はプライベート プレビュー段階であり、サービス コントロールの使用は Azure Communication Services 早期導入者に制限されています。 早期アクセス プログラムに参加するには、[こちらのフォーム](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8MfnD7fOYZEompFbYDoD4JUMkdYT0xKUUJLR001ODdQRk1ITTdOMlRZNSQlQCN0PWcu)に記入してください。
>
> プレビュー API および SDK は、サービス レベル アグリーメントなしで提供されるので、運用ワークロードには推奨されません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Communication Services を使用すると、Microsoft Teams と対話するカスタム アプリケーションを作成できます。 Communication Services アプリケーションのエンド ユーザーは、音声、ビデオ、チャット、画面共有を通じて、Teams の参加者と対話できます。 次のビデオでは、この機能のデモが行われています。


<br>
<br>


> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWGTqQ]


Azure Communication Services では、エンド ユーザーの ID に応じて、次の 2 種類の Teams 相互運用性がサポートされています。

- **独自の ID を使用する。** ご自身でユーザー認証を制御します。カスタム アプリケーションのユーザーには、Teams 会議に参加するための Azure Active Directory ID や Teams ライセンスは必要ありません。 Teams は、アプリケーションを匿名の外部ユーザーとして扱います。
- **Microsoft 365 の Teams ID。** ご自分のアプリケーションが、エンド ユーザーの Microsoft 365 ID と Teams で構成されたリソースに代わって動作します。 これらの認証されたアプリケーションは、Microsoft 365 ユーザーの代わりにシームレスに電話をかけ、会議に参加できます。

アプリケーションでは、両方の認証方式を実装し、認証の選択をエンド ユーザーに任せることができます。

## <a name="bring-your-own-identity"></a>独自の ID を使用する
独自の ID の使用 (BYOI) は、Azure Communication Services と Teams 相互運用性を使用するための一般的なモデルです。 これは、あらゆる ID プロバイダーと認証方式をサポートしています。 アプリは Microsoft Teams 会議に参加でき、Teams はこれらのユーザーを匿名の外部アカウントとして扱います。 Teams に表示される Communication Services ユーザーの名前は、Communication Services Calling SDK を使用して構成できます。

この機能は、従業員 (Teams に精通) と外部ユーザー (カスタム アプリケーション エクスペリエンスを使用) を 1 つの会議エクスペリエンスにまとめる企業-消費者間アプリケーションに最適です。 アプリケーションの外部ユーザーと共有する必要がある会議の詳細を取得するには、Graph API または Microsoft Teams のカレンダーを使用します。

外部ユーザーは、Azure Communication Services SDK を使用して、基本的なオーディオ、ビデオ、画面共有、チャット機能を使用できます。 挙手、集合モード、ブレイクアウト ルームなどの機能は、Teams ユーザーのみが使用できます。 Communication Services ユーザーは、Teams 会議に出席中で、かつ会議がチャネルに対してスケジュールされていない場合にのみメッセージを送受信できます。

カスタム アプリケーションでは、Teams の会議を保護するためにユーザー認証などのセキュリティ対策について考慮する必要があります。 匿名ユーザーを会議に参加させることによって生じるセキュリティへの影響に留意し、[Teams のセキュリティ ガイド](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings)を利用して、匿名ユーザーに提供する機能を構成してください。

Teams 会議に参加するために必要なデータフローに関するその他の情報は、「[クライアントとサーバーのアーキテクチャ](client-and-server-architecture.md)」ページに記載されています。 「[グループ通話のヒーロー サンプル](../samples/calling-hero-sample.md)」では、Web アプリケーションから Teams 会議に参加するためのサンプル コードを入手できます。

## <a name="microsoft-365-teams-identity"></a>Microsoft 365 の Teams ID
Azure Communication Services Calling SDK を Microsoft 365 の Teams ID と共に使用すると、Teams 相互運用性で Teams に似たエクスペリエンスをサポートできます。 Microsoft 365 の Teams ID は、Azure Active Directory によって提供および認証されます。 アプリは、通常の Microsoft 365 ID を使用して電話をかけたり、受けたりできます。 ユーザーに関するすべての属性と詳細が Azure Active Directory ユーザーにバインドされます。

この ID モデルは、カスタムユ ーザー インターフェイスが必要な場合、プラットフォームで Teams クライアントが使用できない場合、または Teams クライアントが十分なレベルのカスタマイズをサポートしていない場合のユース ケースに最適です。 たとえば、アプリケーションは、エンド ユーザーの Teams でプロビジョニングされた PSTN 番号に代わって通話に応答するために使用でき、受付またはコール センターの業務プロセス用に最適化されたユーザー インターフェイスを設定できます。  

通話と画面共有の機能は、Communication Services Calling SDK を介して利用できます。 通話の管理は、Graph API、Teams クライアントまたは Teams 管理ポータルの構成を介して利用できます。 チャット機能は Graph API を介して利用できます。

Teams ユーザーは、クライアント アプリケーションで Azure Active Directory に対して MSAL ライブラリ経由で認証されます。 認証トークンは、Communication Services Identity SDK を使用して Microsoft 365 の Teams トークンと交換されます。 交換の要求は Azure Communication Services の資格情報によって署名されるため、バックエンド サービスにトークンの交換を実装することをお勧めします。 バックエンド サービスでは、追加の認証を要求することができます。

この機能の詳細については、[こちらのフォーム](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8MfnD7fOYZEompFbYDoD4JUMkdYT0xKUUJLR001ODdQRk1ITTdOMlRZNSQlQCN0PWcu)を入力して、早期アクセスのための TAP プログラムに参加してください。

## <a name="comparison"></a>比較

|条件|独自の ID を使用する| Microsoft 365 の Teams ID|
|---|---|---|
|適用できます| コンシューマー アプリケーションの企業-消費者間シナリオ | ビジネス アプリケーションでの企業間または企業-消費者間のシナリオ |
|ID プロバイダー|Any|Azure Active Directory|
|認証と認可|カスタム*| Azure Active Directory とカスタム*|
|通話が利用可能な手段 | Communication Services Calling SDK | Communication Services Calling SDK |
|チャットが利用可能な手段 | Communication Services Chat SDK | Graph API |
|PSTN のサポート| 発信音声通話、発信ダイレクト ルーティング、[詳細](./telephony-sms/telephony-concept.md) | Teams ID に割り当てられた着信通話、通話プランを使用した発信通話|

\* アクセス トークンを発行するサーバー ロジックは、要求の任意のカスタム認証および認可を実行できます。

## <a name="privacy"></a>プライバシー
Azure Communication Services と Microsoft Teams の間の相互運用性により、アプリケーションとユーザーは、Teams の呼び出し、会議、チャットに参加できます。 アプリケーションの開発者は、Teams の呼び出しまたは会議で記録または文字起こしが有効になったら、そのことをユーザーに通知する責任があります。

記録または文字起こしが開始され、この事実をアプリケーションのユーザー インターフェイスを通じてリアルタイムでユーザーに通知する必要があることは、Azure Communication Services API によって開発者に示されます。 この義務に準拠していないことが原因で発生したすべてのコストと損害について、開発者は Microsoft の補償する必要があります。

## <a name="pricing"></a>価格
Azure Communication Services の API と SDK のどれを使用しても、[Azure Communication Services の課金メーター](https://azure.microsoft.com/pricing/details/communication-services/)が増加します。 会議への参加や Teams で割り当てられた番号を使用した電話の開始など、Microsoft Teams との対話によってこれらのメーターが増加しますが、Teams 相互運用性機能自体に追加料金は発生しません。また、BYOI と Microsoft 365 の認証オプションに料金の違いはありません。

Azure アプリケーションのエンド ユーザーが Microsoft Teams のユーザーとの会議で 10 分を費やした場合、これら 2 人のユーザーは合わせて 20 分の通話時間を使用したことになります。 カスタム アプリケーション経由で Azure の API と SDK を使用して実行された 10 分間は、ご自分のリソースに課金されます。 ただし、ネイティブ Teams アプリケーションのエンド ユーザーが使用した 10 分間は、該当する Teams ライセンスの対象になり、Azure によって課金されません。

## <a name="teams-in-government-clouds-gcc"></a>Government Clouds (GCC) のチーム
現時点では、Azure Communication Services の相互運用性は、[Microsoft 365 government clouds (GCC)](/MicrosoftTeams/plan-for-government-gcc) を使用するチーム デプロイとは互換性がありません。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [BYOI 通話アプリを Teams 会議に参加させる](../quickstarts/voice-video-calling/get-started-teams-interop.md)
> [Microsoft 365 ユーザーを認証する](../quickstarts/manage-teams-identity.md)
