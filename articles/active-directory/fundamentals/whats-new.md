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
ms.date: 04/06/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 491908b651cd2b875fcfeed4c55d34f0e8c5cfa1
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802511"
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

## <a name="march-2020"></a>2020 年 3 月

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>2021 年 3 月の B2B 更新プログラムでのアンマネージド Azure Active Directory アカウント

**種類:** 変更の計画  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
**2021 年 3 月 31 日以降**、Microsoft では、B2B コラボレーション シナリオ用にアンマネージド Azure Active Directory (Azure AD) アカウントおよびテナントを作成することによって招待の引き換えをサポートしなくなります。 これに備えて、[電子メール ワンタイム パスコード認証](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)を選択することをお勧めします。

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

[In Case of Crisis – Mobile](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial)、[Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial)、[ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial)、[Tact](https://tact.ai/assistant/)、[OpusCapita Cash Management](http://cm1.opuscapita.com/tenantname)、[Salestim](https://prd.salestim.io/forms)、[Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial)、[Dynatrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial)、[HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process)、[Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial)、[eCornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial)、[ShipHazmat](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial)、[Netskope Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial)、[Contentful](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial)、[Bindtuning](https://bindtuning.com/login)、[HireVue Coordinate – Europe](https://www.hirevue.com/)、[HireVue Coordinate - USOnly](https://www.hirevue.com/)、[HireVue Coordinate - US](https://www.hirevue.com/)、[WittyParrot Knowledge Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup)、[Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial)、[Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial)、[Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup)、[Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial)、[Mail Luck!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial)、[Teamie](https://theteamie.com/)、[Velocity for Teams](https://velocity.peakup.org/teams/login)、[SIGNL4](https://account.signl4.com/manage)、[EAB Navigate IMPL](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial)、[ScreenMeet](https://console.screenmeet.com/)、[Omega Point](https://pi.ompnt.com/)、[Speaking Email for Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune)、[Speaking Email for Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct)、[ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial)、[iHealthHome Care Navigation System](https://ihealthnav.com/account/signin)、[Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

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
