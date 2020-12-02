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
ms.date: 10/30/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71726724e7c018f34b1175f323d0c8e55b604931
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973639"
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
## <a name="october-2020"></a>2020 年 10 月

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>Azure AD オンプレミス ハイブリッド エージェントに影響する Azure TLS 証明書の変更

**種類:** 変更の計画  
**サービス カテゴリ:** 該当なし  
**製品の機能:** プラットフォーム

Microsoft では、異なるルート証明機関 (CA) のセットからの TLS 証明書を使用するように、Azure サービスが更新されています。 この更新は、現在の CA 証明書が CA/ブラウザー フォーラムのベースライン要件の 1 つに準拠していないことによるものです。 この変更は、オンプレミスにインストールされていて、ルート証明書の固定リストを使用して環境を強化している Azure AD ハイブリッド エージェントに影響します。それらは新しい証明書発行者を信頼するように更新する必要があります。

すぐに対処しないと、この変更によってサービスが中断されます。 これらのエージェントには、オンプレミスへのリモート アクセス用の[アプリケーション プロキシ コネクタ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy)、ユーザーが同じパスワードを使用してアプリケーションにサインインできるようにする[パススルー認証](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect)エージェント、AD から Azure AD への同期を実行する[クラウド プロビジョニング プレビュー](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) エージェントが含まれます。 

ファイアウォール規則を使用している環境で、特定の証明書失効リスト (CRL) のダウンロードに限定して送信呼び出しを許可するように設定している場合は、次の CRL と OCSP の URL を許可する必要があります。 この変更およびアクセスを有効にする CRL と OCSP の URL の詳細については、「[Azure TLS 証明書の変更](../../security/fundamentals/tls-certificate-changes.md)」を参照してください。

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>プロビジョニング イベントが監査ログから削除されてプロビジョニング ログのみに公開されます

**種類:** 変更の計画  
**サービス カテゴリ:** レポーティング  
**製品の機能:** 監視とレポート
 
SCIM [プロビジョニング サービス](../app-provisioning/user-provisioning.md)によるアクティビティは、監査ログとプロビジョニング ログの両方に記録されています。 これには、ServiceNow でのユーザーの作成、G Suite でのグループ化、AWS からのロールのインポートなどのアクティビティが含まれます。 今後、これらのイベントはプロビジョニング ログでのみ公開されます。 この変更は、ログ間の重複イベントと、ログ分析でログを使用しているお客様に発生する追加のコストを防ぐために実装されます。 

日付が完了したときに更新プログラムが提供されます。 この非推奨化は、2020 カレンダー年には計画されていません。 

> [!NOTE]
> これは、プロビジョニング サービスによって出力された監査ログ内の同期イベント以外のイベントには影響しません。 アプリケーション、条件付きアクセス ポリシー、ディレクトリ内のユーザーなどの作成といったイベントは、引き続き監査ログに出力されます。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs?context=azure/active-directory/app-provisioning/context/app-provisioning-context)。
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>Azure AD オンプレミス ハイブリッド エージェントに影響する Azure トランスポート層セキュリティ (TLS) 証明書の変更

**種類:** 変更の計画  
**サービス カテゴリ:** 該当なし  
**製品の機能:** プラットフォーム
 
Microsoft では、異なるルート証明機関 (CA) のセットからの TLS 証明書を使用するように、Azure サービスが更新されています。 現在の CA 証明書が CA/ブラウザー フォーラムのベースライン要件の 1 つに準拠していないため、更新が行われます。 この変更は、オンプレミスにインストールされていて、ルート証明書の固定リストを使用して環境を強化している Azure AD ハイブリッド エージェントに影響します。 これらのエージェントは、新しい証明書発行者を信頼するように更新する必要があります。

すぐに対処しないと、この変更によってサービスが中断されます。 次のエージェントが含まれます。 
- オンプレミスへのリモート アクセス用の[アプリケーション プロキシ コネクタ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) 
- ユーザーが同じパスワードを使用してアプリケーションにサインインできるようにする[パススルー認証](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect)エージェント
- AD から Azure AD への同期を行う[クラウド プロビジョニング プレビュー](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) エージェント 

ファイアウォール規則を使用している環境で、特定の証明書失効リスト (CRL) のダウンロードに限定して送信呼び出しを許可するように設定している場合は、CRL と OCSP の URL を許可する必要があります。 この変更およびアクセスを有効にする CRL と OCSP の URL の詳細については、「[Azure TLS 証明書の変更](../../security/fundamentals/tls-certificate-changes.md)」を参照してください。
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>US Gov クラウドでの Azure Active Directory TLS 1.0、TLS 1.1、3DES の非推奨化

**種類:** 変更の計画  
**サービス カテゴリ:** 該当なし  
**製品の機能:** 標準
 
2021 年 3 月 31 日までに Azure Active Directory で次のプロトコルが非推奨になります。
- TLS 1.0
- TLS 1.1
- 3DES 暗号スイート (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Azure、Office 365、Microsoft 365 サービスの Azure Active Directory へのセキュリティで保護された接続を維持するために、クライアントとサーバーおよびブラウザーとサーバーのすべての組み合わせで、TLS 1.2 と最新の暗号スイートを使用する必要があります。

影響を受ける環境は次のとおりです。
- Azure US Gov
- [Office 365 GCC High および DoD](https://docs.microsoft.com/microsoft-365/compliance/tls-1-2-in-office-365-gcc?view=o365-worldwide)
 
---

### <a name="assign-applications-to-roles-on-au-and-object-scope"></a>AU およびオブジェクト スコープのロールへのアプリケーションの割り当て

**種類:** 新機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** アクセス制御
 
この機能を使用すると、管理単位のスコープで管理者ロールにアプリケーション (SPN) を割り当てることができます。 詳細については、「[スコープ付きロールを管理単位に割り当てる](../roles/admin-units-assign-roles.md)」を参照してください。

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>リソースへのアクセスが拒否された場合にゲスト ユーザーを無効化して削除できるようになりました

**種類:** 新機能  
**サービス カテゴリ:** アクセス レビュー  
**製品の機能:** Identity Governance
 
無効化して削除は、組織がグループとアプリで外部のゲストをより適切に管理するのに役立つ、Azure AD アクセス レビューの高度なコントロールです。 ゲストがアクセス レビューで拒否された場合、**無効化して削除** により、30 日間サインインをブロックできます。 30 日が経過すると、テナントから完全に削除されます。

この機能の詳細については、「[Azure AD アクセス レビューを使用して外部 ID の無効化および削除を行う (プレビュー)](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews-preview)」を参照してください。
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>アクセス レビューの作成者がレビュー担当者への電子メールにカスタム メッセージを追加できる

**種類:** 新機能  
**サービス カテゴリ:** アクセス レビュー  
**製品の機能:** Identity Governance
 
Azure AD アクセス レビューで管理者がレビューを作成す場合に、レビュー担当者宛てにカスタム メッセージを書くことができるようになりました。 レビュー担当者は、受信した電子メールで、レビューを完了するように求めるメッセージを確認できます。 この機能の使用方法の詳細については、「[詳細設定](../governance/create-access-review.md#advanced-settings)」セクションの手順 6 を参照してください。

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクタ - 2020 年 10 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合
 
新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Apple Business Manager](../saas-apps/apple-business-manager-provision-tutorial.md)
- [Apple School Manager](../saas-apps/apple-school-manager-provision-tutorial.md)
- [Code42](../saas-apps/code42-provisioning-tutorial.md)
- [AlertMedia](../saas-apps/alertmedia-provisioning-tutorial.md)
- [OpenText Directory Services](../saas-apps/open-text-directory-services-provisioning-tutorial.md)
- [Cinode](../saas-apps/cinode-provisioning-tutorial.md)
- [Global Relay Identity Sync](../saas-apps/global-relay-identity-sync-provisioning-tutorial.md)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)に関するページをご覧ください。
 
---

### <a name="integration-assistant-for-azure-ad-b2c"></a>Azure AD B2C の統合アシスタント

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C
 
Azure AD B2C アプリの登録で統合アシスタント (プレビュー) エクスペリエンスを使用できるようになりました。 このエクスペリエンスは、一般的なシナリオ用にアプリケーションを構成するときに役立ちます。 「[Microsoft ID プラットフォームのベスト プラクティスと推奨事項](../develop/identity-platform-integration-checklist.md)」の詳細を参照してください。
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>Azure portal UI でロール テンプレート ID を表示する

**種類:** 新機能  
**サービス カテゴリ:** Azure ロール  
**製品の機能:** アクセス制御
 

Azure portal 内で各 Azure AD ロールのテンプレート ID を表示できるようになりました。 Azure AD で、選択したロールの **[説明]** を選択します。 

お客様には、PowerShell スクリプトとコードで表示名の代わりにロール テンプレート ID を使用することをお勧めします。 ロール テンプレート ID は、[directoryRoles](https://docs.microsoft.com/graph/api/resources/directoryrole?view=graph-rest-1.0) および [roleDefinition](https://docs.microsoft.com/graph/api/resources/unifiedroledefinition?view=graph-rest-beta) オブジェクトに使用できるようにサポートされています。 ロール テンプレート ID の詳細については、「[ロール テンプレート ID](../roles/permissions-reference.md#role-template-ids)」を参照してください。

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>Azure AD B2C サインアップ ユーザー フロー用 API コネクタがパブリック プレビューになりました

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C
 

API コネクタを Azure Active Directory B2C で使用できるようになりました。 API コネクタを使用すると、Web API を使用してサインアップ ユーザー フローをカスタマイズし、外部クラウド システムと統合することができます。 API コネクタを使用して次のことができます。

- カスタム承認ワークフローと統合する
- ユーザー入力データを検証する
- ユーザー属性を上書する 
- カスタム ビジネス ロジックを実行する 

 詳細については、[API コネクタを使用したサインアップのカスタマイズおよび拡張](../../active-directory-b2c/api-connectors-overview.md)に関するドキュメントを参照してください。

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>エンタイトルメント管理での接続されている組織の状態プロパティ

**種類:** 新機能  
**サービス カテゴリ:** ディレクトリ管理 **製品の機能:** エンタイトルメント管理
 

 接続されているすべての組織に、"状態" というプロパティが追加されています。 この状態を使用すると、"構成済みの接続されたすべての組織" を参照するポリシーで、接続されている組織がどのように使用されるかを制御できます。 値は "構成済み" (組織が "all" 句を使用するポリシーのスコープ内にあることを意味します) または "提案済み" (組織がスコープ外であることを意味します) のいずれかになります。  

手動で作成した接続されている組織の既定の設定は "構成済み" になります。 また、自動作成された (インターネットからあらゆるユーザーがアクセスを要求できるポリシーを使用して作成された) ものは、既定で "提案済み" になります。  2020 年 9 月より前に作成されたすべての接続されている組織は、"構成済み" に設定されます。 管理者は、必要に応じてこのプロパティを更新できます。 [詳細については、こちらを参照してください](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically)。
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>Azure Active Directory の外部 ID に B2C 用の Premium セキュリティ詳細設定が含まれるようになりました

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C
 
ID 保護のリスクベースの条件付きアクセスとリスク検出機能が、[Azure AD B2C](../..//active-directory-b2c/conditional-access-identity-protection-overview.md) で利用できるようになりました。 これらの高度なセキュリティ機能により、お客様は次のことが可能になります。
- インテリジェントな分析情報を活用して、B2C アプリとエンド ユーザー アカウントにかかわるリスクを評価します。 検出には、特殊な移動、匿名 IP アドレス、マルウェアにリンクされた IP アドレス、Azure AD 脅威インテリジェンスが含まれます。 ポータルおよび API ベースのレポートも利用できます。
- B2C ユーザーに対するアダプティブ認証を構成することによって、リスクに自動的に対応します。 アプリの開発者と管理者は、検出されたユーザー リスク レベルに応じて多要素認証 (MFA) を要求したり、アクセスをブロックしたりすることによって、リアルタイムのリスクを軽減できます。また、場所、グループ、アプリに基づいて追加の制御を利用できます。
- Azure AD B2C のユーザー フローおよびカスタム ポリシーとの連携。 条件は、Azure AD B2C の組み込みユーザー フローからトリガーすることも、B2C カスタム ポリシーに組み込むこともできます。 B2C ユーザー フローの他の側面と同様に、エンド ユーザー エクスペリエンスのメッセージングもカスタマイズすることができます。 カスタマイズは、組織の考え方やブランド、対応策の選択肢によって異なります。
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>Azure AD アプリケーション ギャラリーで新しいフェデレーション アプリが利用できるようになりました - 2020 年 10 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2020 年 10 月には、フェデレーションをサポートする次の 27 個の新しいアプリケーションがアプリ ギャラリーに追加されました。

[Sentry](../saas-apps/sentry-tutorial.md)、[Bumblebee - Productivity Superapp](https://app.yellowmessenger.com/user/login)、[ABBYY FlexiCapture Cloud](../saas-apps/abbyy-flexicapture-cloud-tutorial.md)、[EAComposer](../saas-apps/eacomposer-tutorial.md)、[Genesys Cloud Integration for Azure](https://apps.mypurecloud.com/msteams-integration/)、[Zone Technologies Portal](https://portail.zonetechnologie.com/signin)、[Beautiful.ai](../saas-apps/beautiful.ai-tutorial.md)、[Datawiza Access Broker](https://console.datawiza.com/)、[ZOKRI](https://app.zokri.com/)、[CheckProof](../saas-apps/checkproof-tutorial.md)、[Ecochallenge.org](https://events.ecochallenge.org/users/login)、[atSpoke](http://atspoke.com/login)、[Appointment Reminder](https://app.appointmentreminder.co.nz/account/login)、[Cloud.Market](https://cloud.market/)、[TravelPerk](../saas-apps/travelperk-tutorial.md)、[Greetly](https://app.greetly.com/)、[OrgVitality SSO}(../saas-apps/orgvitality-sso-tutorial.md)、[Web Cargo Air](../saas-apps/web-cargo-air-tutorial.md)、[Loop Flow CRM](../saas-apps/loop-flow-crm-tutorial.md)、[Starmind](../saas-apps/starmind-tutorial.md)、[Workstem](https://hrm.workstem.com/login)、[Retail Zipline](../saas-apps/retail-zipline-tutorial.md)、[Hoxhunt](../saas-apps/hoxhunt-tutorial.md)、[MEVISIO](../saas-apps/mevisio-tutorial.md)、[Samsara](../saas-apps/samsara-tutorial.md)、[Nimbus](../saas-apps/nimbus-tutorial.md)、[Pulse Secure Virtual Traffic Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください

Azure AD アプリ ギャラリーにアプリケーションを公開する場合は、こちらの詳細 (https://aka.ms/AzureADAppRequest ) をお読みください

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>プロビジョニング ログをログ分析にストリーミングできるようになりました

**種類:** 新機能  
**サービス カテゴリ:** レポーティング  
**製品の機能:** 監視とレポート
 

次のことを行うためにプロビジョニング ログをログ分析に公開します。
- プロビジョニング ログを 30 日以上保存する
- カスタムのアラートと通知を定義する
- ダッシュボードを構築してログを視覚化する
- 複雑なクエリを実行してログを分析する 

この機能の使用方法については、「[プロビジョニングを Azure Monitor ログと統合する方法の概要](../app-provisioning/application-provisioning-log-analytics.md)」を参照してください。
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>アプリケーション所有者がプロビジョニング ログを表示できるようになりました

**種類:** 変更された機能  
**サービス カテゴリ:** レポーティング  
**製品の機能:** 監視とレポート
 
プロビジョニング サービスによるアクティビティの監視と問題のトラブルシューティングをアプリケーション所有者が行うことを許可できるようになりました。特権ロールを付与する必要はなく、IT がボトルネックになることはありません。 [詳細については、こちらを参照してください](../reports-monitoring/concept-provisioning-logs.md)。
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>10 個の Azure Active Directory ロールの名前変更

**種類:** 変更された機能  
**サービス カテゴリ:** Azure ロール  
**製品の機能:** アクセス制御
 
一部の Azure Active Directory (AD) の組み込みロールには、Microsoft 365 管理センター、Azure AD ポータル、Microsoft Graph に表示される名前とは異なる名前が付いています。 この不整合によって、自動化されたプロセスで問題が発生する可能性があります。 この更新によって 10 個のロールの名前を変更し、一貫性を確保します。 次の表に新しいロール名を示します。

![新しいロール名の表](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>MSAL JS 2.x を使用した SPA の認証コード フローに対する Azure AD B2C サポート

**種類:** 変更された機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C
 
MSAL.js バージョン 2.x でシングル ページ Web アプリ (SPA) の承認コード フローがサポートされるようになりました。 Azure AD B2C において Azure portal で SPA アプリの種類を使用できるようになりました。また、シングル ページ アプリを対象として、PKCE での MSAL.js 承認コード フローの使用がサポートされるようになりました。 これにより、Azure AD B2C を使用している SPA において、新しいブラウザーでの SSO を維持し、新しい認証プロトコルの推奨事項を遵守することができます。 「[Azure Active Directory B2C にシングルページ アプリケーション (SPA) を登録する](../../active-directory-b2c/tutorial-register-spa.md)」のチュートリアルから開始してください。

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>信頼されたデバイス設定での Multi-Factor Authentication (MFA) の記憶に対する更新

**種類:** 変更された機能  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティ & 保護
 

信頼済みデバイス上での [Multi-Factor Authentication (MFA) の記憶](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication)機能に対する最近の更新によって、認証が最大 365 日間に延長されました。 Azure Active Directory (Azure AD) Premium ライセンスの場合は、[条件付きアクセス – サインイン頻度ポリシー](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)も使用できます。これにより、再認証の設定がより柔軟になります。

最適なユーザー エクスペリエンスのためには、信頼済みデバイスで MFA を記憶する設定の代わりに、条件付きアクセスのサインイン頻度を使用して、信頼済みのデバイスや場所、リスクの低いセッションでのセッションの有効期間を延長することをお勧めします。 最初に、[再認証エクスペリエンスの最適化に関する最新のガイダンス](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)を確認してください。

---

## <a name="september-2020"></a>2020 年 9 月

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクタ - 2020 年 9 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合
 
新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Coda](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Recipient Sync](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Webroot Security Awareness](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)に関するページをご覧ください。
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>クラウド プロビジョニングのパブリック プレビューの更新

**種類:** 新機能  
**サービス カテゴリ:** Azure AD クラウド プロビジョニング **製品の機能:** ID ライフサイクル管理
 
Azure AD Connect クラウド プロビジョニングのパブリック プレビューの更新には、お客様からのフィードバックによって開発された 2 つの主な機能強化が取り入れられています。 

- Azure Portal を使用した属性マッピングのエクスペリエンス

    この機能を使用すると、IT 管理者は、現在提供されているさまざまなマッピングの種類を使用して、AD から Azure AD にユーザー、グループ、または連絡先の属性をマップできます。 属性マッピングは、Active Directory から Azure Active Directory にフローする属性の値を標準化するために使用される機能です。 属性値を AD から Azure AD に直接マップするか、ユーザーのプロビジョニング時に式を使用して属性値を変換するかを決定できます。 [詳細情報](../cloud-provisioning/how-to-attribute-mapping.md)

- オンデマンド プロビジョニングまたはユーザー エクスペリエンスのテスト

    構成を設定したら、スコープ内のすべてのユーザーにそれを適用する前に、ユーザー変換が想定どおりに動作するかどうかをテストできます。 オンデマンド プロビジョニングを使用すると、IT 管理者は、AD ユーザーの識別名 (DN) を入力し、それらが想定どおりに同期されるかどうかを確認できます。 オンデマンド プロビジョニングを使用すると、以前に行っていた属性マッピングを想定どおりに動作させるための優れた方法が提供されます。 [詳細情報](../cloud-provisioning/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Azure AD での監査された BitLocker の回復 - パブリック プレビュー

**種類:** 新機能  
**サービス カテゴリ:** デバイス アクセス管理  
**製品の機能:** デバイスのライフサイクル管理
 
IT 管理者またはエンド ユーザーがアクセスできる BitLocker 回復キーを読み取ると、Azure Active Directory によって、回復キーにアクセスしたユーザーをキャプチャする監査ログが生成されるようになりました。 同じ監査で、BitLocker キーが関連付けられていたデバイスの詳細が提供されます。

エンド ユーザーは、[マイ アカウントを使用して回復キーにアクセスする](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key)ことができます。 IT 管理者は、[BitLocker 回復キー API (ベータ)](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) または Azure AD ポータルを使用して、回復キーにアクセスできます。 詳細については、[Azure AD ポータルでの BitLocker キーの表示またはコピー](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys)に関する記事を参照してください。

---

### <a name="teams-devices-administrator-built-in-role"></a>Teams デバイス管理者の組み込みロール

**種類:** 新機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** アクセス制御
 
[Teams デバイス管理者](../roles/permissions-reference.md#teams-devices-administrator)ロールを持つユーザーは、Teams 管理センターから [Teams 認定デバイス](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices)を管理できます。 

このロールを持つユーザーは、すべてのデバイスを一目で見ることができ、デバイスの検索とフィルター処理を行えます。 また、ユーザーは、ログイン アカウント、デバイスの製造元やモデルなど、各デバイスの詳細を確認できます。 ユーザーは、デバイスの設定を変更したり、ソフトウェアのバージョンを更新したりできます。 このロールにより、Teams アクティビティとデバイスの通話品質を調べるアクセス許可は付与されません。
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>ディレクトリ オブジェクトの高度なクエリ機能

**種類:** 新機能  
**サービス カテゴリ:** MS Graph  
**製品の機能:** 開発者エクスペリエンス
 
Azure AD API でディレクトリ オブジェクトに対して導入された新しいクエリ機能はすべて、v1.0 エンドポイントと運用対応で使用できるようになりました。 開発者は、標準の OData 演算子を使用して、ディレクトリ オブジェクトと関連リンクのカウント、検索、フィルター処理、および並べ替えを行うことができます。

詳細については、[こちら](https://aka.ms/BlogPostMezzoGA)のドキュメントを参照してください。また、この[簡単なアンケート](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u)でフィードバックを送ることもできます。
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>パブリック プレビュー: 条件付きアクセス ポリシーを構成したテナントの継続的アクセス評価

**種類:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ID のセキュリティ & 保護
 
条件付きアクセス ポリシーを使用する Azure AD テナントに対し、継続的アクセス評価 (CAE) をパブリック プレビューで使用できるようになりました。 CAE を使用すると、重要なセキュリティ イベントとポリシーがリアルタイムで評価されます。 これには、アカウントの無効化、パスワードのリセット、場所の変更などが含まれます。 詳細については、「[継続的アクセス評価](../conditional-access/concept-continuous-access-evaluation.md)」を参照してください。

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>パブリック プレビュー: アクセス パッケージを要求しているユーザーに対し、承認の決定を向上させるための追加の質問をする

**種類:** 新機能  
**サービス カテゴリ:** ユーザー アクセス管理  
**製品の機能:** エンタイトルメント管理
 
管理者は、Azure AD エンタイトルメント管理のマイ アクセス ポータルで、アクセス パッケージを要求しているユーザーに、業務上の理由だけでなく、追加の質問に回答するよう要求できるようになりました。 これにより、ユーザーの回答が承認者に対して表示され、より正確なアクセス承認を決定できるようになります。 詳細については、「[承認のための追加の要求元情報を収集する (プレビュー)](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview)」を参照してください。
 
---

### <a name="public-preview-enhanced-user-management"></a>パブリック プレビュー: 強化されたユーザー管理

**種類:** 新機能  
**サービス カテゴリ:** [ユーザー管理]  
**製品の機能:** [ユーザー管理]
 

Azure AD ポータルが更新され、[すべてのユーザー] ページと [削除済みのユーザー] ページで、ユーザーを簡単に見つけられるようになりました。 このプレビューの変更点は次のとおりです。 
- オブジェクト ID、ディレクトリ同期の状態、作成タイプ、および ID 発行者を含むより多くのユーザー プロパティが表示されます。
- 検索で、名前、電子メール、およびオブジェクト ID を組み合わせて検索できるようになりました。
- ユーザーの種類 (メンバー、ゲスト、なし)、ディレクトリ同期の状態、作成タイプ、会社名、およびドメイン名によるフィルター処理が機能強化されました。
- 名前、ユーザー プリンシパル名、削除日などのプロパティに基づく新しい並べ替え機能。
- 検索またはフィルターによって更新される新しい合計ユーザー数。

詳細については、「[Azure Active Directory でのユーザー管理の機能強化 (プレビュー)](../enterprise-users/users-search-enhanced.md)」を参照してください。

---

### <a name="new-notes-field-for-enterprise-applications"></a>エンタープライズ アプリケーション用の新しいメモ フィールド

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** SSO

エンタープライズ アプリケーションに、フリー テキストのメモを追加できます。 エンタープライズ アプリケーションでのアプリケーションの管理に役立つ関連情報を追加できます。 詳細については、「[クイック スタート: Azure Active Directory (Azure AD) テナントのアプリケーションのプロパティを構成する](../manage-apps/add-application-portal-configure.md)」を参照してください。 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Azure AD アプリケーション ギャラリーで新しいフェデレーション アプリが利用できるようになりました - 2020 年 9 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2020 年 9 月には、フェデレーションをサポートする次の 34 個の新しいアプリケーションがアプリ ギャラリーに追加されました。

[VMware Horizon - Unified Access Gateway]()、[Pulse Secure PCS](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md)、[Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md)、[Frontitude](https://services.enteksystems.de/sso/microsoft/signup)、[BookWidgets](https://www.bookwidgets.com/sso/office365)、[ZVD_Server](https://zaas.zenmutech.com/user/signin)、[HashData for Business](https://hashdata.app/login.xhtml)、[SecureLogin](https://securelogin.securelogin.nu/sso/azure/login)、[CyberSolutions MAILBASEΣ/CMSS](../saas-apps/cybersolutions-mailbase-tutorial.md)、[CyberSolutions CYBERMAILΣ](../saas-apps/cybersolutions-cybermail-tutorial.md)、[LimbleCMMS](https://auth.limblecmms.com/)、[Glint Inc](../saas-apps/glint-inc-tutorial.md)、[zeroheight](../saas-apps/zeroheight-tutorial.md)、[Gender Fitness](https://app.genderfitness.com/)、[Coeo Portal](https://my.coeo.com/)、[Grammarly](../saas-apps/grammarly-tutorial.md)、[Fivetran](../saas-apps/fivetran-tutorial.md)、[Kumolus](../saas-apps/kumolus-tutorial.md)、[RSA Archer Suite](../saas-apps/rsa-archer-suite-tutorial.md)、[TeamzSkill](../saas-apps/teamzskill-tutorial.md)、[raumfürraum](../saas-apps/raumfurraum-tutorial.md)、[Saviynt](../saas-apps/saviynt-tutorial.md)、[BizMerlinHR](https://marketplace.bizmerlin.net/bmone/signup)、[Mobile Locker](../saas-apps/mobile-locker-tutorial.md)、[Zengine](../saas-apps/zengine-tutorial.md)、[CloudCADI](https://app.cloudcadi.com/login)、[Simfoni Analytics](https://simfonianalytics.com/accounts/microsoft/login/)、[Priva Identity & Access Management](https://my.priva.com/)、[Nitro Pro](https://www.gonitro.com/nps/product-details/downloads)、[Eventfinity](../saas-apps/eventfinity-tutorial.md)、[Fexa](../saas-apps/fexa-tutorial.md)、[Secured Signing Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal)、[Secured Signing Enterprise Portal AAD Setup](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal)、[Wistec Online](https://wisteconline.com/auth/oidc)、[Oracle PeopleSoft - Protected by F5 BIG-IP APM](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください。

Azure AD アプリ ギャラリーにアプリケーションを公開する場合は、こちらの詳細 (https://aka.ms/AzureADAppRequest ) をお読みください。

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Azure AD エンタイトルメント管理での新しい委任ロール: アクセス パッケージ割り当てマネージャー

**種類:** 新機能  
**サービス カテゴリ:** ユーザー アクセス管理  
**製品の機能:** エンタイトルメント管理
 
Azure AD エンタイトルメント管理に、割り当てを管理するための細かなアクセス許可が提供される、新しいアクセス パッケージ割り当てマネージャー ロールが追加されました。 このロールでタスクを委任されたユーザーは、アクセス パッケージの割り当て管理をビジネス所有者に委任できます。 ただし、アクセス パッケージ割り当てマネージャーは、アクセス パッケージのポリシーや、管理者によって設定されたその他のプロパティを、変更することはできません。 

この新しいロールを使用すると、割り当ての管理を委任し、他のすべてのアクセス パッケージ構成の管理制御を維持するために必要な、最小限の特権を実現できます。 詳細については、「[エンタイトルメント管理のロール](../governance/entitlement-management-delegate.md#entitlement-management-roles)」を参照してください。
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Privileged Identity Management のオンボード フローに対する変更

**種類:** 変更された機能  
**サービス カテゴリ:** Privileged Identity Management  
**製品の機能:** Privileged Identity Management
 
以前は、Privileged Identity Management (PIM) にオンボードするには、ユーザーの同意と、Azure AD MFA への登録が含まれる PIM のブレードでのオンボード フローが必要でした。 Azure AD の [ロールと管理者] ブレードへの PIM エクスペリエンスの最近の統合により、このエクスペリエンスは削除されています。 有効な P2 ライセンスを持つテナントは、PIM に自動的にオンボードされます。

PIM へのオンボードにより、テナントが直接悪影響を受けることはありません。 次の変更が予想されます。
- PIM または Azure AD の [ロールと管理者] ブレードで割り当てを行うときに、開始時刻と終了時刻でアクティブか有資格かなどの、追加の割り当てオプション。 
- 割り当てエクスペリエンスに直接導入された、管理単位やカスタム役割などの、追加のスコープ メカニズム。 
- 全体管理者または特権ロール管理者の場合は、PIM の週刊ダイジェストなど、いくつかの追加メールの受け取りを始めることができます。 
- さらに、ロールの割り当てに関連する監査ログに MS-PIM サービス プリンシパルが表示される場合もあります。 この予想される変更は、通常のワークフローには影響しません。

 詳細については、「[Privileged Identity Management の使用開始](../privileged-identity-management/pim-getting-started.md)」を参照してください。

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Azure AD エンタイトルメント管理: 選択されたカタログに現在含まれるリソースが、アクセス パッケージ リソースの [選択] ペインに既定で表示されるようになる

**種類:** 変更された機能  
**サービス カテゴリ:** ユーザー アクセス管理  
**製品の機能:** エンタイトルメント管理
 

アクセス パッケージ作成フローの [リソース ロール] タブで、[選択] ペインの動作が変更されています。 現在の既定の動作の場合、ユーザーによって所有されているすべてのリソースと、選択したカタログに追加されたリソースが表示されます。 

このエクスペリエンスは、カタログに現在追加されているリソースのみが既定で表示されるように変更されます。これにより、ユーザーはカタログからリソースを簡単に選択できるようになります。 この更新により、アクセス パッケージに追加するリソースを見つけやすくなり、カタログに含まれていないユーザーが所有するリソースを誤って追加するリスクが減ります。 詳細については、「[Azure AD エンタイトルメント管理で新しいアクセス パッケージを作成する](../governance/entitlement-management-access-package-create.md#resource-roles)」を参照してください。
 
---

## <a name="august-2020"></a>2020 年 8 月 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Azure Multi-Factor Authentication Server ファイアウォールの要件に対する更新

**種類:** 変更の計画  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティ & 保護
 
2020 年 10 月 1 日以降、Azure MFA Server のファイアウォール要件には、追加の IP 範囲が必要になります。

組織に送信ファイアウォール規則がある場合は、MFA サーバーが必要なすべての IP 範囲と通信できるように規則を更新します。 IP 範囲については、「[Azure Multi-Factor Authentication Server ファイアウォールの要件](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements)」を参照してください。

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>ID セキュリティ スコアのユーザー エクスペリエンスに対する今後の変更点

**種類:** 変更の計画  
**サービス カテゴリ:** ID 保護 **製品の機能:** ID のセキュリティ & 保護

ID セキュリティ スコア ポータルは、Microsoft セキュリティ スコアの[新しいリリース](/microsoft-365/security/mtp/microsoft-secure-score-whats-new?view=o365-worldwide)で導入された変更に合わせて更新されています。 

変更を含むプレビュー バージョンは、9 月初めに利用可能になります。 プレビュー バージョンでの変更点は次のとおりです。
- ID セキュリティ スコアは、Microsoft セキュリティ スコアでのブランドに合わせて "ID のセキュリティ スコア" に名前が変更されます
- ポイントが標準スケールに正規化され、ポイントではなくパーセンテージで報告されます

このプレビューでは、お客様は既存のエクスペリエンスと新しいエクスペリエンスを切り替えることができます。 このプレビューは、2020 年 11 月末まで続きます。 プレビューの後、お客様は自動的に新しい UX エクスペリエンスに誘導されます。

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Azure AD での新しい制限付きゲスト アクセス許可 - パブリック プレビュー

**種類:** 新機能  
**サービス カテゴリ:** アクセス制御   
**製品の機能:** [ユーザー管理]

ゲスト ユーザーに対するディレクトリ レベルのアクセス許可が更新されました。 これらのアクセス許可により、管理者は外部のゲスト ユーザーのアクセスに対して追加の制限と制御を要求できます。 管理者は、外部のゲストによるユーザーとグループのプロファイルおよびメンバーシップ情報へのアクセスに対して制限を追加できるようになりました。 このパブリック プレビュー機能を使用すると、お客様は、グループ メンバーシップを難読化することで、外部ユーザーのアクセスを大規模に管理できます。これには、ゲスト ユーザーが属するグループのメンバーシップの表示の制限が含まれます。

詳細については、[制限されたゲスト アクセス許可](../enterprise-users/users-restrict-guest-permissions.md)および[ユーザーの既定のアクセス許可](./users-default-permissions.md)に関する記事を参照してください。
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>サービス プリンシパルに対するデルタ クエリの一般提供

**種類:** 新機能  
**サービス カテゴリ:** MS Graph  
**製品の機能:** 開発者エクスペリエンス
 
Microsoft Graph デルタ クエリで、v1.0 のリソースの種類がサポートされるようになりました。
- サービス プリンシパル

クライアントは、これらのリソースへの変更を効率的に追跡し、ローカル データ ストアでのそれらのリソースに対する変更を同期するための最適なソリューションを提供できます。 クエリでこれらのリソースを構成する方法については、「[デルタ クエリを使用して、Microsoft Graph データの変更を追跡する](/graph/delta-query-overview)」を参照してください。
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>oAuth2PermissionGrant に対するデルタ クエリの一般提供

**種類:** 新機能  
**サービス カテゴリ:** MS Graph  
**製品の機能:** 開発者エクスペリエンス

Microsoft Graph デルタ クエリで、v1.0 のリソースの種類がサポートされるようになりました。
- OAuth2PermissionGrant

クライアントは、これらのリソースへの変更を効率的に追跡し、ローカル データ ストアでのそれらのリソースに対する変更を同期するための最適なソリューションを提供できます。 クエリでこれらのリソースを構成する方法については、「[デルタ クエリを使用して、Microsoft Graph データの変更を追跡する](/graph/delta-query-overview)」を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Azure AD アプリケーション ギャラリーで新しいフェデレーション アプリが利用できるようになりました - 2020 年 8 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2020 年 8 月には、フェデレーションをサポートする次の 25 個の新しいアプリケーションがアプリ ギャラリーに追加されました。

[Backup365](https://portal.backup365.io/login)、[Soapbox](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2)、[Alma SIS](https://almau.getalma.com/)、[Enlyft Dynamics 365 Connector](http://enlyft.com/)、[Serraview Space Utilization Software Solutions](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md)、[Uniq](https://web.uniq.app/)、[Visibly](../saas-apps/visibly-tutorial.md), [Zylo](../saas-apps/zylo-tutorial.md)、[Edmentum - Courseware Assessments Exact Path](https://auth.edmentum.com/elf/login)、[CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome)、[Altamira HRM](../saas-apps/altamira-hrm-tutorial.md)、[WireWheel](../saas-apps/wirewheel-tutorial.md)、[Zix Compliance and Capture](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common)、[Greenlight Enterprise Business Controls Platform](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md)、[Genetec Clearance](https://www.clearance.network/)、[iSAMS](../saas-apps/isams-tutorial.md)、[VeraSMART](../saas-apps/verasmart-tutorial.md)、[Amiko](https://amiko.web.rivero.app/)、[Twingate](https://auth.twingate.com/signup)、[Funnel Leasing](https://nestiolistings.com/sso/oidc/azure/authorize/)、[Scalefusion](https://scalefusion.com/users/sign_in/)、[Bpanda](https://goto.bpanda.com/login)、[Vivun Calendar Connect](https://app.vivun.com/dashboard/calendar/connect)、[FortiGate SSL VPN](../saas-apps/fortigate-ssl-vpn-tutorial.md)、[Wandera End User](https://www.wandera.com/)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください

Azure AD アプリ ギャラリーにアプリケーションを公開する場合は、こちらの詳細 (https://aka.ms/AzureADAppRequest ) をお読みください

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>Azure AD DS でリソース フォレストを使用できるようになりました 

**種類:** 新機能 **サービス カテゴリ:** Azure AD Domain Services   
**製品の機能:** Azure AD Domain Services
 
Azure AD Domain Services でのリソース フォレストの機能が一般提供されました。 スマートカード認証を含め、Azure AD Domain Services を使用するために、パスワード ハッシュ同期なしで承認を有効にできるようになりました。 詳細については、「[Azure Active Directory Domain Services (プレビュー) のレプリカ セットの概念と機能](../../active-directory-domain-services/concepts-replica-sets.md)」を参照してください。
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>Azure AD DS マネージド ドメインのリージョン レプリカのサポートを使用できるようになりました

**種類:** 新機能   
**サービス カテゴリ:** Azure AD Domain Services  
**製品の機能:** Azure AD Domain Services
 
マネージド ドメインを拡張して、Azure AD テナントごとに複数のレプリカ セットを使用できます。 レプリカ セットは、Azure AD Domain Services がサポートされている任意の Azure リージョンの、ピアリングされた任意の仮想ネットワークに追加できます。 異なる Azure リージョンにレプリカ セットを追加することで、1 つの Azure リージョンがオフラインになった場合に、レガシ アプリケーションの地理的なディザスター リカバリーが実現されます。 詳細については、「[Azure Active Directory Domain Services (プレビュー) のレプリカ セットの概念と機能](../../active-directory-domain-services/concepts-replica-sets.md)」を参照してください。

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Azure AD の自分のサインインの一般提供

**種類:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** エンド ユーザー エクスペリエンス
 
Azure AD の [自分のサインイン] は、企業ユーザーがサインイン履歴を調べて異常なアクティビティを確認できるようにする新しい機能です。 また、この機能により、エンド ユーザーは疑わしいアクティビティについて "自分ではありません" または "自分の操作です" を報告できます。 この機能の使用方法の詳細については、「[[自分のサインイン] ページから最近のサインイン アクティビティを表示および検索する](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity)」を参照してください。
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>SAP SuccessFactors HR 主導による Azure AD へのユーザー プロビジョニングが一般提供されるようになりました

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
権限のある ID ソースとしての SAP SuccessFactors と Azure AD を統合できるようになり、新しい採用者や解雇などの HR イベントを使用してエンド ツー エンドの ID ライフサイクルを自動化し、Azure AD でのアカウントのプロビジョニングとプロビジョニング解除を促進できます。 

Azure AD への SAP SuccessFactors 受信プロビジョニングを構成する方法の詳細については、「[SAP SuccessFactors から Active Directory へのユーザー プロビジョニングを構成する](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)」チュートリアルを参照してください。
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Azure AD B2C に対するカスタム Open ID Connect MS Graph API のサポート

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C
 
以前のカスタム Open ID Connect プロバイダーは、Azure portal によってのみ追加または管理できました。 Azure AD B2C のお客様は、Microsoft Graph API のベータ版を使用してそれらを追加および管理することもできるようになりました。 API でこのリソースを構成する方法については、「[identityProvider リソース タイプ](/graph/api/resources/identityprovider?view=graph-rest-beta)」を参照してください。
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Azure AD の組み込みロールをクラウド グループに割り当てる

**種類:** 新機能  
**サービス カテゴリ:** Azure AD ロール  
**製品の機能:** アクセス制御

この新機能を使用して、Azure AD の組み込みロールをクラウド グループに割り当てることができるようになりました。 たとえば、SharePoint 管理者ロールを Contoso_SharePoint_Admins グループに割り当てることができます。 また、PIM を使用すると、継続的なアクセス権を付与するのではなく、グループをロールの適格なメンバーにすることもできます。 この機能の構成方法については、「[クラウド グループを使用して Azure Active Directory でロールの割り当てを管理する (プレビュー)](../roles/groups-concept.md)」を参照してください。
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Insights ビジネス リーダーの組み込みロールを利用できるようになりました

**種類:** 新機能  
**サービス カテゴリ:** Azure AD ロール  
**製品の機能:** アクセス制御
 
Insights ビジネス リーダー ロールのユーザーは、[M365 Insights アプリケーション](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics)を使用して、一連のダッシュボードと分析情報にアクセスできます。 これには、すべてのダッシュボード、表示される分析情報、およびデータ探索機能へのフル アクセスが含まれます。 ただし、このロールのユーザーには、製品の構成設定へのアクセス権がありません (これは Insights 管理者ロールの責任範囲です)。 このロールの詳細については、「[Azure Active Directory での管理者ロールのアクセス許可](../roles/permissions-reference.md#insights-business-leader)」を参照してください
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Insights 管理者の組み込みロールを利用できるようになりました

**種類:** 新機能  
**サービス カテゴリ:** Azure AD ロール  
**製品の機能:** アクセス制御
 
Insights 管理者ロールのユーザーは、[M365 Insights アプリケーション](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics)の管理機能の完全なセットにアクセスできます。 このロールのユーザーは、ディレクトリ情報の読み取り、サービスの正常性の監視、サポート チケットの提出、Insights 管理者設定の側面へのアクセスを行うことができます。 このロールの詳細については、「[Azure Active Directory での管理者ロールのアクセス許可](../roles/permissions-reference.md#insights-administrator)」を参照してください
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>アプリケーション管理者とクラウド アプリケーション管理者は、アプリケーションの拡張機能プロパティを管理できます

**種類:** 変更された機能  
**サービス カテゴリ:** Azure AD ロール  
**製品の機能:** アクセス制御
 
以前は、[拡張機能プロパティ](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http)を管理できるのは全体管理者だけでした。 アプリケーション管理者とクラウド アプリケーション管理者もこの機能を利用できるようになりました。
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>MIM 2016 SP2 修正プログラム 4.6.263.0 とコネクタ 1.1.1301.0

**種類:** 変更された機能  
**サービス カテゴリ:** Microsoft Identity Manager  
**製品の機能:** ID ライフサイクル管理

Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2) の[修正プログラム ロールアップ パッケージ (ビルド 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) の提供が開始されました。 このロールアップ パッケージには、MIM CM、MIM 同期マネージャー、PAM コンポーネントの更新プログラムが含まれています。 さらに、MIM 汎用コネクタ ビルド 1.1.1301.0 には、Graph コネクタの更新プログラムが含まれています。

---
 
## <a name="july-2020"></a>2020 年 7 月

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>IT 管理者は、条件付きアクセスを使用してクライアント アプリを対象にする必要があります

**種類:** 変更の計画   
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護
 
条件付きアクセスでのクライアント アプリ条件の GA リリースにより、新しいポリシーが既定で、すべてのクライアント アプリケーションに適用されるようになりました。 これには、レガシ認証クライアントが含まれます。 既存のポリシーは変更されませんが、ポリシーによって提供されるクライアント アプリを簡単に確認できるように、"*構成の "はい、いいえ"* " の切り替えは既存のポリシーから削除されます。 

新しいポリシーを作成するときは、従来の認証をまだ使用しているユーザーとサービス アカウントを除外してください。そうしないと、ブロックされます。 [詳細については、こちらを参照してください](../conditional-access/concept-conditional-access-conditions.md)。
 
---

### <a name="upcoming-scim-compliance-fixes"></a>今後の SCIM コンプライアンスの修正

**種類:** 変更の計画  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
Azure AD プロビジョニング サービスでは、アプリケーションとの統合のために SCIM 標準が利用されています。 SCIM 標準の実装は進化しており、PATCH 操作の実行方法に関する変更や、リソースでのプロパティ "アクティブ" の設定が想定されています。 [詳細については、こちらを参照してください](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md)。
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>Azure 管理者ポータルのグループ所有者の設定が変更される

**種類:** 変更の計画  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション

グループの全般設定ページの所有者の設定は、Azure 管理者ポータルとアクセス パネルで制限されたユーザー グループに対して所有者割り当て特権を制限するように構成できます。 間もなく、この 2 つの UX ポータルでグループ所有者特権を割り当てることができるだけでなく、PowerShell や Microsoft Graph など、バックエンドでポリシーを適用してエンドポイント間で一貫性のある動作を実現することもできるようになります。 

現在の設定を使用していないお客様については現在の設定を無効にする作業が始められており、今後数か月の間に、グループ所有者特権に対してユーザーを対象にするオプションが提供されます。 グループ設定の更新に関するガイダンスについては、[Azure Active Directory](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) を使用したグループ情報の編集に関するページを参照してください。

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Azure Active Directory 登録サービスでの TLS 1.0 および 1.1 のサポートが終了します

**種類:** 変更の計画  
**サービス カテゴリ:** デバイスの登録と管理  
**製品の機能:** プラットフォーム

トランスポート層セキュリティ (TLS) 1.2 およびサーバーとクライアントの更新は、間もなく Azure Active Directory Device Registration サービスと通信するようになります。 Azure AD Device Registration サービスとの通信用の TLS 1.0 および 1.1 のサポートは廃止されます。
- 2020 年 8 月 31 日、すべてのソブリン クラウド (GCC High、DoD など)
- 2020 年 10 月 30 日、すべての商用クラウド

Azure AD 登録サービスに対する TLS 1.2 について[詳細を確認してください](../devices/reference-device-registration-tls-1-2.md)。

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Azure AD サインイン ログで確認できる Windows Hello for Business のサインイン

**種類:** 固定  
**サービス カテゴリ:** レポーティング  
**製品の機能:** 監視とレポート
 
Windows Hello for Business を使用すると、エンド ユーザーはジェスチャ (PIN や生体認証など) を使用して Windows コンピューターにサインインできます。 Azure AD 管理者は、組織のパスワードレス認証への移行の過程として、Windows Hello for Business のサインインを他の Windows サインインと区別することができます。 

管理者は、Azure portal の Azure AD サインイン ブレードで Windows サインイン イベントに対する [認証の詳細] タブを調べることにより、Windows 認証で Windows Hello for Business が使用されたかどうかを確認できます。 Windows Hello for Business 認証では、[認証方法] フィールドに "WindowsHelloForBusiness" が含まれます。 サインイン ログの解釈の詳細については、[サインイン ログのドキュメント](../reports-monitoring/concept-sign-ins.md)を参照してください。
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>グループ削除の動作の修正とパフォーマンスの向上

**種類:** 固定  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
以前は、グループを "スコープ内" から "スコープ外" に変更し、変更が完了する前に管理者が再起動をクリックすると、グループ オブジェクトは削除されませんでした。 現在は、グループ オブジェクトがスコープ外になった (無効、削除済み、未割り当て、またはスコープ フィルターを通過しなかった) 時点で、ターゲット アプリケーションから削除されます。 [詳細については、こちらを参照してください](../app-provisioning/how-provisioning-works.md#incremental-cycles)。
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>パブリック プレビュー:管理者は、アクセス レビューを作成するときに、レビュー担当者宛のメールにカスタム コンテンツを追加できるようになりました

**種類:** 新機能  
**サービス カテゴリ:** アクセス レビュー  
**製品の機能:** Identity Governance
 
新しいアクセス レビューが作成されると、レビュー担当者は、アクセス レビューの完了を求めるメールを受け取ります。 多くのお客様から、連絡先情報などのカスタム コンテンツ、またはレビュー担当者に指示するための他のサポート コンテンツをメールに追加する機能の要望がありました。 

パブリック プレビューになった機能を使用すると、管理者は、Azure AD アクセス レビューの [詳細設定] セクションでコンテンツを追加することにより、レビュー担当者に送信されるメールでカスタム コンテンツを指定できます。 アクセス レビューの作成に関するガイダンスについては、「[グループとアプリケーションのアクセス レビューを Azure AD アクセス レビューで作成する](../governance/create-access-review.md)」を参照してください。
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>シングルページ アプリの承認コード フローを使用できるようになりました

**種類:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** 開発者エクスペリエンス
 
最新のブラウザーのサード パーティ Cookie の制限 (Safari ITP など) により、SPA では、SSO を維持するために、暗黙的フローではなく、承認コード フローを使用する必要があります。MSAL.js v 2.x では、認証コード フローをサポートするようになりました。 

Azure portal の対応する更新により、ご自分の SPA の種類を "spa" に更新し、認証コード フローを使用することができます。 詳細については、「[承認コード フローを使用して JavaScript SPA 内でユーザーをサインインさせ、アクセス トークンを取得する](../develop/quickstart-v2-javascript-auth-code.md)」を参照してください。
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Azure AD アプリケーション プロキシでリモート デスクトップ サービス Web クライアントがサポートされるようになりました

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** アクセス制御

Azure AD アプリケーション プロキシでリモート デスクトップ サービス (RDS) Web クライアントがサポートされるようになりました。 RDS Web クライアントを使用すると、ユーザーは、Microsoft Edge、Internet Explorer 11、Google Chrome などの任意の HTML5 対応ブラウザーを使用して、リモート デスクトップ インフラストラクチャにアクセスできます。ユーザーは、どこからでもローカル デバイスと同様にリモート アプリやデスクトップと対話できます。 Azure AD アプリケーション プロキシを使用すると、すべての種類のリッチ クライアント アプリに対して事前認証と条件付きアクセス ポリシーを適用することにより、RDS デプロイのセキュリティを強化できます。 詳細については、「[Azure AD アプリケーション プロキシを使用したリモート デスクトップの発行](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)」を参照してください。
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>次世代の Azure AD B2C ユーザー フローのパブリック プレビュー

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C
 
簡素化されたユーザー フロー エクスペリエンスは、プレビュー機能と同等の機能を備えており、すべての新機能のホームとなります。 ユーザーは、同じユーザー フロー内で新機能を有効にすることができるため、新しい機能をリリースするたびに複数のバージョンを作成する必要が少なくなります。 最後に、新しいユーザー フレンドリな UX により、ユーザー フローの選択と作成が簡単になります。 今すぐ[ユーザー フローを作成](../../active-directory-b2c/tutorial-create-user-flows.md)して試してください。 

ユーザー フローの詳細については、「[Azure Active Directory B2C のユーザー フロー バージョン](../../active-directory-b2c/user-flow-versions.md#:~:text=    User flow  ,account. Usi ...  1 more rows )」を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Azure AD アプリケーション ギャラリーで新しいフェデレーション アプリが利用できるようになりました - 2020 年 7 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2020 年 7 月には、フェデレーションをサポートする次の 55 個の新しいアプリケーションがアプリ ギャラリーに追加されました。

[Clap Your Hands](http://www.rmit.com.ar/)、[Appreiz](https://microsoftteams.appreiz.com/)、[Inextor Vault](https://inexto.com/inexto-suite/inextor)、[Beekast](https://my.beekast.com/)、[Templafy OpenID Connect](https://app.templafy.com/)、[PeterConnects receptionist](https://msteams.peterconnects.com/)、[AlohaCloud](https://appfusions.alohacloud.com/auth)、[Control Tower](https://bpm.tnxcorp.com/sso/microsoft)、[Cocoom](https://start.cocoom.com/)、[COINS Construction Cloud](https://sso.coinsconstructioncloud.com/#login/)、[Medxnote MT](https://task.teamsmain.medx.im/authorization)、[Reflekt](https://reflekt.konsolute.com/login)、[Rever](https://app.reverscore.net/access)、[MyCompanyArchive](https://login.mycompanyarchive.com/)、[GReminders](https://app.greminders.com/o365-oauth)、[Titanfile](../saas-apps/titanfile-tutorial.md)、[Wootric](../saas-apps/wootric-tutorial.md)、[SolarWinds Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US)、[OpenText Directory Services](../saas-apps/opentext-directory-services-tutorial.md)、[Datasite](../saas-apps/datasite-tutorial.md)、[BlogIn](../saas-apps/blogin-tutorial.md)、[IntSights](../saas-apps/intsights-tutorial.md)、[kpifire](../saas-apps/kpifire-tutorial.md)、[Textline](../saas-apps/textline-tutorial.md)、[Cloud Academy - SSO](../saas-apps/cloud-academy-sso-tutorial.md)、[Community Spark](../saas-apps/community-spark-tutorial.md)、[Chatwork](../saas-apps/chatwork-tutorial.md)、[CloudSign](../saas-apps/cloudsign-tutorial.md)、[C3M Cloud Control](../saas-apps/c3m-cloud-control-tutorial.md)、[SmartHR](https://smarthr.jp/)、[NumlyEngage™](../saas-apps/numlyengage-tutorial.md)、[Michigan Data Hub Single Sign-On](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md)、[Egress](../saas-apps/egress-tutorial.md)、[SendSafely](../saas-apps/sendsafely-tutorial.md)、[Eletive](https://app.eletive.com/)、[Right-Hand Cybersecurity ADI](https://right-hand.ai/)、[Fyde Enterprise Authentication](https://enterprise.fyde.com/)、[Verme](../saas-apps/verme-tutorial.md)、[Lenses.io](../saas-apps/lensesio-tutorial.md)、[Momenta](../saas-apps/momenta-tutorial.md)、[Uprise](https://app.uprise.co/sign-in)、[Q](https://q.moduleq.com/login)、[CloudCords](../saas-apps/cloudcords-tutorial.md)、[TellMe Bot](https://tellme365liteweb.azurewebsites.net/)、[Inspire](https://app.inspiresoftware.com/)、[Maverics Identity Orchestrator SAML Connector](https://www.strata.io/identity-fabric/)、[Smartschool (School Management System)](https://smartschoolz.com/login)、[Zepto - Intelligent timekeeping](https://user.zepto-ai.com/signin)、[Studi.ly](https://studi.ly/)、[Trackplan](http://www.trackplanfm.com/)、[Skedda](../saas-apps/skedda-tutorial.md)、[WhosOnLocation](../saas-apps/whos-on-location-tutorial.md)、[Coggle](../saas-apps/coggle-tutorial.md)、[Kemp LoadMaster](https://kemptechnologies.com/cloud-load-balancer/)、[BrowserStack Single Sign-on](../saas-apps/browserstack-single-sign-on-tutorial.md)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください

Azure AD アプリ ギャラリーにアプリケーションを公開する場合は、こちらの詳細 (https://aka.ms/AzureADAppRequest ) をお読みください

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2020"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクタ - 2020 年 7 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合

新しく統合されたアプリ [LinkedIn Learning](../saas-apps/linkedin-learning-provisioning-tutorial.md) でのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)に関するページをご覧ください。

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>すべてのスコープでロールの割り当てを表示し、それらを csv ファイルにダウンロードする機能

**種類:** 変更された機能  
**サービス カテゴリ:** Azure AD ロール  
**製品の機能:** アクセス制御
 
Azure AD ポータルの [ロールと管理者] タブで、ロールのすべてのスコープについてロールの割り当てを表示できるようになりました。 また、ロールごとにそれらのロール割り当てを CSV ファイルにダウンロードすることもできます。 ロール割り当ての表示と追加に関するガイダンスについては、「[Azure Active Directory で管理者ロールを表示して割り当てる](../roles/manage-roles-portal.md)」を参照してください。
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>非推奨になった Azure Multi-Factor Authentication ソフトウェア開発 (Azure MFA SDK)

**種類:** 非推奨  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティ & 保護
 
2017 年 11 月に最初に発表されたように、2018 年 11 月 14 日をもって Azure Multi-Factor Authentication ソフトウェア開発 (Azure MFA SDK) の提供は終了しました。 Microsoft は、2020 年 9 月 30 日に有効な SDK サービスを終了します。 SDK に対するすべての呼び出しは失敗します。

組織で Azure MFA SDK を使用している場合は、2020 年 9 月 30 日までに移行する必要があります。
- MIM に対する Azure MFA SDK: MIM で SDK を使用している場合は、Azure MFA Server に移行し、次の[の手順](/microsoft-identity-manager/working-with-mfaserver-for-mim)に従って Privileged Access Management (PAM) をアクティブ化する必要があります。   
- カスタマイズされたアプリに対する Azure MFA SDK: アプリを Azure AD に統合し、条件付きアクセスを使用して MFA を適用することを検討してください。 始めるには、こちらの[ページ](../manage-apps/plan-an-application-integration.md)を確認してください。 

---

## <a name="june-2020"></a>2020 年 6 月 

### <a name="user-risk-condition-in-conditional-access-policy"></a>条件付きアクセス ポリシーのユーザー リスク条件

**種類:** 変更の計画  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護
 

Azure AD 条件付きアクセス ポリシーのユーザー リスク サポートを使用すると、複数のユーザー リスクベースのポリシーを作成できます。 ユーザーやアプリごとに、必要な最小ユーザー リスク レベルが異なる可能性があります。 ユーザー リスクに基づいて、アクセスをブロックしたり、多要素認証を要求したり、パスワードの変更をセキュリティで保護したり、Microsoft Cloud App Security にリダイレクトして追加監査などのセッション ポリシーを強制したりするポリシーを作成できます。

ユーザー リスク条件には、Azure AD Premium P2 が必要です。P2 オファリングである Azure Identity Protection が使用されるためです。 条件付きアクセスの詳細については、[Azure AD 条件付きアクセスのドキュメント](../conditional-access/index.yml)を参照してください。

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>要求時に SPNameQualifier を設定する必要があるアプリが SAML SSOでサポートされるようになりました

**種類:** 固定  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO
 
一部の SAML アプリケーションでは、要求時にアサーション サブジェクトで SPNameQualifier が返される必要があります。 要求 NameID ポリシーで SPNameQualifier が要求されたときに、Azure AD が正しく応答するようになりました。 これは、SP initiated サインインでも機能します。また、IdP initiated サインインでも機能するようになる予定です。  Azure Active Directory での SAML プロトコルの詳細については、「[シングル サインオンの SAML プロトコル](../develop/single-sign-on-saml-protocol.md)」を参照してください。

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Azure AD B2B Collaboration により Azure Government テナントでの MSA および Google ユーザーの招待がサポートされます

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 

B2B Collaboration 機能を使用する Azure Government テナントは、Microsoft または Google アカウントを持つユーザーを招待できるようになりました。 お使いのテナントでこれらの機能を使用できるかどうかを確認するには、「[B2B コラボレーションが Azure US Government テナントで利用可能かどうかを確認するにはどうすればよいですか？](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)」の手順に従ってください。

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>MS Graph v1 のユーザー オブジェクトに externalUserState と externalUserStateChangedDateTime プロパティが含まれるようになりました

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 

externalUserState と externalUserStateChangedDateTime プロパティを使用すると、招待したがまだ招待を受諾していない B2B ゲストを検索できます。また、数日間経過した後にまだ招待を受諾していないユーザーを削除するなどのビルド自動化を行うことができます。 これらのプロパティが、MS Graph v1 で使用できるようになりました。 これらのプロパティの使用方法については、[ユーザー リソースの種類](/graph/api/resources/user?view=graph-rest-1.0)に関するページを参照してください。
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Azure AD 条件付きアクセスでの認証セッションの管理が一般提供されるようになりました

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護
 
認証セッションの管理機能を使用すると、ユーザーがサインイン資格情報を提供する必要がある頻度と、ブラウザーを閉じて再び開いた後に資格情報を提供する必要があるかどうかを構成することで、環境内でより高いセキュリティと柔軟性を提供できます。
 
また、認証セッション管理は以前、Azure AD 参加済み、Hybrid Azure AD 参加済み、および Azure AD 登録済みのデバイスでの第一要素認証のみに適用されていました。 現在、認証セッション管理は MFA にも適用されるようになりました。 詳細については、「[条件付きアクセスを使用して認証セッション管理を構成する](../conditional-access/howto-conditional-access-session-lifetime.md)」を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Azure AD アプリケーション ギャラリーで新しいフェデレーション アプリが利用できるようになりました - 2020 年 6 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2020 年 6 月、フェデレーションをサポートする次の 29 個の新しいアプリケーションがアプリ ギャラリーに追加されました。

[Shopify Plus](../saas-apps/shopify-plus-tutorial.md)、[Ekarda](../saas-apps/ekarda-tutorial.md)、[MailGates](../saas-apps/mailgates-tutorial.md)、[BullseyeTDP](../saas-apps/bullseyetdp-tutorial.md)、[Raketa](../saas-apps/raketa-tutorial.md)、[Segment](../saas-apps/segment-tutorial.md)、[Ai Auditor](https://www.mindbridge.ai/products/ai-auditor/)、[Pobuca Connect](https://app.pobu.ca/)、[Proto.io](../saas-apps/proto.io-tutorial.md)、[Gatekeeper](https://www.gatekeeperhq.com/)、[Hub Planner](../saas-apps/hub-planner-tutorial.md)、[Ansira-Partner Go-to-Market Toolbox](https://ansira.com/technology/channel-engagement)、[IBM Digital Business Automation on Cloud](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md)、[Kisi Physical Security](../saas-apps/kisi-physical-security-tutorial.md)、[ViewpointOne](https://team.viewpoint.com/)、[IntelligenceBank](../saas-apps/intelligencebank-tutorial.md)、[pymetrics](../saas-apps/pymetrics-tutorial.md)、[Zero](https://www.teamzero.com/)、[InStation](https://instation.invillia.com/)、[edX for Business SAML 2.0 Integration](../saas-apps/edx-for-business-saml-integration-tutorial.md)、[MOOC Office 365](https://mooc.office365-training.com/en/)、[SmartKargo](../saas-apps/smartkargo-tutorial.md)、[PKIsigning platform](https://platform.pkisigning.nl/)、[SiteIntel](../saas-apps/siteintel-tutorial.md)、[Field iD](../saas-apps/field-id-tutorial.md)、[Curricula SAML](../saas-apps/curricula-saml-tutorial.md)、[Perforce Helix Core - Helix Authentication Service](../saas-apps/perforce-helix-core-tutorial.md)、[MyCompliance Cloud](https://cloud.metacompliance.com/)、[Smallstep SSH](https://smallstep.com/sso-ssh/)  

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください。 Azure AD アプリ ギャラリーにアプリケーションを公開する場合は、こちらの詳細 (https://aka.ms/AzureADAppRequest ) をお読みください。

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>External Identities セルフサービス サインアップ用の API コネクターは、現在パブリック プレビュー中です

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
External Identities API コネクタを使用すると、Web API を利用してセルフサービス サインアップを外部クラウド システムと統合できます。 つまり、Web API をサインアップ フローの特定の手順として呼び出して、クラウドベースのカスタム ワークフローをトリガーできるようになりました。 たとえば、API コネクタを使用して次のことを行うことができます。

- カスタム承認ワークフローと統合する
- ID 証明を実行する
- ユーザー入力データを検証する
- ユーザー属性を上書する
- カスタム ビジネス ロジックを実行する

API コネクタで可能なすべてのエクスペリエンスの詳細については、「[API コネクタを使用してセルフサービス サインアップをカスタマイズおよび拡張する](../external-identities/api-connectors-overview.md)」、または[Web API 統合を使用した External Identities セルフサービス サインアップのカスタマイズ](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin)に関する記事を参照してください。
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>オンデマンドで数秒でユーザーをアプリにプロビジョニング

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
Azure AD プロビジョニング サービスは、現在、周期的に動作しています。 このサービスは 40 分ごとに実行されます。 [オンデマンド プロビジョニング機能](https://aka.ms/provisionondemand)を使用すると、ユーザーを選択し、数秒でプロビジョニングできます。 この機能を使用すると、プロビジョニング サイクルを強制的に再起動しなくても、プロビジョニングの問題を迅速にトラブルシューティングできます。 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>Graph で Azure AD エンタイトルメント管理を使用するための新しいアクセス許可

**種類:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** エンタイトルメント管理
 
新しい委任されたアクセス許可 EntitlementManagement.Read.All を Microsoft Graph ベータ版のエンタイトルメント管理 API で使用できるようになりました。 使用可能な API の詳細については、[Azure AD エンタイトルメント管理 API の操作](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)に関する記事を参照してください。

---

### <a name="identity-protection-apis-available-in-v10"></a>Identity Protection API を v1.0 で利用できるようになりました

**種類:** 新機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護
 
riskyUsers および riskDetections Microsoft Graph API が一般提供されるようになりました。 v1.0 エンドポイントで利用できるようになったので、運用環境で使用することをお勧めします。 詳細については、[Microsoft Graph のドキュメント](/graph/api/resources/identityprotectionroot?view=graph-rest-1.0)を参照してください。
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Microsoft 365 グループにポリシーを適用するための秘密度ラベルが一般提供されるようになりました

**種類:** 新機能  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション
 

秘密度ラベルを作成し、そのラベルの設定を使用して、プライバシー (パブリックまたはプライベート) と外部ユーザー アクセス ポリシーを含むポリシーを Microsoft 365 グループに適用できるようになりました。 プライバシー ポリシーをプライベートに設定し、外部ユーザー アクセス ポリシーを、ゲスト ユーザーの追加を許可しないように設定したラベルを作成できます。 ユーザーがこのラベルをグループに適用すると、そのグループはプライベートになり、ゲスト ユーザーをグループに追加することは許可されません。 

秘密度ラベルは、ビジネスに不可欠なデータを保護するうえで重要であり、これを使用することにより、コンプライアンスに準拠しセキュリティで保護された方法で、大規模なグループを管理できます。 秘密度ラベルの使用のガイダンスについては、「[Azure Active Directory で Microsoft 365 グループに秘密度ラベルを割り当てる (プレビュー)](../enterprise-users/groups-assign-sensitivity-labels.md)」を参照してください。
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Azure AD Premium のお客様を対象とする Microsoft Identity Manager のサポートに対する更新

**種類:** 変更された機能  
**サービス カテゴリ:** Microsoft Identity Manager  
**製品の機能:** ID ライフサイクル管理
 
Microsoft Identity Manager 2016 の延長サポートの終了により、Microsoft Identity Manager 2016 の Azure AD 統合コンポーネントに対して Azure サポートが利用できるようになりました。 詳細については、「[Microsoft Identity Manager を利用している Azure AD Premium のお客様向けの更新のサポート](/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers)」をお読みください。

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>SSO 要求構成でのグループ メンバーシップ条件の使用が増加しています

**種類:** 変更された機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO
 
以前は、1 つのアプリケーション構成内で、グループ メンバーシップに基づいて条件付きで要求を変更するときに使用できるグループの数は、10 個に制限されていました。 SSO 要求構成でのグループ メンバーシップ条件の使用が、最大 50 グループに増加しました。 要求を構成する方法の詳細については、[エンタープライズ アプリケーションの SSO 要求の構成](../develop/active-directory-saml-claims-customization.md#emitting-claims-based-on-conditions)に関する記事を参照してください。 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>会社のブランドのサインイン ページ テキスト コンポーネントにおける基本的な書式設定の有効化

**種類:** 変更された機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
Azure AD/Microsoft 365 ログイン エクスペリエンスでの会社のブランド機能が更新され、ハイパーリンクや、太字、下線、斜体などの書式設定をお客様が追加できるようになりました。 この機能の使用方法については、「[組織の Azure Active Directory のサインイン ページにブランドを追加する](./customize-branding.md)」を参照してください。

---

### <a name="provisioning-performance-improvements"></a>プロビジョニング パフォーマンスの向上

**種類:** 変更された機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
[増分サイクル](../app-provisioning/how-provisioning-works.md#incremental-cycles)が完了するまでの時間を短縮するようにプロビジョニング サービスが更新されました。 これは、ユーザーとグループが、以前よりも迅速にアプリケーションにプロビジョニングされることを意味します。 2020 年 6 月 10 日より後に作成されたすべての新しいプロビジョニング ジョブは、自動的にパフォーマンス向上の恩恵を受けます。 2020 年 6 月 10 日までにプロビジョニング用に構成されたアプリケーションでこのパフォーマンス向上を活用するためには、2020 年 6 月 10 日より後に 1 回再起動する必要があります。 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>ADAL と MS Graph パリティの非推奨の発表

**種類:** 非推奨  
**サービス カテゴリ:** 該当なし  
**製品の機能:** デバイスのライフサイクル管理

Microsoft 認証ライブラリ (MSAL) が利用できるようになったため、Azure Active Directory 認証ライブラリ (ADAL) への新機能の追加は行われなくなり、セキュリティ更新プログラムは 2022 年 6 月 30 日に終了となります。 MSAL への移行方法については、「[Microsoft Authentication Library (MSAL) へのアプリケーションの移行](../develop/msal-migration.md)」を参照してください。

さらに、MS Graph からすべての Azure AD Graph の機能を利用できるようにする作業を終了しました。 したがって、Azure AD Graph API がバグ修正とセキュリティ修正を受け取るのは、2022 年 6 月 30 日までのみとなります。 詳細については、[Microsoft 認証ライブラリと Microsoft Graph API を使用するようにアプリケーションを更新する](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)ことに関する記事を参照してください。
 
---
 
## <a name="may-2020"></a>2020 年 5 月

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>signIns、riskyUsers、および riskDetections API でのプロパティの提供終了

**種類:** 変更の計画  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護

現在、riskDetections API と riskyUserHistoryItem (プレビュー段階) の両方で、riskType プロパティを表すために列挙型が使用されています。 signIns API の riskEventTypes プロパティにも列挙型が使用されています。 今後、これらのプロパティは文字列として表します。 

お客様は、ベータ版 riskDetections と riskyUserHistoryItem API の riskEventType プロパティに、そして 2020 年 9 月 9 日までにはベータ版 signIns API の riskEventTypes_v2 プロパティに移行する必要があります。 その日付の時点で、現在の riskType と riskEventTypes プロパティは廃止されます。 詳細については、[Microsoft Graph でのリスク イベント プロパティと ID 保護 API に対する変更](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/)に関する記事を参照してください。

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>Microsoft Graph での signIns v1.0 API の riskEventTypes プロパティの非推奨

**種類:** 変更の計画  
**サービス カテゴリ:** レポーティング  
**製品の機能:** ID のセキュリティ & 保護

2020 年 9 月より、Microsoft Graph でリスク イベント プロパティを表すときに、列挙型から文字列型に切り替えられます。 この変更は、プレビュー API に影響を与えるだけでなく、運用中の signIns API にも影響します。

signIns v1.0 API に新しい riskEventsTypes_v2 (string) プロパティが導入されました。 Microsoft Graph の非推奨ポリシーに従って、現在の riskEventTypes (enum) プロパティは 2022 年 6 月 11 日に廃止されます。 お客様は、2022 年 6 月 11 日までに v1.0 signIns API の riskEventTypes_v2 プロパティに移行する必要があります。 詳細については、「[Microsoft Graph での signIns v1.0 API の riskEventTypes プロパティの非推奨](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//)」を参照してください。

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>MFA のメール通知の予定されている変更点

**種類:** 変更の計画  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティ & 保護
 

クラウドの MFA の MFA メール通知には、次の変更があります。

メール通知は次のアドレスから送信されるようになります: azure-noreply@microsoft.com と msonlineservicesteam@microsoftonline.com。 不正アクセスのアラート メールの内容を更新して、使用のブロックを解除するために必要な手順をわかりやすく示します。

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Azure Active Directory に同期されていないため、Microsoft Teams にアクセスできないフェデレーション ドメイン内のユーザーのための新しいセルフサービス サインアップ。

**種類:** 変更の計画  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 

現時点では、Azure AD のフェデレーション ドメインに属していても、テナントに同期されていないユーザーは、Teams にアクセスできません。 6 月末から、この新機能により、既存のメール確認済みサインアップ機能を拡張することで、そうすることができるようになります。 これにより、フェデレーション IdP にサインインできても、まだ Azure ID 内にまだユーザー オブジェクトがないユーザーは、ユーザー オブジェクトが自動的に作成され、Teams に対して認証することができます。 ユーザー オブジェクトは、"セルフサービス サインアップ" としてマークされます。 これは、マネージド ドメイン内のユーザーが実行でき、同じフラグを使用して制御できる、メール確認済みセルフサインアップを実行するための、既存機能の拡張機能です。 この変更は、今後 2 か月の間にロールアウトが完了する予定です。 [こちら](../enterprise-users/directory-self-service-signup.md)のドキュメントの更新にご期待ください。
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>予定されている修正点: Azure Government クラウドの OIDC 検出ドキュメントが正しい Graph エンドポイントを参照するように更新されます。

**種類:** 変更の計画  
**サービス カテゴリ:** ソブリン クラウド  
**製品の機能:** ユーザー認証
 
6 月以降、[Azure Government クラウド](../develop/authentication-national-cloud.md) エンドポイント (login.microsoftonline.us) の OIDC 検出ドキュメント [Microsoft ID プラットフォームと OpenID Connect プロトコル](../develop/v2-protocols-oidc.md)では、指定されたテナントに基づいて正しい[国内クラウド Graph](/graph/deployments) エンドポイント (https://graph.microsoft.us または https://dod-graph.microsoft.us) ) が返されるようになります。  現在は、正しくない Graph エンドポイント (graph.microsoft.com) の "msgraph_host" フィールドが返されています。  

このバグ修正は約 2 か月の間に段階的にロールアウトされます。  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure Government ユーザーが login.microsoftonline.com にサインインできなくなります

**種類:** 変更の計画  
**サービス カテゴリ:** ソブリン クラウド  
**製品の機能:** ユーザー認証
 
2018 年 6 月 1 日、Azure Government に対する Azure Active Directory (Azure AD) の公式な機関が、 https://login-us.microsoftonline.com から https://login.microsoftonline.us に変更されました。 Azure Government テナント内でアプリケーションを所有している場合は、.us エンドポイント上でユーザーをサインインさせるようにご使用のアプリケーションを更新する必要があります。

5 月 5 日以降、Azure AD でエンドポイントの変更の適用が開始され、Azure Government ユーザーはパブリック エンドポイント (microsoftonline.com) を使用して Azure Government テナント内でホストされているアプリにサインインできなくなります。 影響を受けるアプリでは、エラー AADSTS900439 - USGClientNotSupportedOnPublicEndpoint が表示されます。 

この変更は段階的にロールアウトされ、2020 年 6 月にすべてのアプリに対して適用が完了することが想定されています。 詳細については、[Azure Government ブログ記事](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)を参照してください。

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>SAML シングル ログアウト要求で NameID が正しい形式で送信されるようになりました

**種類:** 固定  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
ユーザーが (MyApps ポータルなどで) サインアウトをクリックすると、Azure AD は、ユーザー セッション内でアクティブになっていて、ログアウト URL が構成されている各アプリに SAML シングル ログアウト メッセージを送信します。 これらのメッセージには、永続的な形式で NameID が含まれています。

元の SAML サインイン トークンで NameID (メールや UPN など) に異なる形式が使用されていた場合、SAML アプリではログアウト メッセージ内の NameID を既存のセッションに関連付けることができません (両方のメッセージ内で使用されている NameID が異なるため)。それにより、SAML アプリによってログアウト メッセージが破棄され、ユーザーがログインしたままになります。 この修正により、アプリケーション用に構成された NameID とのサインアウト メッセージの整合性が確保されます。

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>クラウド プロビジョニングでハイブリッド ID 管理者の役割を使用できるようになりました

**種類:** 新機能  
**サービス カテゴリ:** Azure AD のクラウド プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
IT 管理者は、Azure AD Connect クラウド プロビジョニングを設定するための最小特権ロールとして新しい "ハイブリッド管理" ロールの使用を開始できます。 この新しいロールにより、クラウド プロビジョニングの設定と構成に全体管理者ロールを使用する必要がなくなります。 [詳細については、こちらを参照してください](../roles/delegate-by-task.md#connect)。
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Azure AD アプリケーション ギャラリーで入手できる新しいフェデレーション アプリ - 2020 年 5 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2020 年 5 月、フェデレーションをサポートする次の 36 個の新しいアプリケーションがアプリ ギャラリーに追加されました。

[Moula](https://moula.com.au/pay/merchants)、[Surveypal](https://www.surveypal.com/app)、[Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/)、[TackleBox](http://www.tacklebox.app/)、[Powell Teams](https://powell-software.com/en/powell-teams-en/)、[Talentsoft Assistant](https://msteams.talent-soft.com/)、[ASC Recording Insights](https://teams.asc-recording.app/product)、[GO1](https://www.go1.com/)、[B-Engaged](https://b-engaged.se/)、[Competella Contact Center Workgroup](http://www.competella.com/)、[Asite](http://www.asite.com/)、[ImageSoft Identity](https://identity.imagesoftinc.com/)、[My IBISWorld](https://identity.imagesoftinc.com/)、[insuite](../saas-apps/insuite-tutorial.md)、[Change Process Management](../saas-apps/change-process-management-tutorial.md)、[Cyara CX Assurance Platform](../saas-apps/cyara-cx-assurance-platform-tutorial.md)、[Smart Global Governance](../saas-apps/smart-global-governance-tutorial.md)、[Prezi](../saas-apps/prezi-tutorial.md)、[Mapbox](../saas-apps/mapbox-tutorial.md)、[Datava Enterprise Service Platform](../saas-apps/datava-enterprise-service-platform-tutorial.md)、[Whimsical](../saas-apps/whimsical-tutorial.md)、[Trelica](../saas-apps/trelica-tutorial.md)、[EasySSO for Confluence](../saas-apps/easysso-for-confluence-tutorial.md)、[EasySSO for BitBucket](../saas-apps/easysso-for-bitbucket-tutorial.md)、[EasySSO for Bamboo](../saas-apps/easysso-for-bamboo-tutorial.md)、[Torii](../saas-apps/torii-tutorial.md)、[Axiad Cloud](../saas-apps/axiad-cloud-tutorial.md)、[Humanage](../saas-apps/humanage-tutorial.md)、[ColorTokens ZTNA](../saas-apps/colortokens-ztna-tutorial.md)、[CCH Tagetik](../saas-apps/cch-tagetik-tutorial.md)、[ShareVault](../saas-apps/sharevault-tutorial.md)、[Vyond](../saas-apps/vyond-tutorial.md)、[TextExpander](../saas-apps/textexpander-tutorial.md)、[Anyone Home CRM](../saas-apps/anyone-home-crm-tutorial.md)、[askSpoke](../saas-apps/askspoke-tutorial.md)、[ice Contact Center](../saas-apps/ice-contact-center-tutorial.md)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください。

Azure AD アプリ ギャラリー内でご自分のアプリケーションを公開する場合、詳細については、こちら (https://aka.ms/AzureADAppRequest ) をご覧ください。

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>条件付きアクセスのレポート専用モードの一般提供開始

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護

[Azure AD 条件付きアクセスのレポート専用モード](../conditional-access/concept-conditional-access-report-only.md)を使用すると、アクセス制御を実施せずにポリシーの結果を評価できます。 組織全体でレポート専用ポリシーをテストし、その影響について理解してから有効にすることで、展開がより安全で簡単になります。 これまで数か月にわたって、私たちはレポート専用モードを広範に導入しており、既に 2,600 万を超えるユーザーがレポート専用ポリシーのスコープ内に存在しています。 本日のこの発表では、新しい Azure AD 条件付きアクセス ポリシーが、既定でレポート専用モードで作成されます。 これは、ポリシーが作成された時点からその影響を監視できることを意味します。 また、MS Graph API シリーズを使用しているユーザーは、[プログラムによってレポート専用ポリシーを管理する](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta)こともできます。 

---

### <a name="self-service-sign-up-for-guest-users"></a>ゲスト ユーザーのセルフサービス サインアップ

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
Azure AD 内で外部 ID を使用すると、ご自身の組織外のユーザーに組織のアプリとリソースへのアクセスを許可することができ、その際、外部ユーザーは希望する任意の ID を使用してサインインできます。 アプリケーションを外部ユーザーと共有する場合、アプリケーションにアクセスする必要があるユーザーが常に事前にわかっているとは限りません。 [セルフサービス サインアップ](../external-identities/self-service-sign-up-overview.md)を使用すると、ゲスト ユーザーがサインアップして、あなたの基幹業務 (LOB) アプリのゲスト アカウントを取得できるようになります。 サインアップ フローを作成して、Azure AD とソーシャル ID をサポートするようにカスタマイズできます。 サインアップ時にユーザーに関する追加情報を収集することもできます。

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>条件付きアクセスに関する分析情報とレポートのブックの一般提供開始

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護

[分析情報とレポートのブック](../conditional-access/howto-conditional-access-insights-reporting.md)によって、管理者が各自のテナントの Azure AD 条件付きアクセスの概要ビューを得られます。 個々のポリシーを選択する機能によって、管理者が各ポリシーの動作をよりよく理解し、変更をリアルタイムで監視できます。 このブックにより、Azure Monitor に格納されているデータがストリームされます。これは、[こちらの手順に従って](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)数分で設定可能です。 ダッシュボードは、見つけやすくするために、Azure AD 条件付きアクセス メニュー内の新しい分析情報とレポートのタブに移動されました。

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>条件付きアクセスのポリシー詳細ブレードのパブリック プレビュー開始

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護

新しい[ポリシーの詳細ブレード](../conditional-access/troubleshoot-conditional-access.md)には、条件付きアクセス ポリシーの評価中に満たされた割り当て、条件、および制御が表示されます。 このブレードにアクセスするには、サインインの詳細の [条件付きアクセス] タブまたは [レポート専用] タブにある行を選択します。

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>Microsoft Graph 内のディレクトリ オブジェクトに対する新しいクエリ機能のパブリック プレビュー開始

**種類:** 新機能  
**サービス カテゴリ:** MS Graph **製品の機能:** 開発者エクスペリエンス

Microsoft Graph ディレクトリ オブジェクト API シリーズに新しい機能が導入され、カウント、検索、フィルター処理、並べ替え操作が可能になります。 これにより、開発者は、インメモリのフィルター処理や並べ替えなどの回避策を使用せずに、ご自分のディレクトリ オブジェクトに対してすばやくクエリを実行できるようになります。 詳細については、この[ブログ記事](https://aka.ms/CountFilterMSGraphAAD)をご覧ください。

現在、パブリック プレビュー中で、フィードバックをお待ちしております。 この[簡単なアンケート](https://aka.ms/MsGraphAADSurveyDocs)でお客様のご意見をお送りください。

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>Microsoft Graph API (ベータ版) を使用した SAML ベースのシングル サインオンの構成

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO
 
ベータ版の MS Graph API シリーズを使用した Azure AD ギャラリーからのアプリケーションの作成と構成がサポートされるようになりました。 アプリケーションの複数のインスタンスに対して SAML ベースのシングル サインオンを設定する必要がある場合に、Microsoft Graph API シリーズを使用して [SAML ベースのシングル サインオンの構成を自動化](/graph/application-saml-sso-configure-api)することで、時間を節約できます。
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクタ - 2020 年 5 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合
 
新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

* [8x8](../saas-apps/8x8-provisioning-tutorial.md)
* [Juno Journey](../saas-apps/juno-journey-provisioning-tutorial.md)
* [MediusFlow](../saas-apps/mediusflow-provisioning-tutorial.md)
* [New Relic by Organization](../saas-apps/new-relic-by-organization-provisioning-tutorial.md)
* [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-infratstructure-console-provisioning-tutorial.md)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)に関するページをご覧ください。

---

### <a name="saml-token-encryption-is-generally-available"></a>SAML トークン暗号化の一般提供開始

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO
 
[SAML トークン暗号化](../manage-apps/howto-saml-token-encryption.md)を使用すると、暗号化された SAML アサーションを受信するようにアプリケーションを構成できます。 この機能は、すべてのクラウドで一般提供が開始されています。
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>アプリケーション トークン内のグループ名要求の一般提供開始

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO
 
トークンで発行されたグループ要求が、アプリケーションに割り当てられたグループのみに制限されるようになりました。  これは、ユーザーが属しているグループのメンバーが多数いて、トークン サイズの制限を超えるリスクがある場合に特に重要です。 この新機能の導入により、[トークンにグループ名を追加する](../hybrid/how-to-connect-fed-group-claims.md)機能の一般提供が開始されました。
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Workday Writeback で勤務先電話番号属性の設定がサポートされるようになりました

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
Workday Writeback プロビジョニング アプリを強化し、勤務先電話番号と携帯電話番号の属性の書き戻しがサポートされるようになりました。 メールとユーザー名に加えて、電話番号の値が Azure AD から Workday にフローするように Workday Writeback プロビジョニング アプリを構成できるようになりました。 電話番号の書き戻しを構成する方法の詳細については、[Workday Writeback](../saas-apps/workday-writeback-tutorial.md) アプリのチュートリアルを参照してください。 

---

### <a name="publisher-verification-preview"></a>発行者の確認 (プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** 開発者エクスペリエンス
 
発行者の確認 (プレビュー) は、管理者とエンド ユーザーが、Microsoft ID プラットフォームと統合するアプリケーション開発者の信頼性を理解するのに役立ちます。 詳細については、「[発行者の確認 (プレビュー)](../develop/publisher-verification-overview.md)」を参照してください。
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>シングルページ アプリの承認コード フロー

**種類:** 変更された機能 **サービス カテゴリ:** 認証 **製品の機能:** 開発者エクスペリエンス

最新のブラウザーの[サード パーティ Cookie の制限 (Safari ITP など)](../develop/reference-third-party-cookies-spas.md) により、SPA では、SSO を維持するために、暗黙的フローではなく、承認コード フローを使用する必要があります。MSAL.js v 2.x では、認証コード フローをサポートするようになりました。 Azure portal の対応する更新により、ご自分の SPA の種類を "spa" に更新し、認証コード フローを使用することができます。 ガイダンスについては、「[クイックスタート: 承認コード フローを使用して JavaScript SPA 内でユーザーをサインインさせ、アクセス トークンを取得する](../develop/quickstart-v2-javascript-auth-code.md)」を参照してください。

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>デバイスの強化されたフィルター処理のパブリック プレビュー開始

**種類:** 変更された機能   
**サービス カテゴリ:** デバイス管理 **製品の機能:** デバイスのライフサイクル管理
 
以前は、使用できるフィルターは "有効" と "アクティビティの日付" のみでした。 OS の種類、結合の種類、コンプライアンスなど、[より多くのプロパティでデバイスの一覧をフィルター処理](../devices/device-management-azure-portal.md#device-list-filtering-preview)できるようになりました。 これらの追加により、特定のデバイスを簡単に見つけることができます。

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>Azure AD B2C の新しいアプリの登録エクスペリエンスの一般提供開始

**種類:** 変更された機能   
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** ID ライフサイクル管理
 
Azure AD B2C の新しいアプリの登録エクスペリエンスの一般提供が開始されました。 

以前は、従来の "アプリケーション" エクスペリエンスを使用して、コンシューマー向けの B2C アプリケーションを他のアプリとは別に管理する必要がありました。 これは、Azure 内のさまざまな場所で、さまざまなアプリ作成エクスペリエンスが存在していたことを意味します。

新しいエクスペリエンスでは、すべての B2C アプリの登録と Azure AD アプリの登録を 1 か所で表示し、一貫した方法で管理することができます。 顧客向けアプリや、Microsoft Graph にアクセスして Azure AD B2C リソースをプログラムで管理できるアプリを管理する必要がある場合も、1 つの管理方法を学ぶだけで済みます。

新しいエクスペリエンスを利用するには、Azure AD B2C サービスに移動し、[アプリの登録] ブレードを選択します。 このエクスペリエンスには、Azure Active Directory サービスからアクセスすることもできます。

Azure AD B2C のアプリの登録エクスペリエンスは、Azure AD テナント向けの一般的な[アプリの登録エクスペリエンス](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/)に基づいていますが、Azure AD B2C 向けにカスタマイズされています。 従来の "アプリケーション" エクスペリエンスは今後非推奨となる予定です。

詳細については、[Azure AD B2C の新しいアプリの登録エクスペリエンス](../../active-directory-b2c/app-registrations-training-guide.md)に関するページを参照してください。

---
