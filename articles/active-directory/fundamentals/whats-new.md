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
ms.date: 01/27/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f5bf9c5c80593066e31b1ff23b6def844f67e65
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185778"
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
 
今後、フィルター管理者がユーザーのアプリを整理するように構成できるワークスペースはコレクションと呼ばれます。 それらの構成方法の詳細については、「[マイ アプリ ポータルでコレクションを作成する](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)」を参照してください。

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

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial)、[Fastly Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial)、[Terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial)、[Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial)、[Abibot Netlogistik](https://1030-review-develop-3zknud.netlogistik.com/)、[SkyKick Cloud Backup for Office 365](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access)、[Upshotly](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial)、[LeaveBot](https://leavebot.io/#home)、[DataCamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial)、[TripActions](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial)、[SmartWork](https://www.intumit.com/english/SmartWork.html)、[Dotcom-Monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial)、[SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS、PeopleSoft、および JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial)、[Hosted MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial)、[Yuhu Property Management Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial)、[LumApps](https://sites.lumapps.com/login)、[Upwork Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial)、[Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial)、[SmartDB for Microsoft Teams](http://teams.smartdb.jp/login/)、[PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial)、[ContractSafe Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial)、[Maxient Conduct Manager Software](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial)、[Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial)、[PortalTalk 365](https://www.portaltalk.com/)、[CoreView](https://portal.coreview.com/)、[Squelch Cloud Office365 Connector](https://laxmi.squelch.io/login)、[PingFlow Authentication](https://app-staging.pingview.io/)、[PrinterLogic SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial)、[Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial)、[Sandwai](https://app.sandwai.com/)、[EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial)、[AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial)、[Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/)

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
**製品の機能:** アクセス制御

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
**製品の機能:** アクセス制御

新しいワークスペース機能を使用してアプリを簡単に見つけられるようにするなど、組織のユーザーが最新の [マイ アプリ] エクスペリエンスを表示してアクセスする方法をカスタマイズできるようになりました。 新しいワークスペース機能は、組織のユーザーが既にアクセス権を持っているアプリのフィルターとして機能します。

新しい [マイ アプリ] エクスペリエンスのロール アウトとワークスペースの作成の詳細については、「[マイ アプリ (プレビュー) ポータルでワークスペースを作成する](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)」を参照してください。

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>月間アクティブ ユーザー ベースの課金モデルのサポート (一般提供)

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C

Azure AD B2C では、月間アクティブ ユーザー (MAU) の課金がサポートされるようになりました。 MAU の課金は、1 つのカレンダー月の間に認証アクティビティを行った一意のユーザーの数に基づいて行われます。 既存のお客様はいつでも、この新しい課金方法に切り替えることができます。

2019 年 11 月 1 日以降、すべての新しいお客様は自動的にこの方法で課金されます。 この課金方法はお客様にとって、コスト面でのメリットと、事前に計画できるというメリットがあります。

詳細については、「[Upgrade to monthly active users billing model](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model)」 (月間アクティブ ユーザー課金モデルにアップグレードする) を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2019 年 10 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2019 年 10 月に、フェデレーションを使用した以下の 35 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[In Case of Crisis – Mobile](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial)、[Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial)、[ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial)、[Tact](https://tact.ai/assistant/)、[OpusCapita Cash Management](http://cm1.opuscapita.com/tenantname)、[Salestim](https://prd.salestim.io/forms)、[Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial)、[Dynatrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial)、[HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process)、[Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial)、[eCornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial)、[ShipHazmat](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial)、[Netskope Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial)、[Contentful](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial)、[Bindtuning](https://bindtuning.com/login)、[HireVue Coordinate – EU](https://www.hirevue.com/)、[HireVue Coordinate - USOnly](https://www.hirevue.com/)、[HireVue Coordinate - US](https://www.hirevue.com/)、[WittyParrot Knowledge Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup)、[Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial)、[Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial)、[Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup)、[Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial)、[Mail Luck!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial)、[Teamie](https://theteamie.com/)、[Velocity for Teams](https://velocity.peakup.org/teams/login)、[SIGNL4](https://account.signl4.com/manage)、[EAB Navigate IMPL](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial)、[ScreenMeet](https://console.screenmeet.com/)、[Omega Point](https://pi.ompnt.com/)、[Speaking Email for Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune)、[Speaking Email for Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct)、[ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial)、[iHealthHome Care Navigation System](https://ihealthnav.com/account/signin)、[Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

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

詳細については、「[チュートリアル:Azure Active Directory Domain Services インスタンスを作成して構成する](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)」を参照してください。

---

## <a name="september-2019"></a>2019 年 9 月

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>変更の計画: Power BI コンテンツ パックの廃止

**種類:** 変更の計画  
**サービス カテゴリ:** レポーティング  
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

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2019 年 9 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2019 年 9 月に、フェデレーションを使用した以下の 29 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[ScheduleLook](https://schedulelook.bbsonlineservices.net/)、[MS Azure SSO Access for Ethidex Compliance Office™ - Single  sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial)、[iServer Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/iserver-portal-tutorial)、[SKYSITE](https://docs.microsoft.com/azure/active-directory/saas-apps/skysite-tutorial)、[Concur Travel and Expense](https://docs.microsoft.com/azure/active-directory/saas-apps/concur-travel-and-expense-tutorial)、[WorkBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/workboard-tutorial)、[YeeFlow](https://apps.yeeflow.com/)、[ARC Facilities](https://docs.microsoft.com/azure/active-directory/saas-apps/arc-facilities-tutorial)、[Luware Stratus Team](https://stratus.emea.luware.cloud/login)、[Wide Ideas](https://wideideas.online/wideideas/)、[Prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial)、[JDLT Client Hub](https://clients.jdlt.co.uk/login)、[RENRAKU](https://docs.microsoft.com/azure/active-directory/saas-apps/renraku-tutorial)、[SealPath Secure Browser](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive)、[Prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial)、[Penneo](https://app.penneo.com/)、[Hiretual](https://app.testhtm.com/settings/email-integration)、[Cintoo Cloud](https://aec.cintoo.com/login)、[Whitesource](https://docs.microsoft.com/azure/active-directory/saas-apps/whitesource-tutorial)、[Hosted Heritage Online SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-heritage-online-sso-tutorial)、[IDC](https://docs.microsoft.com/azure/active-directory/saas-apps/idc-tutorial)、[CakeHR](https://docs.microsoft.com/azure/active-directory/saas-apps/cakehr-tutorial)、[BIS](https://docs.microsoft.com/azure/active-directory/saas-apps/bis-tutorial)、[Coo Kai Team Build](https://ms-contacts.coo-kai.jp/)、[Sonarqube](https://docs.microsoft.com/azure/active-directory/saas-apps/sonarqube-tutorial)、[Adobe Identity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/adobe-identity-management-tutorial)、[Discovery Benefits SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/discovery-benefits-sso-tutorial)、[Amelio](https://app.amelio.co/)、[iTask](https://itask.yipinapp.com/)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---

### <a name="new-azure-ad-global-reader-role"></a>新しい Azure AD の全体閲覧者ロール

**種類:** 新機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** アクセス制御

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
**製品の機能:** アクセス制御

Power BI モバイル アプリと Azure AD アプリケーション プロキシの間の新しい統合により、Power BI モバイルアプリに安全にサインインし、オンプレミスの Power BI Report Server でホストされている組織のレポートを表示することができます。

Power BI Mobile アプリの詳細 (アプリのダウンロード場所など) については、[Power BI サイト](https://powerbi.microsoft.com/mobile/)を参照してください。 Azure AD アプリケーション プロキシを使用して Power BI モバイル アプリをセットアップする方法の詳細については、「[Azure AD アプリケーション プロキシを使用して Power BI Mobile へのリモート アクセスを有効にする](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi)」を参照してください。

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>AzureADPreview PowerShell モジュールの新しいバージョンが利用可能になりました

**種類:** 変更された機能  
**サービス カテゴリ:** その他  
**製品の機能:** ディレクトリ

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
**製品の機能:** ディレクトリ

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
**製品の機能:** アクセス制御

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

詳細については、[動作のしくみ:Azure AD のセルフサービス パスワード リセット のクイック スタート](https://aka.ms/authappsspr)に関する記事をご覧ください。 ユーザー エクスペリエンスの詳細については、[職場または学校のパスワードのリセットの概要](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview)に関する記事をご覧ください。

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
**製品の機能:** ディレクトリ

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

詳細については、[動的メンバーシップ ルールの管理](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)に関する記事を参照してください。

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
**サービス カテゴリ:** レポーティング  
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
