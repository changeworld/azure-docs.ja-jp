---
title: 新機能 リリース ノート - Azure Active Directory | Microsoft Docs
description: 最新のリリース ノート、既知の問題、バグの修正、非推奨の機能、予定されている変更点など、Azure Active Directory の新着情報について説明します。
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 4/30/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b918cc2b692ed3d5e6594cd6b888b55eb54e5aa
ms.sourcegitcommit: eda26a142f1d3b5a9253176e16b5cbaefe3e31b3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2021
ms.locfileid: "109733354"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory の新着情報

>URL `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` をコピーして、お使いの ![RSS フィード リーダー アイコン](./media/whats-new/feed-icon-16x16.png) フィード リーダーに貼り付け、更新内容を確認するためにこのページに再度アクセスするタイミングに関する通知を受け取るようにしてください。

Azure AD は随時改善されています。 常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

- 最新のリリース
- 既知の問題
- バグの修正
- 非推奨の機能
- 変更の計画

このページは毎月更新されるため、定期的にアクセスしてご確認ください。 6 か月以上前の項目を探す場合は、「[新着情報のアーカイブ - Azure Active Directory](whats-new-archive.md)」をご覧ください。

---

## <a name="april-2021"></a>2021 年 4 月

### <a name="bug-fixed---azure-ad-will-no-longer-double-encode-the-state-parameter-in-responses"></a>バグの修正 - Azure AD の応答で状態パラメーターが二重エンコードされなくなりました

**種類:** 固定  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
Azure AD で、クライアント アプリケーションへの `/authorize` 応答におけるバグを特定し、修正プログラムをテストしてリリースしました。  Azure AD でクライアントへの応答の送信時に `state` パラメーターの URL エンコードを間違って 2 回行っていました。  これにより、状態パラメーターの不一致によってクライアント アプリケーションで要求が拒否される可能性があります。 [詳細については、こちらを参照してください](../develop/reference-breaking-changes.md#bug-fix-azure-ad-will-no-longer-url-encode-the-state-parameter-twice)。 

---

### <a name="users-can-only-create-security-and-microsoft-365-groups-in-azure-portal-being-deprecated"></a>ユーザーがセキュリティおよび Microsoft 365 グループを Azure portal でのみ作成できることの廃止

**種類:** 変更の計画  
**サービス カテゴリ:** グループ管理  
**製品の機能:** ディレクトリ
 
ユーザーは、Azure portal でのみセキュリティおよび Microsoft 365 グループを作成するように制限されなくなります。 新しい設定を使用すると、ユーザーは Azure portal、PowerShell、および API でセキュリティ グループを作成できます。 ユーザーは、新しい設定を確認して更新することを求められます。 [詳細については、こちらを参照してください](../enterprise-users/groups-self-service-management.md)。

---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-email-one-time-passcode-accounts"></a>パブリック プレビュー - ワンタイム パスコードの電子メール送信アカウントを使用した AAD での External Identities セルフサービス サインアップ

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
外部ユーザーは、ワンタイム パスコードの電子メール送信アカウントを使用して、Azure AD のファースト パーティおよび LOB アプリケーションにサインアップまたはサインインできるようになりました。 [詳細については、こちらを参照してください](../external-identities/one-time-passcode.md)。

---

### <a name="general-availability---external-identities-self-service-sign-up"></a>一般提供 - External Identities セルフサービス サインアップ

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
外部ユーザーのセルフサービス サインアップが一般提供になりました。 この新機能により、外部ユーザーはアプリケーションにセルフサービスでサインアップできるようになりました。 

これらの外部ユーザー向けにカスタマイズされたエクスペリエンスを作成できます。これには、登録プロセス中にユーザーに関する情報を収集することや、Facebook や Google などの外部 ID プロバイダーを許可することが含まれます。 また、ID の検証やユーザーの承認など、さまざまな機能のためにサードパーティのクラウド プロバイダーと連携できます。 [詳細については、こちらを参照してください](../external-identities/self-service-sign-up-overview.md)。
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>一般提供 - Azure AD B2C の組み込みポリシーを使用した電話でのサインアップとサインイン

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C
 
組み込みポリシーを使用した B2C の電話でのサインアップとサインインを使用すると、組織の IT 管理者や開発者は、エンド ユーザーが電話番号を使用してサインインおよびサインアップできるユーザー フローを提供できます。 この機能を使用すると、プライバシー ポリシーや使用条件などの免責事項リンクをカスタマイズして、エンド ユーザーがテキスト メッセージでのワンタイム パスコードの受信に進む前にページに表示できます。 [詳細については、こちらを参照してください](../../active-directory-b2c/phone-authentication-user-flows.md)。
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---april-2021"></a>Azure AD アプリケーション ギャラリーで新しいフェデレーション アプリが利用できるようになりました - 2021 年 4 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2021 年 4 月、フェデレーションをサポートする次の 31 個の新しいアプリケーションがアプリ ギャラリーに追加されました

[Zii Travel Azure AD Connect](http://ziitravel.com/)、[Cerby](../saas-apps/cerby-tutorial.md)、[Selflessly](https://app.selflessly.io/sign-in)、[Apollo CX](https://apollo.cxlabs.de/sso/aad)、[Pedagoo](https://account.pedagoo.com/)、[Measureup](https://account.measureup.com/)、[Wistec Education](https://wisteceducation.fi/login/index.php)、[ProcessUnity](../saas-apps/processunity-tutorial.md)、[Cisco Intersight](../saas-apps/cisco-intersight-tutorial.md)、[Codility](../saas-apps/codility-tutorial.md)、[H5mag](https://account.h5mag.com/auth/request-access/ms365)、[Check Point Identity Awareness](../saas-apps/check-point-identity-awareness-tutorial.md)、[Jarvis](https://jarvis.live/login)、[desknet's NEO](../saas-apps/desknets-neo-tutorial.md)、[SDS & Chemical Information Management](../saas-apps/sds-chemical-information-management-tutorial.md)、[Wúru App](../saas-apps/wuru-app-tutorial.md)、[Holmes](../saas-apps/holmes-tutorial.md)、[Tide Multi Tenant](https://gallery.tideapp.co.uk/)、[Telenor](https://admin.smartansatt.telenor.no/)、[Yooz US](https://us1.getyooz.com/?kc_idp_hint=microsoft)、[Mooncamp](https://app.mooncamp.com/#/login)、[inwise SSO](https://app.inwise.com/defaultsso.aspx)、[Ecolab Digital Solutions](https://ecolabb2c.b2clogin.com/account.ecolab.com/oauth2/v2.0/authorize?p=B2C_1A_Connect_OIDC_SignIn&client_id=01281626-dbed-4405-a430-66457825d361&nonce=defaultNonce&redirect_uri=https://jwt.ms&scope=openid&response_type=id_token&prompt=login)、[Taguchi Digital Marketing System](https://login.taguchi.com.au/)、[XpressDox EU Cloud](https://test.xpressdox.com/Authentication/Login.aspx)、[EZSSH](https://docs.keytos.io/getting-started/registering-a-new-tenant/registering_app_in_tenant/)、[EZSSH Client](https://portal.ezssh.io/signup)、[Verto 365](https://www.vertocloud.com/Login/)、[KPN Grip](https://www.grip-on-it.com/)、[AddressLook](https://portal.bbsonlineservices.net/Manage/AddressLook)、[Cornerstone Single Sign-On](../saas-apps/cornerstone-ondemand-tutorial.md)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください。

Azure AD アプリ ギャラリーにアプリケーションを公開する場合は、次の詳細をお読みください: https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---april-2021"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクタ - 2021 年 4 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合
 
新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Bentley - Automatic User Provisioning](../saas-apps/bentley-automatic-user-provisioning-tutorial.md)
- [Boxcryptor](../saas-apps/boxcryptor-provisioning-tutorial.md)
- [BrowserStack シングル サインオン](../saas-apps/browserstack-single-sign-on-provisioning-tutorial.md)
- [Eletive](../saas-apps/eletive-provisioning-tutorial.md)
- [Jostle](../saas-apps/jostle-provisioning-tutorial.md)
- [Olfeo SAAS](../saas-apps/olfeo-saas-provisioning-tutorial.md)
- [Proware](../saas-apps/proware-provisioning-tutorial.md)
- [Segment](../saas-apps/segment-provisioning-tutorial.md)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD での SaaS アプリケーションへのユーザー プロビジョニングの自動化](../app-provisioning/user-provisioning.md)に関するページを参照してください。
 
---

### <a name="introducing-new-versions-of-page-layouts-for-b2c"></a>B2C 用の新しいバージョンのページ レイアウトの導入

**種類:** 変更された機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C
 
Azure AD B2C での B2C シナリオ用の[ページ レイアウト](../../active-directory-b2c/page-layout.md)が更新され、jQuery と Handlebars JS の新しいバージョンを導入することによって、セキュリティ リスクを軽減しています。
 
---

### <a name="updates-to-sign-in-diagnostic"></a>サインイン診断の更新

**種類:** 変更された機能  
**サービス カテゴリ:** レポーティング  
**製品の機能:** 監視とレポート
 
サインイン診断ツールが対応するシナリオの範囲が広がりました。 

この更新により、次のイベント関連のシナリオがサインイン診断結果に含まれるようになりました。 
- エンタープライズ アプリケーション構成の問題イベント。
- エンタープライズ アプリケーション サービス プロバイダー (アプリケーション側) イベント。
- 間違った資格情報イベント。 

これらの結果には、イベントのコンテキストおよび関連する詳細と、これらの問題を解決するために実行するアクションが示されます。 また、詳細なコンテキスト診断がないシナリオに対しては、サインイン診断によって、エラー イベントに関するより説明的な内容が示されます。

詳細については、「[Azure AD におけるサインイン診断とは](../reports-monitoring/overview-sign-in-diagnostics.md)」を参照してください。

---

## <a name="march-2021"></a>2021 年 3 月

### <a name="guidance-on-how-to-enable-support-for-tls-12-in-your-environment-in-preparation-for-upcoming-azure-ad-tls-1011-deprecation"></a>今後の Azure AD TLS 1.0 および 1.1 の非推奨に備えてお使いの環境で TLS 1.2 のサポートを有効にする方法に関するガイダンス

**種類:** 変更の計画  
**サービス カテゴリ:** 該当なし  
**製品の機能:** 標準

Azure Active Directory では、2021 年 6 月 30 日以降、Azure Active Directory の世界各地のリージョンで次のプロトコルが非推奨になります。


- TLS 1.0
- TLS 1.1
- 3DES 暗号スイート (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

影響を受ける環境は次のとおりです。

- Azure 商用クラウド
- Office 365 GCC および WW

詳細については、[Azure AD TLS 1.1 と 1.0 の非推奨に備えてお使いの環境で TLS 1.2 のサポートを有効にする](/troubleshoot/azure/active-directory/enable-support-tls-environment)方法に関する記事を参照してください。

---

### <a name="public-preview---azure-ad-entitlement-management-now-supports-multi-geo-sharepoint-online"></a>パブリック プレビュー - Azure AD のエンタイトルメント管理で、Multi-Geo の SharePoint Online がサポートされるようになりました

**種類:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** エンタイトルメント管理
 
Multi-Geo の SharePoint Online を使用している組織では、特定の Multi-Geo 環境のサイトをエンタイトルメント管理アクセス パッケージに含められるようになりました。 [詳細については、こちらを参照してください](../governance/entitlement-management-catalog-create.md#add-a-multi-geo-sharepoint-site-preview)。

---

### <a name="public-preview---restore-deleted-apps-from-app-registrations"></a>パブリック プレビュー - アプリの登録から削除されたアプリを復元する

**種類:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** 開発者エクスペリエンス
 
Azure portal から、削除されたアプリの登録を表示、復元、および完全に削除できるようになりました。 これは、個人の Microsoft アカウントのアプリケーションではなく、ディレクトリに関連付けられたアプリケーションにのみ適用されます。 [詳細については、こちらを参照してください](../develop/howto-restore-app.md)。
 
---

### <a name="public-preview---new-user-action-in-conditional-access-for-registering-or-joining-devices"></a>パブリック プレビュー - デバイスを登録または参加させるための条件付きアクセスでの新しい "ユーザー操作"

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護
 
 条件付きアクセスで、「デバイスの登録または参加」という新しいユーザー操作が利用できるようになりました。 このユーザー操作を使用すると、Azure AD デバイス登録に対する多要素認証 (MFA) ポリシーを制御できます。 

現時点では、このユーザー操作は、ユーザーがデバイスを Azure AD に登録または参加させるときの制御として MFA を有効にできるだけです。 Azure AD デバイスの登録に依存している、または適用されないその他の制御は、このユーザー操作では無効になっています。 [詳細については、こちらを参照してください](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions)。 
 
---

### <a name="public-preview---optimize-connector-groups-to-use-the-closest-application-proxy-cloud-service"></a>パブリック プレビュー - 最も近いアプリケーション プロキシ クラウド サービスを使用するようにコネクタ グループを最適化する

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** アクセス制御
 
この新しい機能を使用すると、アプリケーションがホストされている最も近いリージョンのアプリケーション プロキシ サービスにコネクタ グループを割り当てることができます。 これにより、アプリがホーム テナントのリージョン以外のリージョンでホストされているシナリオで、アプリのパフォーマンスを向上させることができます。 [詳細については、こちらを参照してください](../app-proxy/application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview)。 
 
---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-email-one-time-passcode-accounts"></a>パブリック プレビュー - ワンタイム パスコードの電子メール送信アカウントを使用した AAD での External Identities セルフサービス サインアップ

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C

外部ユーザーは、ワンタイム パスコードの電子メール送信アカウントを使用して、Azure AD ファースト パーティおよび LOB アプリにサインアップできるようになります。 [詳細については、こちらを参照してください](../external-identities/one-time-passcode.md)。

---

### <a name="public-preview---availability-of-ad-fs-sign-ins-in-azure-ad"></a>パブリック プレビュー - Azure AD で AD FS サインインが利用可能

**種類:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** 監視とレポート
 
AD FS サインイン アクティビティを Azure AD のアクティビティ レポートと統合して、ハイブリッド ID インフラストラクチャの統合ビューを提供できるようになりました。 Azure AD のサインイン レポート、Log Analytics、および Azure Monitor Workbooks を使用すると、AD FS アカウントのロックアウト、不適切なパスワード試行、予期しないサインイン試行のスパイクなど、AAD と AD FS サインインの両方のシナリオについて詳細な分析を行うことができます。

詳細については、「[Connect Health を使用した Azure AD での AD FS サインイン](../hybrid/how-to-connect-health-ad-fs-sign-in.md)」を参照してください。

---

### <a name="general-availability---staged-rollout-to-cloud-authentication"></a>一般提供 - クラウド認証への段階的ロールアウト

**種類:** 新機能  
**サービス カテゴリ:** AD Connect  
**製品の機能:** ユーザー認証
 
クラウド認証への段階的ロールアウトが一般公開されました。 段階的ロールアウト機能を使用すると、パススルー認証 (PTA) やパスワード ハッシュの同期 (PHS) などのクラウド認証方法を使用して、ユーザー グループを選択的にテストできます。 一方、フェデレーション ドメイン内の他のすべてのユーザーは、引き続き AD FS などのフェデレーションサービスや、その他のフェデレーション サービスを使用してユーザーを認証します。 [詳細については、こちらを参照してください](../hybrid/how-to-connect-staged-rollout.md)。

---

### <a name="general-availability---user-type-attribute-can-now-be-updated-in-the-azure-admin-portal"></a>一般提供 - ユーザー タイプ属性を Azure 管理ポータルで更新できるようになりました

**種類:** 新機能  
**サービスカテゴリ:** ユーザー エクスペリエンスと管理  
**製品の機能:** [ユーザー管理]
 
Azure 管理ポータルからユーザー プロファイル情報を更新するときに、Azure AD ユーザーのユーザー タイプを更新できるようになりました。 ユーザー タイプは Microsoft Graph からも更新できます。 詳細については、[ユーザー プロファイル情報の追加または更新](active-directory-users-profile-azure-portal.md)に関する記事を参照してください。
 
---

### <a name="general-availability---replica-sets-for-azure-active-directory-domain-services"></a>一般提供 - Azure Active Directory Domain Services のレプリカ セット

**種類:** 新機能  
**サービス カテゴリ:** Azure AD Domain Services  
**製品の機能:** Azure AD Domain Services
 
Azure Active Directory Domain Services のレプリカ セット機能が一般提供されました。 [詳細については、こちらを参照してください](../../active-directory-domain-services/concepts-replica-sets.md)。
 
---

### <a name="general-availability---collaborate-with-your-partners-using-email-one-time-passcode-in-the-azure-government-cloud"></a>一般提供 - Azure Government クラウドでワンタイム パスコードの電子メール送信を使用してパートナーと共同作業を行う

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
Microsoft Azure Government クラウド内の組織では、ゲストはワンタイム パスコードの電子メール送信を使用して招待の引き換えができるようになりました。 これにより、Azure AD、Microsoft、または Gmail のアカウントを使用していない Azure Government クラウド内のゲスト ユーザーも、共有リソースにサインインするための一時的コードを要求して入力することによって、パートナーと共同作業できます。 [詳細については、こちらを参照してください](../external-identities/one-time-passcode.md#note-for-azure-us-government-customers)。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---march-2021"></a>Azure AD アプリケーション ギャラリーで新しいフェデレーション アプリが利用できるようになりました - 2021 年 3 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2021 年 3 月、フェデレーションをサポートする次の 37 個の新しいアプリケーションがアプリ ギャラリーに追加されました。

[Bambuser Live Video Shopping](https://lcx.bambuser.com/)、[DeepDyve Inc](https://www.deepdyve.com/azure-sso)、[Moqups](../saas-apps/moqups-tutorial.md)、[RICOH Spaces Mobile](https://ricohspaces.app/welcome)、[Flipgrid](https://auth.flipgrid.com/)、[hCaptcha Enterprise](../saas-apps/hcaptcha-enterprise-tutorial.md)、[SchoolStream ASA](https://jsd.schoolstreamk12.com/ASA/ASAlogin.aspx)、[TransPerfect GlobalLink Dashboard](../saas-apps/transperfect-globallink-dashboard-tutorial.md)、[SimplificaCI](https://app.simplificaci.com.br/)、[Thrive LXP](../saas-apps/thrive-lxp-tutorial.md)、[Lexonis TalentScape](../saas-apps/lexonis-talentscape-tutorial.md)、[Exium](../saas-apps/exium-tutorial.md)、[Sapient](../saas-apps/sapient-tutorial.md)、[TrueChoice](../saas-apps/truechoice-tutorial.md)、[RICOH Spaces](https://ricohspaces.app/welcome)、[Saba Cloud](../saas-apps/learning-at-work-tutorial.md)、[Acunetix 360](../saas-apps/acunetix-360-tutorial.md)、[Exceed.ai](../saas-apps/exceed-ai-tutorial.md)、[GitHub Enterprise Managed User](../saas-apps/github-enterprise-managed-user-tutorial.md)、[Enterprise Vault.cloud for Outlook](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=openid%20profile%20User.Read&client_id=7176efe5-e954-4aed-b5c8-f5c85a980d3a&nonce=4b9e1981-1bcb-4938-a283-86f6931dc8cb)、[Smartlook](../saas-apps/smartlook-tutorial.md)、[Accenture Academy](../saas-apps/accenture-academy-tutorial.md)、[Onshape](../saas-apps/onshape-tutorial.md)、[Tradeshift](../saas-apps/tradeshift-tutorial.md)、[JuriBlox](../saas-apps/juriblox-tutorial.md)、[SecurityStudio](../saas-apps/securitystudio-tutorial.md)、[ClicData](https://app.clicdata.com/)、[Evergreen](../saas-apps/evergreen-tutorial.md)、[Patchdeck](https://patchdeck.com/ad_auth/authenticate/)、[FAX.PLUS](../saas-apps/fax.plus-tutorial.md)、[ValidSign](../saas-apps/validsign-tutorial.md)、[AWS Single Sign-on](../saas-apps/aws-single-sign-on-tutorial.md)、[Nura Space](https://dashboard.nuraspace.com/login)、[Broadcom DX SaaS](../saas-apps/broadcom-dx-saas-tutorial.md)、[Interplay Learning](https://skilledtrades.interplaylearning.com/#login)、[SendPro Enterprise](../saas-apps/sendpro-enterprise-tutorial.md)、[FortiSASE SIA](../saas-apps/fortisase-sia-tutorial.md)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください。

Azure AD アプリ ギャラリーにアプリケーションを公開する場合は、次の詳細をお読みください: https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---march-2021"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクタ - 2021 年 3 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合

新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [AWS Single Sign-On](../saas-apps/aws-single-sign-on-provisioning-tutorial.md)
- [Bpanda](../saas-apps/bpanda-provisioning-tutorial.md)
- [Britive](../saas-apps/britive-provisioning-tutorial.md)
- [GitHub Enterprise Managed User](../saas-apps/github-enterprise-managed-user-provisioning-tutorial.md)
- [Grammarly](../saas-apps/grammarly-provisioning-tutorial.md)
- [LogicGate](../saas-apps/logicgate-provisioning-tutorial.md)
- [SecureLogin](../saas-apps/secure-login-provisioning-tutorial.md)
- [TravelPerk](../saas-apps/travelperk-provisioning-tutorial.md)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)に関するページをご覧ください。
 
---

### <a name="introducing-ms-graph-api-for-company-branding"></a>会社のブランド向けに MS Graph API を導入する

**種類:** 変更された機能  
**サービス カテゴリ:** MS Graph  
**製品の機能:** B2B/B2C

[会社のブランド用の MS Graph API](/graph/api/resources/organizationalbrandingproperties) は、Azure AD または Microsoft 365 のログイン エクスペリエンスで使用できます。これを使用すると、プログラムによるブランド化パラメーターの管理が可能になります。

---

### <a name="general-availability---header-based-authentication-sso-with-application-proxy"></a>一般提供 - アプリケーション プロキシによるヘッダーベースの認証 SSO

**種類:** 変更された機能  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** アクセス制御
 
ヘッダーベースの認証に対する Azure AD アプリケーション プロキシのネイティブ サポートが一般公開されました。 この機能を使用すると、追加コンポーネントのデプロイを必要とせずに、必要なユーザー属性をアプリケーションの HTTP ヘッダーとして構成できます。 [詳細については、こちらを参照してください](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md)。

---

### <a name="azure-ad-connect-cloud-sync-general-availability-refresh"></a>Azure AD Connect クラウド同期の一般提供の更新 
**種類:** 変更された機能  
**サービス カテゴリ:** Azure AD Connect クラウド同期 **製品の機能:** ディレクトリ

Azure AD Connect クラウド同期でエージェントが更新されました (バージョン番号 - 1.1.359)。 バグの修正など、エージェントの更新の詳細については、[バージョン履歴](../cloud-sync/reference-version-history.md)を確認してください。 更新されたエージェントを使用すると、クラウド同期のお客様は GMSA コマンドレットを使用して、gMSA アクセス許可をきめ細かいレベルで設定およびリセットできます。 さらに、グループ スコープのフィルター処理を使用したメンバーの同期の制限を 1499 から 50,000 (50K) メンバーに変更しました。 

クラウド同期用に新しく使用可能になった[式ビルダー](../cloud-sync/how-to-expression-builder.md#deploy-the-expression)を確認してください。これは、属性マッピングを使用して AD から Azure AD への属性値の変換を行う場合に、単純な式だけでなく複雑な式を構築するのに役立ちます。

---

### <a name="two-way-sms-for-mfa-server-is-no-longer-supported"></a>MFA Server での双方向 SMS はサポートされなくなりました

**種類:** 非推奨  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティ & 保護
 

MFA Server での双方向 SMS は、最初、2018 年に非推奨になり、2021 年 2 月 24 日以降サポートされなくなります。 管理者は、現在も双方向 SMS を使用しているユーザーに対して、別の方法を有効にする必要があります。

影響を受ける管理者に対して、2020 年 12 月 8 日と 2021 年 1 月 28 日に、電子メール通知と Azure portal Service Health 通知が送信されました。 アラートは、サブスクリプションに関連付けられている、所有者、共同所有者、管理者、およびサービス管理者の各 RBAC の役割に送信されました。 [詳細については、こちらを参照してください](../authentication/how-to-authentication-two-way-sms-unsupported.md)。
 
---
 
## <a name="february-2021"></a>2021 年 2 月

### <a name="email-one-time-passcode-authentication-on-by-default-starting-october-2021"></a>2021 年 10 月以降、既定でワンタイム パスコード認証が電子メールで送信される

**種類:** 変更の計画  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 

2021 年 10 月 31 日以降、Microsoft Azure Active Directory の[電子メールによるワンタイム パスコード認証](../external-identities/one-time-passcode.md)が、B2B コラボレーションのシナリオでアカウントとテナントを招待する既定の方法になります。 今のところ、管理対象外の Azure Active Directory アカウントを使用した招待の引き換えは許可されなくなります。 

---

### <a name="unrequested-but-consented-permissions-will-no-longer-be-added-to-tokens-if-they-would-trigger-conditional-access"></a>要求されていない承認済みの権限が、条件付きアクセスをトリガーする場合、それらがトークンに追加されることはなくなります。

**種類:** 変更の計画  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** プラットフォーム
 
現在、[動的な権限](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent)を使用するアプリケーションには、アクセスを承認したすべての権限が付与されます。 これには、要求されていないアプリケーションが含まれ、条件付きアクセスをトリガーする場合も該当します。 これにより、たとえば、`user.read` のみを要求し、`files.read` も承諾したアプリケーションが、`files.read` 権限に割り当てられた条件付きアクセスを受け入れるように強制されるということが起こります。 

不要な条件付きアクセスのプロンプトの数を減らすため、Azure AD は、要求されていないスコープがアプリケーションに提供される方法を変更しています。 アプリケーションは、明示的に要求した権限に対してのみ、条件付きアクセスをトリガーします。 詳細については、[認証の新機能](../develop/reference-breaking-changes.md#conditional-access-will-only-trigger-for-explicitly-requested-scopes)に関する記事を参照してください。
 
---
 
### <a name="public-preview---use-a-temporary-access-pass-to-register-passwordless-credentials"></a>パブリック プレビュー - 一時アクセス パスを使用してパスワードレスの資格情報を登録する

**種類:** 新機能  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティ & 保護

一時アクセス パスは、強力な資格情報として機能する、時間限定のパスコードです。ユーザーが強力な認証要素 (FIDO2 セキュリティ キーや Microsoft Authenticator など) アプリを紛失したり忘れたりしたときに、サインインして新しい強力な認証方法を登録する必要がある場合に、パスワードレスの資格情報と復元を実現できます。 [詳細については、こちらを参照してください](../authentication/howto-authentication-temporary-access-pass.md)。

---

### <a name="public-preview---keep-me-signed-in-kmsi-in-next-generation-of-user-flows"></a>パブリック プレビュー - 次世代のユーザー フローでの KMSI (サインインしたままにする) 機能

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C

次世代の B2C ユーザー フローでは、[KMSI (サインインしたままにする)](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) 機能がサポートされるようになりました。これにより、Web やネイティブ アプリケーションを使用しているユーザーのセッション期間を、永続的な Cookie を使用することで延長できます。  この機能は、ユーザーがブラウザーを終了して再び開いた後もセッションをアクティブなままにし、ユーザーがサインアウトしたときに取り消します。

---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-msa-accounts"></a>パブリック プレビュー - MSA アカウントを使用した AAD での External Identities セルフサービス サインアップ

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
外部ユーザーは、Microsoft アカウントを使用して Azure AD ファースト パーティおよび LOB アプリにサインインできるようになりました。 [詳細については、こちらを参照してください](../external-identities/self-service-sign-up-overview.md)。

---

### <a name="public-preview---reset-redemption-status-for-a-guest-user"></a>パブリック プレビュー - ゲスト ユーザーの引き換え状態をリセットする

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
引き換え状態をリセットするように、既存の外部ゲスト ユーザーを招待し直すことができるようになりました。これにより、ゲスト ユーザー アカウントをアクセス権を失うことなくそのまま残すことができます。 [詳細については、こちらを参照してください](../external-identities/reset-redemption-status.md)。
 
---

### <a name="public-preview---synchronization-provisioning-apis-now-support-application-permissions"></a>パブリックプレビュー - 同期 (プロビジョニング) API でアプリケーションのアクセス許可をサポート

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
同期 API を呼び出すため、application.readwrite.ownedby をアプリケーションのアクセス許可として使用できるようになりました。 これは、Azure AD からサードパーティ アプリケーション (AWS、Data Bricks など) にプロビジョニングする場合のみサポートされます。 HR プロビジョニング (Workday / Successfactors) や Cloud Sync (AD から Azure AD) については現在サポートされていません。 [詳細については、こちらを参照してください](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true)。
 
---

### <a name="general-availability---authentication-policy-administrator-built-in-role"></a>一般提供 - 認証ポリシー管理者組み込みロール

**種類:** 新機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** アクセス制御
 
このロールを持つユーザーは、認証方法のポリシー、テナント全体の MFA 設定、およびパスワード保護ポリシーを構成できます。 このロールは、パスワード保護設定を管理するためのアクセス許可を付与します (スマート ロックアウトの構成とカスタムの禁止パスワード リストの更新)。 [詳細については、こちらを参照してください](../roles/permissions-reference.md#authentication-policy-administrator)。

---

### <a name="general-availability---user-collections-on-my-apps-are-available-now"></a>一般提供 - マイ アプリのユーザー コレクションを使用可能

**種類:** 新機能  
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** エンド ユーザー エクスペリエンス
 
ユーザーは、マイ アプリのアプリ ランチャーで独自のアプリ グループを作成できるようになりました。 共有されているコレクションを並べ替えたり、非表示にすることも管理者によって可能です。 [詳細については、こちらを参照してください](../user-help/my-apps-portal-user-collections.md)。

---

### <a name="general-availability---autofill-in-authenticator"></a>一般提供 - Authenticator のオートフィル

**種類:** 新機能  
**サービスカテゴリ:** Microsoft Authenticator アプリ  
**製品の機能:** ID のセキュリティ & 保護
 
Microsoft Authenticator は、多要素認証 (MFA) とアカウント管理機能を提供しているほか、ユーザーが自分のモバイル機器 (iOS および Android) でサイトやアプリにアクセスする際にパスワードのオートフィルを実現するようになりました。 

Authenticator でオートフィルを使用するには、ユーザーが自分の個人 Microsoft アカウントを Authenticator に追加し、それを使用してパスワードを同期する必要があります。 現時点では、職場や学校のアカウントを使用してパスワードを同期することはできません。 [詳細については、こちらを参照してください](../user-help/user-help-auth-app-faq.md#autofill-for-it-admins)。

---

### <a name="general-availability---invite-internal-users-to-b2b-collaboration"></a>一般提供 - B2B コラボレーションへの内部ユーザーの招待

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
お客様は、既存の内部アカウントに招待状を送信する代わりに、内部ゲストを招待して B2B コラボレーションを使用できるようになりました。 これにより、ユーザーのオブジェクト ID、UPN、グループ メンバーシップ、アプリの割り当てを保持することができます。 [詳細については、こちらを参照してください](../external-identities/invite-internal-users.md)。

---

### <a name="general-availability---domain-name-administrator-built-in-role"></a>一般提供 - ドメイン名管理者組み込みロール

**種類:** 新機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** アクセス制御
 
このロールのユーザーは、ドメイン名の管理 (読み取り、追加、検証、更新、および削除) を行うことができます。 また、ユーザー、グループ、およびアプリケーションに関するディレクトリ情報も読み取ることができます。これらのオブジェクトにはドメインの依存関係があるためです。 

オンプレミス環境の場合、このロールを持つユーザーは、関連付けられたユーザーが常にオンプレミスで認証されるように、フェデレーションのドメイン名を構成できます。 これらのユーザーは、シングル サインオンを介してオンプレミスのパスワードを使用して Azure AD ベースのサービスにサインインできます。 フェデレーション設定を Azure AD Connect 経由で同期する必要があるため、ユーザーは Azure AD Connect を管理する権限も持っています。 [詳細については、こちらを参照してください](../roles/permissions-reference.md#domain-name-administrator)。
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---february-2021"></a>Azure AD アプリケーション ギャラリーで新しいフェデレーション アプリが利用できるようになりました - 2021 年 2 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2021 年 2 月には、フェデレーションをサポートする次の 37 個の新しいアプリケーションがアプリ ギャラリーに追加されました。

[Loop Messenger Extension](https://loopworks.com/loop-flow-messenger/)、[Silverfort Azure AD Adapter](http://www.silverfort.com/)、[Interplay Learning](https://skilledtrades.interplaylearning.com/#login)、[Nura Space](https://dashboard.nuraspace.com/login)、[Yooz EU](https://eu1.getyooz.com/?kc_idp_hint=microsoft)、[UXPressia](https://uxpressia.com/users/sign-in)、[introDus Pre- and Onboarding Platform](http://app.introdus.dk/login)、[Happybot](https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize?client_id=34353e1e-dfe5-4d2f-bb09-2a5e376270c8&response_type=code&redirect_uri=https://api.happyteams.io/microsoft/integrate&response_mode=query&scope=offline_access%20User.Read%20User.Read.All)、[LeaksID](https://app.leaksid.com/)、[ShiftWizard](http://www.shiftwizard.com/)、[PingFlow SSO](https://app.pingview.io/)、[Swiftlane](https://admin.swiftlane.com/login)、[Quasydoc SSO](https://www.quasydoc.eu/login)、[Fenwick Gold Account](https://businesscentral.dynamics.com/)、[SeamlessDesk](https://www.seamlessdesk.com/login)、[Learnsoft LMS & TMS](http://www.learnsoft.com/)、[P-TH+](https://p-th.jp/)、[myViewBoard](https://api.myviewboard.com/auth/microsoft/)、[Tartabit IoT Bridge](https://bridge-us.tartabit.com/)、[AKASHI](../saas-apps/akashi-tutorial.md)、[Rewatch](../saas-apps/rewatch-tutorial.md)、[Zuddl](../saas-apps/zuddl-tutorial.md)、[Parkalot - Car park management](../saas-apps/parkalot-car-park-management-tutorial.md)、[HSB ThoughtSpot](../saas-apps/hsb-thoughtspot-tutorial.md)、[IBMid](../saas-apps/ibmid-tutorial.md)、[SharingCloud](../saas-apps/sharingcloud-tutorial.md)、[PoolParty Semantic Suite](../saas-apps/poolparty-semantic-suite-tutorial.md)、[GlobeSmart](../saas-apps/globesmart-tutorial.md)、[Samsung Knox and Business Services](../saas-apps/samsung-knox-and-business-services-tutorial.md)、[Penji](../saas-apps/penji-tutorial.md)、[Kendis- Scaling Agile Platform](../saas-apps/kendis-scaling-agile-platform-tutorial.md)、[Maptician](../saas-apps/maptician-tutorial.md)、[Olfeo SAAS](../saas-apps/olfeo-saas-tutorial.md)、[Sigma Computing](../saas-apps/sigma-computing-tutorial.md)、[CloudKnox Permissions Management Platform](../saas-apps/cloudknox-permissions-management-platform-tutorial.md)、[Klaxoon SAML](../saas-apps/klaxoon-saml-tutorial.md)、[Enablon](../saas-apps/enablon-tutorial.md)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください。

Azure AD アプリ ギャラリーにアプリケーションを公開する場合は、次の詳細をお読みください: https://aka.ms/AzureADAppRequest

--- 

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2021"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクター - 2021 年 2 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合
 

新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Atea](../saas-apps/atea-provisioning-tutorial.md)
- [Getabstract](../saas-apps/getabstract-provisioning-tutorial.md)
- [HelloID](../saas-apps/helloid-provisioning-tutorial.md)
- [Hoxhunt](../saas-apps/hoxhunt-provisioning-tutorial.md)
- [Iris Intranet](../saas-apps/iris-intranet-provisioning-tutorial.md)
- [Preciate](../saas-apps/preciate-provisioning-tutorial.md)

詳細については、[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングの自動化](../app-provisioning/user-provisioning.md)に関する記事を参照してください。

---

### <a name="general-availability---10-azure-active-directory-roles-now-renamed"></a>一般提供 - 10 件の Azure Active Directory ロールの名前変更

**種類:** 変更された機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** アクセス制御
 
10 件の Azure AD 組み込みロールの名前が変更され、[Microsoft 365 管理センター](/microsoft-365/admin/microsoft-365-admin-center-preview)、[Azure AD ポータル](https://portal.azure.com/)、[Microsoft Graph](https://developer.microsoft.com/graph/) で統一されました。 新しいロールの詳細については、「[Azure Active Directory での管理者ロールのアクセス許可](../roles/permissions-reference.md#all-roles)」を参照してください。

![MS Graph API と Azure portal のロール名、および API、Azure portal、Mac 全体で提案された最終的な名前を示す表。](media/whats-new/roles-table-rbac.png)

---

### <a name="new-company-branding-in-mfasspr-combined-registration"></a>MFA/SSPR の統合登録での新しい会社のブランディングの表示

**種類:** 変更された機能  
**サービスカテゴリ:** ユーザー エクスペリエンスと管理  
**製品の機能:** エンド ユーザー エクスペリエンス
 
これまで、Azure Active Directory サインイン ページで会社のロゴは使用されていませんでした。 このたび、会社のブランディングが、MFA/SSPR の統合登録の左上に配置されました。 会社のブランディングには、自分のサインインとセキュリティ情報ページにも表示されます。 [詳細については、こちらを参照してください](../fundamentals/customize-branding.md)。

---

### <a name="general-availability---second-level-manager-can-be-set-as-alternate-approver"></a>一般提供 - 別の承認者として第 2 レベルのマネージャーを設定できる

**種類:** 変更された機能  
**サービス カテゴリ:** ユーザー アクセス管理  
**製品の機能:** エンタイトルメント管理
 
エンタイトルメント管理で、承認者を選択するときに追加のオプションが使用できるようになりました。 [最初の承認者] として [承認者としてのマネージャー] を選択した場合は、別の承認者フィールドで選択できる追加オプション [別の承認者としての第 2 レベルのマネージャー] を使用できます。 このオプションを選択した場合は、システムが第 2 レベルのマネージャーを見つけられない場合に、要求の転送先のフォールバック承認者を追加する必要があります。 [詳細については、こちらを参照してください](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)。
 
---

### <a name="authentication-methods-activity-dashboard"></a>認証方法アクティビティのダッシュボード

**種類:** 変更された機能  
**サービス カテゴリ:** レポーティング  
**製品の機能:** 監視とレポート
 

[認証方法の更新] アクティビティダッシュボードでは、管理者は、テナントの認証方法の登録と使用状況アクティビティの概要を管理できます。 レポートには、各方法に登録されているユーザー数と、サインインおよびパスワードのリセット時に使用される方法の概要が表示されます。 [詳細については、こちらを参照してください](../authentication/howto-authentication-methods-activity.md)。
 
---

### <a name="refresh-and-session-token-lifetimes-configurability-in-configurable-token-lifetime-ctl-are-retired"></a>構成可能なトークンの有効期間 (CTL) での更新とセッション トークンの有効期間の構成を廃止

**種類:** 非推奨  
**サービス カテゴリ:** その他  
**製品の機能:** ユーザー認証
 
CTL での更新とセッション トークンの有効期間の構成は廃止されました。 Azure Active Directory では、既存のポリシーの更新およびセッション トークンの構成が考慮されなくなくなりました。 [詳細については、こちらを参照してください](../develop/active-directory-configurable-token-lifetimes.md#token-lifetime-policies-for-refresh-tokens-and-session-tokens)。
 
---
 
## <a name="january-2021"></a>2021 年 1 月

### <a name="secret-token-will-be-a-mandatory-field-when-configuring-provisioning"></a>シークレット トークンがプロビジョニングの構成時に必須フィールドになる

**種類:** 変更の計画  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理

以前は、カスタム/BYOA アプリケーションでプロビジョニングを設定するときに、シークレット トークン フィールドを空のままにすることができました。 この機能は、テストに使用することのみを目的としていました。 このフィールドが必須になるように、UI が更新されます。 

お客様は、ブラウザーの URL で機能フラグを使用することで、このテスト目的用の要件を回避できます。 [詳細については、こちらを参照してください](../app-provisioning/use-scim-to-provision-users-and-groups.md#authorization-to-provisioning-connectors-in-the-application-gallery)。
 
---

### <a name="public-preview---customize-and-configure-android-shared-devices-for-frontline-workers-at-scale"></a>パブリック プレビュー - 現場担当者用の Android 共有デバイスを大規模にカスタマイズして構成する

**種類:** 新機能  
**サービス カテゴリ:** デバイスの登録と管理  
**製品の機能:** ID のセキュリティ & 保護
 
Azure AD と Microsoft Endpoint Manager チームが合同で、現場担当者のデバイスをカスタマイズし、スケーリングし、セキュリティで保護するための機能を提供しました。

次のプレビュー機能を使用すると、以下を行うことができます。
- Microsoft Endpoint Manager を使用して Android 共有デバイスを大規模にプロビジョニングする
- デバイス ベースの条件付きアクセスを使用して、シフト作業者のアクセスをセキュリティで保護する
- 管理ホーム画面を使用してシフト作業者のサインイン エクスペリエンスをカスタマイズする

詳細については、「[現場担当者用の共有デバイスを大規模にカスタマイズして構成する](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-and-configure-shared-devices-for-firstline-workers-at/ba-p/1751708)」を参照してください。

---

### <a name="public-preview---provisioning-logs-can-now-be-downloaded-as-a-csv-or-json"></a>パブリック プレビュー - プロビジョニング ログを CSV または JSON としてダウンロードできるようになった

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理

お客様は、UI と Graph API を使用して、プロビジョニング ログを CSV または JSON ファイルとしてダウンロードできます。 詳細については、[Azure Active Directory ポータルのプロビジョニング レポート](../reports-monitoring/concept-provisioning-logs.md)に関する記事を参照してください。

---

### <a name="public-preview---assign-cloud-groups-to-azure-ad-custom-roles-and-admin-unit-scoped-roles"></a>パブリック プレビュー - クラウド グループを Azure AD カスタム ロールと管理単位スコープ ロールに割り当てる

**種類:** 新機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** アクセス制御
 
お客様は、クラウド グループを Azure AD カスタム ロールまたは管理単位スコープ ロールに割り当てることができます。 この機能の使用方法については、「[クラウド グループを使用して Azure Active Directory でロールの割り当てを管理する](../roles/groups-concept.md)」を参照してください。

---

### <a name="general-availability---azure-ad-connect-cloud-sync-previously-known-as-cloud-provisioning"></a>一般提供 - Azure AD Connect クラウド同期 (以前はクラウド プロビジョニングと呼ばれていました)

**種類:** 新機能  
**サービス カテゴリ:** Azure AD Connect クラウド同期  
**製品の機能:** ID ライフサイクル管理
 
Azure AD Connect クラウド同期が、すべてのお客様に一般提供されるようになりました。

Azure AD Connect クラウドにより、変換ロジックの大量の処理がクラウドに移行され、オンプレミスでの占有領域が削減されます。 さらに、同期の可用性を高めるために、複数の軽量エージェントを配置できます。 [詳細については、こちらを参照してください](https://aka.ms/cloudsyncGA)。
 
---
### <a name="general-availability---attack-simulation-administrator-and-attack-payload-author-built-in-roles"></a>一般提供 - 攻撃シミュレーション管理者と攻撃ペイロード作成者の組み込みロール

**種類:** 新機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** アクセス制御
 
ロール ベースのアクセス制御で、2 つの新しいロールである攻撃シミュレーション管理者と攻撃ペイロード作成者をユーザーに割り当てることができます。 

[攻撃シミュレーション管理者](../roles/permissions-reference.md#attack-simulation-administrator)ロールのユーザーは、テナント内のすべてのシミュレーションにアクセスでき、さらに以下を行うことができます。
- 攻撃のシミュレーション作成のすべての側面を作成および管理する
- シミュレーションの起動/スケジューリング
-  シミュレーションの結果を確認する 

[攻撃ペイロード作成者](../roles/permissions-reference.md#attack-payload-author)ロールのユーザーは、攻撃のペイロードを作成することはできますが、それらを実際に起動することやスケジュールすることはできません。 攻撃のペイロードは、それらを使用してシミュレーション作成できるテナントの管理者全員が利用できます。

---

### <a name="general-availability---usage-summary-reports-reader-built-in-role"></a>一般提供 - 使用状況の概要のレポート閲覧者組み込みロール

**種類:** 新機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** アクセス制御
 
使用状況の概要のレポート閲覧者ロールのユーザーは、使用状況と生産性スコアについて、Microsoft 365 管理センターでテナント レベルの集計データと関連する分析情報にアクセスできます。 ただし、ユーザー レベルの詳細や分析情報にはアクセスできません。 

2 つのレポートについて、Microsoft 365 管理センターでは、テナント レベルの集計データとユーザー レベルの詳細が区別されます。 このロールにより、個々のユーザーを特定できるデータに対する保護レイヤーが追加されます。 [詳細については、こちらを参照してください](../roles/permissions-reference.md#usage-summary-reports-reader)。

---

### <a name="general-availability---require-app-protection-policy-grant-in-azure-ad-conditional-access"></a>一般提供 - Azure AD 条件付きアクセスでの、アプリ保護ポリシーを必須にする許可

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護
 
"アプリ保護ポリシーを必須にする" の Azure AD 条件付きアクセスの許可が GA になりました。 

このポリシーにより、以下の機能が提供されます。
- Intune App Protection をサポートするモバイル アプリケーションを使用している場合にだけアクセスを許可する
- ユーザーがモバイル アプリケーションに Intune App Protection ポリシーを配布している場合にだけアクセスを許可する

アプリ保護の条件付きアクセス ポリシーを設定する方法の詳細については、[こちら](../conditional-access/app-protection-based-conditional-access.md)を参照してください。
 
---

### <a name="general-availability---email-one-time-passcode"></a>一般提供 - ワンタイム パスコードをメールで送信する

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
メール OTP を使用すると、世界中の組織が、メールでリンクまたは招待状を送信することで、だれとでもコラボレーションすることができます。 招待されたユーザーは、自分のメールに送信されたワンタイム パスコードを使用して自分の身元を証明して、パートナーのリソースにアクセスできます。 [詳細については、こちらを参照してください](../external-identities/one-time-passcode.md)。 
 
---

 ### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2021"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクター - 2021 年 1 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合
 
新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。
- [Fortes Change Cloud](../saas-apps/fortes-change-cloud-provisioning-tutorial.md)
- [Gtmhub](../saas-apps/gtmhub-provisioning-tutorial.md)
- [monday.com](../saas-apps/mondaycom-provisioning-tutorial.md)
- [Splashtop](../saas-apps/splashtop-provisioning-tutorial.md)
- [Templafy OpenID Connect](../saas-apps/templafy-openid-connect-provisioning-tutorial.md)
- [WEDO](../saas-apps/wedo-provisioning-tutorial.md)

詳細については、「[Azure AD での SaaS アプリ ユーザー プロビジョニングの自動化とは](../app-provisioning/user-provisioning.md)」を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---january-2021"></a>Azure AD アプリケーション ギャラリーで新しいフェデレーション アプリが利用できるようになりました - 2021 年 1 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2021 年 1 月には、フェデレーションをサポートする次の 29 個の新しいアプリケーションがアプリ ギャラリーに追加されました。

[mySCView](https://dev.myscview.com/)、[Talentech](https://talentech.com/contact/)、[Bipsync](https://www.bipsync.com/)、[OroTimesheet](https://app.orotimesheet.com/login.php)、[Mio](https://app.m.io/auth/install/microsoft?scopetype=hub)、[Sovelto Easy](https://login.soveltoeasy.fi/)、[Supportbench](https://account.supportbench.net/agent/login/)、[Bienvenue Formation](https://formation.bienvenue.pro/login)、[AIDA Healthcare SSO](https://aidaforparents.com/login/organizations)、[International SOS Assistance 製品](../saas-apps/international-sos-assistance-products-tutorial.md)、[NAVEX One](../saas-apps/navex-one-tutorial.md)、[LabLog](../saas-apps/lablog-tutorial.md)、[Oktopost SAML](../saas-apps/oktopost-saml-tutorial.md)、[EPHOTO DAM](../saas-apps/ephoto-dam-tutorial.md)、[Notion](../saas-apps/notion-tutorial.md)、[Syndio](../saas-apps/syndio-tutorial.md)、[Yello Enterprise](../saas-apps/yello-enterprise-tutorial.md)、[Timeclock 365 SAML](../saas-apps/timeclock-365-saml-tutorial.md)、[Nalco E-data](https://www.ecolab.com/)、[Vacancy Filler](https://app.vacancy-filler.co.uk/VFMVC/Account/Login)、[Synerise AI Growth Ecosystem](../saas-apps/synerise-ai-growth-ecosystem-tutorial.md)、[Imperva Data Security](../saas-apps/imperva-data-security-tutorial.md)、[Illusive Networks](../saas-apps/illusive-networks-tutorial.md)、[Proware](../saas-apps/proware-tutorial.md)、[Splan Visitor](../saas-apps/splan-visitor-tutorial.md)、[Aruba User Experience Insight](../saas-apps/aruba-user-experience-insight-tutorial.md)、[Contentsquare SSO](../saas-apps/contentsquare-sso-tutorial.md)、[Perimeter 81](../saas-apps/perimeter-81-tutorial.md)、[Burp Suite Enterprise Edition](../saas-apps/burp-suite-enterprise-edition-tutorial.md)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください

Azure AD アプリ ギャラリーにアプリケーションを公開する場合は、こちらの詳細 (https://aka.ms/AzureADAppRequest ) をお読みください 

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>パブリック プレビュー - 別の承認者として第 2 レベルのマネージャーを設定できる

**種類:** 変更された機能  
**サービス カテゴリ:** ユーザー アクセス管理  
**製品の機能:** エンタイトルメント管理
 
エンタイトルメント管理で、承認者を選択するときに追加のオプションが使用できるようになりました。 [最初の承認者] として [承認者としてのマネージャー] を選択した場合は、別の承認者フィールドで選択できる追加オプション [別の承認者としての第 2 レベルのマネージャー] を使用できます。 このオプションを選択した場合は、システムが第 2 レベルのマネージャーを見つけられない場合に、要求の転送先のフォールバック承認者を追加する必要があります。 [詳細情報](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)
 
---

### <a name="general-availability---navigate-to-teams-directly-from-my-access-portal"></a>一般提供 - マイ アクセス ポータルから直接 Teams に移動する

**種類:** 変更された機能  
**サービス カテゴリ:** ユーザー アクセス管理  
**製品の機能:** エンタイトルメント管理
 
マイ アクセス ポータルから直接 Teams を起動できるようになりました。 

それを行うには、[マイ アクセス] (https://myaccess.microsoft.com/) ) にサインインし、[アクセス パッケージ] に移動します。次に、[アクティブ] タブに移動して、既にアクセス権があるすべてのアクセス パッケージを表示します。 選択したアクセス パッケージを展開して Teams をマウスでポイントすると、[開く] ボタンをクリックして起動できるようになります。 [詳細については、こちらを参照してください](../governance/entitlement-management-request-access.md)。
 
---

### <a name="improved-logging--end-user-prompts-for-risky-guest-users"></a>危険なゲスト ユーザーのログとエンドユーザー プロンプトが向上

**種類:** 変更された機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護
 

危険なゲスト ユーザーのログとエンドユーザー プロンプトが更新されました。 詳細については、「[Identity Protection と B2B ユーザー](../identity-protection/concept-identity-protection-b2b.md)」を参照してください。
 
---
 
## <a name="december-2020"></a>2020 年 12 月

### <a name="public-preview---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>パブリック プレビュー - 組み込みポリシーを使用した Azure AD B2C Phone のサインアップとサインイン

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C
 
組み込みポリシーを使用した B2C Phone のサインアップとサインインを使用すると、組織の IT 管理者や開発者は、エンド ユーザーが電話番号を使用してサインインおよびサインアップできるユーザー フローを提供できます。 詳細については、「[ユーザー フローの電話でのサインアップとサインインを設定する (プレビュー)](../../active-directory-b2c/phone-authentication-user-flows.md)」を参照してください。

---

### <a name="general-availability---security-defaults-now-enabled-for-all-new-tenants-by-default"></a>一般提供 - すべての新規テナントに対して既定で有効にされるようになったセキュリティの既定値群

**種類:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** ID のセキュリティ & 保護
 
ユーザー アカウントを保護するため、2020 年 11 月 12 日以降に作成されるすべての新しいテナントについては、セキュリティの既定値群が有効になります。 セキュリティの既定値群により、次のような複数のポリシーが適用します。
- すべてのユーザーと管理者は、Microsoft Authenticator アプリを使用して MFA に登録する必要があります
- クリティカルな管理者ロールは、サインインするたびに MFA を使用する必要があります。 他のすべてのユーザーは、必要に応じて MFA の入力を求められます。 
- レガシ認証は、テナント全体でブロックされます。 

詳細については、「[セキュリティの既定値群とは](../fundamentals/concept-fundamentals-security-defaults.md)」を参照してください。

---

### <a name="general-availability---support-for-groups-with-up-to-250k-members-in-aadconnect"></a>一般提供 - AADConnect での最大 250,000 メンバーが含まれるグループのサポート

**種類:** 変更された機能  
**サービス カテゴリ:** AD Connect  
**製品の機能:** ID ライフサイクル管理
 
Microsoft がデプロイした Azure AD Connect 用の新しいエンドポイント (API) では、Azure Active Directory に対する同期サービス操作のパフォーマンスが向上しています。 新しい [V2 エンドポイント](../hybrid/how-to-connect-sync-endpoint-api-v2.md)を使用すると、Azure AD に対するエクスポートとインポートのパフォーマンスが明らかに向上していることがわかります。 この新しいエンドポイントでは、次のシナリオがサポートされています。

- メンバー数が最大 250,000 までのグループの同期
- Azure AD に対するエクスポートとインポートのパフォーマンスの向上

---

### <a name="general-availability---entitlement-management-available-for-tenants-in-azure-china-cloud"></a>一般提供 - Azure China クラウドのテナントに利用できるエンタイトルメント管理

**種類:** 新機能  
**サービス カテゴリ:** ユーザー アクセス管理  
**製品の機能:** エンタイトルメント管理
 

Azure China クラウドのすべてのテナントで、エンタイトルメント管理の機能を使用できるようになりました。 詳細については、[ID ガバナンスのドキュメント](https://docs.azure.cn/zh-cn/active-directory/governance/)に関するサイトを参照してください。

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---december-2020"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクタ - 2020 年 12 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合

新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Bizagi Studio for Digital Process Automation](../saas-apps/bizagi-studio-for-digital-process-automation-provisioning-tutorial.md)
- [CybSafe](../saas-apps/cybsafe-provisioning-tutorial.md)
- [GroupTalk](../saas-apps/grouptalk-provisioning-tutorial.md)
- [PaperCut Cloud Print Management](../saas-apps/papercut-cloud-print-management-provisioning-tutorial.md)
- [Parsable](../saas-apps/parsable-provisioning-tutorial.md)
- [Shopify Plus](../saas-apps/shopify-plus-provisioning-tutorial.md)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)に関するページをご覧ください。
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---december-2020"></a>Azure AD アプリケーション ギャラリーで新しいフェデレーション アプリが利用できるようになりました - 2020 年 12 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2020 年 12 月には、フェデレーションをサポートする次の 18 個の新しいアプリケーションがアプリ ギャラリーに追加されました。

[AwareGo](../saas-apps/awarego-tutorial.md)、[HowNow SSO](https://gethownow.com/)、[ZyLAB ONE Legal Hold](https://www.zylab.com/en/product/legal-hold)、[Guider](http://www.guider-ai.com/)、[Softcrisis](https://www.softcrisis.se/sv/)、[Pims 365](http://www.omega365.com/pims)、[InformaCast](../saas-apps/informacast-tutorial.md)、[RetrieverMediaDatabase](../saas-apps/retrievermediadatabase-tutorial.md)、[vonage](../saas-apps/vonage-tutorial.md)、[Count Me In - Operations Dashboard](../saas-apps/count-me-in-operations-dashboard-tutorial.md)、[ProProfs Knowledge Base](../saas-apps/proprofs-knowledge-base-tutorial.md)、[RightCrowd Workforce Management](../saas-apps/rightcrowd-workforce-management-tutorial.md)、[JLL TRIRIGA](../saas-apps/jll-tririga-tutorial.md)、[Shutterstock](../saas-apps/shutterstock-tutorial.md)、[FortiWeb Web Application Firewall](../saas-apps/linkedin-talent-solutions-tutorial.md)、[LinkedIn Talent Solutions](../saas-apps/linkedin-talent-solutions-tutorial.md)、[Equinix Federation App](../saas-apps/equinix-federation-app-tutorial.md)、[KFAdvance](../saas-apps/kfadvance-tutorial.md)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください

Azure AD アプリ ギャラリーにアプリケーションを公開する場合は、こちらの詳細 (https://aka.ms/AzureADAppRequest ) をお読みください

---

### <a name="navigate-to-teams-directly-from-my-access-portal"></a>マイ アクセス ポータルから直接 Teams に移動する

**種類:** 変更された機能  
**サービス カテゴリ:** ユーザー アクセス管理 **製品の機能:** エンタイトルメント管理

マイ アクセス ポータルから直接 Teams を起動できるようになりました。 それを行うには、[[マイ アクセス]](https://myaccess.microsoft.com/) にサインインし、 **[アクセス パッケージ]** に移動します。次に、 **[アクティブ]** タブに移動して、既にアクセス権があるすべてのアクセス パッケージを表示します。 アクセス パッケージを展開して Teams をマウスでポイントすると、 **[開く]** ボタンをクリックして起動できるようになります。 

マイ アクセス ポータルの使用の詳細については、[Azure AD エンタイトルメント管理でのアクセス パッケージへのアクセスの要求](../governance/entitlement-management-request-access.md#sign-in-to-the-my-access-portal)に関するページに移動してください。

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>パブリック プレビュー - 別の承認者として第 2 レベルのマネージャーを設定できる

**種類:** 変更された機能  
**サービス カテゴリ:** ユーザー アクセス管理  
**製品の機能:** エンタイトルメント管理

エンタイトルメント管理の承認プロセスで追加オプションを使用できるようになりました。 [最初の承認者] として [承認者としてのマネージャー] を選択した場合は、別の承認者フィールドで選択できる追加オプション [別の承認者としての第 2 レベルのマネージャー] を使用できます。 このオプションを選択した場合は、システムが第 2 レベルのマネージャーを見つけられない場合に、要求の転送先のフォールバック承認者を追加する必要があります。

詳細については、[Azure AD エンタイトルメント管理でのアクセス パッケージの承認設定の変更](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)に関するページに移動してください。

--- 

## <a name="november-2020"></a>2020 年 11 月

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation"></a>Azure Active Directory TLS 1.0、TLS 1.1、3DES の非推奨化

**種類:** 変更の計画  
**サービス カテゴリ:** すべての Azure AD アプリケーション  
**製品の機能:** 標準

Azure Active Directory では、2021 年 6 月 30 日以降、Azure Active Directory の世界各地のリージョンで次のプロトコルが非推奨になります。

- TLS 1.0
- TLS 1.1
- 3DES 暗号スイート (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

影響を受ける環境は次のとおりです。
- Azure 商用クラウド
- Office 365 GCC および WW

非推奨のプロトコルを削除するためのガイダンスについては、[Azure AD TLS 1.1 と 1.0 の非推奨に備えて、お使いの環境で TLS 1.2 のサポートを有効にする](/troubleshoot/azure/active-directory/enable-support-tls-environment)方法に関する記事を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---november-2020"></a>Azure AD アプリケーション ギャラリーで新しいフェデレーション アプリが利用できるようになりました - 2020 年 11 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2020 年 11 月には、フェデレーションをサポートする次の 52 個の新しいアプリケーションがアプリ ギャラリーに追加されました。

[Travel & Expense Management](https://app.expenseonce.com/Account/Login)、[Tribeloo](../saas-apps/tribeloo-tutorial.md)、[Itslearning File Picker](https://pmteam.itslearning.com/)、[Crises Control](../saas-apps/crises-control-tutorial.md)、[CourtAlert](https://www.courtalert.com/)、[StealthMail](https://stealthmail.com/)、[Edmentum - Study Island](https://app.studyisland.com/cfw/login/)、[Virtual Risk Manager](../saas-apps/virtual-risk-manager-tutorial.md)、[TIMU](../saas-apps/timu-tutorial.md)、[Looker Analytics Platform](../saas-apps/looker-analytics-platform-tutorial.md)、[Talview - Recruit, Real Time Translator](https://recruit.talview.com/login)、[Klaxoon](https://access.klaxoon.com/login)、[Podbean](../saas-apps/podbean-tutorial.md)、[zcal](https://zcal.co/signup)、[expensemanager](https://api.expense-manager.com/)、[Netsparker Enterprise](../saas-apps/netsparker-enterprise-tutorial.md)、[En-trak Tenant Experience Platform](https://portal.en-trak.app/)、[Appian](../saas-apps/appian-tutorial.md)、[Panorays](../saas-apps/panorays-tutorial.md)、[Builterra](https://portal.builterra.com/)、[EVA Check-in](https://my.evacheckin.com/organization)、[HowNow WebApp SSO](../saas-apps/hownow-webapp-sso-tutorial.md)、[Coupa Risk Assess](../saas-apps/coupa-risk-assess-tutorial.md)、[Lucid (すべての製品)](../saas-apps/lucid-tutorial.md)、[GoBright](https://portal.brightbooking.eu/)、[SailPoint IdentityNow](../saas-apps/sailpoint-identitynow-tutorial.md)、[Resource Central](../saas-apps/resource-central-tutorial.md)、[UiPathStudioO365App](https://www.uipath.com/product/platform)、[Jedox](../saas-apps/jedox-tutorial.md)、[Cequence Application Security](../saas-apps/cequence-application-security-tutorial.md)、[PerimeterX](../saas-apps/perimeterx-tutorial.md)、[TrendMiner](../saas-apps/trendminer-tutorial.md)、[Lexion](../saas-apps/lexion-tutorial.md)、[WorkWare](../saas-apps/workware-tutorial.md)、[ProdPad](../saas-apps/prodpad-tutorial.md)、[AWS ClientVPN](../saas-apps/aws-clientvpn-tutorial.md)、[AppSec Flow SSO](../saas-apps/appsec-flow-sso-tutorial.md)、[Luum](../saas-apps/luum-tutorial.md)、[Freight Measure](https://www.gpcsl.com/freight.html)、[Terraform Cloud](../saas-apps/terraform-cloud-tutorial.md)、[Nature Research](../saas-apps/nature-research-tutorial.md)、[Play Digital Signage](https://login.playsignage.com/login)、[RemotePC](../saas-apps/remotepc-tutorial.md)、[Prolorus](../saas-apps/prolorus-tutorial.md)、[Hirebridge ATS](../saas-apps/hirebridge-ats-tutorial.md)、[Teamgage](https://www.teamgage.com/Account/ExternalLoginAzure)、[Roadmunk](../saas-apps/roadmunk-tutorial.md)、[Sunrise Software Relations CRM](https://cloud.relations-crm.com/)、[Procaire](../saas-apps/procaire-tutorial.md)、[Mentor® by eDriving:Business](https://www.edriving.com/)、[Gradle Enterprise](https://gradle.com/)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください

Azure AD アプリ ギャラリーにアプリケーションを公開する場合は、こちらの詳細 (https://aka.ms/AzureADAppRequest ) をお読みください

---

### <a name="public-preview---custom-roles-for-enterprise-apps"></a>パブリック プレビュー - エンタープライズ アプリのカスタム ロール

**種類:** 新機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** アクセス制御
 
 [委任されたエンタープライズ アプリケーション管理のためのカスタム RBAC ロール](../roles/custom-available-permissions.md)がパブリック プレビューになりました。 これらの新しいアクセス許可は、アプリ登録管理のためのカスタム ロールに付与されます。これにより、管理者が持つアクセス権をきめ細かく制御できます。 今後、Azure AD の管理を委任するための追加のアクセス許可がリリースされます。

いくつかの一般的な委任のシナリオを次に示します。
- SAML ベースのシングル サインオン アプリケーションにアクセスできるユーザーとグループの割り当て
- Azure AD ギャラリー アプリケーションの作成
- SAML ベースのシングル サインオン アプリケーションの基本的な SAML 構成の更新と読み取り
- SAML ベースのシングル サインオン アプリケーションに使用される署名証明書の管理
- SAML ベースのシングル サインオン アプリケーションの期限切れが迫っているサインイン証明書の通知メール アドレスの更新
- SAML ベースのシングル サインオン アプリケーションに使用される SAML トークン署名とサインイン アルゴリズムの更新
- SAML ベースのシングル サインオン アプリケーションに使用されるユーザー属性と要求の作成、削除、更新
- プロビジョニング ジョブを有効化、無効化、再開する権限
- 属性マッピングの更新
- オブジェクトに関連付けられているプロビジョニング設定を読み取る権限
- サービス プリンシパルに関連付けられているプロビジョニング設定を読み取る権限
- プロビジョニングのためのアプリケーション アクセスを認可する権限

---

### <a name="public-preview---azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>パブリック プレビュー - Azure AD アプリケーション プロキシでは、認証のためにヘッダーを使用するアプリケーションへのシングル サインオン アクセスがネイティブにサポートされる

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** アクセス制御
 
Azure Active Directory (Azure AD) アプリケーション プロキシ は、認証のためにヘッダーを使用するアプリケーションに対するシングル サインオン アクセスをサポートしています。 Azure AD で、お使いのアプリケーションに必要なヘッダー値を構成できます。 ヘッダー値は、アプリケーション プロキシを介してアプリケーションに送信されます。 詳細については、「[Azure AD アプリ プロキシを使用したオンプレミスのアプリに対するヘッダーベースのシングル サインオン](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md)」を参照してください
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy"></a>一般提供 - Azure AD B2C Phone のカスタム ポリシーを使用したサインアップとサインイン

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C

開発者や企業は、電話番号を使用したサインアップとサインインを使用して、SMS 経由でユーザーの電話番号に送信されるワンタイム パスワードを使用したユーザーのサインアップとサインインを許可できます。 この機能を使用すると、ユーザーが電話にアクセスできなくなった場合に自分の電話番号を変更することもできます。 カスタム ポリシー機能によって、開発者や企業は、ページのカスタマイズを通して自社のブランドを伝達できます。 「[Azure AD B2C でカスタム ポリシーを使用した電話のサインアップとサインインを設定する](../../active-directory-b2c/phone-authentication-user-flows.md)」でその実行方法を確認してください。
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---november-2020"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクタ - 2020 年 11 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合
 
新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Adobe Identity Management](../saas-apps/adobe-identity-management-provisioning-tutorial.md)
- [Blogin](../saas-apps/blogin-provisioning-tutorial.md)
- [Clarizen One](../saas-apps/clarizen-one-provisioning-tutorial.md)
- [Contentful](../saas-apps/contentful-provisioning-tutorial.md)
- [GitHub AE](../saas-apps/github-ae-provisioning-tutorial.md)
- [Playvox](../saas-apps/playvox-provisioning-tutorial.md)
- [PrinterLogic SaaS](../saas-apps/printer-logic-saas-provisioning-tutorial.md)
- [Tic - Tac Mobile](../saas-apps/tic-tac-mobile-provisioning-tutorial.md)
- [Visibly](../saas-apps/visibly-provisioning-tutorial.md)

詳細については、[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングの自動化](../app-provisioning/user-provisioning.md)に関する記事を参照してください。
 
---

### <a name="public-preview---email-sign-in-with-proxyaddresses-now-deployable-via-staged-rollout"></a>パブリック プレビュー - 段階的なロールアウトを通じてデプロイできるようになった ProxyAddresses を使用した電子メール サインイン

**種類:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
テナント管理者は、段階的なロールアウトを使用して、ProxyAddresses を使用した電子メール サインインを特定の Azure AD グループにデプロイできるようになりました。 これは、テナント全体にデプロイする前に、ホーム領域検出ポリシーを使用して機能を試す際に役立ちます。 段階的なロールアウトを通じて ProxyAddresses を使用した電子メール サインインをデプロイする手順については、[ドキュメント](../authentication/howto-authentication-use-email-signin.md)を参照してください。
 
---

### <a name="limited-preview---sign-in-diagnostic"></a>制限付きプレビュー - サインイン診断

**種類:** 新機能  
**サービス カテゴリ:** レポーティング  
**製品の機能:** 監視とレポート
 
サインイン診断の初期プレビュー リリースでは、管理者はユーザーのサインインを確認できるようになりました。管理者は、サインイン中に発生したことと問題の修正方法に関する、状況に応じた具体的で関連性のある詳細とガイダンスを受け取ることができます。 診断は Azure AD レベルと条件付きアクセスの診断と解決ブレードの両方で使用できます。 このリリースが対応する診断シナリオは、条件付きアクセス、Multi-Factor Authentication、成功したサインインです。

詳細については、「[Azure AD におけるサインイン診断とは](../reports-monitoring/overview-sign-in-diagnostics.md)」を参照してください。
 
---

### <a name="improved-unfamiliar-sign-in-properties"></a>通常とは異なるサインイン プロパティの機能強化

**種類:** 変更された機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護

  通常とは異なるサインイン プロパティの検出が更新されました。 お客様は、より高いリスクの通常とは異なるサインイン プロパティが検出されることに気付くことがあります。 詳細については、「[リスクとは](../identity-protection/concept-identity-protection-risks.md)」を参照してください。
 
---

### <a name="public-preview-refresh-of-cloud-provisioning-agent-now-available-version-112810"></a>クラウド プロビジョニング エージェントのパブリック プレビューの更新が利用可能 (バージョン:1.1.281.0)

**種類:** 変更された機能  
**サービス カテゴリ:** Azure AD のクラウド プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
クラウド プロビジョニング エージェントがパブリック プレビューでリリースされ、ポータルから利用できるようになりました。 このリリースには、ドメインでの GMSA のサポートなど、いくつかの機能強化が含まれています。これにより、セキュリティが向上し、初期同期サイクルが改善され、大きいグループがサポートされます。 詳細については、リリース バージョンの[履歴](../app-provisioning/provisioning-agent-release-version-history.md)を参照してください。 
 
---

### <a name="bitlocker-recovery-key-api-endpoint-now-under-informationprotection"></a>BitLocker 回復キー API のエンドポイントが現在は /informationProtection の下に配置される

**種類:** 変更された機能  
**サービス カテゴリ:** デバイス アクセス管理  
**製品の機能:** デバイスのライフサイクル管理
 
以前は、/bitlocker のエンドポイントを使用して BitLocker キーを回復することができました。 このエンドポイントはいずれ非推奨になるため、お客様は /informationProtection の下に配置されるようになった API の使用を開始するようにしてください。 

これらの変更を反映するためのドキュメントの更新については、[BitLocker 回復 API](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta&preserve-view=true) に関する記事を参照してください。

---

### <a name="general-availability-of-application-proxy-support-for-remote-desktop-services-html5-web-client"></a>リモート デスクトップ サービス HTML5 Web クライアントに対するアプリケーション プロキシ サポートの一般提供

**種類:** 変更された機能  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** アクセス制御
 
Azure AD リモート デスクトップ サービス (RDS) Web クライアントに対する Azure AD アプリケーション プロキシのサポートが、一般提供されるようになりました。 RDS Web クライアントを使用すると、ユーザーは、Microsoft Edge、Internet Explorer 11、Google Chrome などの任意の HTML5 対応ブラウザーを使用して、リモート デスクトップ インフラストラクチャにアクセスできます。 ユーザーは、どこからでもローカル デバイスと同様にリモート アプリやデスクトップと対話できます。 

Azure AD アプリケーション プロキシを使用すると、すべての種類のリッチ クライアント アプリに対して事前認証と条件付きアクセス ポリシーを適用することにより、RDS デプロイのセキュリティを強化できます。 詳細については、「[Azure AD アプリケーション プロキシを使用したリモート デスクトップの発行](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)」を参照してください
 
---

### <a name="new-enhanced-dynamic-group-service-is-in-public-preview"></a>新しい機能強化された動的グループ サービス (パブリック プレビュー)

**種類:** 変更された機能  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション
 
機能強化された動的グループ サービスがパブリック プレビューになりました。 テナントに動的グループを作成する新しいお客様は、この新しいサービスを使用することになります。 動的グループの作成に必要な時間は、テナントのサイズではなく、作成されるグループのサイズに比例します。 この更新により、お客様がより小さいグループを作成した場合に、大きいテナントのパフォーマンスが大幅に向上します。 

また、この新しいサービスでは、属性が変更されたことによるメンバーの追加と削除を数分以内に完了することを目標としています。 1 つの処理エラーによってテナントの処理がブロックされることもありません。 動的グループの作成の詳細については、[ドキュメント](../enterprise-users/groups-create-rule.md)を参照してください。
 
---