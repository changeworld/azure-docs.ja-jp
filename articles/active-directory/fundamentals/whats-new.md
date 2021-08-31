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
ms.date: 7/30/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d723423fdb57ad60f5665be2d5fa0a760c9d770d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742906"
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

マイ アプリの詳細については、「[マイ アプリ ポータルからアプリにサインインして開始する](../user-help/my-apps-portal-end-user-access.md)」を参照してください。

---

### <a name="public-preview----application-authentication-method-policies"></a>パブリック プレビュー - アプリケーションの認証方法のポリシー

**種類:** 新機能  
**サービス カテゴリ:** MS Graph  
**製品の機能:** 開発者エクスペリエンス
 
MS Graph のアプリケーション認証方法のポリシーを使用すると、IT 管理者がアプリケーションのパスワード シークレットの資格情報に有効期限を付けたり、シークレットの使用を完全にブロックすることができるようになります。 ポリシーは、既定の設定ではテナント全体に適用することができ、さらに特定のアプリケーションやサービス プリンシパルに適用することもできます。 [詳細については、こちらを参照してください](/graph/api/resources/policy-overview?view=graph-rest-1.0)。
 
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
 
リソースの種類 onPremisesPublishing に、アプリケーションのバックエンド SSL 証明書検証が有効かどうかを示すプロパティ "isBackendCertificateValidationEnabled" が追加されました。 すべての新しいアプリケーション プロキシ アプリでは、このプロパティは既定で true に設定されています。 すべての既存のアプリでは、このプロパティは false に設定されます。 詳細については、[リソースの種類 onPremisesPublishing](/graph/api/resources/onpremisespublishing?view=graph-rest-beta) API を参照してください。
 
---

### <a name="general-availability---improved-authenticator-setup-experience-for-add-azure-ad-account-in-microsoft-authenticator-app-by-directly-signing-into-the-app"></a>一般提供 - アプリに直接サインインできるようになり、Microsoft Authenticator アプリに Azure AD アカウントを追加する際の Authenticator セットアップのエクスペリエンスが向上しました。

**種類:** 新機能  
**サービスカテゴリ:** Microsoft Authenticator アプリ  
**製品の機能:** ユーザー認証
 
ユーザーは、既存の認証方法を使用して Microsoft Authenticator アプリに直接サインインし、資格情報を設定できるようになりました。 QR コードをスキャンする必要がなくなり、一時アクセス パス (TAP) や パスワードと SMS (またはその他の認証方法) を使用して Authenticator アプリでアカウントを設定することができます。

これにより、Microsoft Authenticator アプリのユーザー資格情報のプロビジョニング プロセスが改善され、エンド ユーザーはアプリのプロビジョニングをセルフサービスで行うことができるようになります。 [詳細については、こちらを参照してください](../user-help/user-help-auth-app-add-work-school-account.md#sign-in-with-your-credentials)。
 
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

現時点では、このユーザー操作は、ユーザーがデバイスを Azure AD に登録または参加させるときの制御として MFA を有効にできるだけです。 Azure AD デバイスの登録に依存している、または適用されないその他の制御は、このユーザー操作では引き続き無効になります。 [詳細については、こちらを参照してください](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions)。 

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
 
 Azure AD のアクセス レビューを使用して、サービス プリンシパルの特権 Azure AD および Azure リソース ロールへのアクセスをレビューできます。 [詳細については、こちらを参照してください](../privileged-identity-management/pim-how-to-start-security-review.md#open-access-reviews)。
 
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
 
管理者は、特権ロールの割り当てに関するアクセス レビューを作成する際に、適格に割り当てられたユーザーのみ、またはアクティブに割り当てられたユーザーのみのレビューを作成するよう制限することができます。 [詳細については、こちらを参照してください](../privileged-identity-management/pim-how-to-start-security-review.md)。
 
---

### <a name="public-preview----microsoft-graph-apis-for-mobility-mdmmam-management-policies"></a>パブリック プレビュー - Mobility (MDM/MAM) の Microsoft Graph API の管理ポリシー

**種類:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** デバイスのライフサイクル管理
 
Azure AD のモビリティ (MDM/MAM) 構成に対する Microsoft Graph のサポートがパブリック プレビューになりました。 管理者は、Microsoft Graph v1.0 を使用して、Intune のような MDM アプリケーションのユーザー スコープと URL を構成できます。 詳細については、[リソースの種類 mobilityManagementPolicy](/graph/api/resources/mobilitymanagementpolicy?view=graph-rest-beta) に関するページを参照してください

---

### <a name="general-availability---custom-questions-in-access-package-request-flow-in-azure-active-directory-entitlement-management"></a>一般提供 - Azure Active Directory エンタイトルメント管理のアクセス パッケージ要求フローにおけるカスタム質問

**種類:** 新機能  
**サービス カテゴリ:** ユーザー アクセス管理  
**製品の機能:** エンタイトルメント管理
 
Azure AD エンタイトルメント管理のアクセス パッケージ要求フローで、カスタム質問を作成できるようになりました。 この機能を使用することで、ユーザーはアクセス パッケージ ポリシーでカスタム質問を設定することができるようになります。 これらの質問は申請者に対して表示され、このユーザーはアクセス要求プロセスの一部として回答を入力することができます。 承認者はこれらの回答を確認することができ、アクセス要求についてより良い判断を下すための情報として役立てることができます。 [詳細については、こちらを参照してください](../governance/entitlement-management-access-package-create.md#add-requestor-information-to-an-access-package)。

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
 
Azure Active Directory のアクセス レビューの MS Graph API が v1.0 になり、アクセス レビュー機能が完全に構成可能になりました。 [詳細については、こちらを参照してください](/graph/api/resources/accessreviewsv2-root?view=graph-rest-1.0)。
 
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

[Taksel](https://app.taksel.it/admin/integrations)、[IDrive360](../saas-apps/idrive360-tutorial.md)、[VIDA](../saas-apps/vida-tutorial.md)、[ProProfs Classroom](../saas-apps/proprofs-classroom-tutorial.md)、[WAN-Sign](../saas-apps/wan-sign-tutorial.md)、[Citrix Cloud SAML SSO](../saas-apps/citrix-cloud-saml-sso-tutorial.md)、[Fabric](../saas-apps/fabric-tutorial.md)、[DssAD](https://cloudlicensing.deepseedsolutions.com/)、[RICOH Creative Collaboration RICC](https://www.ricoh-europe.com/products/software-apps/collaboration-board-software/ricc/)、[Styleflow](../saas-apps/styleflow-tutorial.md)、[Chaos](https://accounts.chaosgroup.com/corporate_login)、[Traced Connector](https://control.traced.app/signup)、[Squarespace](https://account.squarespace.com/org/azure)、[MX3 Diagnostics Connector](https://mx3www.playground.dynuddns.com/signin-oidc)、[Ten Spot](https://tenspot.co/api/v1/sso/azure/login/)、[Finvari](../saas-apps/finvari-tutorial.md)、[Mobile4ERP](https://play.google.com/store/apps/details?id=com.negevsoft.mobile4erp)、[WalkMe US OpenID Connect](https://www.walkme.com/)、[Neustar UltraDNS](../saas-apps/neustar-ultradns-tutorial.md)、[cloudtamer.io](../saas-apps/cloudtamer-io-tutorial.md)、[A Cloud Guru](../saas-apps/a-cloud-guru-tutorial.md)、[PetroVue](../saas-apps/petrovue-tutorial.md)、[Postman](../saas-apps/postman-tutorial.md)、[ReadCube Papers](../saas-apps/readcube-papers-tutorial.md)、[Peklostroj](https://app.peklostroj.cz/)、[SynCloud](https://onboard.syncloud.io/)、[Polymerhq.io](https://www.polymerhq.io/)、[Bonos](../saas-apps/bonos-tutorial.md)、[Astra Schedule](../saas-apps/astra-schedule-tutorial.md)、[Draup](../saas-apps/draup-inc-tutorial.md)、[Inc](../saas-apps/draup-inc-tutorial.md)、[Applied Mental Health](../saas-apps/applied-mental-health-tutorial.md)、[iHASCO Training](../saas-apps/ihasco-training-tutorial.md)、[Nexsure](../saas-apps/nexsure-tutorial.md)、[XEOX](https://login.xeox.com/)、[Plandisc](https://create.plandisc.com/account/logon)、[foundU](../saas-apps/foundu-tutorial.md)、[Standard for Success Accreditation](../saas-apps/standard-for-success-accreditation-tutorial.md)、[Penji Teams](https://web.penjiapp.com/)、[CheckPoint Infinity Portal](../saas-apps/checkpoint-infinity-portal-tutorial.md)、[Teamgo](../saas-apps/teamgo-tutorial.md)、[Hopsworks.ai](../saas-apps/hopsworks-ai-tutorial.md)、[HoloMeeting 2](https://backend2.holomeeting.io/)

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

攻撃者がユーザーを騙して悪意のあるアプリケーションにサインインさせるフィッシング攻撃を防ぐために、「[アプリケーションの表示名] にサインインしますか?」というプロンプトが追加されます。 このプロンプトは、デバイス コード フローを使用してログインしようとするすべてのユーザーを対象に表示されます。 セキュリティ対策として、これを削除したりバイパスすることはできません。 [詳細については、こちらを参照してください](../develop/reference-breaking-changes.md#the-device-code-flow-ux-will-now-include-an-app-confirmation-prompt)。

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
 
この新しい検出は、攻撃が発生したことが検出された際に、そのセッションのリスクを「高」にすることで当社のセキュリティ チームからユーザーに通知し、ユーザーを保護することを目的とした方法です。 この検出では、関連するサインインも危険なものとしてマークされます。 この検出は、既存の Azure Active Directory の脅威インテリジェンスによるユーザー リスク検出に続き、Microsoft のセキュリティ チームが観測したさまざまな攻撃を完全にカバーします。 [詳細については、こちらを参照してください](../identity-protection/concept-identity-protection-risks.md#user-risk)。
 
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

- 「デバイスの安全を保つためにご協力ください」が「デバイスを設定してアクセス権を取得する」に変更されました
- 「サインインは完了しましたが、このリソースにアクセスするには、管理者の要求によりデバイスが Microsoft で管理されている必要があります。」 から「[組織名] では、[組織名] のメール、ファイル、データにアクセスする前に、このデバイスをセキュリティで保護する必要があります。」に変更。 
- 「今すぐ登録」から「続行」に変更

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
 
Azure リソース ロールと Azure AD ロールの PIM の API の更新版がリリースされました。 Azure リソース ロールの PIM API は ARM API 標準の下でリリースされ、通常の Azure ロール割り当てのロール管理 API と整合しています。 一方、Azure AD ロールの PIM API も、unifiedRoleManagement API と整合する Graph API の下でリリースされています。 この変更によるメリットとしては、次のようなものがあります。

- ロール管理のために PIM API を ARM や Graph のオブジェクトと整合させることで、新しい Azure リソースをオンボードするために PIM を呼び出す必要性が減ります。 
- すべての Azure リソースが自動的に新しい PIM API で動作します。
- ロールの定義や PIM リソース ID を保持するために PIM を呼び出す必要性の削減
- Azure AD と Azure リソースの両方のロールにおいて、PIM のアプリ専用の API 権限をサポート

/privilegedaccess の下にある旧バージョンの PIM の API は引き続き機能しますが、今後はこの新しい API に移行することをお勧めします。 [詳細については、こちらを参照してください](../privileged-identity-management/pim-apis.md)。
 
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

### <a name="public-preview----azure-ad-entitlement-management-now-supports-multi-geo-sharepoint-online"></a>パブリック プレビュー - Azure AD のエンタイトルメント管理で、Multi-Geo の SharePoint Online がサポートされるようになりました

**種類:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** エンタイトルメント管理
 
Multi-Geo の SharePoint Online を使用している組織では、特定の Multi-Geo 環境のサイトをエンタイトルメント管理アクセス パッケージに含められるようになりました。 [詳細については、こちらを参照してください](../governance/entitlement-management-catalog-create.md#add-a-multi-geo-sharepoint-site)。

---

### <a name="public-preview----restore-deleted-apps-from-app-registrations"></a>パブリック プレビュー - アプリの登録から削除されたアプリを復元する

**種類:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** 開発者エクスペリエンス
 
Azure portal から、削除されたアプリの登録を表示、復元、および完全に削除できるようになりました。 これは、個人の Microsoft アカウントのアプリケーションではなく、ディレクトリに関連付けられたアプリケーションにのみ適用されます。 [詳細については、こちらを参照してください](../develop/howto-restore-app.md)。
 
---

### <a name="public-preview----new-user-action-in-conditional-access-for-registering-or-joining-devices"></a>パブリック プレビュー - デバイスを登録または参加させるための条件付きアクセスでの新しい "ユーザー操作"

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護
 
 条件付きアクセスで、「デバイスの登録または参加」という新しいユーザー操作が利用できるようになりました。 このユーザー操作を使用すると、Azure AD デバイス登録に対する多要素認証 (MFA) ポリシーを制御できます。 

現時点では、このユーザー操作は、ユーザーがデバイスを Azure AD に登録または参加させるときの制御として MFA を有効にできるだけです。 Azure AD デバイスの登録に依存している、または適用されないその他の制御は、このユーザー操作では無効になっています。 [詳細については、こちらを参照してください](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions)。 
 
---

### <a name="public-preview----optimize-connector-groups-to-use-the-closest-application-proxy-cloud-service"></a>パブリック プレビュー - 最も近いアプリケーション プロキシ クラウド サービスを使用するようにコネクタ グループを最適化する

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** アクセス制御
 
この新しい機能を使用すると、アプリケーションがホストされている最も近いリージョンのアプリケーション プロキシ サービスにコネクタ グループを割り当てることができます。 これにより、アプリがホーム テナントのリージョン以外のリージョンでホストされているシナリオで、アプリのパフォーマンスを向上させることができます。 [詳細については、こちらを参照してください](../app-proxy/application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview)。 
 
---

### <a name="public-preview----external-identities-self-service-sign-up-in-aad-using-email-one-time-passcode-accounts"></a>パブリック プレビュー - ワンタイム パスコードの電子メール送信アカウントを使用した AAD での External Identities セルフサービス サインアップ

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C

外部ユーザーは、ワンタイム パスコードの電子メール送信アカウントを使用して、Azure AD ファースト パーティおよび LOB アプリにサインアップできるようになります。 [詳細については、こちらを参照してください](../external-identities/one-time-passcode.md)。

---

### <a name="public-preview----availability-of-ad-fs-sign-ins-in-azure-ad"></a>パブリック プレビュー - Azure AD で AD FS サインインが利用可能になります

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

[Bambuser Live Video Shopping](https://lcx.bambuser.com/)、[DeepDyve Inc](https://www.deepdyve.com/azure-sso)、[Moqups](../saas-apps/moqups-tutorial.md)、[RICOH Spaces Mobile](https://ricohspaces.app/welcome)、[Flipgrid](https://auth.flipgrid.com/)、[hCaptcha Enterprise](../saas-apps/hcaptcha-enterprise-tutorial.md)、[SchoolStream ASA](https://jsd.schoolstreamk12.com/ASA/ASAlogin.aspx)、[TransPerfect GlobalLink Dashboard](../saas-apps/transperfect-globallink-dashboard-tutorial.md)、[SimplificaCI](https://app.simplificaci.com.br/)、[Thrive LXP](../saas-apps/thrive-lxp-tutorial.md)、[Lexonis TalentScape](../saas-apps/lexonis-talentscape-tutorial.md)、[Exium](../saas-apps/exium-tutorial.md)、[Sapient](../saas-apps/sapient-tutorial.md)、[TrueChoice](../saas-apps/truechoice-tutorial.md)、[RICOH Spaces](https://ricohspaces.app/welcome)、[Saba Cloud](../saas-apps/learning-at-work-tutorial.md)、[Acunetix 360](../saas-apps/acunetix-360-tutorial.md)、[Exceed.ai](../saas-apps/exceed-ai-tutorial.md)、[GitHub Enterprise Managed User](../saas-apps/github-enterprise-managed-user-tutorial.md)、[Enterprise Vault.cloud for Outlook](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=openid%20profile%20User.Read&client_id=7176efe5-e954-4aed-b5c8-f5c85a980d3a&nonce=4b9e1981-1bcb-4938-a283-86f6931dc8cb)、[Smartlook](../saas-apps/smartlook-tutorial.md)、[Accenture Academy](../saas-apps/accenture-academy-tutorial.md)、[Onshape](../saas-apps/onshape-tutorial.md)、[Tradeshift](../saas-apps/tradeshift-tutorial.md)、[JuriBlox](../saas-apps/juriblox-tutorial.md)、[SecurityStudio](../saas-apps/securitystudio-tutorial.md)、[ClicData](https://app.clicdata.com/)、[Evergreen](../saas-apps/evergreen-tutorial.md)、[Patchdeck](https://patchdeck.com/ad_auth/authenticate/)、[FAX.PLUS](../saas-apps/fax-plus-tutorial.md)、[ValidSign](../saas-apps/validsign-tutorial.md)、[AWS Single Sign-on](../saas-apps/aws-single-sign-on-tutorial.md)、[Nura Space](https://dashboard.nuraspace.com/login)、[Broadcom DX SaaS](../saas-apps/broadcom-dx-saas-tutorial.md)、[Interplay Learning](https://skilledtrades.interplaylearning.com/#login)、[SendPro Enterprise](../saas-apps/sendpro-enterprise-tutorial.md)、[FortiSASE SIA](../saas-apps/fortisase-sia-tutorial.md)

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
 
ヘッダーベースの認証に対する Azure AD アプリケーション プロキシのネイティブ サポートが一般公開されました。 この機能を使用すると、追加コンポーネントのデプロイを必要とせずに、必要なユーザー属性をアプリケーションの HTTP ヘッダーとして構成できます。 [詳細については、こちらを参照してください](../app-proxy/application-proxy-configure-single-sign-on-with-headers.md)。

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
 
### <a name="public-preview----use-a-temporary-access-pass-to-register-passwordless-credentials"></a>パブリック プレビュー - 一時アクセス パスを使用してパスワードレスの資格情報を登録する

**種類:** 新機能  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティ & 保護

一時アクセス パスは、強力な資格情報として機能する、時間限定のパスコードです。ユーザーが強力な認証要素 (FIDO2 セキュリティ キーや Microsoft Authenticator など) アプリを紛失したり忘れたりしたときに、サインインして新しい強力な認証方法を登録する必要がある場合に、パスワードレスの資格情報と復元を実現できます。 [詳細については、こちらを参照してください](../authentication/howto-authentication-temporary-access-pass.md)。

---

### <a name="public-preview----keep-me-signed-in-kmsi-in-next-generation-of-user-flows"></a>パブリック プレビュー - 次世代のユーザー フローでの KMSI (サインインしたままにする) 機能

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C

次世代の B2C ユーザー フローでは、[KMSI (サインインしたままにする)](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) 機能がサポートされるようになりました。これにより、Web やネイティブ アプリケーションを使用しているユーザーのセッション期間を、永続的な Cookie を使用することで延長できます。  この機能は、ユーザーがブラウザーを終了して再び開いた後もセッションをアクティブなままにし、ユーザーがサインアウトしたときに取り消します。

---

### <a name="public-preview----reset-redemption-status-for-a-guest-user"></a>パブリック プレビュー - ゲスト ユーザーの引き換え状態をリセットする

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
引き換え状態をリセットするように、既存の外部ゲスト ユーザーを招待し直すことができるようになりました。これにより、ゲスト ユーザー アカウントをアクセス権を失うことなくそのまま残すことができます。 [詳細については、こちらを参照してください](../external-identities/reset-redemption-status.md)。
 
---

### <a name="public-preview----synchronization-provisioning-apis-now-support-application-permissions"></a>パブリックプレビュー - 同期 (プロビジョニング) API でアプリケーションのアクセス許可をサポート

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
同期 API を呼び出すため、application.readwrite.ownedby をアプリケーションのアクセス許可として使用できるようになりました。 これは、Azure AD からサードパーティ アプリケーション (AWS、Data Bricks など) にプロビジョニングする場合のみサポートされます。 HR プロビジョニング (Workday / Successfactors) や Cloud Sync (AD から Azure AD) については現在サポートされていません。 [詳細については、こちらを参照してください](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true)。
 
---

### <a name="general-availability---authentication-policy-administrator-built-in-role"></a>一般提供 - 認証ポリシー管理者の組み込みロール

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

### <a name="general-availability---domain-name-administrator-built-in-role"></a>一般提供 - ドメイン名管理者の組み込みロール

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