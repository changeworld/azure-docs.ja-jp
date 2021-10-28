---
title: 新機能 リリース ノート - Azure Active Directory | Microsoft Docs
description: 最新のリリース ノート、既知の問題、バグの修正、非推奨の機能、予定されている変更点など、Azure Active Directory の新着情報について説明します。
author: ajburnle
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 9/30/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c84c121788d3af6279ac2ef1ef4c149f803bf308
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130245571"
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
## <a name="september-2021"></a>2021 年 9 月


### <a name="limits-on-the-number-of-configured-api-permissions-for-an-application-registration-will-be-enforced-starting-in-october-2021"></a>アプリケーション登録に対して構成されている API アクセス許可の数の制限は、2021 年 10 月以降に適用される

**種類:** 変更の計画  
**サービス カテゴリ:** その他  
**製品の機能:** 開発者エクスペリエンス
 
アプリケーション開発者は、許可できる数よりも多くのアクセス許可を必要とするようにアプリを構成することがあります。 これが起こるのを避けるために、アプリの登録に構成できる必要なアクセス許可の合計数に制限を適用しています。

すべての API で、1 つのアプリケーション登録に必要なアクセス許可の合計数は 400 を超えることはできません。 この制限を適用する変更は、2021 年 10 月中旬にはロールアウトが開始されます。 制限を超えているアプリケーションは、構成されているアクセス許可の数を増やすことができません。 アクセス許可が必要な個別の API の数に関する既存の制限は変更されておらず、50 API を超えることはできません。

Azure portal では、必要なアクセス許可が [Azure Active Directory] > [アプリケーションの登録] > (アプリケーションの選択) > [API のアクセス許可] の下に一覧表示されます。 Microsoft Graph または Microsoft Graph PowerShell を使用すると、アプリケーション エンティティの requiredResourceAccess プロパティに必要なアクセス許可が一覧表示されます。 [詳細については、こちらを参照してください](../enterprise-users/directory-service-limits-restrictions.md)。

---

###  <a name="my-apps-performance-improvements"></a>マイ アプリのパフォーマンスの向上

**種類:** 固定  
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** エンド ユーザー エクスペリエンス
 
マイ アプリの読み込み時間が改善されました。 myapps.microsoft.com にアクセスするユーザーは、別のサービス経由でリダイレクトされるのではなく、直接マイ アプリを読み込むことができます。 [詳細については、こちらを参照してください](../user-help/my-apps-portal-end-user-access.md)。

---

### <a name="single-page-apps-using-the-spa-redirect-uri-type-must-use-a-cors-enabled-browser-for-auth"></a>`spa` リダイレクト URI の種類を使用するシングル ページ アプリでは、認証に CORS 対応ブラウザーを使用する必要がある

**種類:** 既知の問題  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** 開発者エクスペリエンス
 
最新の Edge ブラウザーは、[シングル ページ アプリの認証コード](../develop/v2-oauth2-auth-code-flow.md#redirect-uri-setup-required-for-single-page-apps)を利用するときに、`Origin` ヘッダーを提供する要件に含まれるようになりました。 互換性修正プログラムで、最新の Edge ブラウザーが CORS コントロールから誤って除外されていましたが、そのバグは 10 月中に修正されます。 アプリケーションのサブセットは、ブラウザーで無効にされている CORS に依存しています。これには、トラフィックから `Origin` ヘッダーが削除されるという副作用があります。 これは Azure AD を使用する場合にサポートされていない構成であり、CORS の無効化に依存するこれらのアプリでは、セキュリティの回避策として最新の Edge を使用できなくなります。  すべての最新のブラウザーには、CORS が強制的に適用されるように、HTTP 仕様ごとに `Origin` ヘッダーが含まれている必要があります。 [詳細については、こちらを参照してください](../develop/reference-breaking-changes.md#the-device-code-flow-ux-will-now-include-an-app-confirmation-prompt)。 

---

### <a name="general-availability---access-packages-can-expire-after-a-number-of-hours"></a>一般提供 - アクセス パッケージを、数時間後に期限切れにすることができる

**種類:** 新機能  
**サービス カテゴリ:** ユーザー アクセス管理 **製品の機能:** エンタイトルメント管理
 
エンタイトルメント管理で有効期限の詳細設定に、オプションが追加されました。 以前の設定に加えて、数時間で期限切れになるアクセス パッケージを構成できます。 [詳細については、こちらを参照してください](../governance/entitlement-management-access-package-create.md#lifecycle)。

---

### <a name="general-availability---on-the-my-apps-portal-users-can-choose-to-view-their-apps-in-a-list"></a>一般提供 - マイ アプリ ポータルでは、ユーザーはアプリを一覧で表示するように選択できる

**種類:** 新機能  
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** エンド ユーザー エクスペリエンス
 
既定で、マイ アプリではアプリがグリッド ビューに表示されます。 ユーザーは、マイ アプリ ビューを切り替えて、一覧にアプリを表示できるようになりました。 [詳細については、こちらを参照してください](../user-help/my-apps-portal-end-user-access.md)。
 
---

### <a name="general-availability---new-and-enhanced-device-related-audit-logs"></a>一般提供 - 新しい拡張されたデバイス関連の監査ログ

**種類:** 新機能  
**サービス カテゴリ:** Audit  
**製品の機能:** デバイスのライフサイクル管理
 
管理者は、さまざまな新しい改善されたデバイス関連の監査ログを表示できるようになりました。 新しい監査ログには、作成および削除のパスワードレス資格情報 (電話によるサインイン、FIDO2 キー、および Windows Hello for Business)、デバイスの登録/登録解除とデバイスの事前作成/事前作成されたデバイスの削除が含まれます。 さらに、デバイスの詳細情報の追加など、既存のデバイス関連の監査ログに小さな改善が行われています。 [詳細については、こちらを参照してください](../reports-monitoring/concept-audit-logs.md)。

---

### <a name="general-availability---azure-ad-users-can-now-view-and-report-suspicious-sign-ins-and-manage-their-accounts-within-microsoft-authenticator"></a>一般提供 - Azure AD ユーザーは、Microsoft Authenticator 内で疑わしいサインインを表示して報告し、アカウントを管理できるようになった

**種類:** 新機能  
**サービスカテゴリ:** Microsoft Authenticator アプリ  
**製品の機能:** ID のセキュリティ & 保護
 
この機能により、Azure AD ユーザーは Microsoft Authenticator アプリ内で職場または学校のアカウントを管理できます。 ユーザーは管理機能により、サインイン履歴とサインイン アクティビティを表示できます。 必要に応じて、サインイン履歴とアクティビティに基づいて、不審または見慣れないアクティビティを報告できます。 また、ユーザーは Azure AD アカウント パスワードを変更して、アカウントのセキュリティ情報を更新することもできます。 [詳細については、こちらを参照してください](../user-help/my-account-portal-sign-ins-page.md)。
 
---

### <a name="general-availability---new-ms-graph-apis-for-role-management"></a>一般提供 - ロール管理用の新しい MS Graph API

**種類:** 新機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** アクセス制御
 
MS Graph v1.0 エンドポイントのロール管理用の新しい API が一般提供されています。 古い[ディレクトリ ロール](/graph/api/resources/directoryrole?view=graph-rest-1.0&preserve-view=true)ではなく、[unifiedRoleDefinition](/graph/api/resources/unifiedroledefinition?view=graph-rest-1.0&preserve-view=true) と [unifiedRoleAssignment](/graph/api/resources/unifiedroleassignment?view=graph-rest-1.0&preserve-view=true) を使用します。
 
---

### <a name="general-availability---access-packages-can-expire-after-a-number-of-hours"></a>一般提供 - アクセス パッケージを、数時間後に期限切れにすることができる

**種類:** 新機能  
**サービス カテゴリ:** ユーザー アクセス管理  
**製品の機能:** エンタイトルメント管理

エンタイトルメント管理では、これまでの数日や特定の日付のサポートに加えて、数時間後に期限切れになるアクセス パッケージを構成することが可能になりました。 [詳細については、こちらを参照してください](../governance/entitlement-management-access-package-create.md#lifecycle)。
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2021"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクタ - 2021 年 9 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合
 
新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [BLDNG APP](../saas-apps/bldng-app-provisioning-tutorial.md)
- [Cato Networks](../saas-apps/cato-networks-provisioning-tutorial.md)
- [Rouse Sales](../saas-apps/rouse-sales-provisioning-tutorial.md)
- [SchoolStream ASA](../saas-apps/schoolstream-asa-provisioning-tutorial.md)
- [Taskize Connect](../saas-apps/taskize-connect-provisioning-tutorial.md)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)に関するページをご覧ください。
 
---
 
[1585267](https://identitydivision.visualstudio.com/IAM/IXR/_queries?id=1585267&triage=true&fullScreen=false&_a=edit)

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2021"></a>Azure AD アプリケーション ギャラリーで新しいフェデレーション アプリが利用できるようになる - 2021 年 9 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2021 年 9 月には、フェデレーションをサポートする次の 44 個の新しいアプリケーションがアプリ ギャラリーに追加されました

[Studybugs](https://studybugs.com/signin)、[Yello](https://yello.co/yello-for-microsoft-teams/)、[LawVu](../saas-apps/lawvu-tutorial.md)、[Formate eVo Mail](https://www.document-genetics.co.uk/formate-evo-erp-output-management)、[Revenue Grid](https://app.revenuegrid.com/login)、[Orbit for Office 365](https://azuremarketplace.microsoft.com/marketplace/apps/aad.orbitforoffice365?tab=overview)、[Upmarket](https://app.upmarket.ai/)、[Alinto Protect](https://protect.alinto.net/)、[Cloud Concinnity](https://cloudconcinnity.com/)、[Matlantis](https://matlantis.com/)、[ModelGen for Visio (MG4V)](https://crecy.com.au/model-gen/)、[NetRef: Classroom Management](https://oauth.net-ref.com/microsoft/sso)、[VergeSense](../saas-apps/vergesense-tutorial.md)、[iAuditor](../saas-apps/iauditor-tutorial.md)、[Secutraq](https://secutraq.net/login)、[Active and Thriving](../saas-apps/active-and-thriving-tutorial.md)、[Inova](https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize?client_id=1bacdba3-7a3b-410b-8753-5cc0b8125f81&response_type=code&redirect_uri=https:%2f%2fbroker.partneringplace.com%2fpartner-companion%2f&code_challenge_method=S256&code_challenge=YZabcdefghijklmanopqrstuvwxyz0123456789._-~&scope=1bacdba3-7a3b-410b-8753-5cc0b8125f81/.default)、[TerraTrue](../saas-apps/terratrue-tutorial.md)、[Facebook Work Accounts](../saas-apps/facebook-work-accounts-tutorial.md)、[Beyond Identity Admin Console](../saas-apps/beyond-identity-admin-console-tutorial.md)、[Visult](https://app.visult.io/)、[ENGAGE TAG](https://app.engagetag.com/)、[Appaegis Isolation Access Cloud](../saas-apps/appaegis-isolation-access-cloud-tutorial.md)、[CrowdStrike Falcon Platform](../saas-apps/crowdstrike-falcon-platform-tutorial.md)、[MY Emergency Control](https://my-emergency.co.uk/app/auth/login)、[AlexisHR](../saas-apps/alexishr-tutorial.md)、[Teachme Biz](../saas-apps/teachme-biz-tutorial.md)、[Zero Networks](../saas-apps/zero-networks-tutorial.md)、[Mavim iMprove](https://improve.mavimcloud.com/)、[Azumuta](https://app.azumuta.com/login?microsoft=true)、[Frankli](https://beta.frankli.io/login)、[Amazon Managed Grafana](../saas-apps/amazon-managed-grafana-tutorial.md)、[Productive](../saas-apps/productive-tutorial.md)、[Create!Web フロー](../saas-apps/createweb-tutorial.md)、[Evercate](https://evercate.com/us/sign-up/)、[Ezra Coaching](../saas-apps/ezra-coaching-tutorial.md)、[Baldwin Safety and Compliance](../saas-apps/baldwin-safety-&-compliance-tutorial.md)、[Nulab Pass (Backlog,Cacoo,Typetalk)](../saas-apps/nulab-pass-tutorial.md)、[Metatask](../saas-apps/metatask-tutorial.md)、[Contrast Security](../saas-apps/contrast-security-tutorial.md)、[Animaker](../saas-apps/animaker-tutorial.md)、[Traction Guest](../saas-apps/traction-guest-tutorial.md)、[True Office Learning - LIO](../saas-apps/true-office-learning-lio-tutorial.md)、[Qiita Team](../saas-apps/qiita-team-tutorial.md)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください。

Azure AD アプリ ギャラリーにアプリケーションを公開する場合は、次の詳細をお読みください: https://aka.ms/AzureADAppRequest


---

###  <a name="gmail-users-signing-in-on-microsoft-teams-mobile-and-desktop-clients-will-sign-in-with-device-login-flow-starting-september-30-2021"></a>Microsoft Teams モバイル クライアントおよびデスクトップ クライアントでサインインしている Gmail ユーザーは、2021 年 9 月 30 日以降、デバイス ログイン フローを使用してサインインするようになる

**種類:** 変更された機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
2021 年 9 月 30 日以降、セルフサービス サインアップまたは引き換えた Gmail アカウントを使用してサインインしている Azure AD B2B ゲストおよび Azure AD B2C のお客様は、特別なログイン手順が必要になります。 Microsoft Teams のモバイルおよびデスクトップ クライアントでサインインを完了するには、個別のブラウザー ウィンドウにコードを入力するように求められるようになりました。 そうしていない場合は、サインインにシステム ブラウザーを使用するようにアプリを変更してください。 詳細については、MSAL.NET の「 [埋め込み Web UI とシステム Web UI の比較](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui)」のドキュメントを参照してください。 すべての MSAL SDK が既定でシステム Web ビューを使用します。 

デバイスのログイン フローは 2021 年 9 月 30 日に開始されるため、お客様のリージョンですぐに利用できない場合があります。 まだ使用できない場合、リージョンにデプロイされるまで、エンドユーザーはドキュメントに示されているエラー画面が表示されます。デバイス ログイン フローの詳細と、Google への拡張機能の要求の詳細については、「[Google を B2B ゲスト ユーザーの ID プロバイダーとして追加する](../external-identities/google-federation.md#deprecation-of-web-view-sign-in-support)」を参照してください。
 
---

### <a name="improved-conditional-access-messaging-for-non-compliant-device"></a>準拠していないデバイスの条件付きアクセス メッセージングの改善

**種類:** 変更された機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** エンド ユーザー エクスペリエンス
 
デバイスが非準拠としてマークされているときにユーザーに表示される条件付きアクセスのブロック画面のテキストとデザインが更新されました。 ユーザーは、会社のデバイス コンプライアンス ポリシーを満たすために必要なアクションを実行するまで、ブロックされます。 さらに、ユーザーがデバイス管理ポータルを開くためのフローが整理されました。 これらの機能強化は、すべての条件付きアクセスでサポートされる OS プラットフォームに適用されます。 [詳細情報](https://support.microsoft.com/account-billing/troubleshooting-the-you-can-t-get-there-from-here-error-message-479a9c42-d9d1-4e44-9e90-24bbad96c251) 

---
 
## <a name="august-2021"></a>2021 年 8 月

### <a name="new-major-version-of-aadconnect-available"></a>AADConnect の新しいメジャー バージョンの提供開始

**種類:** 固定  
**サービス カテゴリ:** AD Connect  
**製品の機能:** ID ライフサイクル管理
 
Azure Active Directory Connect の新しいメジャー バージョンがリリースされました。 このバージョンには、基本コンポーネントを最新バージョンにするための更新プログラムがいくつも含まれているため、Azure AD Connect を使用するすべてのお客様にお勧めします。 [詳細については、こちらを参照してください](../hybrid/whatis-azure-ad-connect-v2.md)。
 
---

### <a name="public-preview---azure-ad-single-sign-on-and-device-based-conditional-access-support-in-firefox-on-windows-10"></a>パブリック プレビュー - Windows 10 上の Firefox での Azure AD シングル サインオンとデバイスベースの条件付きアクセスのサポート

**種類:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** SSO
 

Windows 10 および Windows Server 2019 上の Firefox ブラウザーで、ネイティブ シングル サインオン (SSO) と、デバイスベースの条件付きアクセスがサポートされるようになりました。 Firefox バージョン 91 でサポートされます。 [詳細については、こちらを参照してください](../conditional-access/require-managed-devices.md#prerequisites)。
 
---

### <a name="public-preview---beta-ms-graph-apis-for-azure-ad-access-reviews-returns-list-of-contacted-reviewer-names"></a>パブリック プレビュー - Azure AD アクセス レビュー用のベータ版 MS Graph API によって、連絡済みレビュー担当者名の一覧が返される

**種類:** 新機能  
**サービス カテゴリ:** アクセス レビュー  
**製品の機能:** Identity Governance
 

Azure AD アクセス レビュー用のベータ版 MS Graph API がリリースされました。 この API には、レビュー担当者の種類に加えて、連絡済みのレビュー担当者の名前を返すメソッドが含まれています。 [詳細については、こちらを参照してください](/graph/api/resources/accessreviewinstance?view=graph-rest-beta&preserve-view=true)。
 
---

### <a name="general-availability---register-or-join-devices-user-action-in-conditional-access"></a>一般提供 - 条件付きアクセスでの "デバイスの登録または参加" ユーザー操作

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護
 

条件付きアクセスで、"デバイスの登録または参加" ユーザー操作が一般提供になりました。 このユーザー操作により、Azure Active Directory (AD) のデバイス登録に関する多要素認証 (MFA) ポリシーを制御できます。 現時点では、このユーザー操作は、ユーザーがデバイスを Azure AD に登録または参加させるときの制御として多要素認証 (MFA) を有効にできるだけです。 Azure AD デバイスの登録に依存している、または適用されないその他の制御は、このユーザー操作では引き続き無効になります。 [詳細については、こちらを参照してください](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions)。

---

### <a name="general-availability---customers-can-scope-reviews-of-privileged-roles-to-eligible-or-permanent-assignments"></a>一般提供 - お客様は特権ロールのレビュー範囲を、資格のある割り当てまたは永続的な割り当てに設定可能

**種類:** 新機能  
**サービス カテゴリ:** アクセス レビュー  
**製品の機能:** Identity Governance
 
管理者は、権限のある Azure AD ロールまたは Azure リソース ロールに対する永続的な割り当てまたは資格のある割り当てのみのアクセス レビューを作成できるようになりました。 [詳細については、こちらを参照してください](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md)。
 
--- 

### <a name="general-availability---assign-roles-to-azure-active-directory-ad-groups"></a>一般提供 - Azure Active Directory (AD) グループへのロールの割り当て

**種類:** 新機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** アクセス制御
 

Azure AD グループへのロールの割り当てが一般提供になりました。 この機能によって、Azure AD での、全体管理者と特権ロール管理者のロール割り当ての管理を簡略化できます。 [詳細については、こちらを参照してください](../roles/groups-concept.md)。 
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---aug-2021"></a>Azure AD アプリケーション ギャラリーでの新しいフェデレーション アプリの提供 - 2021 年 8 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2021 年 8 月に、フェデレーションをサポートする次の 46 個の新しいアプリケーションがアプリ ギャラリーに追加されました。

[Siriux Customer Dashboard](https://portal.siriux.tech/login)、[STRUXI](https://struxi.app/)、[Autodesk Construction Cloud - Meetings](https://acc.autodesk.com/)、[Eccentex AppBase for Azure](../saas-apps/eccentex-appbase-for-azure-tutorial.md)、[Bookado](https://adminportal.bookado.io/)、[FilingRamp](https://app.filingramp.com/login)、[BenQ IAM](../saas-apps/benq-iam-tutorial.md)、[Rhombus Systems](../saas-apps/rhombus-systems-tutorial.md)、[CorporateExperience](../saas-apps/corporateexperience-tutorial.md)、[TutorOcean](../saas-apps/tutorocean-tutorial.md)、[Bookado Device](https://adminportal.bookado.io/)、[HiFives-AD-SSO](https://app.hifives.in/login/azure)、[Darzin](https://au.darzin.com/)、[Simply Stakeholders](https://au.simplystakeholders.com/)、[KACTUS HCM - Smart People](https://kactusspc.digitalware.co/)、[Five9 UC Adapter for Microsoft Teams V2](https://uc.five9.net/?vendor=msteams)、[Automation Center](https://automationcenter.cognizantgoc.com/portal/boot/signon)、[Cirrus Identity Bridge for Azure AD](../saas-apps/cirrus-identity-bridge-for-azure-ad-tutorial.md)、[ShiftWizard SAML](../saas-apps/shiftwizard-saml-tutorial.md)、[Safesend Returns](https://www.safesendwebsites.com/)、[Brushup](../saas-apps/brushup-tutorial.md)、[directprint.io Cloud Print Administration](../saas-apps/directprint-io-cloud-print-administration-tutorial.md)、[plain-x](https://app.plain-x.com/#/login),[X-point Cloud](../saas-apps/x-point-cloud-tutorial.md)、[SmartHub INFER](../saas-apps/smarthub-infer-tutorial.md)、[Fresh Relevance](../saas-apps/fresh-relevance-tutorial.md)、[FluentPro G.A. Suite](https://gas.fluentpro.com/Account/SSOLogin?provider=Microsoft)、[Clockwork Recruiting](../saas-apps/clockwork-recruiting-tutorial.md)、[WalkMe SAML2.0](../saas-apps/walkme-saml-tutorial.md)、[Sideways 6](https://app.sideways6.com/account/login?ReturnUrl=/)、[Kronos Workforce Dimensions](../saas-apps/kronos-workforce-dimensions-tutorial.md)、[SysTrack Cloud Edition](https://cloud.lakesidesoftware.com/Cloud/Account/Login)、[mailworx Dynamics CRM Connector](https://www.mailworx.info/)、[Palo Alto Networks Cloud Identity Engine - Cloud Authentication Service](../saas-apps/palo-alto-networks-cloud-identity-engine---cloud-authentication-service-tutorial.md)、[Peripass](https://accounts.peripass.app/v1/sso/challenge)、[JobDiva](https://www.jobssos.com/index_azad.jsp?SSO=AZURE&ID=1)、[Sanebox For Office365](https://sanebox.com/login)、[Tulip](../saas-apps/tulip-tutorial.md)、[HP Wolf Security](https://bec-pocda37b439.bromium-online.com/gui/)、[Genesys Engage cloud Email](https://login.microsoftonline.com/common/oauth2/authorize?prompt=consent&accessType=offline&state=07e035a7-6fb0-4411-afd9-efa46c9602f9&resource=https://graph.microsoft.com/&response_type=code&redirect_uri=https://iwd.api01-westus2.dev.genazure.com/iwd/v3/emails/oauth2/microsoft/callback&client_id=36cd21ab-862f-47c8-abb6-79facad09dda)、[Meta Wiki](https://meta.dunkel.eu/)、[Palo Alto Networks Cloud Identity Engine Directory Sync](https://directory-sync.us.paloaltonetworks.com/directory?instance=L2qoLVONpBHgdJp1M5K9S08Z7NBXlpi54pW1y3DDu2gQqdwKbyUGA11EgeaDfZ1dGwn397S8eP7EwQW3uyE4XL)、[Valarea](https://www.valarea.com/en/download)、[LanSchool Air](../saas-apps/lanschool-air-tutorial.md)、[Catalyst](https://www.catalyst.org/sso-login/)、[Webcargo](../saas-apps/webcargo-tutorial.md)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください。

Azure AD アプリ ギャラリーにアプリケーションを公開する場合は、次の詳細をお読みください: https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---august-2021"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクタ - 2021 年 8 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合
 
新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Chatwork](../saas-apps/chatwork-provisioning-tutorial.md)
- [FreshService](../saas-apps/freshservice-provisioning-tutorial.md)
- [InviteDesk](../saas-apps/invitedesk-provisioning-tutorial.md)
- [Maptician](../saas-apps/maptician-provisioning-tutorial.md)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、Azure AD による SaaS アプリケーションへのユーザー プロビジョニングの自動化に関するページを参照してください。
 
---

### <a name="multi-factor-mfa-fraud-report--new-audit-event"></a>多要素認証 (MFA) の不正アクセス レポート - 新しい監査イベント

**種類:** 変更された機能  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティ & 保護
 

不正アクセス レポートの結果としてユーザーが多要素認証 (MFA) でブロックされていることを管理者が理解できるように、新しい監査イベントが追加されました。 この監査イベントは、ユーザーが不正アクセスをレポートした場合に追跡されます。 この監査ログは、サインイン ログに含まれる不正アクセス レポートに関する既存の情報に加えて提供されます。 監査レポートを取得する方法については、[多要素認証の不正アクセスのアラート](../authentication/howto-mfa-mfasettings.md#fraud-alert)に関するページを参照してください。

---

### <a name="improved-low-risk-detections"></a>低リスク検出の改善

**種類:** 変更された機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護

Identity Protection により発行される低リスクのアラートの品質を向上させるために、低リスクの危険なサインインの発行を減らすようにアルゴリズムを変更しました。組織の環境では、低リスクのサインインが大幅に減少する可能性があります。 [詳細については、こちらを参照してください](../identity-protection/concept-identity-protection-risks.md)。
 
---

### <a name="non-interactive-risky-sign-ins"></a>非対話型の危険なサインイン

**種類:** 変更された機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護
 
Identity Protection で、非対話型のサインインについて危険なサインインが生成されるようになりました。管理者は、危険なサインイン レポートで **[サインインの種類]** フィルターを使用して、これらの危険なサインインを見つけることができます。 [詳細については、こちらを参照してください](../identity-protection/howto-identity-protection-investigate-risk.md)。
 
---

### <a name="change-from-user-administrator-to-identity-governance-administrator-in-entitlement-management"></a>エンタイトルメント管理におけるユーザー管理者から Identity Governance 管理者への変更 

**種類:** 変更された機能  
**サービス カテゴリ:** ロール  
**製品の機能:** Identity Governance
 
エンタイトルメント管理でアクセス パッケージなどのリソースを管理するためのアクセス許可の割り当ては、ユーザー管理者ロールから Identity Governance 管理者ロールに移行します。 

ユーザー管理者ロールが割り当てられているユーザーは、カタログを作成したり、所有していないカタログでアクセス パッケージを管理したりすることができなくなりました。 組織内のユーザーに、エンタイトルメント管理でカタログ、アクセス パッケージ、またはポリシーを構成するためのユーザー管理者ロールが割り当てられている場合、新しい割り当てが必要です。 代わりに、これらのユーザーには Identity Governance 管理者ロールを割り当てる必要があります。 [詳細情報](../governance/entitlement-management-delegate.md)

---

### <a name="windows-azure-active-directory-connector-is-deprecated"></a>Microsoft Azure Active Directory コネクタの非推奨化

**種類:** 非推奨  
**サービス カテゴリ:** Microsoft Identity Manager  
**製品の機能:** ID ライフサイクル管理
 
FIM 用 Microsoft Azure AD コネクタは、機能が凍結され非推奨になりました。 FIM と Azure AD コネクタを使用するソリューションが置き換えられました。 FIM 用 Azure AD コネクタで使用される内部インターフェイスが Azure AD から削除されたため、既存のデプロイは、[Azure AD Connect](../hybrid/whatis-hybrid-identity.md)、Azure AD Connect 同期、または [Microsoft Graph コネクタ](/microsoft-identity-manager/microsoft-identity-manager-2016-connector-graph)に移行する必要があります。 [詳細については、こちらを参照してください](/microsoft-identity-manager/microsoft-identity-manager-2016-deprecated-features)。

---

### <a name="retirement-of-older-azure-ad-connect-versions"></a>古い Azure AD Connect バージョンの廃止

**種類:** 非推奨  
**サービス カテゴリ:** AD Connect  
**製品の機能:** [ユーザー管理]
 
2022 年 8 月 31 日以降、Azure AD Connect のすべての V1 バージョンが廃止されます。 まだの場合は、サーバーを Azure AD Connect V2.0 に更新する必要があります。 最適なサポート エクスペリエンスを得るには、必ず最新バージョンの Azure AD Connect を実行していることを確認してください。

廃止されたバージョンの Azure AD Connect を実行すると、予期せず動作が停止する可能性があります。 また、最新のセキュリティ修正プログラム、パフォーマンスの向上、トラブルシューティングおよび診断ツール、サービスの機能強化が受けられない場合があります。 さらに、サポートが必要な場合でも、組織が必要とするレベルのサービスを受けられないおそれがあります。

V2.0 の変更点およびこの変更による影響の詳細については、[Azure Active Directory Connect V2.0](../hybrid/whatis-azure-ad-connect-v2.md) に関する記事を参照してください。

---

### <a name="retirement-of-support-for-installing-mim-on-windows-server-2008-r2-or-sql-server-2008-r2"></a>Windows Server 2008 R2 または SQL Server 2008 R2 に MIM をインストールに関するサポートの廃止

**種類:** 非推奨  
**サービス カテゴリ:** Microsoft Identity Manager  
**製品の機能:** ID ライフサイクル管理
 
MIM 同期、サービス、ポータル、または CM を Windows Server 2008 R2 にデプロイしたり、基になるデータベースとして SQL Server 2008 R2 を使用したりすることは、これらのプラットフォームがメインストリーム サポートではなくなったため非推奨となりました。 MIM 同期およびその他のコンポーネントは、Windows Server 2016 以降にインストールしたり、SQL Server 2016 以降で使用したりすることをお勧めします。

PRIV フォレスト内での Windows Server 2012 R2 ドメイン コントローラーを使用した Privileged Access Management の MIM のデプロイは、非推奨となりました。 PRIV フォレスト ドメインについては、Windows Server 2016 以降の Active Directory を Windows Server 2016 の機能レベルで使用してください。 CORP フォレストのドメインについては、Windows Server 2012 R2 の機能レベルを引き続き使用できます。 [詳細については、こちらを参照してください](/microsoft-identity-manager/microsoft-identity-manager-2016-supported-platforms)。

---

## <a name="july-2021"></a>2021 年 7 月

### <a name="new-google-sign-in-integration-for-azure-ad-b2c-and-b2b-self-service-sign-up-and-invited-external-users-will-stop-working-starting-july-12-2021"></a>Azure AD B2C と B2B のセルフサービス サインアップおよび招待された外部ユーザー向けの新しい Google サインイン統合が、2021 年 7 月 12 日をもって動作しなくなります

**種類:** 変更の計画  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 

以前、[Gmail 認証用の埋め込み Web ビューの例外が 2021 年後半に失効する](https://www.yammer.com/cepartners/threads/1188371962232832)ことをお知らせしました。

2021 年 7 月 7 日、Google から、これらの制限の一部が **2021 年 7 月 12 日** から適用されるという連絡を受けました。 外部ユーザーを招待したり、セルフサービス サインアップを可能にしたりするために、カスタムまたは基幹業務アプリケーションで新しい Google ID サインインを設定している Azure AD B2B および B2C をご利用のお客様に対しては、この制限が直ちに適用されます。 そのため、認証がシステム Web ビューに移動されていない場合、ユーザーの画面には Gmail のサインインをブロックするエラー画面が表示されます。 詳細については、以下のリンク先のドキュメントをご覧ください。 

ほとんどのアプリでは既定でシステム Web ビューが使用されているため、この変更による影響はありません。 これは、埋め込み Web ビュー (既定ではない設定) を使用しているお客様にのみ適用されます。お客様には、新しい Google 統合を作成する前に、アプリケーションの認証をシステム ブラウザーに移行することをお勧めします。 Gmail 認証用のシステム ブラウザーに移行する方法については、ドキュメント「[Web ブラウザーを使用する (MSAL.NET)](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui)」の埋め込み Web UI とシステム Web UI の比較のセクションを参照してください。 すべての MSAL SDK が既定でシステム Web ビューを使用します。 [詳細については、こちらを参照してください](../external-identities/google-federation.md#deprecation-of-web-view-sign-in-support)。

---

### <a name="google-sign-in-on-embedded-web-views-expiring-september-30-2021"></a>埋め込み Web ビューでの Google サインインが 2021 年 9 月 30 日に失効します

**種類:** 変更の計画  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 

約 2 か月前に、Gmail 認証用の埋め込み Web ビューの例外が 2021 年後半に失効することをお知らせしました。 

最近、Google はその期日を **2021 年 9 月 30 日** に決定しました。 

これは 2021 年 9 月 30 日からグローバルにロールアウトされます。今後は、Azure AD B2B のゲストが Microsoft Teams のモバイルおよびデスクトップ クライアントから Gmail アカウントを使用してサインインを行うと、別のブラウザー ウィンドウでコードを入力するよう促されるようになります。 これは、招待されたゲスト、およびセルフサービス サインアップを使用してサインアップしたゲストにも当てはまります。 

Azure AD B2C をご利用のお客様で、カスタムまたは基幹業務アプリに埋め込み Web ビューの Gmail 認証を設定しているか、既存の Google 統合を使用している場合は、Gmail アカウントでユーザーをサインインさせることができなくなります。 これを回避するには、システム ブラウザーを使用してサインインするようにアプリを修正してください。 詳細については、ドキュメント「[Web ブラウザーを使用する (MSAL.NET)](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui)」の埋め込み Web UI とシステム Web UI の比較のセクションを参照してください。 すべての MSAL SDK が既定でシステム Web ビューを使用します。 

デバイスのログイン フローのロールアウトは、2021 年 9 月 30 日に開始されるため、お客様のリージョンへのロールアウトはまだ完了していない可能性があります (この場合、お客様のリージョンにデプロイされるまで、お客様のエンド ユーザーにはドキュメントにあるようなエラー画面が表示されます)。 

既に影響を受けることがわかっているシナリオや、ユーザーにどのような影響があるかについては、「[Google を B2B ゲスト ユーザーの ID プロバイダーとして追加する](../external-identities/google-federation.md#deprecation-of-web-view-sign-in-support)」をご覧ください。

---

### <a name="bug-fixes-in-my-apps"></a>マイ アプリのバグ修正

**種類:** 固定  
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** エンド ユーザー エクスペリエンス
 
- これまでは、コレクションの使用を推奨するバナーが表示されると、コンテンツがヘッダーの後ろにスクロールしてしまっていました。 この問題は解決されました。 
- また、過去にはコレクションにアプリを追加すると、すべてのアプリのコレクションに含まれているアプリの順番がランダムに入れ替わるという問題もありました。 この問題もすでに解決済みです。 

マイ アプリの詳細については、「[マイ アプリ ポータルからアプリにサインインして開始する](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)」を参照してください。

---

### <a name="public-preview----application-authentication-method-policies"></a>パブリック プレビュー - アプリケーションの認証方法のポリシー

**種類:** 新機能  
**サービス カテゴリ:** MS Graph  
**製品の機能:** 開発者エクスペリエンス
 
MS Graph のアプリケーション認証方法のポリシーを使用すると、IT 管理者がアプリケーションのパスワード シークレットの資格情報に有効期限を付けたり、シークレットの使用を完全にブロックすることができるようになります。 ポリシーは、既定の設定ではテナント全体に適用することができ、さらに特定のアプリケーションやサービス プリンシパルに適用することもできます。 [詳細については、こちらを参照してください](/graph/api/resources/policy-overview?view=graph-rest-beta&preserve-view=true)。
 
---

### <a name="public-preview----authentication-methods-nudge-to-download-microsoft-authenticator"></a>パブリック プレビュー - Authentication Methods により Microsoft Authenticator をダウンロードするよう促される

**種類:** 新機能  
**サービスカテゴリ:** Microsoft Authenticator アプリ  
**製品の機能:** ユーザー認証
 
Authenticator のナッジ ポリシーは、ユーザーに Microsoft Authenticator アプリの導入を促すことで、管理者が組織をより安全なセキュリティ体制に移行させるのに役立ちます。 この機能が導入される前は、管理者がユーザーに Authenticator アプリを設定するよう促す方法はありませんでした。 

組織全体にスムーズに導入できるよう、ナッジには、管理者がナッジする対象としてユーザーやグループを含めたり除外することで、対象をスコープする機能があります。 [詳細情報](../authentication/how-to-nudge-authenticator-app.md)
 
---

### <a name="public-preview----separation-of-duties-check"></a>パブリック プレビュー - 職務の分離チェック 

**種類:** 新機能  
**サービス カテゴリ:** ユーザー アクセス管理  
**製品の機能:** エンタイトルメント管理
 
Azure AD のエンタイトルメント管理を使用すると、管理者はあるアクセス パッケージが他のアクセス パッケージやグループと互換性がないことを定義できます。  互換性のないメンバーシップを持っているユーザーは、追加のアクセスを要求できなくなります。 [詳細については、こちらを参照してください](../governance/entitlement-management-access-package-request-policy.md#prevent-requests-from-users-with-incompatible-access-preview)。
 
---

### <a name="public-preview----identity-protection-logs-in-log-analytics-storage-accounts-and-event-hubs"></a>パブリック プレビュー - Log Analytics、ストレージ アカウント、Event Hubs の Identity Protection のログ

**種類:** 新機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護
 
Azure AD ブレードの診断設定を使用して、危険なユーザーとリスク検出のログを Azure Monitor、ストレージ アカウント、または Log Analytics に送信できるようになりました。 [詳細については、こちらを参照してください](../identity-protection/howto-export-risk-data.md)。
 
---

### <a name="public-preview----application-proxy-api-addition-for-backend-ssl-certificate-validation"></a>パブリック プレビュー - バックエンド SSL 証明書の検証用のアプリケーション プロキシ API の追加

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** アクセス制御
 
リソースの種類 onPremisesPublishing に、アプリケーションのバックエンド SSL 証明書検証が有効かどうかを示すプロパティ "isBackendCertificateValidationEnabled" が追加されました。 すべての新しいアプリケーション プロキシ アプリでは、このプロパティは既定で true に設定されています。 すべての既存のアプリでは、このプロパティは false に設定されます。 詳細については、[リソースの種類 onPremisesPublishing](/graph/api/resources/onpremisespublishing?view=graph-rest-beta&preserve-view=true) API を参照してください。
 
---

### <a name="general-availability---improved-authenticator-setup-experience-for-add-azure-ad-account-in-microsoft-authenticator-app-by-directly-signing-into-the-app"></a>一般提供 - アプリに直接サインインできるようになり、Microsoft Authenticator アプリに Azure AD アカウントを追加する際の Authenticator セットアップのエクスペリエンスが向上しました。

**種類:** 新機能  
**サービスカテゴリ:** Microsoft Authenticator アプリ  
**製品の機能:** ユーザー認証
 
ユーザーは、既存の認証方法を使用して Microsoft Authenticator アプリに直接サインインし、資格情報を設定できるようになりました。 QR コードをスキャンする必要がなくなり、一時アクセス パス (TAP) や パスワードと SMS (またはその他の認証方法) を使用して Authenticator アプリでアカウントを設定することができます。

これにより、Microsoft Authenticator アプリのユーザー資格情報のプロビジョニング プロセスが改善され、エンド ユーザーはアプリのプロビジョニングをセルフサービスで行うことができるようになります。 [詳細については、こちらを参照してください](https://support.microsoft.com/account-billing/add-your-work-or-school-account-to-the-microsoft-authenticator-app-43a73ab5-b4e8-446d-9e54-2a4cb8e4e93c#sign-in-with-your-credentials)。
 
---

### <a name="general-availability---set-manager-as-reviewer-in-azure-ad-entitlement-management-access-packages"></a>一般提供 - Azure AD エンタイトルメント管理のアクセス パッケージでマネージャーをレビュアーに設定する

**種類:** 新機能  
**サービス カテゴリ:** ユーザー アクセス管理  
**製品の機能:** エンタイトルメント管理
 
Azure AD エンタイトルメント管理のアクセス パッケージで、定期的に行われるアクセス レビューのレビュアーとして、ユーザーのマネージャーを設定できるようになりました。 [詳細については、こちらを参照してください](../governance/entitlement-management-access-reviews-create.md)。

---

### <a name="general-availability---enable-external-users-to-self-service-sign-up-in-aad-using-msa-accounts"></a>一般提供 - MSA アカウントを使用して外部ユーザーが AAD にセルフサービスでサインアップできるようにする

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
ユーザーは、外部ユーザーが Microsoft アカウントを使用して Azure Active Directory にセルフサービスでサインアップすることを有効にできるようになりました。 [詳細については、こちらを参照してください](../external-identities/microsoft-account.md)。
 
---
 
### <a name="general-availability---external-identities-self-service-sign-up-with-email-one-time-passcode"></a>一般提供 - メールでのワンタイム パスコードによる External Identities のセルフサービス サインアップ

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 

ユーザーは、外部ユーザーが電子メールとワンタイム パスコードを使って Azure Active Directory にセルフサービスでサインアップすることを有効にできるようになりました。 [詳細については、こちらを参照してください](../external-identities/one-time-passcode.md)。
 
---

### <a name="general-availability---anomalous-token"></a>一般提供 - 異常なトークン

**種類:** 新機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護
 
Identity Protection に異常なトークンの検出機能が追加されました。 この機能を使用すると、アクティブな時間や見知らぬ IP アドレスからの認証など、トークンに異常な特性があるかどうかを検知することができます。 [詳細については、こちらを参照してください](../identity-protection/concept-identity-protection-risks.md#sign-in-risk)。
 
---

### <a name="general-availability---register-or-join-devices-in-conditional-access"></a>一般提供 - 条件付きアクセスでのデバイスの登録または参加

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護
 
条件付きアクセスのデバイスの登録または参加ユーザー操作が、一般提供されるようになりました。 このユーザー操作を使用すると、Azure AD デバイス登録に対する多要素認証 (MFA) ポリシーを制御できます。 

現時点では、このユーザー操作は、ユーザーがデバイスを Azure AD に登録または参加させるときの制御として多要素認証 (MFA) を有効にできるだけです。 Azure AD デバイスの登録に依存している、または適用されないその他の制御は、このユーザー操作では引き続き無効になります。 [詳細については、こちらを参照してください](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions)。 

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2021"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクタ - 2021 年 7 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合
 
新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Clebex](../saas-apps/clebex-provisioning-tutorial.md)
- [Exium](../saas-apps/exium-provisioning-tutorial.md)
- [SoSafe](../saas-apps/sosafe-provisioning-tutorial.md)
- [Talentech](../saas-apps/talentech-provisioning-tutorial.md)
- [Thrive LXP](../saas-apps/thrive-lxp-provisioning-tutorial.md)
- [Vonage](../saas-apps/vonage-provisioning-tutorial.md)
- [[Zip]](../saas-apps/zip-provisioning-tutorial.md)
- [TimeClock 365](../saas-apps/timeclock-365-provisioning-tutorial.md)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)に関するページをご覧ください。

---

### <a name="changes-to-security-and-microsoft-365-group-settings-in-azure-portal"></a>Azure portal のセキュリティと Microsoft 365 グループの設定の変更点

**種類:** 変更された機能  
**サービス カテゴリ:** グループ管理  
**製品の機能:** ディレクトリ
 

これまでは、ユーザーは Azure portal でセキュリティ グループや Microsoft 365 グループを作成することができました。 今後は、Azure portal、PowerShell、API にまたがってグループを作成できるようになります。 お客様は、組織用に構成された新しい設定を確認し、更新する必要があります。 [詳細については、こちらを参照してください](../enterprise-users/groups-self-service-management.md#group-settings)。
 
---

### <a name="all-apps-collection-has-been-renamed-to-apps"></a>"すべてのアプリ" コレクションの名前が "アプリ" に変更されました

**種類:** 変更された機能  
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** エンド ユーザー エクスペリエンス
 
マイ アプリ ポータルの "すべてのアプリ" と呼ばれていたコレクションが、"アプリ" に名称変更されました。 製品の進化に伴い、この既定のコレクションには "アプリ" の方がより適切な名前になります。 [詳細については、こちらを参照してください](../manage-apps/my-apps-deployment-plan.md#plan-the-user-experience)。
 
---
 
## <a name="june-2021"></a>2021 年 6 月

### <a name="context-panes-to-display-risk-details-in-identity-protection-reports"></a>Identity Protection レポートにリスクの詳細を表示するコンテキスト ペイン

**種類:** 変更の計画  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護
 
Identity Protection の危険なユーザー、危険なサインイン、リスク検出レポートにおいて、選択したエントリのリスクの詳細が、2021 年 7 月以降、ページの右側に表示されるコンテキスト ウィンドウに表示されます。 この変更はユーザー インターフェースにのみ影響し、既存の機能には影響しません。 これらの機能の詳細については、「[方法:リスクの調査](../identity-protection/howto-identity-protection-investigate-risk.md)」を参照してください。
 
---

### <a name="public-preview----create-azure-ad-access-reviews-of-service-principals-that-are-assigned-to-privileged-roles"></a>パブリック プレビュー - 特権ロールに割り当てられているサービス プリンシパルの Azure AD アクセス レビューの作成

**種類:** 新機能  
**サービス カテゴリ:** アクセス レビュー  
**製品の機能:** Identity Governance
 
 Azure AD のアクセス レビューを使用して、サービス プリンシパルの特権 Azure AD および Azure リソース ロールへのアクセスをレビューできます。 [詳細については、こちらを参照してください](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md#create-access-reviews)。
 
---

### <a name="public-preview----group-owners-in-azure-ad-can-create-and-manage-azure-ad-access-reviews-for-their-groups"></a>パブリック プレビュー - Azure AD のグループの所有者は、自分のグループの Azure AD アクセス レビューを作成、管理することができます

**種類:** 新機能  
**サービス カテゴリ:** アクセス レビュー  
**製品の機能:** Identity Governance
 
Azure AD のグループ所有者が、自分のグループの Azure AD アクセス レビューを作成および管理できるようになりました。 この機能は、テナント管理者であれば Azure AD のアクセス レビュー設定を通じて有効にすることができ、既定では無効になっています。 [詳細については、こちらを参照してください](../governance/create-access-review.md#allow--group-owners-to-create-and-manage-access-reviews-preview)。
 
---

### <a name="public-preview----customers-can-scope-access-reviews-of-privileged-roles-to-just-users-with-eligible-or-active-access"></a>パブリック プレビュー - お客様は、特権ロールのアクセス レビューを、適格に割り当てられた、またはアクティブなアクセス許可を持つユーザーのみに限定することができます

**種類:** 新機能  
**サービス カテゴリ:** アクセス レビュー  
**製品の機能:** Identity Governance
 
管理者は、特権ロールの割り当てに関するアクセス レビューを作成する際に、適格に割り当てられたユーザーのみ、またはアクティブに割り当てられたユーザーのみのレビューを作成するよう制限することができます。 [詳細については、こちらを参照してください](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md)。
 
---

### <a name="public-preview----microsoft-graph-apis-for-mobility-mdmmam-management-policies"></a>パブリック プレビュー - Mobility (MDM/MAM) の Microsoft Graph API の管理ポリシー

**種類:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** デバイスのライフサイクル管理
 
Azure AD のモビリティ (MDM/MAM) 構成に対する Microsoft Graph のサポートがパブリック プレビューになりました。 管理者は、Microsoft Graph v1.0 を使用して、Intune のような MDM アプリケーションのユーザー スコープと URL を構成できます。 詳細については、[リソースの種類 mobilityManagementPolicy](/graph/api/resources/mobilitymanagementpolicy?view=graph-rest-beta&preserve-view=true) に関するページを参照してください

---

### <a name="general-availability---custom-questions-in-access-package-request-flow-in-azure-active-directory-entitlement-management"></a>一般提供 - Azure Active Directory エンタイトルメント管理のアクセス パッケージ要求フローにおけるカスタム質問

**種類:** 新機能  
**サービス カテゴリ:** ユーザー アクセス管理  
**製品の機能:** エンタイトルメント管理
 
Azure AD エンタイトルメント管理のアクセス パッケージ要求フローで、カスタム質問を作成できるようになりました。 この機能を使用することで、ユーザーはアクセス パッケージ ポリシーでカスタム質問を設定することができるようになります。 これらの質問は申請者に対して表示され、このユーザーはアクセス要求プロセスの一部として回答を入力することができます。 承認者はこれらの回答を確認することができ、アクセス要求についてより良い判断を下すための情報として役立てることができます。 [詳細については、こちらを参照してください](../governance/entitlement-management-access-package-create.md)。

---

### <a name="general-availability---multi-geo-sharepoint-sites-as-resources-in-entitlement-management-access-packages"></a>一般提供 - エンタイトルメント管理のアクセス パッケージのリソースとしての Multi-Geo SharePoint サイト

**種類:** 新機能  
**サービス カテゴリ:** ユーザー アクセス管理  
**製品の機能:** エンタイトルメント管理
 
エンタイトルメント管理のアクセス パッケージでは、SharePoint Online の Multi-Geo 機能を使用しているお客向けに、Multi-Geo の SharePoint サイトがサポートされるようになりました。 [詳細については、こちらを参照してください](../governance/entitlement-management-catalog-create.md#add-a-multi-geo-sharepoint-site)。
 
---

### <a name="general-availability---knowledge-admin-and-knowledge-manager-built-in-roles"></a>一般提供 - 知識管理者とナレッジ マネージャーの組み込みロール

**種類:** 新機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** アクセス制御
 
知識管理者とナレッジ マネージャーという 2 つの新しいロールが一般提供されるようになりました。

- 知識管理者ロールのユーザーは、Microsoft 365 管理センターのすべての組織のナレッジ設定にフル アクセスできます。 このユーザーは、トピックや頭字語などのコンテンツを作成および管理できます。 さらに、これらのユーザーは、コンテンツ センターの作成、サービス正常性の監視、サービス要求の作成を行うことができます。 [詳細情報](../roles/permissions-reference.md#knowledge-administrator)
- ナレッジ マネージャー ロールのユーザーは、コンテンツを作成および管理することができ、主にナレッジの品質と構造を担当します。 このユーザーは、トピックを確認したり、編集を承認したり、トピックを削除したりするトピック管理アクションのフル権限を持っています。 このロールはまた、用語ストア管理ツールの一部として分類を管理したり、コンテンツ センターを作成したりすることもできます。 [詳細については、こちらを参照してください](../roles/permissions-reference.md#knowledge-manager)。

---

### <a name="general-availability---cloud-app-security-administrator-built-in-role"></a>一般提供 - Cloud App Security 管理者の組み込みロール

**種類:** 新機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** アクセス制御
 
 このロールが割り当てられたユーザーは、Cloud App Security でフル アクセス許可を持ちます。 管理者の追加、Microsoft Cloud App Security (MCAS) のポリシーと設定の追加、ログのアップロードを行うことができ、ガバナンス アクションを実行できます。 [詳細については、こちらを参照してください](../roles/permissions-reference.md#cloud-app-security-administrator)。
 
---

### <a name="general-availability---windows-update-deployment-administrator"></a>一般提供 - Windows Update デプロイ管理者

**種類:** 新機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** アクセス制御
 

 このロールに含まれるユーザーは、Windows Update for Business 展開サービスを使用して、Windows Update の展開のすべての側面を作成および管理できます。 この展開サービスを使用すると、ユーザーは更新プログラムをいつ、どのように展開するかの設定を定義できます。 また、ユーザーはテナント内のデバイスのグループに提供する更新プログラムを指定できます。 それだけでなく、ユーザーは更新の進捗状況を監視することもできます。 [詳細については、こちらを参照してください](../roles/permissions-reference.md#windows-update-deployment-administrator)。
 
---

### <a name="general-availability---multi-camera-support-for-windows-hello"></a>一般提供 - Windows Hello のマルチカメラのサポート

**種類:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
Windows 10 21H1 の更新プログラムにより、Windows Hello で複数のカメラの使用がサポートされるようになりました。 この更新プログラムには、内蔵カメラと外付けカメラの両方が存在する場合、外付けカメラを使用する規定値群が含まれています。 [詳細については、こちらを参照してください](https://techcommunity.microsoft.com/t5/windows-it-pro-blog/it-tools-to-support-windows-10-version-21h1/ba-p/2365103)。

---
 
### <a name="general-availability---access-reviews-ms-graph-apis-now-in-v10"></a>一般提供 - アクセス レビューの MS Graph API が v1.0 になりました

**種類:** 新機能  
**サービス カテゴリ:** アクセス レビュー  
**製品の機能:** Identity Governance
 
Azure Active Directory のアクセス レビューの MS Graph API が v1.0 になり、アクセス レビュー機能が完全に構成可能になりました。 [詳細については、こちらを参照してください](/graph/api/resources/accessreviewsv2-root?view=graph-rest-1.0&preserve-view=true)。
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---june-2021"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクタ - 2021 年 6 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合
 
新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [askSpoke](../saas-apps/askspoke-provisioning-tutorial.md)
- [Cloud Academy - SSO](../saas-apps/cloud-academy-sso-provisioning-tutorial.md)
- [CheckProof](../saas-apps/checkproof-provisioning-tutorial.md)
- [GoLinks](../saas-apps/golinks-provisioning-tutorial.md)
- [Holmes Cloud](../saas-apps/holmes-cloud-provisioning-tutorial.md)
- [H5mag](../saas-apps/h5mag-provisioning-tutorial.md)
- [LimbleCMMS](../saas-apps/limblecmms-provisioning-tutorial.md)
- [LogMeIn](../saas-apps/logmein-provisioning-tutorial.md)
- [SECURE DELIVER](../saas-apps/secure-deliver-provisioning-tutorial.md)
- [Sigma Computing](../saas-apps/sigma-computing-provisioning-tutorial.md)
- [Smallstep SSH](../saas-apps/smallstep-ssh-provisioning-tutorial.md)
- [Tribeloo](../saas-apps/tribeloo-provisioning-tutorial.md)
- [Twingate](../saas-apps/twingate-provisioning-tutorial.md)

詳細については、[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングの自動化](../app-provisioning/user-provisioning.md)に関する記事を参照してください。
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2021"></a>Azure AD アプリケーション ギャラリーで新しいフェデレーション アプリが利用できるようになりました - 2021 年 6 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2021 年 6 月に、フェデレーションをサポートする次の 42 個の新しいアプリケーションがアプリ ギャラリーに追加されました

[Taksel](https://help.ubuntu.com/community/Tasksel)、[IDrive360](../saas-apps/idrive360-tutorial.md)、[VIDA](../saas-apps/vida-tutorial.md)、[ProProfs Classroom](../saas-apps/proprofs-classroom-tutorial.md)、[WAN-Sign](../saas-apps/wan-sign-tutorial.md)、[Citrix Cloud SAML SSO](../saas-apps/citrix-cloud-saml-sso-tutorial.md)、[Fabric](../saas-apps/fabric-tutorial.md)、[DssAD](https://cloudlicensing.deepseedsolutions.com/)、[RICOH Creative Collaboration RICC](https://www.ricoh-europe.com/products/software-apps/collaboration-board-software/ricc/)、[Styleflow](../saas-apps/styleflow-tutorial.md)、[Chaos](https://accounts.chaosgroup.com/corporate_login)、[Traced Connector](https://control.traced.app/signup)、[Squarespace](https://account.squarespace.com/org/azure)、[MX3 Diagnostics Connector](https://mx3www.playground.dynuddns.com/signin-oidc)、[Ten Spot](https://tenspot.co/api/v1/sso/azure/login/)、[Finvari](../saas-apps/finvari-tutorial.md)、[Mobile4ERP](https://play.google.com/store/apps/details?id=com.negevsoft.mobile4erp)、[WalkMe US OpenID Connect](https://www.walkme.com/)、[Neustar UltraDNS](../saas-apps/neustar-ultradns-tutorial.md)、[cloudtamer.io](../saas-apps/cloudtamer-io-tutorial.md)、[A Cloud Guru](../saas-apps/a-cloud-guru-tutorial.md)、[PetroVue](../saas-apps/petrovue-tutorial.md)、[Postman](../saas-apps/postman-tutorial.md)、[ReadCube Papers](../saas-apps/readcube-papers-tutorial.md)、[Peklostroj](https://app.peklostroj.cz/)、[SynCloud](https://onboard.syncloud.io/)、[Polymerhq.io](https://www.polymerhq.io/)、[Bonos](../saas-apps/bonos-tutorial.md)、[Astra Schedule](../saas-apps/astra-schedule-tutorial.md)、[Draup](../saas-apps/draup-inc-tutorial.md)、[Inc](../saas-apps/draup-inc-tutorial.md)、[Applied Mental Health](../saas-apps/applied-mental-health-tutorial.md)、[iHASCO Training](../saas-apps/ihasco-training-tutorial.md)、[Nexsure](../saas-apps/nexsure-tutorial.md)、[XEOX](https://login.xeox.com/)、[Plandisc](https://create.plandisc.com/account/logon)、[foundU](../saas-apps/foundu-tutorial.md)、[Standard for Success Accreditation](../saas-apps/standard-for-success-accreditation-tutorial.md)、[Penji Teams](https://web.penjiapp.com/)、[CheckPoint Infinity Portal](../saas-apps/checkpoint-infinity-portal-tutorial.md)、[Teamgo](../saas-apps/teamgo-tutorial.md)、[Hopsworks.ai](../saas-apps/hopsworks-ai-tutorial.md)、[HoloMeeting 2](https://backend2.holomeeting.io/)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください。

Azure AD アプリ ギャラリーにアプリケーションを公開する場合は、次の詳細をお読みください: https://aka.ms/AzureADAppRequest
 
---

### <a name="device-code-flow-now-includes-an-app-verification-prompt"></a>デバイス コード フローにアプリ検証プロンプトが含まれるようになりました

**種類:** 変更された機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
[デバイス コード フロー](../develop/v2-oauth2-device-code.md)が更新され、ユーザー プロンプトが 1 つ追加されました。 サインインするユーザーに対して、サインイン先のアプリを検証するよう求めるプロンプトが表示されるようになります。  このプロンプトにより、ユーザーがフィッシング攻撃の対象となっていないことを確認します。 [詳細については、こちらを参照してください](../develop/reference-breaking-changes.md#the-device-code-flow-ux-will-now-include-an-app-confirmation-prompt)。
 
---

### <a name="user-last-sign-in-date-and-time-is-now-available-on-azure-portal"></a>Azure portal でユーザーの最終ログイン日時が利用可能になります

**種類:** 変更された機能  
**サービス カテゴリ:** [ユーザー管理]  
**製品の機能:** [ユーザー管理]
 
Azure portal で、ユーザーの最終ログイン日とタイムスタンプを表示できるようになりました。 この情報は、ユーザー プロファイルのページで、各ユーザーごとに確認できます。 この情報は、非アクティブなユーザーを特定し、リスク イベントを効果的に管理するのに役立ちます。 [詳細については、こちらを参照してください](./active-directory-users-profile-azure-portal.md?context=%2fazure%2factive-directory%2fenterprise-users%2fcontext%2fugr-context)。
 
---

### <a name="mim-bhold-suite-impact-of-end-of-support-for-microsoft-silverlight"></a>Microsoft Silverlight のサポート終了による MIM BHOLD Suite への影響について

**種類:** 変更された機能  
**サービス カテゴリ:** Microsoft Identity Manager  
**製品の機能:** Identity Governance
 
Microsoft Silverlight は、2021 年 10 月 12 日にサポートが終了します。 この変更は、Microsoft BHOLD Suite を使用しているお客様にのみ影響し、他の Microsoft Identity Manager シナリオには影響しません。 詳細については、「[Silverlight のサポート終了日](https://support.microsoft.com/windows/silverlight-end-of-support-0a3be3c7-bead-e203-2dfd-74f0a64f1788)」を参照してください。  

ブラウザーに Microsoft Silverlight をインストールしていないユーザーは、Silverlight を必要とする BHOLD Suite モジュールを使用できません。 これには、BHOLD Model Generator、BHOLD FIM セルフサービス統合、BHOLD Analytics が含まれます。  これらのモジュールの内、1 つ以上で BHOLD 展開を使用している場合は、2021 年 10 月までにそれらのモジュールを BHOLD サーバー コンピューターからアンインストールするよう計画してください。 また、以前その BHOLD 展開を使用していたユーザーのコンピューターからも Silverlight をアンインストールするよう計画してください。
 
---

### <a name="my-experiences-end-of-support-for-internet-explorer-11"></a>My* エクスペリエンス: Internet Explorer 11 のサポートの終了

**種類:** 非推奨  
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** エンド ユーザー エクスペリエンス
 

Microsoft 365 やその他のアプリでは、2021 年 8 月 21 日に Internet Explorer 11 のサポートが終了しますが、これには My* エクスペリエンスも含まれます。 Internet Explorer を介してアクセスした My* には、バグ修正や更新プログラムが一切適用されないため、問題が発生する可能性があります。 これらの日程は Edge チームが主導しているため、変更される可能性があります。 [詳細については、こちらを参照してください](https://blogs.windows.com/windowsexperience/2021/05/19/the-future-of-internet-explorer-on-windows-10-is-in-microsoft-edge/)。
 
---

### <a name="planned-deprecation---malware-linked-ip-address-detection-in-identity-protection"></a>非推奨の予定 - Identity Protection のマルウェアにリンクした IP アドレスの検出

**種類:** 非推奨  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護
 
2021 年 10 月 1 日をもって、Azure AD Identity Protection では、"マルウェアにリンクした IP アドレス" の検知が生成されなくなります。 アクションは必要なく、お客様は、引き続き Identity Protection によって提供される他の検出によって保護されます。 保護ポリシーの詳細については、「[Identity Protection ポリシー](../identity-protection/concept-identity-protection-policies.md)」を参照してください。
 
---
 
## <a name="may-2021"></a>2021 年 5 月

### <a name="public-preview----azure-ad-verifiable-credentials"></a>パブリック プレビュー - Azure AD の検証可能な資格情報

**種類:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** ユーザー認証
 
Azure AD を使用して、検証可能な資格情報を簡単に設計、発行できるようになりました。 検証可能な資格情報を使用することで、プライバシーを尊重しながら、雇用、教育、またはその他の主張の証明を行うことができます。 あらゆる人物やビジネスに関する情報を、デジタルで検証することができます。 [詳細については、こちらを参照してください](../verifiable-credentials/index.yml)。

---

### <a name="public-preview----device-code-flow-now-includes-an-app-verification-prompt"></a>パブリック プレビュー - デバイス コード フローにアプリ検証プロンプトが含まれるようになりました

**種類:** 新機能  
**サービス カテゴリ:** ユーザー認証  
**製品の機能:** 認証 (ログイン)
 
セキュリティ強化として[デバイス コード フロー](../develop/v2-oauth2-device-code.md)が更新され、ユーザーが想定しているアプリにサインインしていることを検証するためのプロンプトが新たに追加されました。 ロールアウトは 6 月に開始され、6 月 30 日までに完了する予定です。

攻撃者がユーザーを騙して悪意のあるアプリケーションにサインインさせるフィッシング攻撃を防ぐために、"[アプリケーションの表示名] にサインインしますか?" というプロンプトが追加されます。 このプロンプトは、デバイス コード フローを使用してログインしようとするすべてのユーザーを対象に表示されます。 セキュリティ対策として、これを削除したりバイパスすることはできません。 [詳細については、こちらを参照してください](../develop/reference-breaking-changes.md#the-device-code-flow-ux-will-now-include-an-app-confirmation-prompt)。

---

### <a name="public-preview----build-and-test-expressions-for-user-provisioning"></a>パブリック プレビュー - ユーザー プロビジョニング用の式をビルドしてテストする

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
式ビルダーを使用すると、完全同期サイクルが終わるまで待たずに、式の作成とテストを行うことができます。 [詳細については、こちらを参照してください](../app-provisioning/functions-for-customizing-application-data.md)。 

---

### <a name="public-preview----enhanced-audit-logs-for-conditional-access-policy-changes"></a>パブリック プレビュー - 条件付きアクセス ポリシーの変更の監査ログの強化

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護
 
条件付きアクセスを管理する上で重要なことは、ポリシーの変更を時系列で把握することです。 ポリシーの変更はエンド ユーザーに混乱を招く可能性があるため、変更のログを取り、管理者が以前のバージョンのポリシーに戻せるようにすることが重要です。 

監査ログには、誰がいつポリシーを変更したかを示すだけでなく、変更されたプロパティの値も含まれるようになりました。 この変更により、管理者は、割り当て、条件、または制御がどのように変更されたかをより詳しく把握できます。 以前のバージョンのポリシーに戻す場合は、古いバージョンの JSON 表現をコピーし、条件付きアクセス API を使用してポリシーを以前の状態に変更することができます。 [詳細については、こちらを参照してください](../conditional-access/concept-conditional-access-policies.md)。

---

### <a name="public-preview----sign-in-logs-include-authentication-methods-used-during-sign-in"></a>パブリック プレビュー - サインイン ログに、サインイン時に使用された認証方法が含まれる

**種類:** 新機能  
**サービス カテゴリ:** MFA  
**製品の機能:** 監視とレポート
 

管理者は、サインイン時に使用された認証方法など、ユーザーがサインインするまでの一連の手順を確認できるようになりました。 

これらの詳細にアクセスするには、Azure AD のサインイン ログにアクセスし、サインインを選択して、[Authentication Method Details]\(認証方法の詳細\) タブに移動します。ここには、使用された方法、方法の詳細 (電話番号、電話名など)、満たされた認証要件、結果の詳細などの情報が含まれています。 [詳細については、こちらを参照してください](../reports-monitoring/concept-sign-ins.md)。

---

### <a name="public-preview----pim-adds-support-for-abac-conditions-in-azure-storage-roles"></a>パブリック プレビュー - PIM に Azure Storage ロールでの ABAC 条件のサポートが追加

**種類:** 新機能  
**サービス カテゴリ:** Privileged Identity Management  
**製品の機能:** Privileged Identity Management
 
特定の Azure RBAC ロールにおける属性ベースのアクセス制御のパブリック プレビューに伴い、資格のある割り当ての Privileged Identity Management 内に ABAC 条件を追加することができるようになります。 [詳細については、こちらを参照してください](../../role-based-access-control/conditions-overview.md#conditions-and-privileged-identity-management-pim)。

---

### <a name="general-availability---conditional-access-and-identity-protection-reports-in-b2c"></a>一般提供 - B2C の条件付きアクセスと Identity Protection レポート

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C

B2C で、企業-消費者間 (B2C) アプリおよびユーザーのための条件付きアクセスおよび Identity Protection がサポートされるようになりました。 これにより、お客様はリスクや場所に応じたきめ細かなアクセス制御でユーザーを保護することができます。 これらの機能により、お客様はシグナルを確認してポリシーを作成し、より安全なアクセスを提供できるようになりました。 [詳細については、こちらを参照してください](../../active-directory-b2c/conditional-access-identity-protection-overview.md)。

---

### <a name="general-availability---kmsi-and-password-reset-now-in-next-generation-of-user-flows"></a>一般提供 - KMSI とパスワード リセットが次世代のユーザー フローに追加されました

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C

次世代の B2C ユーザー フローで、[KMSI (サインインしたままにする)](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) 機能がサポートされるようになりました。 KMSI 機能を使用すると、永続的な Cookie を使用して、Web やネイティブ アプリケーションを使用しているユーザーのセッション期間を延長することができます。 この機能により、ユーザーがブラウザーを終了して再び開いた後もセッションはアクティブなまま維持されます。 ユーザーがサインアウトすると、セッションは無効になります。パスワード リセットを使用すると、ユーザーは [パスワードを忘れた場合] というリンクからパスワードをリセットできます。 これにより、管理者が Azure AD B2C ディレクトリでユーザーの期限切れパスワードを強制的にリセットすることもできます。 [詳細については、こちらを参照してください](../../active-directory-b2c/add-password-reset-policy.md?pivots=b2c-user-flow)。
 
---

### <a name="general-availability---new-log-analytics-workbook-application-role-assignment-activity"></a>一般提供 - 新しい Log Analytics ブックのアプリケーション ロールの割り当てアクティビティ

**種類:** 新機能  
**サービス カテゴリ:** ユーザー アクセス管理  
**製品の機能:** エンタイトルメント管理
 
アプリケーション ロールの割り当ての変更に関する監査イベントを表示するための新しいブックが追加されました。 [詳細については、こちらを参照してください](../governance/entitlement-management-logs-and-reporting.md)。

---

### <a name="general-availability---next-generation-azure-ad-b2c-user-flows"></a>一般提供 - 次世代 Azure AD B2C ユーザー フロー 

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C
 
簡素化された新しいユーザー フロー エクスペリエンスは、プレビュー機能と同等の機能を備えており、すべての新機能のホームとなります。 ユーザーは、同じユーザー フロー内で新機能を有効にすることができるため、新しい機能をリリースするたびに複数のバージョンを作成する必要が少なくなります。 新しいユーザー フレンドリな UX により、ユーザー フローの選択と作成も簡単になります。 この機能の使用方法については、[Azure AD B2C でのユーザー フローの作成](../../active-directory-b2c/tutorial-create-user-flows.md?pivots=b2c-user-flow)に関するページを参照してください。 [詳細については、こちらを参照してください](../../active-directory-b2c/user-flow-versions.md)。

---

### <a name="general-availability---azure-active-directory-threat-intelligence-for-sign-in-risk"></a>一般提供 - Azure Active Directory のサインイン リスクに対する脅威インテリジェンス

**種類:** 新機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護
 
この新しい検出は、攻撃が発生したことが検出された際に、そのセッションのリスクを「高」にすることで当社のセキュリティ チームからユーザーに通知し、ユーザーを保護することを目的とした方法です。 この検出では、関連するサインインも危険なものとしてマークされます。 この検出は、既存の Azure Active Directory の脅威インテリジェンスによるユーザー リスク検出に続き、Microsoft のセキュリティ チームが観測したさまざまな攻撃を完全にカバーします。 [詳細については、こちらを参照してください](../identity-protection/concept-identity-protection-risks.md#user-linked-detections)。
 
---

### <a name="general-availability---conditional-access-named-locations-improvements"></a>一般提供 - 条件付きアクセスのネームド ロケーションの機能強化

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護
 
ネームド ロケーションでの IPv6 サポートが一般提供されるようになりました。 更新プログラムには次のものが含まれています。

- IPv6 アドレス範囲を定義する機能の追加
- ネームド ロケーションの制限を 90 から 195 に引き上げ
- ネームド ロケーションあたりの IP 範囲の制限を 1,200 から 2,000 に引き上げ
- ネームド ロケーションの検索と並び替え、およびロケーションの種類と信頼の種類でのフィルタリング機能の追加
- サインイン ログにサインインが属するネームド ロケーションを追加
 
さらに、管理者が問題のあるネームド ロケーションを定義するのを防ぐために、潜在的な構成ミスを減らすために追加のチェックが追加されました。 [詳細については、こちらを参照してください](../conditional-access/location-condition.md)。

---

### <a name="general-availability---restricted-guest-access-permissions-in-azure-ad"></a>一般提供 - Azure AD の制限付きゲスト アクセス許可

**種類:** 新機能  
**サービス カテゴリ:** [ユーザー管理]  
**製品の機能:** ディレクトリ
 
ゲスト ユーザーに対するディレクトリ レベルのアクセス許可が更新されました。 これらのアクセス許可により、管理者は外部のゲスト ユーザーのアクセスに対して追加の制限と制御を要求できます。

管理者は、外部のゲストによるユーザーとグループのプロファイルおよびメンバーシップ情報へのアクセスに対して制限を追加できるようになりました。 さらに、グループ メンバーシップを非表示にすることで、外部ユーザーのアクセスを大規模に管理できます。これには、ゲスト ユーザーが属するグループのメンバーシップの表示の制限が含まれます。 詳細については、「[Azure Active Directory でゲストのアクセス許可を制限する](../enterprise-users/users-restrict-guest-permissions.md)」を参照してください。
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2021"></a>Azure AD アプリケーション ギャラリーで入手できる新しいフェデレーション アプリ - 2021 年 5 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [AuditBoard](../saas-apps/auditboard-provisioning-tutorial.md)
- [Cisco Umbrella User Management](../saas-apps/cisco-umbrella-user-management-provisioning-tutorial.md)
- [Insite LMS](../saas-apps/insite-lms-provisioning-tutorial.md)
- [Kpifire](../saas-apps/kpifire-provisioning-tutorial.md)
- [UNIFI](../saas-apps/unifi-provisioning-tutorial.md)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)に関するページをご覧ください。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2021"></a>Azure AD アプリケーション ギャラリーで入手できる新しいフェデレーション アプリ - 2021 年 5 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2021 年 5 月に、フェデレーションをサポートする次の 29 個の新しいアプリケーションがアプリ ギャラリーに追加されました

[InviteDesk](https://app.invitedesk.com/login)、[Webrecruit ATS](https://id-test.webrecruit.co.uk/)、[Workshop](../saas-apps/workshop-tutorial.md)、[Gravity Sketch](https://landingpad.me/)、[JustLogin](../saas-apps/justlogin-tutorial.md)、[Custellence](https://custellence.com/sso/)、[WEVO](https://hello.wevoconversion.com/login)、[AppTec360 MDM](https://www.apptec360.com/ms/autopilot.html)、[Filemail](https://www.filemail.com/login),[Ardoq](../saas-apps/ardoq-tutorial.md)、[Leadfamly](../saas-apps/leadfamly-tutorial.md)、[Documo](../saas-apps/documo-tutorial.md)、[Autodesk SSO](../saas-apps/autodesk-sso-tutorial.md)、[Check Point Harmony Connect](../saas-apps/check-point-harmony-connect-tutorial.md)、[BrightHire](https://app.brighthire.ai/)、[Rescana](../saas-apps/rescana-tutorial.md)、[Bluewhale](https://cloud.bluewhale.dk/)、[AlacrityLaw](../saas-apps/alacritylaw-tutorial.md)、[Equisolve](../saas-apps/equisolve-tutorial.md)、[Zip](../saas-apps/zip-tutorial.md)、[Cognician](../saas-apps/cognician-tutorial.md)、[Acra](https://www.acrasuite.com/)、[VaultMe](https://app.vaultme.com/#/signIn)、[TAP App Security](../saas-apps/tap-app-security-tutorial.md)、[Cavelo Office365 Cloud Connector](https://dashboard.prod.cavelodata.com/)、[Clebex](../saas-apps/clebex-tutorial.md)、[Banyan Command Center](../saas-apps/banyan-command-center-tutorial.md)、[Check Point Remote Access VPN](../saas-apps/check-point-remote-access-vpn-tutorial.md)、[LogMeIn](../saas-apps/logmein-tutorial.md)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください

Azure AD アプリ ギャラリーにアプリケーションを公開する場合は、こちらの詳細 (https://aka.ms/AzureADAppRequest ) をお読みください

---

### <a name="improved-conditional-access-messaging-for-android-and-ios"></a>Android および iOS 用の条件付きアクセス メッセージングの改善

**種類:** 変更された機能  
**サービス カテゴリ:** デバイスの登録と管理  
**製品の機能:** エンド ユーザー エクスペリエンス
 

企業リソースへのアクセスがブロックされたユーザーに対して表示される条件付きアクセスの画面の文言を更新しました。 このユーザーは、モバイル デバイス管理にデバイスを登録するまで、アクセスがブロックされます。 これらの機能強化は、Android および iOS または iPadOS プラットフォームに適用されます。 以下は、変更点をまとめたものです。

- "デバイスの安全を保つためにご協力ください" から "デバイスを設定してアクセス権を取得する" に変更
- "サインインは完了しましたが、このリソースにアクセスするには、管理者の要求によりデバイスが Microsoft で管理されている必要があります。" から "[組織名] では、[組織名] のメール、ファイル、データにアクセスする前に、このデバイスをセキュリティで保護する必要があります。" に変更 
- "今すぐ登録" から "続行" に変更

「[Android enterprise デバイスを登録する](https://support.microsoft.com/topic/enroll-your-android-enterprise-device-d661c82d-fa28-5dfd-b711-6dff41ae83bb)」に記載されている情報は最新ではありません。

---

### <a name="azure-information-protection-service-will-begin-asking-for-consent"></a>Azure Information Protection サービスによる同意の要求の開始

**種類:** 変更された機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
Azure Information Protection サービスでは、ドキュメントへのアクセスを提供する一環として、ドキュメントを暗号化したテナントにユーザーをサインインさせます。  6 月以降、Azure AD では組織間でこのアクセスが行われる際に、ユーザーに対して同意を求めるプロンプトが表示されるようになります。  これは、ドキュメントへのアクセスの一環として、ドキュメントを所有する組織がユーザーに関するいくつかの情報を収集するということを、ユーザーが把握できるようにするためのものです。 [詳細については、こちらを参照してください](/azure/information-protection/known-issues#sharing-external-doc-types-across-tenants)。
 
---

### <a name="provisioning-logs-schema-change-impacting-graph-api-and-azure-monitor-integration"></a>Graph API と Azure Monitor の統合に影響するプロビジョニング ログのスキーマ変更

**種類:** 変更された機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** 監視とレポート
 

属性 "Action" と "statusInfo" が "provisioningAction" と "provisoiningStatusInfo" に変更されます。 [プロビジョニング ログの Graph API](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true) や [Azure Monitor の統合](../app-provisioning/application-provisioning-log-analytics.md)を使用して、作成したスクリプトを更新してください。 
 

---

### <a name="new-arm-api-to-manage-pim-for-azure-resources-and-azure-ad-roles"></a>Azure リソースと Azure AD ロールの PIM を管理する新しい ARM API

**種類:** 変更された機能  
**サービス カテゴリ:** Privileged Identity Management  
**製品の機能:** Privileged Identity Management
 
Azure リソース ロールと Azure AD ロールの PIM API の更新版がリリースされました。 Azure リソース ロールの PIM API は ARM API 標準の下でリリースされ、通常の Azure ロール割り当てのロール管理 API と整合しています。 一方、Azure AD ロールの PIM API も、unifiedRoleManagement API と整合する Graph API の下でリリースされています。 この変更によるメリットとしては、次のようなものがあります。

- ロール管理のために PIM API を ARM や Graph のオブジェクトと整合させることで、新しい Azure リソースをオンボードするために PIM を呼び出す必要性が減ります。 
- すべての Azure リソースが自動的に新しい PIM API で動作します。
- ロールの定義や PIM リソース ID を保持するために PIM を呼び出す必要性の削減
- Azure AD と Azure リソースの両方のロールにおいて、PIM のアプリ専用の API 権限をサポート

`/privilegedaccess` の下にある旧バージョンの PIM API は引き続き機能しますが、今後はこの新しい API に移行することが推奨されます。 [詳細については、こちらを参照してください](../privileged-identity-management/pim-apis.md)。
 
---

### <a name="revision-of-roles-in-azure-ad-entitlement-management"></a>Azure AD エンタイトルメント管理のロールの見直し

**種類:** 変更された機能  
**サービス カテゴリ:** ロール  
**製品の機能:** エンタイトルメント管理
 
新しいロール Identity Governance 管理者が最近導入されました。 このロールは、Azure AD エンタイトルメント管理でカタログやアクセス パッケージを管理する際に、ユーザー管理者ロールの代わりとなります。 Azure AD エンタイトルメント管理でアクセス パッケージを管理するために、管理者をユーザー管理者ロールに割り当てたり、このロールを有効にしてもらっている場合は、代わりに Identity Governance 管理者ロールに切り替えてください。 ユーザー管理者ロールには、カタログやアクセス パッケージに対する管理者権限が付与されなくなります。 [詳細については、こちらを参照してください](../governance/identity-governance-overview.md#appendix---least-privileged-roles-for-managing-in-identity-governance-features)。

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

### <a name="public-preview----external-identities-self-service-sign-up-in-aad-using-email-one-time-passcode-accounts"></a>パブリック プレビュー - ワンタイム パスコードの電子メール送信アカウントを使用した AAD での External Identities セルフサービス サインアップ

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
### <a name="azure-ad-connect-cloud-sync-general-availability-refresh"></a>Azure AD Connect クラウド同期の一般提供の更新 
**種類:** 変更された機能  
**サービス カテゴリ:** Azure AD Connect クラウド同期 **製品の機能:** ディレクトリ

Azure AD Connect クラウド同期でエージェントが更新されました (バージョン番号 - 1.1.359)。 バグの修正など、エージェントの更新の詳細については、[バージョン履歴](../cloud-sync/reference-version-history.md)を確認してください。 更新されたエージェントを使用すると、クラウド同期のお客様は GMSA コマンドレットを使用して、gMSA アクセス許可をきめ細かいレベルで設定およびリセットできます。 さらに、グループ スコープのフィルター処理を使用したメンバーの同期の制限を 1499 から 50,000 (50K) メンバーに変更しました。 

クラウド同期用に新しく使用可能になった[式ビルダー](../cloud-sync/how-to-expression-builder.md#deploy-the-expression)を確認してください。これは、属性マッピングを使用して AD から Azure AD への属性値の変換を行う場合に、単純な式だけでなく複雑な式を構築するのに役立ちます。

---
