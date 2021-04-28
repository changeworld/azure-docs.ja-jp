---
title: Microsoft Azure Peering Service の Azure セキュリティ ベースライン
description: Microsoft Azure Peering Service のセキュリティ ベースラインでは、Azure セキュリティ ベンチマークに指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: peering-service
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 854c669861c6aff71861481843bff97fdb7157ab
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315358"
---
# <a name="azure-security-baseline-for-microsoft-azure-peering-service"></a>Microsoft Azure Peering Service の Azure セキュリティ ベースライン

このセキュリティ ベースラインによって、[Azure セキュリティ ベンチマーク バージョン 2.0](../security/benchmarks/overview.md) のガイダンスが Microsoft Azure Peering Service に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 内容は、Microsoft Azure Peering Service に適用される Azure セキュリティ ベンチマークと関連ガイダンスで定義されている **セキュリティ コントロール** によってグループ化されています。

> [!NOTE]
> Microsoft Azure Peering Service に適用されない **コントロール**、または Microsoft が責任を持つものは、除外されています。 Microsoft Azure Peering Service が Azure セキュリティ ベンチマークにどれほど徹底して対応しているかについては、 **[Microsoft Azure Peering Service セキュリティ ベースラインのマッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)** を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](/azure/security/benchmarks/security-controls-v2-network-security)」を参照してください。*

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: 侵入検出および侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**: Peering Service では、ネイティブの侵入検出および防止のサービスは提供されません。 お客様は、ビジネス要件に応じて、Azure Firewall からの脅威インテリジェンスベースのフィルター処理を使用して、悪意のある既知の IP アドレスおよびドメインとの間のトラフィックに対してアラートを出したりブロックしたりするなど、セキュリティのベスト プラクティスに従う必要があります。 これらの IP アドレスとドメインのソースは、Microsoft の脅威インテリジェンス フィードです。 

ペイロード検査が必要な場合は、ペイロード検査機能を備えたサードパーティ製の侵入検出または防止システム (IDS/IPS) を、Azure Marketplace からデプロイしてください。  
または、ネットワーク ベースの侵入検出または防止システムと組み合わせて (またはその代わりに)、ホストベースのエンドポイント検出と応答 (EDR) ソリューションを使用します。  

侵入検出または侵入防止システムを利用するという規制要件がある場合は、質の高いアラートが常に使用または提供されるように調整する必要があります。 

- [Azure Firewall をデプロイする方法l](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace にはサードパーティの ID 機能が含まれる](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Microsoft Defender の ATP EDR 機能](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="identity-management"></a>ID 管理

*詳細については、[Azure セキュリティ ベンチマークの「ID 管理](/azure/security/benchmarks/security-controls-v2-identity-management).* 」を参照してください。

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1:Azure Active Directory を中央 ID および認証システムとして標準化する

**ガイダンス**: Microsoft Azure Peering Service リソースのコントロール プレーン (Azure portal など) への管理アクセス権を持つユーザー アカウントのインベントリを維持します。

お使いのサブスクリプションの Azure portal にある ID およびアクセス管理 (IAM) ペインを使用して、Azure ロールベースのアクセス制御 (Azure RBAC) を構成します。 ロールは、Azure Active Directory (Azure AD) 内のユーザー、グループ、サービス プリンシパル、およびマネージド ID に適用されます。

- [Azure portal を使用して Azure ロールの割り当てを追加または削除する](../role-based-access-control/role-assignments-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2:アプリケーション ID を安全かつ自動的に管理する

**ガイダンス**: マネージド ID は、Peering Service ではサポートされていません。 Peering Service など、マネージド ID がサポートされていないサービスの場合は、Azure Active Directory (Azure AD) を使用して、リソース レベルでの制限されたアクセス許可を持つサービス プリンシパルを代わりに作成します。 

- [Azure マネージド ID](../active-directory/managed-identities-azure-resources/overview.md)

- [Azure リソースのマネージド ID をサポートするサービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Azure サービス プリンシパル](/powershell/azure/create-azure-service-principal-azureps)

- [証明書を使用してサービス プリンシパルを作成する](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3:アプリケーションのアクセスに Azure AD シングル サインオン (SSO) を使用する

**ガイダンス**:Microsoft Azure Peering Service では、Azure Active Directory (Azure AD) を使用して、Azure リソースに対する ID およびアクセス管理を提供します。 これには、従業員などの企業 ID だけでなく、パートナー、ベンダー、サプライヤーなどの外部 ID も含まれます。 

シングル サインオンを使用して、オンプレミスとクラウドにある組織のデータとリソースへのアクセスを管理し、セキュリティで保護します。 すべてのユーザー、アプリケーション、デバイスを Azure AD に接続することで、シームレスで安全なアクセスを実現し、可視性と制御性を高めることができます。

- [Azure AD を使用したアプリケーションの SSO について理解する](../active-directory/manage-apps/what-is-single-sign-on.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4:すべての Azure Active Directory ベースのアクセスに強力な認証制御を使用する

**ガイダンス**: Azure Active Directory (Azure AD) の多要素認証を有効にし、Azure Security Center の ID およびアクセス管理の推奨事項に従います。

- [Azure で多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5:アカウントの異常を監視してアラートを出す

**ガイダンス**: Azure Active Directory (Azure AD) の Privileged Identity Management (PIM) を使用して、環境内で疑わしいまたは安全ではないアクティビティが発生したときにログとアラートを生成します。 また、Azure AD のリスク検出を使用して、危険なユーザーの行動に関するアラートとレポートを表示します。

- [Privileged Identity Management (PIM) をデプロイする方法](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure AD のリスク検出の概要](../active-directory/identity-protection/overview-identity-protection.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6:条件に基づいて Azure リソースへのアクセスを制限する

**ガイダンス**: 特定の IP 範囲からのユーザー ログインには多要素認証を使用するよう要求するなど、ユーザーが定義した条件に基づいて、よりきめ細かいアクセス制御を行うには、Azure Active Directory (Azure AD) の条件付きアクセスを使用します。 詳細な認証セッションの管理は、さまざまなユース ケースに対する Azure AD 条件付きアクセス ポリシーによって使用することもできます。 

- [Azure での条件付きアクセスの概要](../active-directory/conditional-access/overview.md)

- [一般的な条件付きアクセス ポリシー](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [条件付きアクセスを使用して認証セッション管理を構成する](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7:意図しない資格情報の公開を排除する

**ガイダンス**: Azure DevOps 内で資格情報スキャナーを実装して、コード内で使用されている資格情報を特定します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

GitHub の場合、ネイティブ シークレット スキャン機能を使用して、コード内で資格情報やその他の形式のシークレットを識別できます。

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [GitHub シークレット スキャン](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="privileged-access"></a>特権アクセス

*詳細については、[Azure セキュリティ ベンチマークの「特権アクセス](/azure/security/benchmarks/security-controls-v2-privileged-access)」を参照してください。*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1:高い特権を持つユーザーを保護および制限する

**ガイダンス**:高い特権を持つユーザー アカウントの数を制限し、これらのアカウントを昇格されたレベルで保護します。 

Azure Active Directory (Azure AD) で最も重要な組み込みロールは、全体管理者と特権ロール管理者です。その理由は、この 2 つのロールを割り当てられたユーザーは管理者ロールを委任できるからです。 

これらの特権を使用すると、ユーザーは Azure 環境内のすべてのリソースを直接または間接に読み取り、変更できます。

- 全体管理者または会社の管理者:このロールが割り当てられたユーザーは、Azure AD のすべての管理機能に加え、Azure AD ID を使用するサービスにもアクセスできます。

- 特権ロール管理者:このロールが割り当てられたユーザーは、Azure AD と Azure AD Privileged Identity Management (PIM) 内でロールの割り当てを管理できます。 さらに、このロールは、PIM と管理単位のすべての側面を管理できます。

特定の特権アクセス許可を割り当てられたカスタム ロールを使用する場合は、管理する必要のある他の重要なロールがある場合があります。 重要なビジネス資産の管理者アカウントに同様のコントロールを適用します。  

Azure Privileged Identity Management (PIM) を使用して、Azure リソースと Azure AD への Just-In-Time (JIT) の特権アクセスを有効にします。 ユーザーが必要とする場合にのみ特権タスクを実行するための一時的なアクセス許可は、JIT によって付与されます。 PIM を使用すると、Azure AD 組織に不審なアクティビティや安全でないアクティビティがある場合に、セキュリティ アラートを生成することもできます。

- [Azure AD での管理者ロールのアクセス許可](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [Azure Privileged Identity Management のセキュリティ アラートを使用する](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Azure AD でのハイブリッドおよびクラウド デプロイ用の特権アクセスをセキュリティで保護する](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2:ビジネス クリティカルなシステムへの管理アクセスを制限する

**ガイダンス**: 特権アクセスが付与されているアカウントを、その所属先のサブスクリプションと管理グループに制限することによって、ビジネス クリティカルなシステムへのアクセスを分離します。 

ビジネス クリティカルなシステムにインストールされたエージェントを使用して、Active Directory ドメイン コントローラー (DC)、セキュリティ ツール、システム管理ツールなど、ビジネス クリティカルな資産への管理アクセス権を持つ管理、ID、セキュリティのシステムへのアクセスを制限します。 これらの管理システムおよびセキュリティ システムを侵害した攻撃者は、それらをすぐに悪用してビジネス クリティカルな資産を侵害することができます。 

一貫したアクセス制御を確保するため、自分の企業のセグメント化戦略にすべての種類のアクセス制御を合わせる必要があります。 

電子メール、閲覧、生産性のタスクに使用される標準ユーザー アカウントとは別に、特権アカウントを割り当てます。

- [Azure のコンポーネントと参照モデル](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [管理グループ アクセス](https://docs.microsoft.com/azure/governance/management-groups/overview#management-group-access)

- [Azure サブスクリプション管理者](../cost-management-billing/manage/add-change-subscription-administrator.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3:ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory (AD) では、古いアカウントの検出に役立つログが提供されます。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

- [Azure AD アクティビティ レポートの検出](../active-directory/reports-monitoring/howto-find-activity-reports.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4:Azure AD で緊急アクセスを設定する

**ガイダンス**: 通常の管理者アカウントが使用できない場合にアクセスするための緊急アカウントを設定して、Azure Active Directory (Azure AD) 組織から誤ってロックアウトされないようにします。 緊急アクセス アカウントは、より高いレベルの特権を持つことができます。特定の個人には割り当てないでください。 緊急アクセス アカウントは、緊急時または "非常時" のシナリオに制限します。

緊急アクセス用アカウントの資格情報 (パスワード、証明書、スマート カードなど) は、安全に保管し、緊急時にそれらの使用が許可されているユーザーにのみに知らせるようにしてください。

- [Azure AD で緊急アクセス用アカウントを管理する](/azure/active-directory/users-groups-roles/directory-emergency-access)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="pa-5-automate-entitlement-management"></a>PA-5:エンタイトルメント管理を自動化する 

**ガイダンス**: Azure Active Directory (Azure AD) のエンタイトルメント管理機能を使用して、アクセス権の割り当て、レビュー、有効期限など、アクセス要求のワークフローを自動化します。 2 段階または複数段階の承認もサポートされています。

- [Azure Active Directory (Azure AD) のアクセス レビューとは](../active-directory/governance/access-reviews-overview.md)

- [Azure Active Directory (Azure AD) のエンタイトルメント管理とは](../active-directory/governance/entitlement-management-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6:特権アクセス ワークステーションを使用する

**ガイダンス**: Azure Active Directory (Azure AD) の多要素認証して Azure Sentinel 関連リソースへのログインと構成が可能な特権アクセス ワークステーション (PAW) を使用します。

- [Privileged Access Workstations](/windows-server/identity/securing-privileged-access/privileged-access-workstations) (Privileged Access Workstation)

- [クラウドベースの Azure AD 多要素認証のデプロイの計画](../active-directory/authentication/howto-mfa-getstarted.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7:Just Enough Administration (最小限の特権の原則) に従う 

**ガイダンス**: Azure ロールベースのアクセス制御 (Azure RBAC) を使用すると、ロールの割り当てを通じて Azure リソースへのアクセスを管理できます。 これらのロールを、ユーザー、グループ サービス プリンシパル、およびマネージド ID に割り当てることができます。 特定のリソースに対して定義済みの組み込みロールがあります。これらのロールは、Azure CLI、Azure PowerShell、Azure portal などのツールを使用してインベントリまたは照会できます。

Azure RBAC を使用してリソースに割り当てる特権は、常に、ロールで必要なものに制限する必要があります。 制限された特権は、Azure Active Directory (Azure AD) Privileged Identity Management (PIM) の Just-In-Time (JIT) アプローチを補完するものであり、それらの特権は定期的に確認する必要があります。

組み込みのロールを使用してアクセス許可を割り当て、カスタム ロールは必要な場合にのみ作成します。

- [Azure ロールベースのアクセス制御 (Azure RBAC) とは](../role-based-access-control/overview.md)

- [Azure RBAC を構成する方法](../role-based-access-control/role-assignments-portal.md)

- [Azure AD の ID およびアクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

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

- [Azure 管理グループの概要](../governance/management-groups/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2:セキュリティ チームが資産インベントリとメタデータに確実にアクセスできるようにする

**ガイダンス**: セキュリティ チームが、Azure 上の継続的に更新される資産インベントリに確実にアクセスできるようにします。 セキュリティ チームは、組織が新たなリスクにさらされる可能性を評価するため、および継続的なセキュリティ改善への入力として、このインベントリを必要とすることがよくあります。 

Azure Security Center インベントリ機能と Azure Resource Graph を使用すると、クエリを実行してサブスクリプション内のすべてのリソース (Azure サービス、アプリケーション、ネットワーク リソースなど) を検出できます。  

タグと Azure の他のメタデータ (名前、説明、カテゴリ) を使用して、組織の分類に従って資産を論理的に整理します。  

- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [Azure Security Center の資産インベントリ管理](../security-center/asset-inventory.md)

- 資産のタグ付けの詳細については、「[リソースの名前付けとタグ付けの意思決定ガイド](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)」を参照

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="am-3-use-only-approved-azure-services"></a>AM-3:承認された Azure サービスのみを使用する

**ガイダンス**: Microsoft Azure Peering Service では、"Microsoft.Peering/peerings" 名前空間での Azure Policy を使用した Azure Resource Manager ベースのデプロイと構成の適用がサポートされています。 Azure Policy を使用して、環境内でユーザーがプロビジョニングできるサービスを監査および制限します。 Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。 また、Azure Monitor を使用して、承認されていないサービスが検出されたときにアラートをトリガーするルールを作成することもできます。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: Azure 条件付きアクセスを使用して Azure Resource Manager とやりとりするユーザーの機能を制限するには、"Microsoft Azure 管理" アプリに対して [アクセスのブロック] を構成します。

- [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../role-based-access-control/conditional-access-azure-management.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="logging-and-threat-detection"></a>ログと脅威検出

*詳細については、[Azure セキュリティ ベンチマークの「ログと脅威検出](/azure/security/benchmarks/security-controls-v2-logging-threat-protection)」を参照してください。*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1:Azure リソースの脅威検出を有効にする

**ガイダンス**: 潜在的な脅威や異常がないか、さまざまな種類の Azure 資産を監視していることを確認してください。 アナリストが選別しやすいように、質の高いアラートを取得して誤検知を減らすことに専念します。 アラートは、ログ データ、エージェント、その他のデータを元に生成できます。

Azure Security Center の組み込みの脅威検出機能を使用します。これは、Azure サービス テレメトリの監視とサービス ログの分析に基づいています。 データは、Log Analytics エージェントを使用して収集されます。このエージェントは、セキュリティ関連のさまざまな構成とイベント ログをシステムから読み取り、分析のためにデータをワークスペースにコピーします。 

さらに、Azure Sentinel を使用して分析ルールを構築し、環境全体で特定の条件に一致する脅威を探します。 ルールにより、条件が一致したときにインシデントが生成されます。これにより、各インシデントを調査できます。 Azure Sentinel にサード パーティの脅威インテリジェンスをインポートして、脅威の検出機能を強化することもできます。 

- [Azure Security Center での脅威の防止](/azure/security-center/threat-protection)

- [Azure Security Center セキュリティ アラート リファレンス ガイド](../security-center/alerts-reference.md)

- [脅威を検出するためのカスタム分析規則を作成する](../sentinel/tutorial-detect-threats-custom.md)

- [Azure Sentinel を使用したサイバー脅威インテリジェンス](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2:Azure ID とアクセスの管理のために脅威検出を有効にする

**ガイダンス**: Azure Active Directory (Azure AD) で提供されるユーザー ログを、Azure AD レポートを使用して表示したり、Azure Monitor に統合したりできます。 これらのログは、より高度な監視および分析ユース ケースのために、Azure Sentinel や他のセキュリティ情報イベント管理 (SIEM) ソリューション、または監視ツールに統合することもできます。

- サインイン - サインイン レポートでは、マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報が提供されます。

- 監査ログ - Azure AD 内のさまざまな機能によって加えられたすべての変更のログを通して追跡可能性を提供します。 監査ログの例としては、ユーザー、アプリ、グループ、ロール、ポリシーの追加や削除など、Azure AD 内のあらゆるリソースに加えられた変更があります。

- リスクの高いサインイン - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によって実行された可能性があるサインイン試行の指標です。

- リスクのフラグ付きユーザー - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。

Azure Security Center では、使用すべきでないアカウントによる認証試行の失敗回数が多すぎるなど、サブスクリプションでの特定の不審なアクティビティに対してアラートを生成することもできます。 Security Center の脅威保護モジュールでは、基本的なセキュリティ検疫監視に加えて、個々の Azure コンピューティング リソース (仮想マシン、コンテナー、アプリ サービスなど)、データ リソース (SQL DB、ストレージなど)、Azure サービス レイヤーから、さらに詳細なセキュリティ アラートを収集することもできます。 この機能を使用すると、個々のリソース内でアカウントの異常を確認できます。

- [Azure AD でアクティビティ レポートを監査する](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure Identity Protection を有効にする](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center での脅威の防止](/azure/security-center/threat-protection)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3:Azure ネットワーク アクティビティのログ記録を有効にする

**ガイダンス**: 接続場所と次の間の接続性に関する分析情報を提供するように接続テレメトリを構成します:

Microsoft Azure Peering Service を介した Microsoft ネットワーク。

- [接続テレメトリを構成する](measure-connection-telemetry.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4:Azure リソースのログ記録を有効にする

**ガイダンス**: Azure アクティビティ ログを有効にし、Log Analytics ワークスペース、Azure イベント ハブ、または Azure ストレージア カウントにログを送信してアーカイブします。 

アクティビティ ログは、コントロール プレーン レベルで Azure ExpressRoute リソースで実行された操作に関する分析情報を提供します。 Azure アクティビティ ログのデータを使用すると、リソースのコントロール プレーン レベルで実行された書き込み操作 (PUT、POST、DELETE) について、"いつだれが何を" 行ったのかを特定できます。

- [Azure アクティビティ ログ](/azure/azure-monitor/platform/activity-log)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5:セキュリティ ログの管理と分析を一元化する

**ガイダンス**:ログ記録のストレージと分析を一元化して、相関関係を有効にします。 ログ ソースごとに、データ所有者、アクセス ガイダンス、ストレージの場所、データの処理とアクセスに使用するツール、データ保持の要件を割り当てたことを確認します。

Azure アクティビティ ログを一元的なログ記録に統合していることを確認します。 Azure Monitor を介してログを取り込み、エンドポイント デバイス、ネットワーク リソース、およびその他のセキュリティ システムによって生成されたセキュリティ データを集計します。 Azure Monitor で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期のアーカイブ ストレージには Azure Storage アカウントを使用します。

さらに、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードします。

多くの組織では、頻繁に使用される "ホット" データに対しては Azure Sentinel を、使用頻度の低い "コールド" データに対しては Azure Storage を使用することを選択しています。

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="lt-6-configure-log-storage-retention"></a>LT-6:ログの保持期間を構成する

**ガイダンス**: Azure Monitor で、組織のコンプライアンス規則に従って、Azure リソースに関連付けられている Log Analytics ワークスペースのログの保持期間を設定します。

- [ログ保持期間のパラメーターを設定する方法](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

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

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: 検出と分析 – 高品質のアラートに基づいてインシデントを作成する 

**ガイダンス**:高品質のアラートを作成し、アラートの品質を測定するプロセスがあることを確認します。 これにより、過去のインシデントから教訓を学び、アナリストに対するアラートに優先順位を付けることができるので、擬陽性に時間を無駄にすることがありません。 

高品質のアラートは、過去のインシデントからの経験、検証されたコミュニティ ソース、およびさまざまなシグナル ソースの融合と関連付けによってアラートを生成してクリーンアップするように設計されたツールに基づいて構築できます。 

Azure Security Center では、多数の Azure 資産について高品質のアラートが提供されます。 ASC データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。 Azure Sentinel を使用すると、高度なアラート ルールを作成し、調査のためにインシデントを自動的に生成できます。 

エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートし、Azure リソースへのリスクを特定します。 アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートします。

- [エクスポートを構成する方法](../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: 検出と分析 – インシデントを調査する

**ガイダンス**:アナリストが潜在的なインシデントを調査するときに、さまざまなデータ ソースを照会して使用し、発生した内容の全体像を作成できることを確認します。 キル チェーン全体で潜在的な攻撃者のアクティビティを追跡して死角を回避するには、さまざまなログを収集する必要があります。  また、他のアナリストのため、および将来において履歴が参照される場合のために、確実に分析情報と学習がキャプチャされているようにします。  

調査のためのデータ ソースには、スコープ内のサービスおよび実行中のシステムから既に収集されている一元化されたログ ソースが含まれますが、次のものも含まれます。

- ネットワーク データ - ネットワーク セキュリティ グループのフロー ログ、Azure Network Watcher、Azure Monitor を使用して、ネットワーク フロー ログやその他の分析情報をキャプチャします。 

- 実行中のシステムのスナップショット: 

    - Azure 仮想マシンのスナップショット機能を使用して、実行中のシステムのディスクのスナップショットを作成します。 

    - オペレーティング システムのネイティブ メモリ ダンプ機能を使用して、実行中のシステムのメモリのスナップショットを作成します。

    - Azure サービスのスナップショット機能またはソフトウェア独自の機能を使用して、実行中のシステムのスナップショットを作成します。

Azure Sentinel により、事実上すべてのログソースに対して広範な Data Analytics と、インシデントのライフサイクル全体を管理するためのケース管理ポータルが提供されます。 調査中のインテリジェンス情報を、追跡とレポートのためにインシデントに関連付けることができます。 

- [Windows マシンのディスクのスナップショットを作成する](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux マシンのディスクのスナップショットを作成する](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure サポートの診断情報とメモリ ダンプ コレクション](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Azure Sentinel でインシデントを調査します](../sentinel/tutorial-investigate-cases.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: 検出と分析 – インシデントの優先順位を付ける

**ガイダンス**:アラートの重要度と資産の機密性に基づいて、最初に注目するインシデントについてのコンテキストをアナリストに提供します。 

Azure Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

さらに、タグを使用してリソースをマークし、Azure リソース (特に、機密データを処理するもの) を識別して分類するための命名システムを作成します。  インシデントが発生した Azure リソースと環境の重要度に基づいて、アラートの修復に優先順位を付けることは、お客様の責任です。

- [Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [タグを使用した Azure リソースの整理](/azure/azure-resource-manager/resource-group-using-tags)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: 包含、根絶、復旧 – インシデントの処理を自動化する

**ガイダンス**:手動による反復タスクを自動化して、応答時間を短縮し、アナリストの負担を軽減します。 手動タスクの実行には時間がかかり、各インシデントの速度が低下し、アナリストが処理できるインシデントの数が減少します。 手動タスクではアナリストの疲労も増加します。これにより、遅延が発生する人的エラーのリスクが増加し、複雑なタスクに効果的に焦点を当てるアナリストの能力が低下します。 Azure Security Center と Azure Sentinel のワークフロー自動化機能を使用して、自動的にアクションをトリガーしたり、プレイブックを実行して受信したセキュリティ アラートに応答したりします。 プレイブックにより、通知の送信、アカウントの無効化、問題のあるネットワークの特定などのアクションが実行されます。 

- [Security Center でワークフロー自動化を構成する](../security-center/workflow-automation.md)

- [Azure Security Center で脅威への自動対応を設定する](https://docs.microsoft.com/azure/security-center/tutorial-security-incident#triage-security-alerts)

- [Azure Sentinel で脅威への自動対応を設定します](../sentinel/tutorial-respond-threats-playbook.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="posture-and-vulnerability-management"></a>体制と脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「体制と脆弱性の管理](/azure/security/benchmarks/security-controls-v2-vulnerability-management)」を参照してください。*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Azure サービスのセキュリティで保護された構成を確立する 

**ガイダンス**:使用する Azure サービスの安全な構成を簡単にできるようにすることで、インフラストラクチャ チームと DevOps チームに対するセキュリティ ガードレールを定義します。 

Azure セキュリティ ベンチマークのサービス ベースラインを使用した Azure サービスのセキュリティ構成を開始し、必要に応じて組織に合わせてカスタマイズします。 

Azure Security Center を使用して、Azure リソースの構成を監査および強制する Azure Policy を構成します。 

Azure Blueprints を使用すると、1 つのブループリント定義で、Azure Resource Manager テンプレート、Azure RBAC コントロール、ポリシーなど、サービスとアプリケーション環境のデプロイと構成を自動化することができます。

- [エンタープライズ規模のランディング ゾーンでのガードレールの実装の図](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Azure Security Center でのセキュリティ ポリシーの操作](../security-center/tutorial-security-policy.md)

- [コンプライアンスを強制するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint](../governance/blueprints/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Azure サービスのセキュリティで保護された構成を維持する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: 定期的に攻撃シミュレーションを実施する

**ガイダンス**:必要に応じて、Azure リソースの侵入テストまたはレッド チーム アクティビティを実施し、セキュリティに関するすべての重大な調査結果が確実に修復されるようにします。
お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft クラウド侵入テストの実施ルールに従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。

- [Azure での侵入テスト](../security/fundamentals/pen-testing.md)

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
- [Azure セキュリティ アーキテクチャに関する推奨事項 - ストレージ、データ、暗号化](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Azure のセキュリティの基礎 - Azure のデータ セキュリティ、暗号化、ストレージ](../security/fundamentals/encryption-overview.md)

- [クラウド導入フレームワーク - Azure のデータ セキュリティと暗号化のベスト プラクティス](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

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

- [Azure のネットワーク セキュリティの概要](../security/fundamentals/network-overview.md)

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

- [Azure ID 管理のセキュリティの概要](../security/fundamentals/identity-management-overview.md)

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
