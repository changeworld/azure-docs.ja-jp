---
title: 新機能 リリース ノート - Azure Active Directory | Microsoft Docs
description: 最新のリリース ノート、既知の問題、バグの修正、非推奨の機能、予定されている変更点など、Azure Active Directory の新着情報について説明します。
services: active-directory
author: msmimart
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c58c99faa83d6f04ac37d03789202e111f1b06d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611162"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory の新着情報

>URL `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` をコピーして、お使いの ![RSS フィード リーダー アイコン](./media/whats-new/feed-icon-16x16.png) フィード リーダーに貼り付け、更新内容を確認するためにこのページに再度アクセスするタイミングに関する通知を受け取るようにしてください。

Azure AD は随時改善されています。 常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

- 最新のリリース
- 既知の問題
- バグの修正
- 非推奨の機能
- 変更の計画

このページは毎月更新されるため、定期的にアクセスしてご確認ください。 6 か月以上前の項目を探す場合は、「[新着情報のアーカイブ - Azure Active Directory](whats-new-archive.md)」をご覧ください。

---

## <a name="april-2020"></a>2020 年 4 月

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>統合されたセキュリティ情報登録エクスペリエンスの一般提供開始

**種類:** 新機能

**サービス カテゴリ:** 認証 (ログイン)

**製品の機能:** ID のセキュリティ & 保護

多要素認証 (MFA) とセルフサービス パスワード リセット (SSPR) 用の統合された登録エクスペリエンスの一般提供が開始されました。 この新しい登録エクスペリエンスによって、ユーザーは 1 つの順を追ったプロセスで MFA と SSPR に登録できるようになります。 ご自身の組織に向けて新しいエクスペリエンスを展開すると、ユーザーはより短時間で、より少ない作業で登録できるようになります。 [こちら](https://bit.ly/3etiRyQ)のブログ記事をご覧ください。

---

### <a name="continuous-access-evaluation"></a>継続的アクセス評価

**種類:** 新機能

**サービス カテゴリ:** 認証 (ログイン)

**製品の機能:** ID のセキュリティ & 保護

継続的アクセス評価は新しいセキュリティ機能です。これを使うと、Azure AD でイベント (ユーザー アカウントの削除など) が発生した場合に、Azure AD アクセス トークンを利用する証明書利用者に対してほぼリアルタイムでポリシーを適用できるようになります。 この機能は、最初に Teams と Outlook のクライアント向けにロールアウトされます。 詳細については、Microsoft の[ブログ](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)と[ドキュメント](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation)をご覧ください。

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>SMS サインイン:現場担当者による、電話番号を使用したパスワードなしの Azure AD 対応アプリケーションへのサインイン

**種類:** 新機能

**サービス カテゴリ:** 認証 (ログイン)

**製品の機能:** ユーザー認証

Office では、非従来型の組織と、主要なコミュニケーション方法として電子メールを使用しない大規模な組織の従業員に対応した、一連のモバイルファースト ビジネス アプリが発表されています。 これらのアプリは、雇用者からメール アドレスを提供されない場合がある現場の従業員、デスクレス ワーカー、フィールド エージェント、または小売従業員が、コンピューターまたは IT にアクセスできるようにすることを目的としています。 このプロジェクトにより、このような従業員が、電話番号を入力してコードを送り返すことによってビジネス アプリケーションにサインインできます。 詳細については、[管理者向けのドキュメント](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-sms-signin)と[エンド ユーザー向けのドキュメント](https://docs.microsoft.com/azure/active-directory/user-help/sms-sign-in-explainer)を参照してください。

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>内部ユーザーを B2B コラボレーションの使用に招待する

**種類:** 新機能

**サービス カテゴリ:** B2B

**製品の機能:**

B2B の招待機能は拡張中であり、今後、B2B コラボレーションの資格情報を使用するように既存の内部アカウントを招待できるようにする予定です。 これを実行するために、招待 API に対して、招待されたメール アドレスなどの通常のパラメーターに加えてユーザー オブジェクトが渡されます。 そのユーザーのオブジェクト ID、UPN、グループ メンバーシップ、アプリの割り当てなどは変更されませんが、今後は、招待前に使用していた内部資格情報ではなく、B2B を使用してホーム テナントの資格情報で認証を行うようになります。 詳細については、こちらの[ドキュメント](https://docs.microsoft.com/azure/active-directory/b2b/invite-internal-users)をご覧ください。

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>条件付きアクセスのレポート専用モードの一般提供開始

**種類:** 新機能

**サービス カテゴリ:** 条件付きアクセス

**製品の機能:** ID のセキュリティ & 保護

[Azure AD 条件付きアクセスのレポート専用モード](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only)を使用すると、アクセス制御を実施せずにポリシーの結果を評価できます。 組織全体でレポート専用ポリシーをテストし、その影響について理解してから有効にすることで、展開がより安全で簡単になります。 これまで数か月にわたって、私たちはレポート専用モードを広範に導入しており、既に 26M のユーザーがレポート専用ポリシーのスコープ内に存在しています。 この発表では、新しい Azure AD 条件付きアクセス ポリシーが、既定でレポート専用モードで作成されます。 これは、ポリシーが作成された時点からその影響を監視できることを意味します。 また、MS Graph API を使用しているユーザーは、[プログラムによってレポート専用ポリシーを管理する](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta)こともできます。 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>条件付きアクセスに関する分析情報とレポートのブックの一般提供開始

**種類:** 新機能

**サービス カテゴリ:** 条件付きアクセス

**製品の機能:** ID のセキュリティ & 保護

条件付きアクセスに関する[分析情報とレポートのブック](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting)によって、管理者が各自のテナントの Azure AD 条件付きアクセスの概要ビューを得られます。 個々のポリシーを選択する機能によって、管理者が各ポリシーの動作をよりよく理解し、変更をリアルタイムで監視できます。 このブックにより、Azure Monitor に格納されているデータがストリームされます。これは、[こちらの手順に従って](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)数分で設定可能です。 ダッシュボードは、見つけやすくするために、Azure AD 条件付きアクセス メニュー内の新しい分析情報とレポートのタブに移動されました。

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>条件付きアクセスのポリシー詳細ブレードのパブリック プレビュー開始

**種類:** 新機能

**サービス カテゴリ:** 条件付きアクセス

**製品の機能:** ID のセキュリティ & 保護

新しい[ポリシーの詳細ブレード](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access)には、条件付きアクセス ポリシーの評価中に満たされた割り当て、条件、および制御が表示されます。 このブレードにアクセスするには、サインインの詳細の **[条件付きアクセス]** タブまたは **[レポート専用]** タブにある行を選択します。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2020 年 4 月

**種類:** 新機能

**サービス カテゴリ:** エンタープライズ アプリケーション

**製品の機能:** サード パーティ統合

2020 年 4 月に、フェデレーションを使用した以下の 31 の新規アプリのサポートが、アプリ ギャラリーに追加されました。 

[SincroPool Apps](https://www.sincropool.com/)、[SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/)、[Float](https://docs.microsoft.com/azure/active-directory/saas-apps/float-tutorial)、[LMS365](https://lms.365.systems/)、[IWT Procurement Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/iwt-procurement-suite-tutorial)、[Lunni](https://lunni.fi/)、[EasySSO for Jira](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-jira-tutorial)、[Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft)、[Meraki Dashboard](https://docs.microsoft.com/azure/active-directory/saas-apps/meraki-dashboard-tutorial)、[Office 365 Mover](https://app.mover.io/login)、[Speaker Engage](https://speakerengage.com/login.php)、[Honestly](https://docs.microsoft.com/azure/active-directory/saas-apps/honestly-tutorial)、[Ally](https://docs.microsoft.com/azure/active-directory/saas-apps/ally-tutorial)、[DutyFlow](https://app.dutyflow.nl/)、[AlertMedia](https://docs.microsoft.com/azure/active-directory/saas-apps/alertmedia-tutorial)、[gr8 People](https://docs.microsoft.com/azure/active-directory/saas-apps/gr8-people-tutorial)、[Pendo](https://docs.microsoft.com/azure/active-directory/saas-apps/pendo-tutorial)、[HighGround](https://docs.microsoft.com/azure/active-directory/saas-apps/highground-tutorial)、[Harmony](https://docs.microsoft.com/azure/active-directory/saas-apps/harmony-tutorial)、[Timetabling Solutions](https://docs.microsoft.com/azure/active-directory/saas-apps/timetabling-solutions-tutorial)、[SynchroNet CLICK](https://docs.microsoft.com/azure/active-directory/saas-apps/synchronet-click-tutorial)、[empower](https://www.made-in-office.com/en/)、[Fortes Change Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fortes-change-cloud-tutorial)、[Litmus](https://docs.microsoft.com/azure/active-directory/saas-apps/litmus-tutorial)、[GroupTalk](https://recorder.grouptalk.com/)、[Frontify](https://docs.microsoft.com/azure/active-directory/saas-apps/frontify-tutorial)、[MongoDB Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/mongodb-cloud-tutorial)、[TickitLMS Learn](https://docs.microsoft.com/azure/active-directory/saas-apps/tickitlms-learn-tutorial)、[COCO](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/)、[Nitro Productivity Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/nitro-productivity-suite-tutorial) 、[Trend Micro Web Security(TMWS)](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>oAuth2PermissionGrant に対する Microsoft Graph デルタ クエリのサポートのパブリック プレビュー開始

**種類:** 新機能

**サービス カテゴリ:** MS Graph

**製品の機能:** 開発者エクスペリエンス

oAuth2PermissionGrant に対するデルタ クエリがパブリック プレビューで利用可能になりました。 ユーザーは、Microsoft Graph を継続的にポーリングすることなく、変更を追跡できるようになりました。 [詳細情報。](https://docs.microsoft.com/graph/api/oAuth2PermissionGrant-delta?view=graph-rest-beta&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>組織の連絡先に対する Microsoft Graph デルタ クエリのサポートの一般提供開始

**種類:** 新機能

**サービス カテゴリ:** MS Graph

**製品の機能:** 開発者エクスペリエンス

組織の連絡先に対するデルタ クエリが一般提供されました。 ユーザーは、Microsoft Graph を継続的にポーリングすることなく、実稼働アプリの変更を追跡できるようになりました。 パフォーマンスを大幅に改善するために、orgContact データに継続的にポーリングする既存のコードをデルタ クエリで置き換えてください。 [詳細情報。](https://docs.microsoft.com/graph/api/orgcontact-delta?view=graph-rest-1.0&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>アプリケーションに対する Microsoft Graph デルタ クエリのサポートの一般提供開始

**種類:** 新機能

**サービス カテゴリ:** MS Graph

**製品の機能:** 開発者エクスペリエンス

アプリケーションに対するデルタ クエリが一般提供されました。 ユーザーは、Microsoft Graph を継続的にポーリングすることなく、実稼働アプリの変更を追跡できるようになりました。 パフォーマンスを大幅に改善するために、アプリケーション データに継続的にポーリングする既存のコードをデルタ クエリで置き換えてください。 [詳細情報。](https://docs.microsoft.com/graph/api/application-delta?view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>管理単位に対する Microsoft Graph デルタ クエリのサポートのパブリック プレビュー開始

**種類:** 新機能

**サービス カテゴリ:** MS Graph

**製品の機能:** 管理単位に対する開発者エクスペリエンスのデルタ クエリがパブリック プレビューで利用可能になりました。 ユーザーは、Microsoft Graph を継続的にポーリングすることなく、変更を追跡できるようになりました。 [詳細情報。](https://docs.microsoft.com/graph/api/administrativeunit-delta?view=graph-rest-beta&tabs=http)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>新しい Microsoft Graph ベータ API で認証用電話番号などを管理する

**種類:** 新機能

**サービス カテゴリ:** MS Graph

**製品の機能:** 開発者エクスペリエンス

これらの API は、ユーザーの認証方法を管理するための主要なツールです。 MFA とセルフサービス パスワード リセット (SSPR) に使用される認証子を、プログラムで事前登録および管理できるようになりました。 これは、Azure MFA、SSPR、および Microsoft Graph の領域で最も要求の多い機能の 1 つでした。 このウェーブでリリースされた新しい API により、次のことが可能になります。

- ユーザーの認証用電話を読み取り、追加、更新、削除する
- ユーザーのパスワードをリセットする
- SMS サインインを有効または無効にする

詳細については、「[Azure AD 認証方法の API の概要](https://docs.microsoft.com/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)」をご覧ください。

---

### <a name="administrative-units-public-preview"></a>管理単位のパブリック プレビュー

**種類:** 新機能

**サービス カテゴリ:** RBAC

**製品の機能:** アクセス制御

管理単位を使用すると、定義した組織の部門、リージョン、またはその他のセグメントに制限されている管理者アクセス許可を付与できます。 管理単位を使用して、各地域の管理者に権限を委任したり、ポリシーを細かく設定したりできます。 たとえば、ユーザー アカウント管理者は、管理単位内のユーザーに対してのみ、プロファイル情報の更新、パスワードのリセット、およびライセンスの割り当てを実行できます。

全体管理者は、管理単位を使用して以下を行うことができます。

- リソースの分散管理のために管理単位を作成する
- 管理単位内の Azure AD ユーザーのみに対して管理アクセス許可を含むロールを割り当てる
- 必要に応じてユーザーとグループで管理単位を設定する

詳細については、「[Azure Active Directory での管理単位の管理 (プレビュー)](https://aka.ms/AdminUnitsDocs)」をご覧ください。

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>プリンター管理者とプリンター技術者の組み込みロール

**種類:** 新機能

**サービス カテゴリ:** RBAC

**製品の機能:** アクセス制御

**プリンター管理者**:このロールを持つユーザーは、プリンターを登録して、ユニバーサル印刷コネクタの設定など、Microsoft ユニバーサル印刷ソリューションのすべてのプリンター構成のすべての側面を管理できます。 すべての委任された印刷アクセス許可要求に同意することができます。 プリンター管理者は、印刷レポートにアクセスすることもできます。 

**プリンター技術者**:このロールを持つユーザーは、プリンターを登録して、Microsoft ユニバーサル印刷ソリューションでプリンターの状態を管理できます。 すべてのコネクタ情報を読み取ることもできます。 プリンター技術者が行うことができない主要なタスクは、プリンターに対するユーザー アクセス許可の設定と、プリンターの共有です。 [詳細情報。](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>ハイブリッド ID 管理者の組み込みロール

**種類:** 新機能

**サービス カテゴリ:** RBAC

**製品の機能:** アクセス制御

このロールのユーザーは、Azure AD でのハイブリッド ID の有効化に関連するサービスと設定を有効化、構成、管理できます。 このロールでは、サポートされている 3 つの認証方法 &#8212; パスワード ハッシュ同期 (PHS)、パススルー認証 (PTA)、フェデレーション (AD FS またはサード パーティのフェデレーション プロバイダー) &#8212; のいずれかに Azure AD を構成し、関連するオンプレミス インフラストラクチャを展開してそれらを有効にすることが許可されます。 オンプレミス インフラストラクチャには、プロビジョニング エージェントと PTA エージェントが含まれます。 このロールでは、Windows 10 以外のデバイスまたは Windows Server 2016 以外のコンピューターでシームレス シングル サインオン (S-SSO) を有効にして、シームレスな認証を有効にすることが許可されます。 さらに、このロールでは、監視とトラブルシューティングのために、サインイン ログを表示し、正常性と分析にアクセスすることが許可されます。 [詳細情報。](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>ネットワーク管理者の組み込みロール

**種類:** 新機能

**サービス カテゴリ:** RBAC

**製品の機能:** アクセス制御

このロールを持つユーザーは、ユーザーの場所からのネットワーク テレメトリに基づいて、Microsoft によるネットワーク境界アーキテクチャに関する推奨事項を確認できます。 Office 365 のネットワーク パフォーマンスは、通常はユーザーの場所に固有である、企業の顧客の慎重なネットワーク境界アーキテクチャに依存します。 このロールを使用では、検出されたユーザーの場所と、それらの場所に対するネットワーク パラメーターの構成を編集して、テレメトリの測定と設計に関する推奨事項の改善できます。 [詳細情報。](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Azure AD 管理ポータル エクスペリエンスでの一括アクティビティとダウンロード

**種類:** 新機能

**サービス カテゴリ:** [ユーザー管理]

**製品の機能:** ディレクトリ

Azure AD 管理ポータルのエクスペリエンスにおいて、CSV ファイルをアップロードすることで Azure AD のユーザーとグループに対して一括アクティビティを実行できるようになりました。 ユーザーの作成、ユーザーの削除、ゲスト ユーザーの招待を行うことができます。 また、グループのメンバーを追加および削除することもできます。

また、Azure AD 管理ポータルのエクスペリエンスから Azure AD リソースの一覧をダウンロードすることもできます。 ディレクトリ内のユーザーの一覧、ディレクトリ内のグループの一覧、および特定のグループのメンバーをダウンロードできます。

詳細については、以下を参照してください。

- [ユーザーの作成](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-add)または[ゲスト ユーザーの招待](https://docs.microsoft.com/azure/active-directory/b2b/tutorial-bulk-invite)に関する記事
- [ユーザーの削除](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-delete)または[削除済みユーザーの復元](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-restore)に関する記事
- [ユーザーの一覧のダウンロード](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-download)または[グループの一覧のダウンロード](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)に関する記事
- グループに対する[メンバーの追加 (インポート)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members)、[メンバーの削除](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members)、または[メンバーの一覧のダウンロード](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)に関する記事

---

### <a name="my-staff-delegated-user-management"></a>マイ スタッフによって委任されたユーザー管理

**種類:** 新機能

**サービス カテゴリ:** [ユーザー管理]

**製品の機能:**

ストア マネージャーなどの現場のマネージャーが、マイ スタッフを使用して、自分のスタッフ メンバーが Azure AD アカウントにアクセスできるようにすることができます。 組織では、パスワードのリセットや電話番号の変更などの一般的なタスクを、中央のヘルプデスクに頼る代わりに現場のマネージャーに委任することができます。 マイ スタッフを使用すると、自分のアカウントにアクセスできないユーザーが数回のクリックでアクセスを回復することができ、ヘルプデスクや IT スタッフは必要ありません。 詳細については、「[マイ スタッフを使用してユーザーを管理する (プレビュー)](https://aka.ms/MyStaffAdminDocs)」および「[マイ スタッフでユーザーの管理を委任する (プレビュー)](https://aka.ms/MyStaffUserDocs)」をご覧ください。

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>アクセス レビューにおけるアップグレードされたエンド ユーザー エクスペリエンス

**種類:** 変更された機能

**サービス カテゴリ:** アクセス レビュー

**製品の機能:** Identity Governance

マイ アプリ ポータルでの Azure AD アクセス レビューにおけるレビュー担当者エクスペリエンスが更新されました。 4 月の終わりに、Azure AD アクセス レビューのレビュー担当者エクスペリエンスにログインしたレビュー担当者に、マイ アクセスでの更新されたエクスペリエンスを試すことができるバナーが表示されます。 更新されたアクセス レビュー エクスペリエンスでは、現在のエクスペリエンスと同じ機能が提供されますが、ユーザーの生産性を向上させる新機能に加えてユーザー インターフェイスが改善されることにご注意ください。 [更新されたエクスペリエンスの詳細については、こちらをご覧ください](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review)。 このパブリック プレビューは、2020 年 7 月の終わりまで続きます。 7 月の終わりに、このプレビュー エクスペリエンスをオプトインしていないレビュー担当者が、アクセス レビューを実行するためのマイ アクセスに自動的にリダイレクトされます。 レビュー担当者が今すぐマイ アクセスのプレビュー エクスペリエンスに完全に切り替えられるようにしたい場合は、[こちらからリクエストを作成してください](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u)。

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Workday 受信ユーザー プロビジョニングおよび書き戻しアプリでの最新バージョンの Workday Web Services API サポートの開始

**種類:** 変更された機能

**サービス カテゴリ:** アプリ プロビジョニング

**製品の機能:** 

お客様からのフィードバックに基づいて、エンタープライズ アプリ ギャラリーの Workday 受信ユーザー プロビジョニングおよび書き戻しアプリが更新され、最新バージョンの Workday Web Services (WWS) API がサポートされるようになりました。 この変更により、お客様は、接続文字列内で使用したい WWS API バージョンを指定できるようになります。 これにより、お客様は Workday のリリースで使用可能なさらなる HR 属性を取得できるようになります。 Workday Writeback アプリでは、Maintain_Contact_Info の制限を克服するために、推奨される Change_Work_Contact_Info Workday Web サービスが使用されるようになりました。

接続文字列内でバージョンが指定されなかった場合、既定では、Workday 受信プロビジョニング アプリでは引き続き WWS v21.1 が使用されます。受信ユーザー プロビジョニングを最新の Workday API に切り替えるには、[こちらのチュートリアル](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)の記述に従って接続文字列を更新し、また、[Workday 属性リファレンス ガイド](https://docs.microsoft.com/azure/active-directory/app-provisioning/workday-attribute-reference#xpath-values-for-workday-web-services-wws-api-v30)の記述に従って Workday 属性で使用される XPATH を更新する必要があります。 

書き戻しに新しい API を使用する場合、Workday Writeback プロビジョニング アプリを変更する必要はありません。 Workday 側では、チュートリアルのセクション「[ビジネス プロセス セキュリティ ポリシーのアクセス許可の構成](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#configuring-business-process-security-policy-permissions)」に記載されているように、Workday 統合システム ユーザー (ISU) アカウントに Change_Work_Contact ビジネス プロセスを呼び出すためのアクセス許可が付与されていることを確認してください。 

新しい API バージョンのサポートを反映するために、[チュートリアル ガイド](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)が更新されています。

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>既定のアクセス ロールを持つユーザーのプロビジョニング対象範囲への追加

**種類:** 変更された機能

**サービス カテゴリ:** アプリ プロビジョニング

**製品の機能:** ID ライフサイクル管理

従来、既定のアクセス ロールのユーザーはプロビジョニングの対象外でした。 このロールのユーザーはプロビジョニングの対象にする必要があるというフィードバックが寄せられています。 2020 年 4 月 16 日以降、すべての新しいプロビジョニング構成で既定のアクセス ロールを持つユーザーのプロビジョニングが許可されます。 既存のプロビジョニング構成の動作は、このロールを持つユーザーのプロビジョニングをサポートするために段階的に変更されます。 [詳細情報。](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)

---

### <a name="updated-provisioning-ui"></a>プロビジョニング UI の更新

**種類:** 変更された機能

**サービス カテゴリ:** アプリ プロビジョニング

**製品の機能:** ID ライフサイクル管理

より焦点を絞った管理ビューを作成するために、プロビジョニング エクスペリエンスが更新されました。 既に構成済みのエンタープライズ アプリケーションのプロビジョニング ブレードに移動したときに、簡単にプロビジョニングの進行状況を監視し、プロビジョニングの開始、停止、再開などの操作を管理できるようになります。 [詳細情報。](https://docs.microsoft.com/azure/active-directory/app-provisioning/configure-automatic-user-provisioning-portal)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>動的グループ ルールの検証のパブリック プレビュー開始

**種類:** 変更された機能

**サービス カテゴリ:** グループ管理

**製品の機能:** コラボレーション

Azure Active Directory (Azure AD) で、動的グループ ルールを検証する手段が提供されるようになりました。 **[ルールの検証]** タブで、サンプル グループ メンバーに対して動的ルールを検証し、ルールが想定どおりに機能していることを確認できます。 動的グループ ルールの作成時または更新時に、管理者は、ユーザーまたはデバイスがグループのメンバーであるかどうかを確認できます。 これは、ユーザーまたはデバイスがルールの条件を満たしているかどうかを評価し、メンバーシップが想定されていない場合のトラブルシューティングを支援するのに役立ちます。

詳細については、[動的グループ メンバーシップ ルールの検証 (プレビュー)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-rule-validation) に関する記事をご覧ください。

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>ID セキュリティ スコア - セキュリティの既定値群と MFA の改善アクションの更新

**種類:** 変更された機能

**サービス カテゴリ:** 該当なし

**製品の機能:** ID のセキュリティ & 保護

**Azure AD 改善アクションのセキュリティの既定値群のサポート:** Microsoft セキュリティ スコアでは、[Azure AD のセキュリティの既定値群](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)をサポートするように改善アクションが更新されます。これにより、一般的な攻撃に対する事前構成済みのセキュリティ設定を使用して組織を保護しやすくなります。 これは、次の改善アクションに影響します。

- すべてのユーザーがセキュリティで保護されたアクセスのために多要素認証を完了できるようにする
- 管理者ロールに対して MFA を必須にする
- レガシ認証をブロックするポリシーを有効にする
 
**MFA の改善アクションの更新:** ビジネスに適したポリシーを適用しつつ、最高のセキュリティを保証するという企業のニーズを反映するために、Microsoft セキュリティ スコアでは多要素認証を中心とした 3 つの改善アクションが削除され、新たに 2 つが追加されました。

削除された改善アクション:

- すべてのユーザーを多要素認証に登録する
- すべてのユーザーに対して MFA を必須にする
- Azure AD 特権ロールに対して MFA を必須にする

追加された改善アクション:

- すべてのユーザーがセキュリティで保護されたアクセスのために多要素認証を完了できるようにする
- 管理者ロールに対して MFA を必須にする

これらの新しい改善アクションでは、ディレクトリ全体で多要素認証 (MFA) のためにユーザーまたは管理者を登録し、組織のニーズに合った適切な一連のポリシーを確立する必要があります。 主な目的は、すべてのユーザーと管理者が多要素またはリスクベースの本人確認プロンプトで認証できるようにしながら、柔軟性を高めることです。 それには、スコープに基づく決定を適用する複数のポリシーの使用や、ユーザーに MFA を要求するタイミングが Microsoft によって決定されるようにするためのセキュリティの既定値群の設定 (3 月 16 日以降) などの形態があります。 [Microsoft セキュリティ スコアの新機能をご覧ください](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new)。

---

## <a name="march-2020"></a>2020 年 3 月

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>2021 年 3 月の B2B 更新プログラムでのアンマネージド Azure Active Directory アカウント

**種類:** 変更の計画  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
**2021 年 3 月 31 日以降**、Microsoft では、B2B コラボレーション シナリオ用にアンマネージド Azure Active Directory (Azure AD) アカウントおよびテナントを作成することによって招待の引き換えをサポートしなくなります。 これに備えて、[電子メール ワンタイム パスコード認証](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)を選択することをお勧めします。

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>既定のアクセス ロールのユーザーは、プロビジョニングの対象ユーザーとなります

**種類:** 変更の計画  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
従来、既定のアクセス ロールのユーザーはプロビジョニングの対象外でした。 このロールのユーザーはプロビジョニングの対象にする必要があるというフィードバックが寄せられています。 すべての新しいプロビジョニング構成で既定のアクセス ロールのユーザーがプロビジョニングできるように、変更の展開に取り組んでいます。 段階的に、このロールを使用したユーザーのプロビジョニングをサポートするために、既存のプロビジョニング構成の動作を変更していきます。 お客様による対応は必要ありません。 この変更の準備が整ったら、[ドキュメント](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)に更新プログラムを投稿します。

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD B2B コラボレーションは、21Vianet (Azure China 21Vianet) テナントで運用される Microsoft Azure で利用できるようになります

**種類:** 変更の計画  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
Azure AD B2B コラボレーション機能は、21Vianet (Azure China 21Vianet) テナントが運用している Microsoft Azure で利用できるようになります。これにより、Azure China 21Vianet テナントのユーザーは、他の Azure China 21Vianet テナントのユーザーとシームレスに共同作業を行えるようになります。 [詳細については、Azure AD B2B コラボレーションを参照してください](https://docs.microsoft.com/azure/active-directory/b2b/)。

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD B2B Collaboration の招待メールの再設計

**種類:** 変更の計画  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
ユーザーをディレクトリに招待するために Azure AD B2B コラボレーションの招待サービスによって送信される[メール](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements)は、招待情報とユーザーの次の手順がわかりやすくなるよう再設計されます。

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>HomeRealmDiscovery ポリシーの変更は、監査ログに表示されるようになります

**種類:** 固定  
**サービス カテゴリ:** Audit  
**製品の機能:** 監視とレポート
 
[HomeRealmDiscovery ポリシー](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)の変更が監査ログに含まれていないというバグを修正しました。 これで、ポリシーがいつどのように、だれによって変更されたかを確認できるようになります。 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2020 年 3 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2020 年 3 月に、フェデレーションがサポートされる次の 51 の新しいアプリがアプリ ギャラリーに追加されました。 

[Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)、[Zoho One China](https://docs.microsoft.com/azure/active-directory/saas-apps/zoho-one-china-tutorial)、[PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/)、[Profit.co SAML App](https://docs.microsoft.com/azure/active-directory/saas-apps/profitco-saml-app-tutorial)、[iPoint Service Provider](https://docs.microsoft.com/azure/active-directory/saas-apps/ipoint-service-provider-tutorial)、[contexxt.ai SPHERE](https://contexxt-sphere.com/login)、[Wisdom By Invictus](https://docs.microsoft.com/azure/active-directory/saas-apps/wisdom-by-invictus-tutorial)、[Flare Digital Signage](https://spark-dev.pixelnebula.com/login)、[Logz.io - Cloud Observability for Engineers](https://docs.microsoft.com/azure/active-directory/saas-apps/logzio-cloud-observability-for-engineers-tutorial)、[SpectrumU](https://docs.microsoft.com/azure/active-directory/saas-apps/spectrumu-tutorial)、[BizzContact](https://bizzcontact.app/)、[Elqano SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/elqano-sso-tutorial)、[MarketSignShare](http://www.signshare.com/)、[CrossKnowledge Learning Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/crossknowledge-learning-suite-tutorial)、[Netvision Compas](https://docs.microsoft.com/azure/active-directory/saas-apps/netvision-compas-tutorial)、[FCM HUB](https://docs.microsoft.com/azure/active-directory/saas-apps/fcm-hub-tutorial)、[RIB A/S Byggeweb Mobile](https://apps.apple.com/us/app/docia/id529058757)、[GoLinks](https://docs.microsoft.com/azure/active-directory/saas-apps/golinks-tutorial)、[Datadog](https://docs.microsoft.com/azure/active-directory/saas-apps/datadog-tutorial)、[Zscaler B2B User Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-b2b-user-portal-tutorial)、[LIFT](https://docs.microsoft.com/azure/active-directory/saas-apps/lift-tutorial)、[Planview Enterprise One](https://docs.microsoft.com/azure/active-directory/saas-apps/planview-enterprise-one-tutorial)、[WatchTeams](https://www.devfinition.com/)、[Aster](https://demo.asterapp.io/login)、[Skills Workflow](https://docs.microsoft.com/azure/active-directory/saas-apps/skills-workflow-tutorial)、[Node Insight](https://admin.nodeinsight.com/AADLogin.aspx)、[IP Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/ip-platform-tutorial)、[InVision](https://docs.microsoft.com/azure/active-directory/saas-apps/invision-tutorial)、[Pipedrive](https://docs.microsoft.com/azure/active-directory/saas-apps/pipedrive-tutorial)、[Showcase Workshop](https://app.showcaseworkshop.com/)、[Greenlight Integration Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-integration-platform-tutorial)、[Greenlight Compliant Access Management](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-compliant-access-management-tutorial)、[Grok Learning](https://docs.microsoft.com/azure/active-directory/saas-apps/grok-learning-tutorial)、[Miradore Online](https://login.online.miradore.com/)、[Khoros Care](https://docs.microsoft.com/azure/active-directory/saas-apps/khoros-care-tutorial)、[AskYourTeam](https://docs.microsoft.com/azure/active-directory/saas-apps/askyourteam-tutorial)、[TruNarrative](https://docs.microsoft.com/azure/active-directory/saas-apps/trunarrative-tutorial)、[Smartwaiver](https://www.smartwaiver.com/m/user/sw_login.php?wms_login)、[Bizagi Studio for Digital Process Automation](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial)、[insuiteX](https://www.insuite.jp/)、[sybo](https://www.systexsoftware.com.tw/)、[Britive](https://docs.microsoft.com/azure/active-directory/saas-apps/britive-tutorial)、[WhosOffice](https://docs.microsoft.com/azure/active-directory/saas-apps/whosoffice-tutorial)、[E-days](https://docs.microsoft.com/azure/active-directory/saas-apps/e-days-tutorial)、[Kollective SDN](https://portal.kollective.app/login)、[Witivio](https://app.witivio.com/)、[Playvox](https://my.playvox.com/login)、[Korn Ferry 360](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-360-tutorial)、[Campus Café](https://docs.microsoft.com/azure/active-directory/saas-apps/campus-cafe-tutorial)、[Catchpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/catchpoint-tutorial)、[Code42](https://docs.microsoft.com/azure/active-directory/saas-apps/code42-tutorial)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Azure AD B2B Collaboration が Azure Government テナントで利用可能になりました

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
Azure AD B2B コラボレーション機能が Azure Government テナント間で利用可能になりました。  お使いのテナントでこれらの機能を使用できるかどうかを確認するには、「[B2B コラボレーションが Azure US Government テナントで利用可能かどうかを確認するにはどうすればよいですか？](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)」の手順に従ってください。

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Azure ログの Azure Monitor 統合が Azure Government で利用できるようになりました

**種類:** 新機能  
**サービス カテゴリ:** レポーティング  
**製品の機能:** 監視とレポート
 
Azure AD ログとの Azure Monitor 統合が Azure Government で利用できるようになりました。 Azure AD ログ (監査ログとサインイン ログ) をストレージ アカウント、イベント ハブ、Log Analytics にルーティングすることができます。 Azure AD のシナリオについては、[詳細なドキュメント](https://aka.ms/aadlogsinamd)と[レポートと監視のデプロイ計画](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting)に関するページを確認してください。

---

### <a name="identity-protection-refresh-in-azure-government"></a>Azure Government での Identity Protection の更新

**種類:** 新機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護

[Microsoft Azure Government ポータル](https://portal.azure.us/)で、更新された [Azure AD Identity Protection](https://aka.ms/IdentityProtectionDocs)  エクスペリエンスをロールアウトしました。 詳しくは、[お知らせブログの投稿](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667)をご覧ください。

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>ディザスター リカバリー:プロビジョニング構成のダウンロードと保存

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
Azure AD プロビジョニング サービスには、豊富な構成機能が用意されています。 お客様は、後で参照したり、既知の適切なバージョンにロールバックしたりするために、構成を保存できる必要があります。 プロビジョニング構成を JSON ファイルとしてダウンロードし、必要に応じてアップロードする機能が追加されました。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration)。

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (セルフサービス パスワード リセット) で、21Vianet (Azure China 21Vianet) によって運営される Microsoft Azure の管理者に 2 つのゲートが必要になりました 

**種類:** 変更された機能  
**サービス カテゴリ:** セルフサービス パスワード リセット  
**製品の機能:** ID のセキュリティ & 保護
 
以前は、21Vianet (Azure China 21Vianet) によって運営されている Microsoft Azure で、セルフサービス パスワード リセット (SSPR) を使用して自分のパスワードをリセットする管理者は、ID を証明するために必要な "ゲート" (チャレンジ) は 1 つだけでした。 パブリックおよびその他の国内のクラウドでは、通常、管理者は SSPR を使用するときに 2 つのゲートを使用して ID を証明する必要があります。 しかし、Azure China 21Vianet では SMS や電話をサポートしていなかったため、管理者による 1 ゲートのパスワード リセットを許可していました。

Azure China 21Vianet とパブリック クラウドの間に SSPR 機能パリティを作成中です。 今後、管理者は SSPR を使用するときに 2 つのゲートを使用する必要があります。 SMS、電話、認証アプリの通知とコードがサポートされるようになります。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences)。

---

### <a name="password-length-is-limited-to-256-characters"></a>パスワードの長さは 256 文字に制限されています

**種類:** 変更された機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
Azure AD サービスの信頼性を確保するために、ユーザーのパスワードの長さが 256 文字までに制限されるようになりました。 これよりも長いパスワードを持つユーザーは、管理者に連絡するか、セルフサービス パスワード リセット機能を使用して、以降のログインでパスワードを変更するように求められます。

この変更は、2020 年 3 月 13 日、午前 10 時 PST (UTC - 18 時) に有効になりました。エラーは AADSTS 50052、InvalidPasswordExceedsMaxLength です。 詳細については、[重大な変更に関する通知](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters)を参照してください。

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Azure AD サインイン ログが Azure portal を通じてすべての無料テナントで利用できるようになりました

**種類:** 変更された機能  
**サービス カテゴリ:** レポーティング  
**製品の機能:** 監視とレポート
 
今後、無料のテナントを所有しているお客様は、最大 7 日間、[Azure portal から Azure AD サインイン ログ](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins)にアクセスできます。 以前は、サインイン ログを利用できるのは、Azure Active Directory Premium ライセンスをお持ちのお客様でした。 この変更により、すべてのテナントがポータル経由でこれらのログにアクセスできるようになります。

> [!NOTE]
> また、Microsoft Graph API と Azure Monitor を通じてサインイン ログにアクセスするには、Premium ライセンス (Azure Active Directory Premium P1 または P2) が必要です。

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Azure portal のグループの全般設定からのディレクトリ全体のグループ オプションの廃止

**種類:** 非推奨  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション

お客様がニーズに最も合ったディレクトリ全体のグループを作成するためのより柔軟な方法を提供するために、Azure portal の **[グループ]**  >  **[全般]** 設定の **[ディレクトリ全体のグループ]** オプションを[動的グループのドキュメント](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)へのリンクに置き換えました。 管理者がゲスト ユーザーを含めたり除外したりするすべてのユーザー グループを作成できるように、ドキュメントを改良しました。

---

## <a name="february-2020"></a>2020 年 2 月

### <a name="upcoming-changes-to-custom-controls"></a>カスタム コントロールへの今後の変更

**種類:** 変更の計画  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティ & 保護
 
現在のカスタム コントロールのプレビューを、パートナーが提供する認証機能で Azure Active Directory 管理者およびエンド ユーザー エクスペリエンスとシームレスに連携させることができる方法に置き換える予定です。 現在、パートナー MFA ソリューションは次の制約に直面しています。パスワードが入力された後にのみ機能すること、他の主要なシナリオではステップアップ認証用の MFA としては機能しないこと、およびエンド ユーザーまたは管理者の資格情報管理機能と統合されていないことです。 新しい実装では、パートナーが提供する認証要素を、登録、使用状況、MFA 要求、ステップアップ認証、レポート、ログ記録などの主要なシナリオに対して組み込みの要素と共に使用できます。 

カスタム コントロールは、一般提供が開始されるまで、新しい設計と共にプレビューで引き続きサポートされます。 その時点で、お客様に新しい設計に移行する猶予を与えます。 現在のアプローチには制約があるため、新しい設計を利用できるようになるまで新しいプロバイダーをオンボードしません。 Microsoft はお客様やプロバイダーと密接に協力しており、近づいてきたらスケジュールをお知らせします。 [詳細については、こちらを参照してください](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#)。

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>ID セキュリティ スコア - MFA の改善アクションの更新

**種類:** 変更の計画  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティ & 保護
 
ビジネスに適したポリシーを適用しつつ、セキュリティを強化するという企業のニーズを反映するために、Microsoft のセキュリティ スコアは、多要素認証 (MFA) を中心とした 3 つの改善アクションを削除し、新たに 2 つ追加します。

次の改善アクションが削除されます。

- すべてのユーザーを MFA に登録する
- すべてのユーザーに対して MFA を必須にする
- Azure AD 特権ロールに対して MFA を必須にする

次の改善アクションが追加されます。

- すべてのユーザーが安全にアクセスできるよう MFA を完了できることを確認する
- 管理者ロールに対して MFA を必須にする

これらの新しい改善アクションでは、ユーザーまたは管理者をディレクトリ全体での MFA のために登録し、組織のニーズに合った適切な一連のポリシーを確立する必要があります。 主な目的は、すべてのユーザーと管理者が多要素またはリスクベースの本人確認プロンプトで認証できるようにしながら、柔軟性を高めることです。 これには、Microsoft がユーザーに MFA を要求するタイミングを決定するセキュリティの既定値群の設定や、スコープに基づく決定を適用する複数のポリシーの使用などの形態があります。 これらの改善アクションの更新の一環として、ベースライン保護ポリシーはスコアの計算に含まれなくなります。 [Microsoft セキュリティ スコアに関する最新情報をご覧ください](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide)。

---

### <a name="azure-ad-domain-services-sku-selection"></a>Azure AD Domain Services の SKU の選択

**種類:** 新機能  
**サービス カテゴリ:** Azure AD Domain Services  
**製品の機能:** Azure AD Domain Services
 
Microsoft では、Azure AD Domain Services をご利用のお客さまより、インスタンスのパフォーマンス レベルをより柔軟に選択したいというフィードバックをいただいておりました。 2020 年 2 月 1 日より、動的モデル (オブジェクト数に基づいて Azure AD のパフォーマンスと価格を決定するモデル) から自己選択モデルへの切り替えを行いました。 これにより、お客様は環境に合ったパフォーマンス レベルを選択できるようになりました。 この変更により、リソース フォレストなどの新しいシナリオや、毎日のバックアップなどの Premium 機能をも有効にすることができます。 すべての SKU でオブジェクト数は無制限になりましたが、各レベルでのオブジェクト数の提案は引き続き提供されます。

**お客様による早急な対応は必要はありません。** 既存のお客様については、2020 年 2 月 1 日時点で使用していた動的レベルによって、新しい既定のレベルが決まります。 この変更による、価格やパフォーマンスへの影響はありません。 今後、Azure AD DS をご利用のお客様は、ディレクトリのサイズとワークロードの特性の変化に応じて、パフォーマンス要件を評価する必要があります。 サービス レベル間の切り替え操作は引き続きダウンタイムなしで行われ、お客様がディレクトリの拡張に応じて新しいレベルに自動的に移動されることはなくなります。 さらに、価格の引き上げは行われず、新しい価格は現在の課金モデルに従います。 詳細については、[Azure AD DS SKU のドキュメント](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) および [Azure AD Domain Services の価格に関するページ](https://azure.microsoft.com/pricing/details/active-directory-ds/)を参照してください。

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2020 年 2 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2020 年 2 月に、フェデレーションがサポートされる次の 31 の新しいアプリがアプリ ギャラリーに追加されました。 

[IamIP Patent Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial)、[Experience Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial)、[NS1 SSO For Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial)、[Barracuda Email Security Service](https://ess.barracudanetworks.com/sso/azure)、[ABa Reporting](https://myaba.co.uk/client-access/signin/auth/msad)、[In Case of Crisis - Online Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial)、[BIC Cloud Design](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial)、[Beekeeper Azure AD Data Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial)、[Korn Ferry Assessments](https://www.kornferry.com/solutions/kf-digital/kf-assess)、[Verkada Command](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial)、[Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial)、[Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial)、[EAB Navigate](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial)、[New Relic (Limited Release)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial)、[Thulium](https://admin.thulium.com/login/instance)、[Ticket Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial)、[Template Chooser for Teams](https://links.officeatwork.com/templatechooser-download-teams)、[Beesy](https://www.beesy.me/index.php/site/login)、[Health Support System](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial)、[MURAL](https://app.mural.co/signup)、[Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial)、[LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview)、[Wakelet](https://wakelet.com/login)、[Firmex VDR](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial)、[ThingLink for Teachers and Schools](https://www.thinglink.com/)、[Coda](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial)、[NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product)、[WEDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial)、[InvitePeople](https://invitepeople.com/login)、[Reprints Desk - Article Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial)、[TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクター - 2020 年 2 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)に関するページをご覧ください。

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>ハイブリッド環境での FIDO2 セキュリティ キーの Azure AD サポート

**種類:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
Microsoft は、ハイブリッド環境での FIDO2 セキュリティ キーの Azure AD サポートのパブリック プレビューを発表しました。 ユーザーは、FIDO2 セキュリティ キーを使用して Hybrid Azure AD 参加済み Windows 10 デバイスにサインインし、オンプレミスとクラウドのリソースにシームレスにサインオンできるようになりました。 Azure AD 参加済みデバイスの FIDO2 サポートのパブリック プレビューを最初にローンチして以来、ハイブリッド環境のサポートは、パスワードレス認証をご利用のお客様からのご要望に最も多い機能でした。 生体認証や公開キーおよび秘密キーの暗号化などの高度なテクノロジーを使用したパスワードレス認証は、セキュリティを担保しつつ、利便性と使いやすさを提供します。 このパブリック プレビューにより、FIDO2 セキュリティ キーのような最新の認証を使用して、従来の Active Directory リソースにアクセスできるようになりました。 詳細については、「[オンプレミスのリソースへの SSO](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises)」を参照してください。 

使用を開始するには、「[テナントの FIDO2 セキュリティ キーを有効にする](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key)」で詳しい手順を参照してください。 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>新しいマイ アカウントのエクスペリエンスの一般公開

**種類:** 変更された機能  
**サービス カテゴリ:** マイ プロファイル/アカウント  
**製品の機能:** エンド ユーザー エクスペリエンス
 
すべてのエンド ユーザー アカウント管理のニーズに対応する機能を 1 か所に統合するマイ アカウントが一般公開されました。 エンド ユーザーは、URL から、または新しいマイ アプリ エクスペリエンスのヘッダーから、この新しいサイトにアクセスできます。 新しいエクスペリエンスで提供されるすべてのセルフサービス機能の詳細については、「[マイ アカウント ポータルの概要](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)」を参照してください。

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>マイ アカウントのサイトの URL が myaccount.microsoft.com に更新

**種類:** 変更された機能  
**サービス カテゴリ:** マイ プロファイル/アカウント  
**製品の機能:** エンド ユーザー エクスペリエンス
 
新しいマイ アカウントのエンド ユーザーエクスペリエンスの URL は、来月中に `https://myaccount.microsoft.com` に更新されます。 エクスペリエンスと、エンド ユーザーに提供されるアカウントのすべてのセルフサービス機能の詳細については「[マイ アカウント ポータルのヘルプ](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)」を参照してください。

---
 
## <a name="january-2020"></a>2020 年 1 月
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>新しいマイ アプリ ポータルの一般提供が開始されました

**種類:** 変更の計画  
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** エンド ユーザー エクスペリエンス
 
一般提供が開始された新しいマイ アプリ ポータルに組織をアップグレードしてください。 この新しいポータルとコレクションの詳細については、「[マイ アプリ ポータルでコレクションを作成する](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)」を参照してください。

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Azure AD のワークスペースの名前がコレクションに変更されています

**種類:** 変更された機能  
**サービス カテゴリ:** マイ アプリ   
**製品の機能:** エンド ユーザー エクスペリエンス
 
今後、フィルター管理者がユーザーのアプリを整理するように構成できるワークスペースは、コレクションと呼ばれます。 それらの構成方法の詳細については、「[マイ アプリ ポータルでコレクションを作成する](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)」を参照してください。

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C Phone のカスタム ポリシーを使用した サインアップとサインイン (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C
 
開発者や企業は、電話番号を使用したサインアップとサインインを使用して、SMS 経由でユーザーの電話番号に送信されるワンタイム パスワードを使用したユーザーのサインアップとサインインを許可できます。 この機能を使用すると、ユーザーが電話にアクセスできなくなった場合に自分の電話番号を変更することもできます。 カスタム ポリシー機能によって、開発者や企業は、電話でのサインアップとサインインを使用して、ページのカスタマイズを通して自社のブランドを伝達できます。 「[Azure AD B2C でカスタム ポリシーを使用した電話のサインアップとサインインを設定する](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication)」でその実行方法を確認してください。
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクター - 2020 年 1 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)に関するページをご覧ください。

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2020 年 1 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2020 年 1 月に、フェデレーションを使用した以下の 33 の新規アプリのサポートがアプリ ギャラリーに追加されました。 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial)、[Fastly Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial)、[Terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial)、[Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial)、[Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik)、[SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access)、[Upshotly](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial)、[LeaveBot](https://leavebot.io/#home)、[DataCamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial)、[TripActions](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial)、[SmartWork](https://www.intumit.com/english/SmartWork.html)、[Dotcom-Monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial)、[SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial)、[Hosted MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial)、[Yuhu Property Management Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial)、[LumApps](https://sites.lumapps.com/login)、[Upwork Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial)、[Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial)、[SmartDB for Microsoft Teams](http://teams.smartdb.jp/login/)、[PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial)、[ContractSafe Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial)、[Maxient Conduct Manager Software](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial)、[Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial)、[PortalTalk 365](https://www.portaltalk.com/)、[CoreView](https://portal.coreview.com/)、[Squelch Cloud Office365 Connector](https://laxmi.squelch.io/login)、[PingFlow Authentication](https://app-staging.pingview.io/)、[ PrinterLogic SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial)、[Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial)、[Sandwai](https://app.sandwai.com/)、[EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial)、[AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial)、[Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---

### <a name="two-new-identity-protection-detections"></a>2 つの新しい Identity Protection の検出

**種類:** 新機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護
 
2 つの新しいサインインにリンクされた検出の種類が Identity Protection に追加されました。不審な受信トレイ操作ルールとあり得ない移動です。 これらのオフライン検出は Microsoft Cloud App Security (MCAS) によって検出され、Identity Protection のユーザーとサインインのリスクに影響を及ぼします。 これらの検出の詳細については、[サインインのリスクの種類](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk)に関する記事を参照してください。
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>重要な変更: URI フラグメントがログイン リダイレクトを通じて実行されることはありません

**種類:** 変更された機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
2020 年 2 月 8 日以降、ユーザーがサインインするための要求が login.microsoftonline.com に送信されると、サービスによって空のフラグメントが要求に追加されます。  これにより、ブラウザーで要求内の既存のフラグメントが消去されることで、リダイレクト クラスの攻撃を防止できます。 アプリはこの動作に依存しないようにしてください。 詳細については、Microsoft ID プラットフォームのドキュメントの[重大な変更](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020)に関する記事を参照してください。

---

## <a name="december-2019"></a>2019 年 12 月

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>SAP SuccessFactors のプロビジョニングを Azure AD およびオンプレミス AD に統合する (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理

Azure AD で、信頼できる ID ソースとして SAP SuccessFactors を統合できるようになりました。 この統合により、ID ライフサイクル全体の自動化が促進されます。たとえば、新規採用や退職などの人事イベントに基づいて Azure AD アカウントのプロビジョニングを制御できるようになります。

Azure AD への SAP SuccessFactors の受信プロビジョニングを設定する方法については、[SAP SuccessFactors の自動プロビジョニングを構成する](https://aka.ms/SAPSuccessFactorsInboundTutorial)チュートリアルを参照してください。

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Azure AD B2C でのカスタマイズされた電子メールのサポート (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C

ユーザーがアプリを使用するための新規登録をするときに、Azure AD B2C を使用して、カスタマイズされた電子メールを作成できるようになりました。 DisplayControls (現在プレビュー段階) とサード パーティの電子メール プロバイダー ([SendGrid](https://sendgrid.com/)、[SparkPost](https://sparkpost.com/)、カスタム REST API など) を使用することで、独自の電子メール テンプレート、**From** アドレス、件名テキストを使用できるだけでなく、ローカライズやカスタムのワンタイム パスワード (OTP) 設定をサポートできます。

詳細については、[Azure Active Directory B2C でのカスタム電子メール検証](https://docs.microsoft.com/azure/active-directory-b2c/custom-email)に関する記事を参照してください。

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>セキュリティ既定値によるベースライン ポリシーの置き換え

**種類:** 変更された機能  
**サービス カテゴリ:** その他  
**製品の機能:** ID のセキュリティと保護

"既定でセキュリティ保護" の認証モデルの一環として、すべてのテナントから既存のベースライン保護ポリシーを削除する取り組みが進められています。 この削除は 2 月末の完了を目標としています。 これらのベースライン保護ポリシーは、セキュリティの既定値に置き換えられます。 ベースライン保護ポリシーを使用している場合は、新しいセキュリティの既定ポリシーまたは条件付きアクセスへの移行を計画する必要があります。 これらのポリシーを使用していない場合は、特に対処の必要はありません。

新しいセキュリティの既定値の詳細については、[セキュリティの既定値](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)に関する記事を参照してください。 条件付きアクセス ポリシーの詳細については、「[一般的な条件付きアクセス ポリシー](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)」を参照してください。

---

## <a name="november-2019"></a>2019 年 11 月

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>SameSite 属性と Chrome 80 のサポート

**種類:** 変更の計画  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証

Cookie の "既定でセキュリティ保護" モデルの一環として、Chrome 80 ブラウザーでは、`SameSite` 属性を使用せずに Cookie を扱う方法の変更が進められています。 `SameSite` 属性を指定しない Cookie は、この属性が `SameSite=Lax` に設定されたものとして扱われるようになります。これにより、アプリが依存している可能性がある一部のクロスドメイン Cookie 共有シナリオが Chrome でブロックされるようになります。 従来の Chrome の動作を維持するために、HTTPS 接続でしかクロスサイト Cookie にアクセスできないよう、`SameSite=None` 属性を使用し、さらに `Secure` 属性を追加することができます。 Chrome は 2020 年 2 月 4 日までにこの変更を完了する予定です。

すべての開発者が、次のガイダンスを参考にしてアプリをテストすることを推奨します。

- **[セキュリティで保護された Cookie を使用します]** 設定の既定値を **[はい]** に設定します。

- **SameSite** 属性の既定値を **None** に設定します。

- **Secure** の `SameSite` 属性を追加します。

詳細については、[ASP.NET と ASP.NET Core で予定されている SameSite Cookie の変更](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)および [Chrome バージョン 79 以降で顧客 Web サイトおよび Microsoft の製品とサービスが中断される可能性](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79)に関する記事を参照してください。

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2) の新しい修正プログラム

**種類:** 固定  
**サービス カテゴリ:** Microsoft Identity Manager  
**製品の機能:** ID ライフサイクル管理

Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2) の修正プログラム ロールアップ パッケージ (ビルド 4.6.34.0) の提供が開始されました。 このロールアップ パッケージは、「この更新プログラムで修正された問題と追加された機能強化」で説明されているように、問題を解決し、機能強化を追加します。

修正プログラム パッケージの詳細およびダウンロード方法については、[Microsoft Identity Manager 2016 Service Pack 2 (ビルド 4.6.34.0) 更新プログラムのロールアップの提供開始](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r)に関する記事を参照してください。

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Azure AD へのアプリの移行に役立つ新しい AD FS アプリ アクティビティ レポート (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO

Azure portal で、新しい Active Directory フェデレーション サービス (AD FS) アプリ アクティビティ レポートを使用して、どのアプリが Azure AD に移行できるかを特定します。 このレポートは、Azure AD との互換性に関してすべての AD FS アプリを評価し、問題がないかどうか確認し、個々のアプリの移行の準備に関してガイダンスを提供します。

詳細については、「[AD FS アプリケーション アクティビティ レポート (プレビュー) を使用してアプリケーションを Azure AD に移行する](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity)」を参照してください。

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>ユーザーが管理者の同意を要求するための新しいワークフロー (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** アクセス制御

新しい管理者の同意ワークフローによって、管理者は、管理者の承認を必要とするアプリへのアクセス権を付与することができます。 ユーザーがアプリにアクセスしようとしているものの同意ができない場合に、ユーザーは管理者の承認の要求を送信できるようになりました。 要求は、レビュー担当者として指定されているすべての管理者宛てに電子メールで送信され、Azure portal からアクセスできるキューに配置されます。 保留中の要求に対してレビュー担当者がアクションを実行すると、要求元のユーザーにアクションが通知されます。

詳細については、「[管理者の同意ワークフローの構成 (プレビュー)](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow)」を参照してください。

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>オプションの要求を管理するための新しい Azure AD アプリ登録トークンの構成エクスペリエンス (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** 開発者エクスペリエンス

Azure portal の新しい **[Azure AD App Registrations Token configuration]\(Azure AD アプリ登録トークンの構成\)** ブレードには、アプリのオプション要求の動的な一覧がアプリ開発者に表示されます。 この新しいエクスペリエンスにより、Azure AD アプリの移行を効率化し、オプションの要求の構成ミスを最小限に抑えることができます。

詳細については、「[Azure AD アプリに省略可能な要求を提供する](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)」を参照してください。

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Azure AD エンタイトルメント管理の新しい 2 段階承認ワークフロー (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** エンタイトルメント管理

新しく導入された 2 段階の承認ワークフローでは、アクセス パッケージへのユーザーの要求を 2 人の承認者が承認することを義務付けることができます。 たとえば、要求元ユーザーの上司が最初の承認を与える必要があるように設定し、その後、リソース所有者にも承認を求めることができます。 いずれかの承認者が承認しない場合、アクセス権は付与されません。

詳細については、「[Azure AD エンタイトルメント管理でアクセス パッケージの要求と承認の設定を変更する](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy)」を参照してください。

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>[マイ アプリ] ページと新しいワークスペースに対する更新 (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** サード パーティ統合

組織のユーザーによる、更新された [マイ アプリ] エクスペリエンスの表示とアクセスの方法をカスタマイズできるようになりました。 この新しいエクスペリエンスには、ユーザーがアプリを簡単に見つけて整理できるようにする新しいワークスペース機能も含まれています。

新しい [マイ アプリ] エクスペリエンスとワークスペースの作成の詳細については、「[マイ アプリ (プレビュー) ポータルでワークスペースを作成する](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)」を参照してください。

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Azure AD B2B コラボレーションの Google ソーシャル ID サポート (一般提供)

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** ユーザー認証

Azure AD で Google ソーシャル ID (Gmail アカウント) を使用するための新しいサポートにより、ユーザーとパートナーのコラボレーションがより簡単になります。 パートナーが新しい Microsoft 固有のアカウントを作成および管理する必要がなくなりました。 Microsoft Teams は、すべてのクライアントで、また、一般的な認証エンドポイントとテナント関連の認証エンドポイントにまたがって Google ユーザーを完全にサポートするようになりました。

詳細については、「[Google を B2B ゲスト ユーザーの ID プロバイダーとして追加する](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)」を参照してください。

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Microsoft Edge Mobile での条件付きアクセスとシングル サインオンのサポート (一般提供)

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護

iOS および Android 版 Microsoft Edge 向けの Azure AD で、Azure AD シングル サインオンと条件付きアクセスがサポートされるようになりました。

- **Microsoft Edge シングル サインオン (SSO):** Azure AD で接続されるすべてのアプリのネイティブ クライアント (Microsoft Outlook や Microsoft Edge など) を横断して、シングル サインオンが利用できるようになりました。

- **Microsoft Edge 条件付きアクセス:** アプリケーション ベースの条件付きアクセス ポリシーでは、ユーザーは Microsoft Edge などの Microsoft Intune で保護されたブラウザーを使用する必要があります。

Microsoft Edge での条件付きアクセスと SSO の詳細については、[Microsoft Edge Mobile での条件付きアクセスとシングル サインオンのサポートの一般提供開始](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179)に関するブログ記事を参照してください。 [アプリ ベースの条件付きアクセス](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)または[デバイス ベースの条件付きアクセス](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)を使用してクライアント アプリを設定する方法については、「[Microsoft Intune のポリシーで保護されたブラウザーを使用して Web アクセスを管理する](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser)」を参照してください。

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Azure AD エンタイトルメント管理 (一般提供)

**種類:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** エンタイトルメント管理

Azure AD のエンタイトルメント管理は、組織で ID とアクセスのライフサイクルを大規模に管理するために役立つ新しい ID ガバナンス機能です。 この新しい機能は、グループ、アプリ、および SharePoint Online サイトにまたがって、アクセス要求ワークフロー、アクセスの割り当て、レビュー、および有効期限を自動化するために役立ちます。

Azure AD エンタイトルメント管理を使用すると、従業員だけでなく、組織外のユーザーがこれらのリソースにアクセスする必要がある場合も、より効率的にアクセスを管理できます。

詳細については、「[Azure AD エンタイトルメント管理とは](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements)」を参照してください。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>新しくサポートされた SaaS アプリに対するユーザー アカウントのプロビジョニングを自動化する

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合  

新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

[SAP Cloud Platform Identity Authentication Service](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial)、[RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial)、[SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial)、[Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial)、[Cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial)、[Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial)、[OfficeSpace Software](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial)、[Priority Matrix](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)に関するページをご覧ください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2019 年 11 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2019 年 11 月に、フェデレーションを使用した以下の 21 の新規アプリのサポートが、アプリ ギャラリーに追加されました:

[Airtable](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial)、[Hootsuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial)、[Blue Access for Members (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial)、[Bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial)、[Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial)、[ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=)、[NegometrixPortal Single Sign On (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial)、[TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279)、[Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial)、[MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial)、[BlueMail](https://loginself1.bluemail.me/)、[Beedle](https://teams-web.beedle.co/#/)、[Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial)、[OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial)、[Foko Retail](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial)、[Qmarkets Idea & Innovation Management](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial)、[Netskope User Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial)、[uniFLOW Online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial)、[Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial)、[Jisc Student Voter Registration](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial)、[e4enable](https://portal.e4enable.com/)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>改良された新しい Azure AD アプリケーション ギャラリー

**種類:** 変更された機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO

Azure AD アプリケーション ギャラリーが更新され、プロビジョニング、OpenID Connect、および SAML を Azure Active Directory テナントでサポートする事前統合アプリを簡単に見つけられるようになりました。

詳細については、「[Azure Active Directory テナントにアプリケーションを追加する](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal)」を参照してください。

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>アプリ ロール定義の長さの上限が 120 文字から 240 文字に増加

**種類:** 変更された機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO

一部のアプリやサービスで、アプリ ロール定義値の長さ制限が 120 文字では短すぎるとの意見がお客様から寄せられました。 ご要望に応え、ロール値定義の長さの上限を 240 文字に引き上げました。

アプリケーション固有のロール定義の使用方法については、「[アプリケーションにアプリ ロールを追加してトークンで受け取る](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)」を参照してください。

---
