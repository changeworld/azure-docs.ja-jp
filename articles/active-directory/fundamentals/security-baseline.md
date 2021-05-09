---
title: Azure Active Directory 用の Azure セキュリティ ベースライン
description: Azure Active Directory セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: active-directory
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8d4203b7b5d7742bf198778864fa4f42b7423596
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107286002"
---
# <a name="azure-security-baseline-for-azure-active-directory"></a>Azure Active Directory 用の Azure セキュリティ ベースライン

このセキュリティ ベースラインにより、[Azure セキュリティ ベンチマーク バージョン 2.0](../../security/benchmarks/overview.md) のガイダンスが Azure Active Directory に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 内容は、**セキュリティ制御** によってグループ化されています。これは、Azure セキュリティ ベンチマークと、Azure Active Directory に適用できる関連ガイダンスによって定義されています。 

> [!NOTE]
> Azure Active Directory に適用されない **制御**、または Microsoft が責任を持つものは、このベースラインから除外されています。 Azure Active Directory を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Azure Active Directory セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](/azure/security/benchmarks/security-controls-v2-network-security)」を参照してください。*

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: ネットワーク セキュリティ規則を簡略化する

**ガイダンス**: Azure Virtual Network サービス タグを使用して、Azure Active Directory リソース用に構成されたネットワーク セキュリティ グループまたは Azure Firewall に対するネットワーク アクセス制御を定義します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたはターゲット フィールドにサービス タグ名 ("AzureActiveDirectory" など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。 

- [サービス タグとその使用方法の概要](../../virtual-network/service-tags-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="identity-management"></a>ID 管理

*詳細については、[Azure セキュリティ ベンチマークの「ID 管理](/azure/security/benchmarks/security-controls-v2-identity-management).* 」を参照してください。

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1:Azure Active Directory を中央 ID および認証システムとして標準化する

**ガイダンス**: Azure Active Directory (Azure AD) を既定の ID およびアクセス管理サービスとして使用します。 Azure AD を標準化して、次のリソースでの組織の ID とアクセス管理を統制する必要があります。 
- Azure portal、Azure Storage、Azure 仮想マシン (Linux と Windows)、Azure Key Vault、PaaS、SaaS アプリケーションなどの Microsoft Cloud リソース。 
- Azure 上のアプリケーションや企業ネットワーク リソースなどの組織のリソース。 
 

Azure AD を保護することは、組織のクラウド セキュリティ プラクティスの中で高い優先順位を持つ必要があります。 Azure AD では、Microsoft のベスト プラクティスの推奨事項を基準にした、お客様の ID セキュリティ体制を評価するために役立つ ID セキュリティ スコアが提供されます。 スコアを使用して、構成がベスト プラクティスの推奨事項とどの程度一致しているかを測定し、セキュリティ体制を強化します。 

Azure AD では、外部 ID がサポートされます。これにより、Microsoft アカウントを持たないユーザーが、外部 ID を使用してアプリケーションやリソースにサインインできるようになります。 

- [Azure Active Directory のテナント](../develop/single-and-multi-tenant-apps.md)

- [Azure AD インスタンスを作成して構成する方法](active-directory-access-create-new-tenant.md)

- [アプリケーションに外部 ID プロバイダーを使用する](/azure/active-directory/b2b/identity-providers)

- [Azure Active Directory の ID セキュリティ スコアとは](identity-secure-score.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2:アプリケーション ID を安全かつ自動的に管理する

**ガイダンス**: Azure リソースのマネージド ID をサービスやオートメーションなどの人間以外のアカウントに使用します。リソースへのアクセスや実行のためにより強力な人間のアカウントを作成するのではなく、Azure マネージド ID 機能を使用することをお勧めします。 ソース コードまたは構成ファイルにハードコーディングされた資格情報を使用しなくても、事前に定義されたアクセス許可規則によって、Azure Active Directory (Azure AD) 認証をサポートしている Azure サービスまたはリソースに対してネイティブに認証することができます。 Azure マネージド ID を Azure AD リソースに割り当てることはできませんが、Azure AD は他のサービスのリソースに割り当てられているマネージド ID を使用して認証するメカニズムです。

- [Azure リソースのマネージド ID](../managed-identities-azure-resources/overview.md)

- [Azure リソースのマネージド ID をサポートするサービス](../managed-identities-azure-resources/services-support-managed-identities.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3:アプリケーションのアクセスに Azure AD シングル サインオン (SSO) を使用する

**ガイダンス**: Azure Active Directory を使用して、Azure リソース、クラウド アプリケーション、およびオンプレミスのアプリケーションに ID とアクセス管理を提供します。 これには、従業員などの企業 ID だけでなく、パートナー、ベンダー、サプライヤーなどの外部 ID も含まれます。 これにより、オンプレミスおよびクラウド内の組織のデータとリソースへのアクセスを管理し、セキュリティで保護するためのシングルサインオン (SSO) が可能になります。 すべてのユーザー、アプリケーション、デバイスを Azure AD に接続することで、シームレスで安全なアクセスを実現し、可視性と制御性を高めることができます。

 
- [Azure AD を使用したアプリケーションの SSO について理解する](../manage-apps/what-is-single-sign-on.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4:すべての Azure Active Directory ベースのアクセスに強力な認証制御を使用する

**ガイダンス**: Azure Active Directory を使用して、多要素認証 (MFA) による強力な認証制御と、強力なパスワードレス手法をサポートします。
- 多要素認証 - Azure AD MFA を有効にし、Azure Security Center の ID とアクセス管理の推奨事項に従って、MFA 構成のベスト プラクティスを導入します。 MFA は、サインインの条件とリスク要因に基づいて、すべてのユーザー、選択されたユーザー、またはユーザー単位のレベルで適用できます。
- パスワードレス認証 - 3 つのパスワードレス認証オプションを使用できます。Windows Hello for Business、Microsoft Authenticator アプリ、スマート カードなどのオンプレミスの認証方法。

管理者と特権ユーザーについて、高いレベルの強力な認証方法が使用されていることを確認し、その後、適切な強力な認証ポリシーを他のユーザーにロールアウトします。

Azure AD では、ベースライン パスワード ポリシーを持つクラウド専用アカウント (Azure AD で直接作成されたユーザー アカウント) や、オンプレミス パスワード ポリシーに従うハイブリッド アカウント (オンプレミスの Azure Directory のユーザー アカウント) など、従来のパスワードベースの認証がサポートされています。 パスワード ベースの認証を使用すると、Azure AD により、ユーザーが推測されやすいパスワードを設定できないようにするパスワード保護機能が提供されます。 Microsoft では、テレメトリに基づいて更新される禁止されたパスワードのグローバル リストが提供されており、顧客はニーズ (ブランド、文化の参照など) に基づいてリストを補強することができます。 このパスワード保護は、クラウド専用アカウントとハイブリッド アカウントに使用できます。

パスワード資格情報だけに基づく認証は、一般的な攻撃方法の影響を受けやすいものです。 セキュリティを強化するには、MFA や強力なパスワード ポリシーなどの強力な認証を使用します。 サードパーティ製のアプリケーションや Marketplace サービスで既定のパスワードが使用されている可能性がある場合は、サービスの初期セットアップ時にそれらを変更する必要があります。

 
- [Azure AD MFA をデプロイする方法](../authentication/howto-mfa-getstarted.md) 

 

 
- [Azure Active Directory のパスワードレス認証オプションの概要](../authentication/concept-authentication-passwordless.md) 

 

 
- [Azure AD の規定のパスワード ポリシー](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#password-policies-that-only-apply-to-cloud-user-accounts)

- [Azure AD のパスワード保護を使用して不適切なパスワードを排除する](../authentication/concept-password-ban-bad.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5:アカウントの異常を監視してアラートを出す

**ガイダンス**: Azure Active Directory には、次のデータ ソースが用意されています。

 
- サインイン - サインイン レポートでは、マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報が得られます。

 
- 監査ログ - Azure AD 内のさまざまな機能によって行われたすべての変更についてログによる追跡可能性を提供します。 監査ログの例として、ユーザー、アプリ、グループ、ロール、ポリシーの追加や削除など、Azure AD 内のあらゆるリソースに加えられた変更があります。

 
- リスクの高いサインイン - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。

 
- リスクのフラグ付きユーザー - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。

 

これらのデータ ソースは、Azure Monitor、Azure Sentinel、またはサードパーティの SIEM システムと統合できます。

 

 
Azure Security Center で、認証試行の失敗回数が多すぎるなど、サブスクリプションにおける特定の不審なアクティビティや、非推奨のアカウントに対してアラートを生成することもできます。

 

 
セキュリティ ソリューションである Azure Advanced Threat Protection (ATP) では、Active Directory シグナルを使用することで、高度な脅威、セキュリティ侵害を受けた ID、および悪意のある内部関係者のアクションを特定、検出、および調査できます。

 

 
- [Azure Active Directory の監査アクティビティ レポート](../reports-monitoring/concept-audit-logs.md) 

 

 
- [Azure AD の危険なサインインを表示する方法](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

 

 
- [危険なアクティビティのフラグが設定された Azure AD ユーザーを識別する方法](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

 

 
- [Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法](../../security-center/security-center-identity-access.md) 

 

 
- [Azure Security Center の脅威インテリジェンス保護モジュールでのアラート](../../security-center/alerts-reference.md) 

 

 
- [Azure アクティビティ ログを Azure Monitor に統合する方法](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6:条件に基づいて Azure リソースへのアクセスを制限する

**ガイダンス**: 特定の IP 範囲からのユーザー ログインでは MFA を使用してログインする必要があるなど、ユーザー定義の条件に基づくよりきめ細かいアクセス制御には、Azure Active Directory (Azure AD) の条件付きアクセスを使用します。 さまざまなユース ケースに対してきめ細かな認証セッション管理ポリシーを使用することもできます。

 
- [Azure AD の条件付きアクセスの概要](../conditional-access/overview.md) 

 

 
- [一般的な条件付きアクセス ポリシー](../conditional-access/concept-conditional-access-policy-common.md) 

 

 
- [条件付きアクセスを使用して認証セッション管理を構成する](../conditional-access/howto-conditional-access-session-lifetime.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="im-8-secure-user-access-to-legacy-applications"></a>IM-8:レガシ アプリケーションへのユーザー アクセスをセキュリティで保護する

**ガイダンス**: レガシ アプリケーションとそれらによって格納および処理されるデータに対し、最新のアクセス制御とセッション監視が使用されていることを確かめます。 レガシ アプリケーションへのアクセスには VPN がよく使用されますが、多くの場合、基本的なアクセス制御と限られたセッション監視しか行われません。

 
Azure AD アプリケーション プロキシを使用すると、Azure AD 条件付きアクセスを利用してリモート ユーザーとデバイスの両方の信頼性を明示的に検証しながら、SSO を使ってリモート ユーザーにレガシ オンプレミス アプリケーションを発行することができます。

 

 
また、Microsoft Cloud App Security はクラウド アクセス セキュリティ ブローカー (CASB) サービスであり、それを使用すると、ユーザーのアプリケーション セッションを監視し、アクションをブロックする制御を提供できます (レガシ オンプレミス アプリケーションと、クラウドのサービスとしてのソフトウェア (SaaS) アプリケーションの両方)。

 

 
- [Azure Active Directory アプリケーション プロキシ](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy#what-is-application-proxy) 

 

 
- [Microsoft Cloud App Security のベスト プラクティス](/cloud-app-security/best-practices)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="privileged-access"></a>特権アクセス

*詳細については、[Azure セキュリティ ベンチマークの「特権アクセス](/azure/security/benchmarks/security-controls-v2-privileged-access)」を参照してください。*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1:高い特権を持つユーザーを保護および制限する

**ガイダンス**: Azure AD で最も重要な組み込みロールは、全体管理者と特権ロール管理者です。これは、これら 2 つのロールに割り当てられたユーザーが管理者ロールを委任できるためです。

- グローバル管理者:このロールが割り当てられたユーザーは、Azure AD のすべての管理機能に加え、Azure AD ID を使用するサービスにもアクセスできます。

- 特権ロール管理者:このロールが割り当てられたユーザーは、Azure AD と Azure AD Privileged Identity Management (PIM) 内でロールの割り当てを管理できます。 さらに、このロールは、PIM と管理単位のすべての側面を管理できます。

特定の特権アクセス許可を割り当てられたカスタム ロールを使用する場合は、管理する必要のある他の重要なロールがある場合があります。 また、重要なビジネス資産の管理者アカウントに同様のコントロールを適用することもできます。

Azure AD には高い権限を持つアカウントがあります。これには、直接的または間接的に割り当てられる、あるいはその対象であるユーザーおよびサービス プリンシパル、全体管理者または特権ロール管理者ロール、および Azure AD と Azure のその他の高い権限を持つロールがあります。

高い権限を持つアカウントの数を制限し、それらのアカウントを厳格なレベルで保護してください。このような権限を持つユーザーは、Azure 環境内のあらゆるリソースを直接的または間接的に読み取ったり変更したりすることができるためです。

Azure Privileged Identity Management (PIM) を使用して、Azure リソースと Azure AD への Just-In-Time (JIT) の特権アクセスを有効にすることができます。 ユーザーが必要とする場合にのみ特権タスクを実行するための一時的なアクセス許可は、JIT によって付与されます。 PIM を使用すると、Azure AD 組織に不審なアクティビティや安全でないアクティビティがある場合に、セキュリティ アラートを生成することもできます。

- [Azure AD での管理者ロールのアクセス許可](/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

- [Azure Privileged Identity Management のセキュリティ アラートを使用する](../privileged-identity-management/pim-how-to-configure-security-alerts.md) 

- [Azure AD でのハイブリッドおよびクラウド デプロイ用の特権アクセスをセキュリティで保護する](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2:ビジネス クリティカルなシステムへの管理アクセスを制限する

**ガイダンス**: Azure Active Directory Privileged Identity Management と多要素認証を使用して、ビジネス クリティカルなシステムへの管理アクセスを制限します。

- [Privileged Identity Management でのロールのアクティブ化要求の承認](../privileged-identity-management/azure-ad-pim-approval-workflow.md)

- [多要素認証と条件付きアクセス](../conditional-access/howto-conditional-access-policy-admin-mfa.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3:ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: アカウントとそのアクセスが有効であることを確かめるために、ユーザー アカウント アクセス割り当てを定期的に確認します。特に、全体管理者と特権ロール管理者を含む高い権限を持つロールに重点を置きます。 Azure Active Directory (Azure AD) のアクセス レビューを使用して、Azure AD ロールと Azure ロールの両方について、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを確認できます。 Azure AD レポートによって、古いアカウントの検出に役立つログが提供されます。 また、Azure AD Privileged Identity Management を使用してアクセス レビュー レポート ワークフローを作成し、レビュー プロセスを容易にすることもできます。

さらに、過剰な数の管理者アカウントが作成された場合にアラートを発行したり、古い管理者アカウントや不適切に構成されている管理者アカウントを特定するように Azure Privileged Identity Management を構成することもできます。

- [Privileged Identity Management (PIM) で Azure AD ロールのアクセス レビューを作成する](../privileged-identity-management/pim-how-to-start-security-review.md)

- [Privileged Identity Management (PIM) で Azure リソース ロールのアクセス レビューを作成する](../privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Azure AD の ID およびアクセス レビューの使用方法](../governance/access-reviews-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4:Azure AD で緊急アクセスを設定する

**ガイダンス**: Azure AD 組織から誤ってロックアウトされるのを防ぐために、通常の管理者アカウントを使用できない場合にアクセスするための緊急アクセス用アカウントを設定します。 緊急アクセス用アカウントは高い特権を持っており、特定の個人に割り当てることはできません。
緊急アクセス用アカウントは、通常の管理者アカウントを使うことができない "緊急事態" に制限されます。

緊急アクセス用アカウントの資格情報 (パスワード、証明書、スマート カードなど) は安全に保管し、緊急時にのみそれらを使うことを許可された個人のみに知らせる必要があります。

- [Azure AD で緊急アクセス用アカウントを管理する](/azure/active-directory/users-groups-roles/directory-emergency-access)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="pa-5-automate-entitlement-management"></a>PA-5:エンタイトルメント管理を自動化する 

**ガイダンス**: Azure AD のエンタイトルメント管理機能を使用して、アクセスの割り当て、レビュー、有効期限など、アクセス要求のワークフローを自動化します。 2 段階または複数段階の承認もサポートされています。

- [Azure AD エンタイトルメント管理とは](../governance/entitlement-management-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6:特権アクセス ワークステーションを使用する

**ガイダンス**:セキュリティで保護された分離したワークステーションは、管理者、開発者、重要なサービス オペレーターのような機密性の高い役割のセキュリティには非常に重要です。 管理タスクに高度にセキュリティ保護されたユーザー ワークステーションや Azure Bastion を使用します。 Azure Active Directory、Microsoft Defender Advanced Threat Protection (ATP)、または Microsoft Intune を使用して、管理タスクのためにセキュリティで保護されたマネージド ユーザー ワークステーションを展開します。 セキュリティで保護されたワークステーションを一元管理して、強力な認証、ソフトウェアとハードウェアのベースライン、制限された論理アクセスとネットワーク アクセスなどのセキュリティで保護された構成を実施できます。

- [特権アクセスの一部としてデバイスをセキュリティで保護する](/security/compass/privileged-access-devices)

- [特権アクセスの実装](/security/compass/privileged-access-deployment)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7:Just Enough Administration (最小限の特権の原則) に従う 

**ガイダンス**: お客様は、ユーザーが管理タスクを完了するために必要な最小限のアクセス許可を持つロールにそのユーザーを割り当てることにより、Azure Active Directory (Azure AD) のデプロイを最小限の特権用に構成できます。

- [Azure AD での管理者ロールのアクセス許可](../roles/permissions-reference.md)

- [Azure AD で管理者ロールを割り当てる](../roles/manage-roles-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: Microsoft サポートの承認プロセスを選択する  

**ガイダンス**: Azure Active Directory ではカスタマー ロックボックスがサポートされていません。 Microsoft では、ロックボックス以外の方法を使用してお客様と連絡を取り、カスタマー データへのアクセスを承認する場合があります。

**責任**: 共有

**Azure Security Center の監視**: なし

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](/azure/security/benchmarks/security-controls-v2-data-protection)」を参照してください。*

### <a name="dp-2-protect-sensitive-data"></a>DP-2:機密データを保護する

**ガイダンス**: 機密データの保護を実装するための次のガイダンスを考慮してください。

- **分離:** ディレクトリは、Azure Active Directory (Azure AD) サービスでお客様のデータを格納および処理するために使用するデータの境界です。 お客様は、ディレクトリに Country プロパティを設定することによって、ほとんどの Azure AD 顧客データを格納する場所を決定する必要があります。

- **セグメント化:** 全体管理者のロールでは、すべてのディレクトリ データと、アクセスおよび処理命令を管理する規則を完全に制御できます。 ディレクトリは管理単位にセグメント化され、それらの単位の管理者によって管理されるユーザーとグループでプロビジョニングされる場合があります。全体管理者は、事前に定義されたロールまたは作成可能なカスタム ロールに割り当てることによって、組織内の他のプリンシパルに責任を委任する場合があります。

 
- **アクセス:** アクセス許可は、ユーザー、グループ、ロール、アプリケーション、またはデバイスで適用できます。 割り当ては、Privileged Identity Management 構成ごとに永続的または一時的なものにすることができます。 
  
- **暗号化:** Azure AD では、保存中または転送中のすべてのデータを暗号化します。 このオファリングでは、お客様がディレクトリ データを独自の暗号化キーで暗号化することはできません。 

選択した国がディレクトリの物理的な場所にどのようにマップされているかを確認する場合は、''データの保管場所に関する記事'' を参照してください。

- 「[お客様のデータの場所」の記事](https://www.microsoft.com/trust-center/privacy/data-location)

お客様はディレクトリを操作するさまざまな Azure AD ツール、機能、およびアプリケーションを使うため、「Azure Active Directory - お客様のデータの場所」の記事を使用します。

- [「お客様のデータの場所」のダッシュボード](https://msit.powerbi.com/view?r=eyJrIjoiYzEyZTc5OTgtNTdlZS00ZTVkLWExN2ItOTM0OWU4NjljOGVjIiwidCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsImMiOjV9)

- [Azure AD ロールのドキュメント](/azure/active-directory/roles/)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4:転送中の機密情報を暗号化する

**ガイダンス**:転送中のデータが攻撃者に簡単に読み取られたり変更されたりしないよう、暗号化を使用して "帯域外" 攻撃 (トラフィックのキャプチャなど) から保護し、アクセス制御を補完する必要があります。

Azure AD では、TLS v1.2 以上で転送中のデータの暗号化がサポートされます。

これはプライベート ネットワーク上のトラフィックでは省略できますが、外部ネットワークとパブリック ネットワーク上のトラフィックには重要です。 ご自分の Azure リソースに接続するすべてのクライアントの HTTP トラフィックのネゴシエートには、確実に TLS v1.2 以上を使用してください。 リモート管理には、暗号化されていないプロトコルではなく、(Linux の場合) SSH または (Windows の場合) RDP/TLS を使用します。 SSL、TLS、SSH の古いバージョンとプロトコル、および弱い暗号は無効にする必要があります。

既定では Azure によって、Azure のデータ センター間の転送データが暗号化されます。

- [Azure での転送中の暗号化の概要](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit) 

- [TLS セキュリティに関する情報](/security/engineering/solving-tls1-problem) 

- [転送中の Azure データの二重暗号化](https://docs.microsoft.com/azure/security/fundamentals/double-encryption#data-in-transit)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="asset-management"></a>アセット管理

*詳細については、[Azure セキュリティ ベンチマークの「アセット管理](/azure/security/benchmarks/security-controls-v2-asset-management)」を参照してください。*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1:セキュリティ チームが資産のリスクを確実に可視化できるようにする

**ガイダンス**:セキュリティ チームに Azure テナントとサブスクリプションのセキュリティ閲覧者アクセス許可を付与して、セキュリティ チームが Azure Security Center を使用してセキュリティ上のリスクを監視できるようにします。 

セキュリティ チームの責任がどのように構造化されているかによって、セキュリティ リスクの監視は中央のセキュリティ チームまたはローカル チームの責任になります。 ただし、セキュリティ分析情報とリスクは、常に組織内で一元的に集計する必要があります。 

セキュリティ閲覧者のアクセス許可は、テナント全体 (ルート管理グループ) に幅広く適用することも、管理グループまたは特定のサブスクリプションにスコープ指定することもできます。 

ワークロードとサービスを可視化するには、追加のアクセス許可が必要になることがあります。 

- [セキュリティ閲覧者ロールの概要](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader)

- [Azure 管理グループの概要](../../governance/management-groups/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: "Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure portal を介して Azure AD を操作するユーザーの機能を制限するには、Azure Active Directory (Azure AD) 条件付きアクセスを使用します。

- [条件付きアクセスを構成して Azure Resource Manager へのアクセスをブロックする方法](../../role-based-access-control/conditional-access-azure-management.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="logging-and-threat-detection"></a>ログと脅威検出

*詳細については、[Azure セキュリティ ベンチマークの「ログと脅威検出](/azure/security/benchmarks/security-controls-v2-logging-threat-protection)」を参照してください。*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1:Azure リソースの脅威検出を有効にする

**ガイダンス**: Azure Active Directory (Azure AD) Identity Protection の組み込みの脅威検出機能を Azure AD リソースに使用します。 
 
 
 

 
Azure AD により、脅威の検出と脅威の捜索によく使用されるアクティビティ ログが生成されます。 Azure AD サインイン ログを使用すれば、ユーザー、サービス、およびアプリの認証と承認のアクティビティを記録できます。 Azure AD 監査ログでは、Azure AD テナントに加えられた変更を追跡します。これには、セキュリティ体制を改善または低減させる変更が含まれます。

- [Azure Active Directory Identity Protection とは](../identity-protection/overview-identity-protection.md)

- [Azure Sentinel に Azure AD Identity Protection データを接続する方法](../../sentinel/connect-azure-ad-identity-protection.md)

- [Azure Active Directory データを Azure Sentinel に接続する](../../sentinel/connect-azure-active-directory.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2:Azure ID とアクセスの管理のために脅威検出を有効にする

**ガイダンス**:Azure Active Directory (Azure AD) では、次のユーザー ログが記録され、これは Azure AD レポートで確認できます。また、より高度な監視と分析のユース ケースの場合は、Azure Monitor、Azure Sentinel、または他の SIEM/監視ツールと統合できます。 
- サインイン - サインイン レポートは、マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報を提供します。 
- 監査ログ - Azure AD 内のさまざまな機能によって行われたすべての変更についてログによる追跡可能性を提供します。 監査ログの例として、ユーザー、アプリ、グループ、ロール、ポリシーの追加や削除など、Azure AD 内のあらゆるリソースに加えられた変更があります。 
- リスクの高いサインイン - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。 
- 危険なユーザー - 危険なユーザーは、侵害された可能性があるユーザー アカウントを示す指標です。 

Identity Protection のリスク検出は既定で有効になっており、オンボード プロセスは必要ありません。 細分性またはリスク データは、ライセンス SKU によって決まります。 

- [Azure Active Directory の監査アクティビティ レポート](../reports-monitoring/concept-audit-logs.md)  

- [Azure Identity Protection を有効にする](../identity-protection/overview-identity-protection.md)  

- [Azure Security Center での脅威の防止](/azure/security-center/threat-protection)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4:Azure リソースのログ記録を有効にする

**ガイダンス**: Azure Active Directory (Azure AD) によってアクティビティ ログが生成されます。 一部の Azure サービスとは異なり、Azure AD ではアクティビティおよびリソース ログが区別されません。 アクティビティ ログは、Azure portal の [Azure AD] セクションで自動的に使用できるようになり、Azure Monitor、Azure Event Hubs、Azure Storage、SIEM、およびその他の場所にエクスポートできます。
 
- サインイン - サインイン レポートは、マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報を提供します。  
 
- 監査ログ - Azure AD 内のさまざまな機能によって行われたすべての変更についてログによる追跡可能性を提供します。 監査ログの例として、ユーザー、アプリ、グループ、ロール、ポリシーの追加や削除など、Azure AD 内のあらゆるリソースに加えられた変更があります。  
 
 

Azure AD によって、セキュリティ関連のログも提供されます。これは Azure AD レポートで確認できます。また、より高度な監視と分析のユース ケースの場合は、Azure Monitor、Azure Sentinel、またはその他の SIEM/監視ツールと統合できます。 
- リスクの高いサインイン - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。
- 危険なユーザー - 危険なユーザーは、侵害された可能性があるユーザー アカウントを示す指標です。 

Identity Protection のリスク検出は既定で有効になっており、オンボード プロセスは必要ありません。 細分性またはリスク データは、ライセンス SKU によって決まります。 

 
- [Azure Active Directory のアクティビティおよびセキュリティ レポート](../reports-monitoring/overview-reports.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5:セキュリティ ログの管理と分析を一元化する

**ガイダンス**: お客様がセキュリティ ログを一元管理し、より簡単に脅威を捜索してセキュリティ体制を分析できるようにしたい場合は、次のガイドラインに従うことをお勧めします。
 
- ログ記録のストレージと分析を一元化して、相関関係を有効にします。 Azure AD 内のログ ソースごとに、データ所有者、アクセス ガイダンス、ストレージの場所、データの処理とアクセスに使用するツール、データ保持の要件を割り当てたことを確かめます。  
 
- Azure アクティビティ ログを一元的なログ記録に統合していることを確認します。 Azure Monitor を介してログを取り込み、エンドポイント デバイス、ネットワーク リソース、およびその他のセキュリティ システムによって生成されたセキュリティ データを集計します。 Azure Monitor で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期のアーカイブ ストレージには Azure Storage アカウントを使用します。  
 
- さらに、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードします。  
 

多くの組織では、頻繁に使用される "ホット" データに対しては Azure Sentinel を、使用頻度の低い "コールド" データに対しては Azure Storage を使用することを選択しています。  
 

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](/azure/azure-monitor/platform/diagnostic-settings)   
 

- [Azure Sentinel をオンボードする方法](../../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="lt-6-configure-log-storage-retention"></a>LT-6:ログの保持期間を構成する

**ガイダンス**: Azure Active Directory のサインイン ログ、監査ログおよびリスク データ ログを格納するために使用されるすべてのストレージ アカウントまたは Log Analytics ワークスペースに、組織のコンプライアンス規則に従ったログの保有期間が設定されていることを確かめます。

Azure Monitor で、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定できます。 長期ストレージおよびアーカイブ ストレージには、Azure Storage、Data Lake、または Log Analytics ワークスペースのアカウントを使用します。

- [Log Analytics ワークスペースの保有期間を構成する方法](/azure/azure-monitor/platform/manage-cost-storage)  

- [ Azure ストレージ アカウントでのリソース ログの格納](/azure/azure-monitor/platform/resource-logs-collect-storage)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](/azure/security/benchmarks/security-controls-v2-incident-response)」を参照してください。*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: 準備 – インシデント対応プロセスを Azure 用に更新する

**ガイダンス**:組織において、セキュリティ インシデントに対応するためのプロセスが用意されていること、Azure のこれらのプロセスが更新されていること、それらのプロセスを定期的に使用して準備されていることを確認します。

- [企業環境全体にセキュリティを実装する](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [インシデント対応のリファレンス ガイド](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: 準備 – インシデント通知をセットアップする

**ガイダンス**:Azure Security Center でセキュリティ インシデントの連絡先情報を設定します。 この連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 また、インシデント対応のニーズに応じて、異なる Azure サービスでインシデント アラートと通知をカスタマイズするオプションもあります。 

- [Azure Security Center のセキュリティ連絡先を設定する方法](../../security-center/security-center-provide-security-contact-details.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: 検出と分析 – 高品質のアラートに基づいてインシデントを作成する 

**ガイダンス**:高品質のアラートを作成し、アラートの品質を測定するプロセスがあることを確認します。 これにより、過去のインシデントから教訓を学び、アナリストに対するアラートに優先順位を付けることができるので、擬陽性に時間を無駄にすることがありません。 

高品質のアラートは、過去のインシデントからの経験、検証されたコミュニティ ソース、およびさまざまなシグナル ソースの融合と関連付けによってアラートを生成してクリーンアップするように設計されたツールに基づいて構築できます。 

Azure Security Center では、多数の Azure 資産について高品質のアラートが提供されます。 ASC データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。 Azure Sentinel を使用すると、高度なアラート ルールを作成し、調査のためにインシデントを自動的に生成できます。 

エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートし、Azure リソースへのリスクを特定します。 アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートします。

- [エクスポートを構成する方法](../../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../../sentinel/connect-azure-security-center.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: 検出と分析 – インシデントを調査する

**ガイダンス**:アナリストが潜在的なインシデントを調査するときに、さまざまなデータ ソースを照会して使用し、発生した内容の完全なビューを構築できることを確認します。 キル チェーン全体で潜在的な攻撃者のアクティビティを追跡して死角を回避するには、さまざまなログを収集する必要があります。  また、他のアナリストのため、および将来において履歴が参照される場合のために、確実に分析情報と学習がキャプチャされているようにします。  

調査のためのデータ ソースには、スコープ内のサービスおよび実行中のシステムから既に収集されている一元化されたログ ソースが含まれますが、次のものも含まれます。

- ネットワーク データ - ネットワーク セキュリティ グループのフロー ログ、Azure Network Watcher、Azure Monitor を使用して、ネットワーク フロー ログやその他の分析情報をキャプチャします。 

- 実行中のシステムのスナップショット: 

    - Azure 仮想マシンのスナップショット機能を使用して、実行中のシステムのディスクのスナップショットを作成します。 

    - オペレーティング システムのネイティブ メモリ ダンプ機能を使用して、実行中のシステムのメモリのスナップショットを作成します。

    - Azure サービスのスナップショット機能またはソフトウェア独自の機能を使用して、実行中のシステムのスナップショットを作成します。

Azure Sentinel により、事実上すべてのログソースに対して広範な Data Analytics と、インシデントのライフサイクル全体を管理するためのケース管理ポータルが提供されます。 調査中のインテリジェンス情報を、追跡とレポートのためにインシデントに関連付けることができます。 

- [Windows マシンのディスクのスナップショットを作成する](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux マシンのディスクのスナップショットを作成する](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure サポートの診断情報とメモリ ダンプ コレクション](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Azure Sentinel でインシデントを調査します](../../sentinel/tutorial-investigate-cases.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: 検出と分析 – インシデントの優先順位を付ける

**ガイダンス**:アラートの重要度と資産の機密性に基づいて、最初に注目するインシデントについてのコンテキストをアナリストに提供します。 

Azure Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

さらに、タグを使用してリソースをマークし、Azure リソース (特に、機密データを処理するもの) を識別して分類するための命名システムを作成します。  インシデントが発生した Azure リソースと環境の重要度に基づいて、アラートの修復に優先順位を付けることは、お客様の責任です。

- [Security alerts in Azure Security Center](../../security-center/security-center-alerts-overview.md)

- [タグを使用した Azure リソースの整理](/azure/azure-resource-manager/resource-group-using-tags)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: 包含、根絶、復旧 – インシデントの処理を自動化する

**ガイダンス**:手動による反復タスクを自動化して、応答時間を短縮し、アナリストの負担を軽減します。 手動タスクの実行には時間がかかり、各インシデントの速度が低下し、アナリストが処理できるインシデントの数が減少します。 手動タスクではアナリストの疲労も増加します。これにより、遅延が発生する人的エラーのリスクが増加し、複雑なタスクに効果的に焦点を当てるアナリストの能力が低下します。 Azure Security Center と Azure Sentinel のワークフロー自動化機能を使用して、自動的にアクションをトリガーしたり、プレイブックを実行して受信したセキュリティ アラートに応答したりします。 プレイブックにより、通知の送信、アカウントの無効化、問題のあるネットワークの特定などのアクションが実行されます。 

- [Security Center でワークフロー自動化を構成する](../../security-center/workflow-automation.md)

- [Azure Security Center で脅威への自動対応を設定する](https://docs.microsoft.com/azure/security-center/tutorial-security-incident#triage-security-alerts)

- [Azure Sentinel で脅威への自動対応を設定します](../../sentinel/tutorial-respond-threats-playbook.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="posture-and-vulnerability-management"></a>体制と脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「体制と脆弱性の管理](/azure/security/benchmarks/security-controls-v2-vulnerability-management)」を参照してください。*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Azure サービスのセキュリティで保護された構成を確立する 

**ガイダンス**: Microsoft ID およびアクセス管理ソリューションは、IT 部門でオンプロミス全体およびクラウドのアプリケーションとリソースへのアクセスを保護するのに役立ちます。 組織では、セキュリティのベスト プラクティスに従って、ID およびアクセス管理の実装がセキュリティで保護され、攻撃に対する回復性が強化されていることを確かめることが重要です。 

ID およびアクセス管理の実装戦略に基づき、組織では、ID インフラストラクチャをセキュリティで保護するために Microsoft のベスト プラクティス ガイダンスに従う必要があります。 

外部のパートナーと共同作業を行う組織では、セキュリティ リスクを軽減し、機密リソースを保護するために適切なガバナンス、セキュリティ、およびコンプライアンス構成をさらに評価して実装する必要があります。

- [Azure の ID 管理とアクセス制御セキュリティのベスト プラクティス](../../security/fundamentals/identity-management-best-practices.md)

- [ID インフラストラクチャをセキュリティ保護する 5 つのステップ](../../security/fundamentals/steps-secure-identity.md)

- [Azure Active Directory と Microsoft 365 での外部コラボレーションのセキュリティ保護](secure-external-access-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Azure サービスのセキュリティで保護された構成を維持する

**ガイダンス**: Microsoft セキュリティ スコアを利用することで、組織ではセキュリティ体制を測定し、脅威から組織を保護するのに役立つ可能性がある推奨事項を確認できます。 推奨される改善アクションのセキュリティ スコアを組織で定期的に確認し、ID のセキュリティ体制を改善することをお勧めします。 

- [Azure Active Directory の ID セキュリティ スコアとは](identity-secure-score.md)

- [Microsoft セキュリティ スコア](/microsoft-365/security/mtp/microsoft-secure-score)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: 定期的に攻撃シミュレーションを実施する

**ガイダンス**:必要に応じて、Azure リソースの侵入テストまたはレッド チーム アクティビティを実施し、セキュリティに関するすべての重大な調査結果が確実に修復されるようにします。
お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft クラウド侵入テストの実施ルールに従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。

- [Azure での侵入テスト](../../security/fundamentals/pen-testing.md)

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**責任**: 共有

**Azure Security Center の監視**: なし

## <a name="governance-and-strategy"></a>ガバナンスと戦略

*詳細については、[Azure セキュリティ ベンチマークの「ガバナンスと戦略](/azure/security/benchmarks/security-controls-v2-governance-strategy)」を参照してください。*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: 資産の管理とデータ保護の戦略を定義する 

**ガイダンス**:システムとデータを継続的に監視および保護するための明確な戦略が文書化および伝達されるようにします。 ビジネスに不可欠なデータとシステムの検出、評価、保護、監視の優先順位を決定します。 
 

 
この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 
 
-   ビジネス リスクに応じたデータ分類標準
 
-   リスクと資産のインベントリに対するセキュリティ組織の可視性 
 
-   Azure サービスを使用するためのセキュリティ組織の承認 
 
-   ライフサイクルを通じた資産のセキュリティ
 
-   組織のデータ分類に従った必要なアクセス制御戦略
 
-   Azure ネイティブおよびサードパーティのデータ保護機能の使用
 
-   転送中および保存中のユース ケースのデータ暗号化要件
 
-   適切な暗号化標準
 

 
詳細については、次のリファレンスを参照してください。 
- [Azure セキュリティ アーキテクチャに関する推奨事項 - ストレージ、データ、暗号化](/azure/architecture/framework/security/storage-data-encryption)
 

 
- [Azure のセキュリティの基礎 - Azure のデータ セキュリティ、暗号化、ストレージ](../../security/fundamentals/encryption-overview.md) 

 
- [クラウド導入フレームワーク - Azure のデータ セキュリティと暗号化のベスト プラクティス](../../security/fundamentals/data-encryption-best-practices.md) 

 
- [Azure セキュリティ ベンチマーク - アセット管理](/azure/security/benchmarks/security-benchmark-v2-asset-management) 

 
- [Azure セキュリティ ベンチマーク - データ保護](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: 企業のセグメント化戦略を定義する 

**ガイダンス**:ID、ネットワーク、アプリケーション、サブスクリプション、管理グループ、その他のコントロールを利用し、アセットへのアクセスをセグメント化する企業規模の戦略を確立します。

セキュリティ分離の必要性と、互いと通信し、データにアクセスする必要があるシステムの日常的操作を有効にするという必要性のバランスを慎重に取ります。

セグメント化戦略は、ネットワーク セキュリティ、ID とアクセス モデル、アプリケーション アクセス許可とアクセス モデル、人的プロセスの制御など、あらゆるコントロールの種類を対象として確実に一貫性をもって実装します。

- [Azure のセグメント化戦略に関するガイド (動画)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure のセグメント化戦略に関するガイド (ドキュメント)](/security/compass/governance#enterprise-segmentation-strategy)

- [ネットワークのセグメント化を企業のセグメント化戦略に合わせる](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: セキュリティ態勢管理の戦略を定義する

**ガイダンス**:個々の資産とそれらがホストされている環境に対するリスクを継続的に測定し、軽減します。 高い価値を持つ資産と、攻撃に晒される可能性の高い部分 (公開されたアプリケーション、ネットワークのイングレス ポイントとエグレス ポイント、ユーザーと管理者のエンドポイントなど) を優先します。

- [Azure セキュリティ ベンチマーク - 体制と脆弱性の管理](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: 組織の役割、責任、責務を整合させる

**ガイダンス**:セキュリティ組織における役割と責任に関する明確な戦略が文書化されて伝えられるようにします。 セキュリティに関する決定についてわかりやすく説明すること、共有される責任モデルについて全員に教育すること、クラウドをセキュリティで保護するテクノロジについて技術チームに教育することを優先とします。

- [Azure のセキュリティのベスト プラクティス 1 – 人: クラウド セキュリティに関する取り組みについてチームを教育する](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure のセキュリティのベスト プラクティス 2 - 人: クラウド セキュリティ テクノロジについてチームを教育する](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure のセキュリティのベスト プラクティス 3 - プロセス: クラウドのセキュリティに関する意思決定のアカウンタビリティを割り当てる](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="gs-5-define-network-security-strategy"></a>GS-5: ネットワーク セキュリティ戦略を定義する

**ガイダンス**:組織全体のセキュリティ アクセス制御戦略の一環として、Azure ネットワーク セキュリティ アプローチを確立します。  

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

-   一元的なネットワーク管理とセキュリティ責任

-   エンタープライズ セグメント化戦略と一致する仮想ネットワーク セグメント化モデル

-   さまざまな脅威と攻撃のシナリオにおける修復戦略

-   インターネット エッジとイングレスおよびエグレス戦略

-   クラウドとオンプレミスのハイブリッド相互依存戦略

-   最新のネットワーク セキュリティ成果物 (例: ネットワーク図、参照ネットワーク アーキテクチャ)

詳細については、次のリファレンスを参照してください。
- [Azure のセキュリティのベスト プラクティス 11 - アーキテクチャ。単一の統合セキュリティ戦略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure セキュリティ ベンチマーク - ネットワーク セキュリティ](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Azure のネットワーク セキュリティの概要](../../security/fundamentals/network-overview.md)

- [エンタープライズ ネットワーク アーキテクチャ戦略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: ID と特権アクセス戦略を定義する

**ガイダンス**:組織全体のセキュリティ アクセス制御戦略の一環として、Azure の ID と特権アクセス アプローチを確立します。  

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

-   一元化された ID と認証システム、および他の内部および外部 ID システムとのその相互接続

-   さまざまなユース ケースおよび条件における強力な認証方法

-   高い特権を持つユーザーの保護

-   異常なユーザー アクティビティの監視と処理  

-   ユーザー ID とアクセスの確認と調整のプロセス

詳細については、次のリファレンスを参照してください。

- [Azure セキュリティ ベンチマーク - ID 管理](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure セキュリティ ベンチマーク - 特権アクセス](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure のセキュリティのベスト プラクティス 11 - アーキテクチャ。単一の統合セキュリティ戦略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure ID 管理のセキュリティの概要](../../security/fundamentals/identity-management-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: ログ記録と脅威対応戦略を定義する

**ガイダンス**:コンプライアンス要件を満たしながら脅威を迅速に検出して修復するための、ログ記録と脅威対応戦略を確立します。 アナリストが、統合や手動による手順ではなく、脅威の対応に集中できるように、質の高いアラートとシームレスなエクスペリエンスを提供することを優先してください。 

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

-   セキュリティ運用 (SecOps) 組織の役割と責任 

-   NIST または他の業界フレームワークと一致する、明確に定義されたインシデント対応プロセス 

-   脅威の検出、インシデント対応、コンプライアンスのニーズに対応するためのログのキャプチャと保持

-   SIEM、Azure のネイティブ機能、その他のソースを使用した、脅威に関する情報の一元的な可視化と関連付け 

-   顧客、サプライヤー、および関心を持つパブリック パーティに関するコミュニケーションと通知の計画

-   ログ記録と脅威の検出、フォレンジクス、攻撃の修復と根絶など、インシデント処理に対する Azure ネイティブおよびサードパーティのプラットフォームの使用

-   インシデントとインシデント発生後のアクティビティを処理するためのプロセス (教訓や証拠の保持など)

詳細については、次のリファレンスを参照してください。

- [Azure セキュリティ ベンチマーク - ログと脅威検出](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure セキュリティ ベンチマーク - インシデント対応](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure のセキュリティのベスト プラクティス 4 - プロセス: クラウドのインシデント対応プロセスを更新する](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 導入フレームワーク、ログ、およびレポートの決定ガイド](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure のエンタープライズ スケーリング、管理、監視](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="next-steps"></a>次のステップ

- 「[Azure セキュリティ ベンチマーク V2 の概要](/azure/security/benchmarks/overview)」を参照してください。
- [Azure セキュリティ ベースライン](/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
