---
title: 新機能 リリース ノート - Azure Active Directory | Microsoft Docs
description: 最新のリリース ノート、既知の問題、バグの修正、非推奨の機能、予定されている変更点など、Azure Active Directory の新着情報について説明します。
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 793b9d1b33c244354841402babbd9177ce7ed19b
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687716"
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

## <a name="october-2019"></a>2019 年 10 月

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Azure AD Identity Protection リスク検出用の identityRiskEvent API の廃止  

**種類:** 変更の計画  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護

開発者からのフィードバックに応え、Azure AD Premium P2 サブスクライバーは Microsoft Graph 用の新しい riskDetection API を使用して Azure AD Identity Protection のリスク検出データに対して複雑なクエリを実行できるようになりました。 既存の [identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API ベータ版では、**2020 年 1 月 10 日**頃からデータを戻すことを停止します。 組織で identityRiskEvent API を使用している場合は、新しい riskDetection API に移行する必要があります。

新しい riskDetection API の詳細については、[リスク検出 API のリファレンス ドキュメント](https://aka.ms/RiskDetectionsAPI)をご覧ください。

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>アプリケーション プロキシによる SameSite 属性と Chrome 80 のサポート

**種類:** 変更の計画  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** Access Control

Chrome 80 ブラウザーがリリースされる数週間前に、アプリケーション プロキシの Cookie が **SameSite** 属性を処理する方法を更新する予定です。 Chrome 80 のリリースでは、**SameSite** 属性を指定していない Cookie は `SameSite=Lax` に設定されているかのように扱われます。

この変更によって悪影響を及ぼす可能性を回避するために、次の方法でアプリケーション プロキシのアクセスとセッション Cookie を更新しています。

- **[セキュリティで保護された Cookie を使用します]** 設定の既定値を **[はい]** に設定する。

- **SameSite** 属性の既定値を **None** に設定する。

    >[!NOTE]
    > アプリケーション プロキシ アクセス Cookie は、常にセキュリティで保護されたチャネルを介して排他的に送信されています。 これらの変更は、セッション Cookie にのみ適用されます。

アプリケーション プロキシ Cookie の設定の詳細については、「[Azure Active Directory でオンプレミスのアプリケーションにアクセスするための Cookie 設定](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)」を参照してください。

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>アプリケーション登録ポータル (apps.dev.microsoft.com) からのアプリの登録 (レガシ) と集中型アプリ管理は使用不可となる

**種類:** 変更の計画  
**サービス カテゴリ:** 該当なし  
**製品の機能:** 開発者エクスペリエンス

近い将来、Azure AD アカウントを持つユーザーは、アプリケーション登録ポータル (apps.dev.microsoft.com) を使用して集中型アプリケーションを登録または管理できなくなります。または、Azure portal のアプリの登録 (レガシ) エクスペリエンスでアプリケーションを登録または管理できなくなります。

新しいアプリの登録エクスペリエンスの詳細については、「[トレーニング ガイド: Azure portal でのアプリの登録](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md)」を参照してください。

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>ユーザーは今後、ユーザーごとの MFA から条件付きアクセス ベースの MFA への移行中に再登録をする必要がない

**種類:** 固定  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティ & 保護

ユーザーがユーザーごとの Multi-Factor Authentication (MFA) に対して無効であったところ、条件付きアクセス ポリシーを介して MFA が有効となった場合、ユーザーは再登録する必要がある、という既知の問題が修正されました。

ユーザーに再登録を要求するには、Azure AD ポータルでユーザーの認証方法から **[Required re-register MFA]\(MFA の再登録が必要\)** オプションを選択します。 ユーザーごとの MFA から条件付きアクセス ベースの MFA にユーザーを移行する方法の詳細については、「[ユーザーをユーザーごとの MFA から条件付きアクセス ベースの MFA に変換する](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa)」を参照してください。

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>SAML トークン内の要求を変換および送信するための新機能

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO

SAML トークンで要求をカスタマイズして送信するための機能が追加されました。 これらの新機能は次のとおりです。

- 追加の要求変換関数。要求で送信する値を変更するのに役立ちます。

- 1 つの要求に複数の変換を適用する機能。

- ユーザーの種類とユーザーが属するグループに基づいて、要求のソースを指定する機能。

これらの機能の詳細と使用方法については、「[エンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)」を参照してください。

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Azure AD のエンド ユーザー向けの新しい [自分のサインイン] ページ

**種類:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** 監視とレポート

新しい **[自分のサインイン]** ページ (https://mysignins.microsoft.com) ) を追加しました。これにより、組織のユーザーは、最新のサインイン履歴を表示して、異常なアクティビティを確認できます。 この新しいページでは、ユーザーは次のことを確認できます。

- だれかがパスワードを推測しようとしているか。

- 攻撃者が自分のアカウントへのサインインに成功したか、またその場所。

- 攻撃者がアクセスしようとしたアプリ。

詳細については、「[Users can now check their sign-in history for unusual activity](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066)」 (ユーザーはサインイン履歴で異常なアクティビティを確認できるようになった) ブログを参照してください。

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>クラシック仮想ネットワークから Azure Resource Manager 仮想ネットワークへの Azure AD Domain Services (Azure AD DS) の移行

**種類:** 新機能  
**サービス カテゴリ:** Azure AD Domain Services  
**製品の機能:** Azure AD Domain Services

クラシック仮想ネットワークでスタックしていたお客様にとって、すばらしいニュースがあります。 クラシック仮想ネットワークから既存の Resource Manager 仮想ネットワークへ 1 回に限り移行することができるようになりました。 Resource Manager 仮想ネットワークに移行すると、詳細なパスワード ポリシー、電子メール通知、監査ログなど、追加およびアップグレードされた機能を利用できるようになります。

詳細については、「[プレビュー - クラシック仮想ネットワーク モデルから Resource Manager への Azure AD Domain Services の移行](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet)」を参照してください。

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Azure AD B2C ページ コントラクトのレイアウトの更新

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C

Azure AD B2C のページ コントラクトのバージョン 1.2.0 に対して新しい変更点がいくつか導入されました。 この更新されたバージョンでは、要素の読み込み順序を制御できるようになりました。これにより、スタイルシート (CSS) が読み込まれたときに発生するちらつきを防ぐことができます。

ページ コントラクトに加えられた変更の完全な一覧については、[バージョンの変更ログ](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120)を参照してください。

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>[マイ アプリ] ページと新しいワークスペースに対する更新 (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** Access Control

新しいワークスペース機能を使用してアプリを簡単に見つけられるようにするなど、組織のユーザーが最新の [マイ アプリ] エクスペリエンスを表示してアクセスする方法をカスタマイズできるようになりました。 新しいワークスペース機能は、組織のユーザーが既にアクセス権を持っているアプリのフィルターとして機能します。

新しい [マイ アプリ] エクスペリエンスのロール アウトとワークスペースの作成の詳細については、「[Create workspaces on the My Apps (preview) portal](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)」 ([マイ アプリ] (プレビュー) ポータルでのワークスペースの作成) を参照してください。

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>月間アクティブ ユーザー ベースの課金モデルのサポート (一般提供)

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C

Azure AD B2C では、月間アクティブ ユーザー (MAU) の課金がサポートされるようになりました。 MAU の課金は、1 つのカレンダー月の間に認証アクティビティを行った一意のユーザーの数に基づいて行われます。 既存のお客様はいつでも、この新しい課金方法に切り替えることができます。

2019 年 11 月 1 日以降、すべての新しいお客様は自動的にこの方法で課金されます。 この課金方法はお客様にとって、コスト面でのメリットと、事前に計画できるというメリットがあります。

詳細については、「[Upgrade to monthly active users billing model](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model)」 (月間アクティブ ユーザー課金モデルにアップグレードする) を参照してください。

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Azure AD ポータルの [Consolidated Security]\(統合セキュリティ\) メニュー項目

**種類:** 変更された機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護

Azure portal の新しい **[セキュリティ]** メニュー項目と **[検索]** バーから、Azure AD の利用可能なセキュリティ機能すべてにアクセスできるようになりました。 さらに、 **[Security - Getting started]\(セキュリティ - はじめに\)** という新しい **[セキュリティ]** ランディング ページには、パブリック ドキュメント、セキュリティ ガイダンス、デプロイ ガイドへのリンクが掲載されています。

新しい **[セキュリティ]** メニューには次のものが含まれます。

- 条件付きアクセス
- Identity Protection
- Security Center
- ID セキュリティ スコア
- 認証方法
- MFA
- リスク レポート - 危険なユーザー、危険なサインイン、リスクの検出
- その他...

詳細については、[[Security - Getting started]\(セキュリティ - はじめに\)](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted) を参照してください。

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Office 365 グループの有効期限ポリシーの自動更新に関する拡張

**種類:** 変更された機能  
**サービス カテゴリ:** グループ管理  
**製品の機能:** ID ライフサイクル管理

Office 365 グループの有効期限ポリシーが拡張され、メンバーによってアクティブに使用されているグループは自動的に更新されるようになりました。 グループは、Outlook、SharePoint、および Teams を含むすべての Office 365 アプリでのユーザー アクティビティに基づいて自動更新されます。

この拡張により、グループの有効期限の通知が減り、アクティブなグループを引き続き使用できるようになります。 Office 365 グループのアクティブな有効期限ポリシーが既に設定されている場合は、この新機能を有効にするために何もする必要はありません。

詳細については、「[Office 365 グループの有効期限ポリシーの構成](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle)」を参照してください。

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Azure AD Domain Services (Azure AD DS) 作成エクスペリエンスの更新

**種類:** 変更された機能  
**サービス カテゴリ:** Azure AD Domain Services  
**製品の機能:** Azure AD Domain Services

Azure AD Domain Services (Azure AD DS) を更新し、新しく強化された作成エクスペリエンスを提供することで、3 回クリックするだけでマネージド ドメインを作成できるようになりました。 さらに、テンプレートから Azure AD DS をアップロードしてデプロイできるようになりました。

詳細については、[チュートリアル: Azure Active Directory Domain Services インスタンスを作成して構成する](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)」を参照してください。

---

## <a name="september-2019"></a>2019 年 9 月

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>変更の計画: Power BI コンテンツ パックの廃止

**種類:** 変更の計画  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート

2019 年 10 月 1 日より、Power BI では、Azure AD Power BI コンテンツ パックを含む、すべてのコンテンツ パックの廃止が開始します。 このコンテンツ パックに代わり、Azure AD ブックを使用して、Azure AD 関連のサービスに関する分析情報を取得できます。 レポート専用モードの条件付きアクセス ポリシーに関するブック、アプリ同意に基づく分析情報など、その他のブックも表示されます。

ブックに関する詳細については、[Azure Active Directory レポートに Azure Monitor ブックを使用する方法](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)に関する記事を参照してください。 コンテンツ パックの廃止に関する詳細は、「[Announcing Power BI template apps general availability](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/)」 (Power BI テンプレート アプリの一般提供に関する告知) というブログ投稿をご覧ください。

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>マイ プロファイルの名前が変更され、Microsoft Office アカウント ページに統合されます

**種類:** 変更の計画  
**サービス カテゴリ:** マイ プロファイル/アカウント  
**製品の機能:** コラボレーション

10月から、「マイ プロファイル」エクスペリエンスは、「マイ アカウント」になります。 その変更の一環として、現在「**マイ プロファイル**」となっているところはすべて、「**マイ アカウント**」に変更されます。 名前の変更といくつかの設計上の改善に加え、更新されたエクスペリエンスでは、Microsoft Office アカウント ページとの統合が強化されます。 具体的には、Office のインストール済み環境とサブスクリプションに **[アカウントの概要]** ページからアクセスできるようになります。また、Office に関連した連絡先の設定に **[プライバシー]** ページからアクセスできます。

「マイ プロファイル」(プレビュー) エクスペリエンスの詳細については、「[マイ プロファイル (プレビュー) ポータルの概要](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview)」を参照してください。

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Azure AD ポータルで CSV ファイルを使用してグループとメンバーを一括管理します (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション

一括グループ管理エクスペリエンスのパブリック プレビューが Azure AD ポータルでご利用いただけるようになったことを発表いたします。 CSV ファイルと Azure AD ポータルを使用して、以下を含むグループとメンバーの一覧の管理を行えるようになりました。

- グループのメンバーを追加または削除する。

- ディレクトリからグループの一覧をダウンロードする。

- 特定グループのグループ メンバーの一覧をダウンロードする。

詳細については、[メンバーの一括追加](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members)、[メンバーの一括削除](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members)、[メンバー一覧の一括ダウンロード](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)、[グループ一覧の一括ダウンロード](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)に関する記事を参照してください。

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>新しい管理者の同意エンドポイントを使用した動的な同意がサポートされるようになりました

**種類:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証

動的な同意をサポートするために、新しい管理者の同意エンドポイントを作成しました。これは、Microsoft ID プラットフォームで動的な同意モデルを使用する必要があるアプリに役立ちます。

この新しいエンドポイントを使用する方法について詳しくは、[管理者の同意エンドポイントの使用](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent)に関する記事を参照してください。

---

### <a name="new-azure-ad-global-reader-role"></a>新しい Azure AD の全体閲覧者ロール

**種類:** 新機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** Access Control

2019 年 9 月 24 日から、全体閲覧者という新しい Azure Active Directory (AD) ロールをロールアウトします。 このロールアウトは、運用とグローバル クラウド カスタマー (GCC) から始まり、10 月に全世界で完了します。

全体閲覧者ロールは、全体管理者に対応する読み取り専用のロールです。 このロールのユーザーは、Microsoft 365 の各サービスにわたって設定と管理情報を読み取ることができますが、管理アクションを実行することはできません。 全体閲覧者ロールは、組織内の全体管理者の数を削減するのを支援するために作成されました。 全体管理者アカウントは強力であり、攻撃の対象になりやすいため、全体管理者の数は 5 人未満にすることをお勧めします。 計画、監査、または調査には、全体閲覧者ロールを使用することをお勧めします。 また、全体管理者ロールを必要とせずに作業を行うことができるように、全体閲覧者ロールを Exchange 管理者などの制限付き管理者ロールと組み合わせて使用することをお勧めします。

全体閲覧者ロールは、新しい Microsoft 365 管理センター、Exchange 管理センター、Teams 管理センター、セキュリティ センター、コンプライアンス センター、Azure AD 管理センター、デバイス管理の管理センターと連携します。

>[!NOTE]
> パブリック プレビューの開始時、全体閲覧者ロールは、以下とは連携しません。SharePoint、Privileged Access Management、カスタマー ロックボックス、機密ラベル、Teams ライフサイクル、Teams レポートと通話分析、Teams IP 電話デバイス管理、および Teams アプリ カタログ。 これらのサービスはすべて、将来的にはこのロールとの連携を予定しています。

詳細については、「[Azure Active Directory での管理者ロールのアクセス許可](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)」を参照してください。

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Azure Active Directory アプリケーション プロキシを使用して、オンプレミスのレポート サーバーに Power BI Mobile アプリからアクセスします

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** Access Control

Power BI モバイル アプリと Azure AD アプリケーション プロキシの間の新しい統合により、Power BI モバイルアプリに安全にサインインし、オンプレミスの Power BI Report Server でホストされている組織のレポートを表示することができます。

Power BI Mobile アプリの詳細 (アプリのダウンロード場所など) については、[Power BI サイト](https://powerbi.microsoft.com/mobile/)を参照してください。 Azure AD アプリケーション プロキシを使用して Power BI モバイル アプリをセットアップする方法の詳細については、「[Azure AD アプリケーション プロキシを使用して Power BI Mobile へのリモート アクセスを有効にする](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi)」を参照してください。

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>AzureADPreview PowerShell モジュールの新しいバージョンが利用可能になりました

**種類:** 変更された機能  
**サービス カテゴリ:** その他  
**製品の機能:** Directory

次の新しいコマンドレットが AzureADPreview モジュールに追加され、Azure AD でカスタム ロールを定義し、割り当てることができるようになりました。

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>新しいバージョンの Azure AD Connect

**種類:** 変更された機能  
**サービス カテゴリ:** その他  
**製品の機能:** Directory

自動アップグレードのお客様を対象とした Azure AD Connect の更新バージョンをリリースしました。 この新しいバージョンには、いくつかの新機能、機能強化、およびバグ修正が含まれています。 この新しいバージョンの詳細については、「[Azure AD Connect:バージョンのリリース履歴](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250)」を参照してください。

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Azure Multi-Factor Authentication (MFA) Server バージョン 8.0.2 が利用可能になりました

**種類:** 固定  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティ & 保護

2019 年 7 月 1 日より前に MFA Server をアクティブ化した既存のお客様は、MFA Server の最新バージョン (バージョン 8.0.2) をダウンロードできるようになりました。 この新しいバージョンでは、次のことを行いました。

- Azure AD 同期によってユーザーが無効から有効に変更されると、電子メールがユーザーに送信されるように問題を修正しました。

- Tags の機能の使用を継続しながら、お客様が正常にアップグレードできるように問題を修正しました。

- Kosovo (+383) の国番号を追加しました。

- MultiFactorAuthSvc.log へのワンタイム バイパスの監査ログ記録を追加しました。

- Web サービス SDK のパフォーマンスが向上しました。

- その他の軽微なバグを修正しました。

2019 年 7 月 1 日以降、Microsoft は新しいデプロイに対する MFA Server の提供を停止しました。 多要素認証が必要な新しいお客様は、クラウドベースの Azure Multi-Factor Authentication を使用する必要があります。 詳細については、「[クラウド ベースの Azure Multi-Factor Authentication のデプロイの計画](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)」を参照してください。

---

## <a name="august-2019"></a>2019 年 8 月

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>グループの検索、フィルター処理、および並べ替えの機能強化は、Azure AD ポータル (パブリック プレビュー) で利用できます。

**種類:** 新機能  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション

グループに関連する強化されたエクスペリエンスのパブリック プレビューが、Azure AD ポータルでご利用いただけるようになったことを発表いたします。 次を提供することで、これらの拡張機能がグループとメンバー リストをより適切に管理するのに役立ちます。

- グループ リストでの部分文字列検索などの高度な検索機能。
- メンバーと所有者のリストに対する高度なフィルター処理と並べ替えのオプション。
- メンバーと所有者のリストの新しい検索機能。
- 大規模なグループのより正確なグループ数。

詳細については、[Azure portal でのグループの管理](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)に関する記事をご覧ください。

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>アプリ登録管理に新しいカスタム ロールが使用できる (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** Access Control

(Azure AD P1 または P2 サブスクリプションで使用できる) カスタム ロールで、特定のアクセス許可を持つロール定義を作成し、そのロールを特定のリソースに割り当てて、詳細なアクセスを提供できるようになりました。 現在は、アプリの登録を管理するためのアクセス許可を使用し、ロールを特定のアプリに割り当てて、カスタム ロールを作成します。 カスタム ロールの詳細については、[Azure Active Directory でのカスタム管理者ロール](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview)に関する記事を参照してください。

追加のアクセス許可やリソースのサポートが必要だが、現在表示されていない場合は、[Azure フィードバック サイト](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)にフィードバックをお送りください。ご要望は Microsoft の更新予定に追加されます。

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>新しいプロビジョニング ログを利用して、アプリのプロビジョニング デプロイの監視とトラブルシューティングができる (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理

新しいプロビジョニング ログは、ユーザーとグループのプロビジョニング デプロイの監視とトラブルシューティングに役立ちます。 これらの新しいログ ファイルには、次の情報が含まれます。

- [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial) で正常に作成されたグループ
- [アマゾン ウェブ サービス (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws) からインポートされたロール
- [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial) からインポートされなかった従業員

詳細については、[Azure Active Directory ポータルのプロビジョニング レポート (プレビュー)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) に関する記事を参照してください。

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>すべての Azure AD 管理者のための新しいセキュリティ レポート (一般提供)

**種類:** 新機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護

すべての Azure AD 管理者は間もなく、既定で Azure AD 内の最新のセキュリティ レポートにアクセスできるようになります。 9 月末までは、最新のセキュリティ レポートの上部にあるバナーを使用し、前のレポートに戻ることができます。

最新のセキュリティ レポートでは、前のバージョンから次の追加機能が提供されます。

- 高度なフィルター処理と並べ替え
- ユーザー リスクの無視などの一括操作
- 侵害されたエンティティまたは安全なエンティティの確認
- 次のようなリスクの状態: 危険、無視、修復済み、セキュリティ侵害の確認済み
- 新しいリスク関連の検出 (Azure AD Premium のサブスクライバーが利用可能)

詳細については、[危険なユーザー](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users)、[リスクの高いサインイン](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)、[リスク検出](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections)に関する記事をご覧ください。

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>ユーザー割り当てマネージド ID が Virtual Machines と Virtual Machine Scale Sets で利用できる (一般提供)

**種類:** 新機能  
**サービス カテゴリ:** Azure リソースのマネージド ID  
**製品の機能:** 開発者エクスペリエンス

ユーザー割り当てマネージド ID が Virtual Machines と Virtual Machine Scale Sets で一般提供されました。 この一環として、Azure では、使用中のサブスクリプションで信頼され、1 つまたは複数の Azure サービス インスタンスに割り当てることができる ID を Azure AD テナントで作成できます。 ユーザー割り当てマネージド ID の詳細については、[Azure リソースのマネージド ID とは](https://aka.ms/azuremanagedidentity)に関する記事を参照してください。

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>ユーザーはモバイル アプリまたはハードウェア トークンを利用してパスワードをリセットできる (一般提供)

**種類:** 変更された機能  
**サービス カテゴリ:** セルフサービスによるパスワードのリセット  
**製品の機能:** ユーザー認証

モバイル アプリを組織に登録しているユーザーは、Microsoft Authenticator アプリからの通知に同意するか、モバイル アプリまたはハードウェア トークンからのコードを入力すると、自分のパスワードをリセットできるようになりました。

詳細については、[動作のしくみ:Azure AD のセルフサービスによるパスワードのリセットのクイック スタート](https://aka.ms/authappsspr)に関する記事をご覧ください。 ユーザー エクスペリエンスの詳細については、[職場または学校のパスワードのリセットの概要](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview)に関する記事をご覧ください。

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET が on-behalf-of シナリオで MSAL.NET 共有キャッシュを無視する

**種類:** 固定  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証

Azure AD Authentication ライブラリ (ADAL.NET) バージョン 5.0.0-preview 以降では、アプリの開発者が、[Web アプリおよび Web API のアカウントごとに 1 つのキャッシュをシリアル化](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api)する必要があります。 そうしないと、`UserAssertion` の特定のユース ケースと共に [on-behalf-of フロー](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow)を使用する一部のシナリオで、特権の昇格が生じることがあります。 この脆弱性を回避するために、on-behalf-of のシナリオでは、.NET 用 Microsoft 認証ライブラリ (MSAL.NET) の共有キャッシュが ADAL.NET で無視されるようになりました。

この問題の詳細については、[Azure Active Directory 認証ライブラリの特権の昇格の脆弱性](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258)に関するページを参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2019 年 8 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2019 年 8 月に、フェデレーションを使用した以下の 26 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Civic Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial)、[Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial)、[ProNovos Ops Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial)、[Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial)、[Viareport's Inativ Portal (Europe)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial)、[Azure Databricks](https://azure.microsoft.com/services/databricks)、[Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial)、[Academy Attendance](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial)、[Priority Matrix](https://sync.appfluence.com/pmwebng/)、[Cousto MySpace](https://cousto.platformers.be/account/login)、[Uploadcare](https://uploadcare.com/accounts/signup/)、[Carbonite Endpoint Backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial)、[CPQSync by Cincom](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial)、[Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial)、[deliver.media™ Portal](https://portal.deliver.media)、[Frontline Education](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial)、[F5](https://www.f5.com/products/security/access-policy-manager)、[stashcat AD connect](https://www.stashcat.com)、[Blink](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial)、[Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial)、[ProNovos Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial)、[Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial)、[Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial)、[Watch by Colors](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial)、[Harness](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial)、[EAB Navigate Strategic Care](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>AzureAD PowerShell モジュールと AzureAD Preview PowerShell モジュールの新しいバージョンを利用できる

**種類:** 変更された機能  
**サービス カテゴリ:** その他  
**製品の機能:** Directory

AzureAD モジュールと AzureAD Preview PowerShell モジュールの新しい更新プログラムを利用できます。

- 新しい `-Filter` パラメーターが、AzureAD モジュールの `Get-AzureADDirectoryRole` パラメーターに追加されました。 このパラメーターは、コマンドレットから返されるディレクトリ ロールのフィルター処理に役立ちます。
- 次の新しいコマンドレットが AzureADPreview モジュールに追加され、Azure AD でカスタム ロールを定義し、割り当てることができるようになりました。

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Azure portal の動的グループ ルール ビルダーの UI の機能強化

**種類:** 変更された機能  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション

Azure portal で利用できる動的グループ ルール ビルダーの UI をいくつか機能強化し、新しいルールの設定や既存のルールの変更が簡単になりました。 設計のこの機能強化により、1 つだけではなく、最大 5 つの式を使用してルールを作成できます。 また、デバイス プロパティ リストを更新し、非推奨のデバイス プロパティを削除しました。

詳細については、[動的メンバーシップ ルールの管理](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-update-rule)に関する記事を参照してください。

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>アクセス レビューで新しい Microsoft Graph アプリのアクセス許可が利用できるようになった

**種類:** 変更された機能  
**サービス カテゴリ:** アクセス レビュー  
**製品の機能:** Identity Governance

Microsoft Graph アプリのアクセス許可に新しい `AccessReview.ReadWrite.Membership` を導入しました。これにより、アプリで、アクセス レビューを自動的に作成および取得して、グループ メンバーシップとアプリの割り当てができるようになりました。 このアクセス許可は、スケジュールを設定したジョブで使用したり、自動化の一部として使用したりでき、ログイン ユーザー コンテキストは必要ありません。

詳細については、「[Example how to create Azure AD access reviews using Microsoft Graph app permissions with PowerShell](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241)」 (PowerShell で Microsoft Graph アプリのアクセス許可を使用して Azure AD アクセス レビューを作成する方法の例) というブログをご覧ください。

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Azure AD アクティビティ ログが Azure Monitor の政府クラウド インスタンスで利用できるようになった

**種類:** 変更された機能  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート

Azure AD アクティビティ ログが Azure Monitor の政府クラウド インスタンスで利用できるようになったことをお知らせします。 [Sumologic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic)、[Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)、[ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight) などの SIEM ツールと統合する目的で、ストレージ アカウントやイベント ハブに Azure AD ログを送信できるようになりました。 

Azure Monitor を設定する方法については、[Azure Monitor の Azure AD アクティビティ ログ](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations)に関する記事を参照してください。

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>セキュリティ情報の機能強化された新しいエクスペリエンスをユーザーに提供する

**種類:** 変更された機能  
**サービス カテゴリ:** 認証 (ログイン)   
**製品の機能:** ユーザー認証

2019 年 9 月 25 日に、ユーザー セキュリティ情報の登録と管理のためのセキュリティ情報エクスペリエンスのうち、機能強化されていない前のバージョンを無効にし、新しい[機能強化されたバージョン](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)のみを有効にします。 つまり、ユーザーは以前のエクスペリエンスを使用できなくなります。

機能強化されたセキュリティ情報エクスペリエンスについては、[管理者ドキュメント](https://aka.ms/securityinfodocs) と [ユーザー ドキュメント](https://aka.ms/securityinfoguide)を参照してください。

#### <a name="to-turn-on-this-new-experience-you-must"></a>この新しいエクスペリエンスを有効にするには、次の操作を行う必要があります。

1. Azure portal にグローバル管理者またはユーザー管理者としてサインインします。

2. **[Azure Active Directory]、[ユーザー設定]、[アクセス パネル プレビュー機能の設定を管理]** の順に移動します。

3. **[ユーザーはセキュリティ情報の登録と管理のためにプレビュー機能を使用できます - 拡張]** 領域で **[選択済み]** を選択し、ユーザーのグループを選択するか、 **[すべて]** を選択してテナントのすべてのユーザーに対してこの機能をオンにします。

4. [ユーザーはセキュリティ情報の登録と管理のためにプレビュー機能を使用できます] 領域で **[なし]** を選択します。

5. 設定を保存します。

    設定を保存すると、以前のセキュリティ情報エクスペリエンスにアクセスできなくなります。

>[!Important]
>2019 年 9 月 25 日より前にこれらの手順を完了しないと、機能強化されたエクスペリエンスについて、Azure Active Directory テナントが自動的に有効になります。 ご質問がございましたら、registrationpreview@microsoft.com にお問い合わせください。

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>POST ログインを使用した認証要求の検証がさらに厳密になる

**種類:** 変更された機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** 標準

2019 年 9 月 2 日より、POST メソッドを使用した認証要求は、HTTP 標準に対してさらに厳密に検証されます。 具体的には、スペースと二重引用符 (") が要求フォームの値から削除されなくなります。 これらの変更によって、既存のクライアントが中断されることがなく、Azure AD に送信された要求は毎回確実に処理されます。

詳細については、[Azure AD の破壊的変更の告知](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored)に関するページを参照してください。

---

## <a name="july-2019"></a>2019 年 7 月

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>変更の計画: アプリケーション プロキシ サービスが、TLS 1.2 のみをサポートするように更新される

**種類:** 変更の計画  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** Access Control

最強の暗号化を提供できるように、アプリケーション プロキシ サービスのアクセスを TLS 1.2 プロトコルのみに制限する作業が開始される予定です。 この制限は、最初のうちは TLS 1.2 プロトコルを既に利用されているお客様にロールアウトされるため、影響はありません。 TLS 1.0 と TLS 1.1 のプロトコルは、2019 年 8 月 31 日に完全に非推奨になります。 まだ TLS 1.0 および TLS 1.1 を利用されているお客様には、この変更に備えていただけるよう、事前に通知されます。

この変更期間中もアプリケーション プロキシ サービスへの接続を維持するには、クライアントとサーバーおよびブラウザーとサーバーの組み合わせを TLS 1.2 を使用するように更新することをお勧めします。 また、アプリケーション プロキシ サービスから公開されるアプリに従業員がアクセスする際に使用するすべてのクライアント システムを含めることもお勧めします。

詳しくは、「[Azure Active Directory のアプリケーション プロキシを使用してリモート アクセスするためのオンプレミス アプリケーションを追加する](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)」を参照してください。

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>変更の計画: アプリケーション ギャラリーのデザインの更新が予定されている

**種類:** 変更の計画  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO

**[アプリケーションの追加]** ブレードの **[ギャラリーから追加する]** 領域のデザインが、新しいユーザー インターフェイスに変更されます。 この変更により、自動プロビジョニング、OpenID Connect、Security Assertion Markup Language (SAML)、パスワード シングル サインオン (SSO) がサポートされているアプリをより簡単に見つけることができるようになります。

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>変更の計画: Office 365 IP アドレスから MFA サーバーの IP アドレスを削除する

**種類:** 変更の計画  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティ & 保護

[Office 365 の IP アドレスと URL Web サービス](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service)から、MFA サーバーの IP アドレスを削除する予定です。 現在これらのページに依存してファイアウォールの設定を更新している場合は、「[Azure Multi-Factor Authentication Server の概要](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements)」の「**Azure Multi-Factor Authentication Server ファイアウォールの要件**」セクションに記載されている IP アドレスの一覧も含まれていることを確認する必要があります。

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>アプリ専用トークンで、クライアント アプリがリソース テナントに存在することが必要になった

**種類:** 固定  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証

2019 年 7 月 26 日に、[クライアント資格情報付与](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)によるアプリ専用トークンの提供方法を変更しました。 以前は、クライアント アプリがテナント内に存在するかどうかに関係なく、アプリでは他のアプリを呼び出すためのトークンを取得できました。 この動作が更新され、シングル テナント リソース (Web API とも呼ばれます) は、リソース テナントに存在するクライアント アプリによってのみ呼び出すことができるようになりました。

ご利用のアプリがリソース テナント内に存在しない場合、`The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` のようなエラー メッセージが表示されます。この問題を解決するには、[管理者の同意エンドポイント](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint)または [PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell) を使用して、テナント内にクライアント アプリ サービス プリンシパルを作成する必要があります。これにより、テナントではテナント内で動作するアクセス許可がアプリに付与されます。

詳細については、[認証の新機能](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant)に関する記事をご覧ください。

> [!NOTE]
> クライアントと API の間の既存の同意は引き続き必要ありません。 アプリでは、引き続き独自の承認チェックを実行する必要があります。

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>FIDO2 セキュリティ キーを使用した Azure AD への新しいパスワードなしのサインイン

**種類:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証

Azure AD のお客様は、組織のユーザーとグループに対する FIDO2 セキュリティ キーを管理するためのポリシーを設定できるようになりました。 また、エンド ユーザーは、セキュリティ キーを自分で登録したり、FIDO 対応デバイスを使っているときにキーを使って Web サイト上の Microsoft アカウントにサインインしたり、Azure AD 参加済みの Windows 10 デバイスにサインインしたりできます。

管理者関連の情報の詳細については、[Azure AD へのパスワードなしのサインインを有効にする (プレビュー)](/azure/active-directory/authentication/concept-authentication-passwordless)、エンド ユーザー関連の情報については、[セキュリティ キーを使用するようにセキュリティ情報を設定する (プレビュー)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) に関する記事をご覧ください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2019 年 7 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2019 年 7 月に、フェデレーションを使用した以下の 18 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Ungerboeck Software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial)、[Bright Pattern Omnichannel Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial)、[Clever Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial)、[AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial)、[Looop](https://www.looop.co/schedule-a-demo/)、[productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial)、[MS Azure SSO Access for Ethidex Compliance Office™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso)、[Hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial)、[Abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial)、[Ascentis](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial)、[Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html)、[Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial)、[TwineSocial](https://twinesocial.com/)、[Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial)、[HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial)、[PharmID WasteWitness](https://www.pharmid.com/)、[i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/)、[JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>新しくサポートされた SaaS アプリに対するユーザー アカウントのプロビジョニングを自動化する

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** 監視とレポート

新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federated Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングの自動化](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)に関するページを参照してください

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>ネットワーク セキュリティ グループ用の新しい Azure AD Domain Services サービス タグ

**種類:** 新機能  
**サービス カテゴリ:** Azure AD Domain Services  
**製品の機能:** Azure AD Domain Services

IP アドレスと範囲の長いリストの管理に苦労している場合は、Azure ネットワークのセキュリティ グループで新しい **AzureActiveDirectoryDomainServices** ネットワーク サービス タグを使うと、Azure AD Domain Services 仮想ネットワーク サブネットへの受信トラフィックをセキュリティで保護するのに役立ちます。

この新しいサービス タグの詳細については、[Azure AD Domain Services 用のネットワーク セキュリティ グループ](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports)に関する記事をご覧ください。

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Azure AD Domain Services 用の新しいセキュリティ監査 (プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** Azure AD Domain Services  
**製品の機能:** Azure AD Domain Services

Azure AD Domain Services のセキュリティ監査がパブリック プレビューにリリースされたことをお知らせします。 セキュリティ監査では、Azure AD Domain Services ポータルを使用して、Azure Storage、Azure Log Analytics ワークスペース、Azure Event Hub などのターゲット リソースにセキュリティ監査イベントをストリーミングして、認証サービスに関する重要な分析情報が提供されます。

詳細については、[Azure AD ドメイン サービスでセキュリティ監査を有効にする (プレビュー)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events) に関する記事をご覧ください。

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>新しい認証方法の使用状況と分析情報 (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** セルフサービスによるパスワードのリセット  
**製品の機能:** 監視とレポート

新しい認証方法の使用状況と分析情報のレポートは、Azure Multi-Factor Authentication やパスワード リセットのセルフサービスなどの機能が組織内でどのように登録されて使用されているかを理解するのに役立ちます (各機能の登録済みユーザー数、パスワードのリセットでパスワード リセットのセルフサービスが使用される頻度、リセットが行われた方法など)。

詳細については、[認証方法の使用状況と分析情報 (プレビュー)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights) に関する記事をご覧ください。

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>新しいセキュリティ レポートは、すべての Azure AD 管理者が利用できます (パブリックプレビュー)

**種類:** 新機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護

すべての Azure AD 管理者が、**リスクのフラグ付きユーザー** レポートなど、既存のセキュリティ レポートの上部にあるバナーを選択して、**危険なユーザー** レポートや**危険なサインイン** レポートに表示される新しいセキュリティ エクスペリエンスを使い始めることができるようになりました。 時間をかけて、すべてのセキュリティ レポートが以前のバージョンから新しいバージョンに移行され、新しいレポートでは次の追加機能が提供されます。

- 高度なフィルター処理と並べ替え

- ユーザー リスクの無視などの一括操作

- 侵害されたエンティティまたは安全なエンティティの確認

- 次のようなリスクの状態: 危険、無視、修復済み、セキュリティ侵害の確認済み

詳細については、[危険なユーザー レポート](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users)と[リスクの高いサインイン レポート](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)に関する記事をご覧ください。

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Azure AD Domain Services 用の新しいセキュリティ監査 (プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** Azure AD Domain Services  
**製品の機能:** Azure AD Domain Services

Azure AD Domain Services のセキュリティ監査がパブリック プレビューにリリースされたことをお知らせします。 セキュリティ監査では、Azure AD Domain Services ポータルを使用して、Azure Storage、Azure Log Analytics ワークスペース、Azure Event Hub などのターゲット リソースにセキュリティ監査イベントをストリーミングして、認証サービスに関する重要な分析情報が提供されます。

詳細については、[Azure AD ドメイン サービスでセキュリティ監査を有効にする (プレビュー)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events) に関する記事をご覧ください。

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>SAML/WS フィードを使用した新しい B2B 直接フェデレーション (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C

直接フェデレーションは、SAML または WS フィード標準をサポートする ID システムを使用すると、IT 管理対象の ID ソリューションが Azure AD ではないパートナーとの連携を容易にするのに役立ちます。 パートナーとの直接フェデレーション関係を設定した後、そのドメインから招待するすべての新しいゲスト ユーザーと、既存の組織アカウントを使って共同作業することができ、ゲストに対するユーザー エクスペリエンスがよりシームレスになります。

詳細については、[ゲスト ユーザーのための AD FS およびサード パーティ プロバイダーとの直接フェデレーション (プレビュー)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation) に関する記事をご覧ください。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>新しくサポートされた SaaS アプリに対するユーザー アカウントのプロビジョニングを自動化する

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** 監視とレポート

新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federated Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)に関するページをご覧ください。

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Azure AD ポータルでの重複するグループ名の新しい確認

**種類:** 新機能  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション

Azure AD ポータルからグループ名を作成または更新するときは、リソース内の既存のグループ名を複製しているどうかの確認が行われます。 名前が既に別のグループによって使用されていると判断された場合は、名前を変更するように求められます。

詳細については、[Azure AD ポータルでのグループの管](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)に関する記事をご覧ください。

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>応答 (リダイレクト) URI での静的クエリ パラメーターの Azure AD による新しいサポート

**種類:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証

Azure AD アプリでは、OAuth 2.0 要求に対する静的クエリ パラメーター (`https://contoso.com/oauth2?idp=microsoft` など) で応答 (リダイレクト) URI を登録して使用できるようになりました。 静的クエリ パラメーターは、応答 URI の他の部分と同様に、応答 URI に対する文字列照合の対象になります。 URL でデコードされたリダイレクト URI に一致する登録文字列がない場合、要求は拒否されます。 応答 URI が見つかった場合は、静的なクエリ パラメーターを含む文字列全体が、ユーザーをリダイレクトするために使われます。

動的応答 URI はセキュリティ上のリスクがあり、認証要求全体で状態情報を保持するために使用できないため、引き続き許可されません。 この目的には、`state` パラメーターを使います。

現在、Azure portal のアプリ登録画面では、クエリ パラメーターは引き続きブロックされます。 ただし、アプリ マニフェストを手動で編集して、アプリにクエリ パラメーターを追加し、テストすることはできます。 詳細については、[認証の新機能](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters)に関する記事をご覧ください。

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>PowerShell コマンドレットで使用できるようになった Azure AD のアクティビティログ (MS Graph API)

**種類:** 新機能  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート

Azure AD PowerShell モジュールを通じて Azure AD のアクティビティ ログ (監査レポートとサインイン レポート) を利用できるようになったことをお知らせします。 以前は、MS Graph API エンドポイントを使って独自のスクリプトを作成できましたが、その機能が PowerShell コマンドレットに拡張されました。

これらのコマンドレットの使い方の詳細については、[レポート用の Azure AD PowerShell コマンドレット](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting)に関する記事をご覧ください。

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Azure AD での監査ログとサインイン ログに関するフィルター コントロールを更新

**種類:** 変更された機能  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート

監査ログ レポートとサインイン ログ レポートが更新され、レポート画面で列として追加しなくても、さまざまなフィルターを適用できるようになりました。 さらに、画面に表示するフィルターの数を決定できるようになりました。 これらの更新すべてが連携して、レポートがいっそう読みやすく、ニーズに合うようになります。

これらの更新の詳細については、[監査ログのフィルター処理](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs)および[サインイン アクティビティのフィルター処理](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities)に関する記事をご覧ください。

---

## <a name="june-2019"></a>2019 年 6 月

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Microsoft Graph 用の新しい riskDetections API (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護

Microsoft Graph 用の新しい riskDetections API のパブリック プレビューが開始されました。 この新しい API を使用すると、組織の Identity Protection に関連したユーザーおよびサインインのリスク検出の一覧を表示できます。 また、この API を使用して、検出の種類、状態、レベルなどの詳細を含むリスク検出のクエリをさらに効率良く行うことができます。

詳細については、[リスク検出 API のリファレンス ドキュメント](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta)をご覧ください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2019 年 6 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2019年 6 月に、フェデレーションを使用した以下の 22 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial)、[Otsuka Shokai](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial)、[ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial)、[Azure VPN Client](https://portal.azure.com/)、[ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial)、[Helper Helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial)、[Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial)、[GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial)、[Mercedes-Benz In-Car Office](https://me.secure.mercedes-benz.com/)、[Skore](https://app.justskore.it/)、[Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial)、[CyberArk SAML Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial)、[Scrible Edu](https://www.scrible.com/sign-in/#/create-account)、[PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial)、[Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial)、[Proptimise OS](https://proptimise.co.uk/software/)、[Vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial)、Oracle Access Manager for Oracle Retail Merchandising、Oracle Access Manager for Oracle E-Business Suite、Oracle IDCS for E-Business Suite、Oracle IDCS for PeopleSoft、Oracle IDCS for JD Edwards

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>新しくサポートされた SaaS アプリに対するユーザー アカウントのプロビジョニングを自動化する

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** 監視とレポート

新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングの自動化](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)に関するページを参照してください

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Azure AD プロビジョニング サービスのリアルタイムの進行状況を表示する

**種類:** 変更された機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理

Azure AD プロビジョニング エクスペリエンスが更新されて、ユーザーのプロビジョニング プロセスがどのくらい進んでいるかを示す新しい進行状況バーが組み込まれました。 この更新されたエクスペリエンスでは、現在までにプロビジョニングされたユーザー数だけでなく、現在のサイクル中にプロビジョニングされたユーザー数に関する情報も提供されます。

詳細については、「[ユーザー プロビジョニングの状態を確認する](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)」を参照してください。

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>会社のブランドがサインアウト画面とエラー画面に表示される

**種類:** 変更された機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証

Azure AD が更新されて、会社のブランドがサインイン ページだけでなくサインアウト画面やエラー画面にも表示されるようになりました。 この機能を有効にするための操作は一切不要で、Azure portal の **[会社のブランド]** 領域で既に設定した資産が Azure AD でそのまま使用されます。

会社のブランドの設定について詳しくは、[組織の Azure Active Directory ページへのブランドの追加](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding)に関する記事を参照してください。

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>新しいデプロイでの Azure Multi-Factor Authentication (MFA) Server の廃止

**種類:** 非推奨  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティ & 保護

2019 年 7 月 1 日より、Microsoft では新しいデプロイに対して MFA Server が提供されなくなります。 組織内で多要素認証が必要な新しいお客様は、今後はクラウドベースの Azure Multi-Factor Authentication を使用する必要があります。 7 月 1 日より前に MFA Server をアクティブ化したお客様には、変更が表示されません。 引き続き、最新バージョンをダウンロードしたり、今後の更新プログラムを取得したり、アクティブ化資格情報を生成したりすることができます。

詳細については、「[Azure Multi-Factor Authentication Server の概要](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy)」を参照してください。 クラウドベースの Azure Multi-factor Authentication の詳細については、「[クラウド ベースの Azure Multi-Factor Authentication のデプロイの計画](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)」を参照してください。

---

## <a name="may-2019"></a>2019 年 5 月

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>サービスの変更:アプリケーション プロキシ サービスでの TLS 1.2 プロトコルのみの今後のサポート

**種類:** 変更の計画  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** Access Control

お客様にクラス最高の暗号化を提供するために、Application Proxy サービスでは TLS 1.2 プロトコルのみにアクセスを制限しています。 この変更は、TLS 1.2 プロトコルのみを既に使用しているお客様に徐々に適用されているため、変更を意識することはありません。

2019 年 8 月 31日で TLS 1.0 と TLS 1.1 が廃止されますが、改めて事前通知を行いますので、この変更に備える時間が与えられます。 この変更に備えるために、Application Proxy を通じて発行されたアプリへのアクセスにユーザーが使用するすべてのクライアントを含む、クライアントとサーバーおよびブラウザーとサーバーの組み合わせが、TLS 1.2 プロトコルを使用して Application Proxy サービスへの接続を維持するように更新されていることを確認してください。 詳しくは、「[Azure Active Directory のアプリケーション プロキシを使用してリモート アクセスするためのオンプレミス アプリケーションを追加する](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin)」を参照してください。

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>使用状況および分析情報レポートを使用して、アプリに関連するサインイン データを表示する

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** 監視とレポート

これで、Azure portal の **[エンタープライズ アプリケーション]** 領域にある使用状況および分析情報レポートを使用して、以下に関する情報を含んだサインイン データをアプリケーションを中心にして表示できます。

- 組織内で最もよく使われるアプリ

- サインインの失敗が最も多いアプリ

- アプリごとの上位のサインイン エラー

この機能の詳細については、「[Azure Active Directory ポータルの使用状況と分析情報のレポート](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)」を参照してください

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Azure AD を使用してクラウド アプリへのユーザー プロビジョニングを自動化する

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** 監視とレポート

これらの新しいチュートリアルに従って、Azure AD プロビジョニング サービスを使用して、次のクラウド ベース アプリのユーザー アカウントの作成、削除、および更新を自動化します。

- [Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

グループ オブジェクトのプロビジョニング方法に関する情報が記載されている、この新しい [Dropbox チュートリアル](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)に従うこともできます。

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD での SaaS アプリケーションへのユーザー プロビジョニングの自動化](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)に関するページを参照してください。

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Azure AD で ID セキュリティ スコアが利用可能 (一般提供)

**種類:** 新機能  
**サービス カテゴリ:** 該当なし  
**製品の機能:** ID のセキュリティ & 保護

これで、Azure AD で ID セキュリティ スコア機能を使用することにより、ID セキュリティ体制を監視し改善できます。 ID セキュリティ スコア機能では、次の操作に役立つ単一のダッシュボードを使用します。

- 1 から 223 のスコアに基づいて ID セキュリティ体制を客観的に測定する。

- ID セキュリティの強化を計画する

- セキュリティの強化の成功を確認する

ID セキュリティ スコア機能の詳細については、「[Azure Active Directory の ID セキュリティ スコアとは](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score)」を参照してください。

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>新しいアプリの登録エクスペリエンスが利用可能 (一般提供)

**種類:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** 開発者エクスペリエンス

新しい[アプリの登録](https://aka.ms/appregistrations)エクスペリエンスが一般提供され利用可能になりました。 この新しいエクスペリエンスには、Azure portal とアプリケーション登録ポータルの使い慣れているすべての主要機能が含まれ、これらの機能は以下を通じて向上しています。

- **アプリの管理の向上。** さまざまなポータルにわたってアプリを表示する代わりに、1 か所ですべてのアプリを表示できるようになりました。

- **アプリの登録の簡略化。** 改善されたナビゲーション エクスペリエンスから、刷新されたアクセス許可選択エクスペリエンスまで、アプリの登録および管理が簡単になりました。

- **詳細情報。** クイック スタート ガイドなど、アプリに関する詳細が表示されます。

詳細については、[Microsoft ID プラットフォーム](https://docs.microsoft.com/azure/active-directory/develop/)と[新しいアプリの登録エクスペリエンスが一般に利用可能になった](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/)ことに関するページを参照してください。 ブログでのお知らせ。

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Identity Protection の Risky Users API で使用できる新機能

**種類:** 新機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護

Risky Users API を使用して、ユーザーのリスク履歴を取得し、危険なユーザーを無視し、ユーザーを侵害対象として確認できるようになったことをお知らせします。 この変更により、より効率的にユーザーのリスク状態を更新し、リスク履歴を理解できるようになりました。

詳細については、[Risky Users API のリファレンス ドキュメント](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2019 年 5 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2019年 5 月に、フェデレーションを使用した以下の 21 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial)、[Real Links](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial)、[Kianda](https://app.kianda.com/sso/OpenID/AzureAD/)、[Simple Sign](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial)、[Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial)、[Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial)、[Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial)、[Marketo Sales Engage](https://toutapp.com/login)、[ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial)、[OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial)、[Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial)、[Quantum Workplace](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial)、[Cobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial)、[webMethods API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial)、[RedFlag](https://pocketstop.com/redflag/)、[Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial)、[Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial)、[JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial)、[NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial)、[Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial)、[MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Azure AD ポータルの強化されたグループ作成および管理エクスペリエンス

**種類:** 新機能  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション

Azure AD ポータルでグループに関連するエクスペリエンスの機能強化を行いました。 これらの機能強化により、管理者は、グループの一覧やメンバーの一覧をより適切に管理し、追加の作成オプションを提供できます。

機能強化は次のとおりです。

- メンバーシップの種類およびグループの種類による基本フィルター処理。

- ソースやメール アドレスなどの新しい列の追加。

- 簡単に選択解除するためのグループ、メンバー、および所有者の一覧を複数選択する機能。

- グループの作成中にメール アドレスを選択し、所有者を追加する機能。

詳細については、「[Azure Active Directory を使用して基本グループを作成してメンバーを追加する](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)」を参照してください。

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Azure AD ポータルで Office 365 グループの名前付けポリシーを構成する (一般提供)

**種類:** 変更された機能  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション

管理者は、Azure AD ポータルを使用して、Office 365 グループの名前付けポリシーを構成できるようになりました。 この変更は、組織内のユーザーによって作成または編集される Office 365 グループに対して一貫性のある名前付け規則を適用するのに役立ちます。

Office 365 グループの名前付けポリシーは、次の 2 通りの方法で構成できます。

- グループ名に自動的に追加されるプレフィックスまたはサフィックスを定義します。

- グループ名で許可されない、組織の禁止されている単語のカスタマイズ セットをアップロードします (たとえば、“CEO、Payroll、HR”)。

詳細情報については、「[Azure Active Directory での Office 365 グループの名前付けポリシーの強制](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)」をご覧ください。

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Microsoft Graph API エンドポイントが Azure AD アクティビティ ログで利用可能(一般提供)

**種類:** 変更された機能  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート

Azure AD アクティビティ ログで Microsoft Graph API エンドポイントのサポートが一般的に利用できるようになったことをお知らせします。 このリリースでは、バージョン 1.0 の Azure AD 監査ログとサインイン ログ API を使用できるようになりました。

詳細については、[Azure AD 監査ログ API の概要](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0)に関するページを参照してください。

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>管理者が統合登録プロセスに条件付きアクセスを使用可能 (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護  

管理者は、統合登録ページで使用する条件付きアクセス ポリシーを作成できるようになりました。 これには、次の場合に登録を許可するポリシーの適用が含まれます。

- ユーザーが信頼できるネットワーク上にいる場合。

- ユーザーのサインイン リスクが低い場合。

- ユーザーがマネージド デバイス上にある場合。

- ユーザーが組織の利用規約 (TOU) に同意した場合。

条件付きアクセスとパスワードのリセットの詳細については、[「Azure AD の MFA とパスワードのリセットの統合登録エクスペリエンスの条件付きアクセス」のブログ記事](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348)をご覧ください。 統合登録プロセスの条件付きアクセス ポリシーの詳細については、「[統合登録の条件付きアクセス ポリシー](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration)」を参照してください。 Azure AD Terms of Use 機能の詳細については、「[Azure Active Directory Terms of Use 機能](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)」を参照してください。

---
