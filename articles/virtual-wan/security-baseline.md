---
title: Virtual WAN 用の Azure セキュリティ ベースライン
description: Virtual WAN セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 409c8d163b52e56a1eef68a23e4db79956a68722
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2020
ms.locfileid: "93328644"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Virtual WAN 用の Azure セキュリティ ベースライン

このセキュリティ ベースラインによって、[Azure セキュリティ ベンチマーク バージョン 2.0](../security/benchmarks/overview.md) のガイダンスが Microsoft Azure Virtual WAN に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 内容は、Virtual WAN に適用される Azure セキュリティ ベンチマークとそれに関連するガイダンスで定義されている **セキュリティ コントロール** によってグループ化されています。 Virtual WAN に適用できない **コントロール** は、除外されています。

Azure セキュリティ ベンチマークに対する Virtual WAN の完全なマッピングを確認するには、[Virtual WAN の完全なセキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、 [Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](/azure/security/benchmarks/security-controls-v2-network-security)」を参照してください。*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: 内部トラフィック用のセキュリティを実装する

**ガイダンス** :Azure Virtual WAN による、仮想ネットワークへの直接的なデプロイはサポートされていません。 しかし、この場合でも、ネットワーク セキュリティ グループ規則をスポーク リソースに適用し、Azure Firewall の保護を使用するか、カスタムのルート テーブルを作成して、プライベート トラフィックを禁止または許可することができます。

- [カスタム ルーティングのシナリオ](scenario-any-to-any.md)

**Azure Security Center の監視** : 現在は使用できません

**責任** : Customer

### <a name="ns-2-connect-private-networks-together"></a>NS-2: プライベート ネットワークをまとめて接続する 

**ガイダンス** :Azure ExpressRoute または Azure 仮想プライベート ネットワーク (VPN) を使用して、コロケーション環境内で Azure のデータセンターとオンプレミスのインフラストラクチャ間のプライベート接続を作成できます。 ExpressRoute 接続はパブリック インターネットを経由しないので、一般的なインターネット接続と比べて信頼性が高く、高速で、待ち時間も短くなります。 

ポイント対サイト VPN とサイト間 VPN では、これらの VPN オプションと ExpressRoute の任意の組み合わせを使用して、オンプレミスのデバイスやネットワークを仮想ネットワークに接続します。 Azure で 2 つ以上の仮想ネットワークを接続するには、Virtual Network ピアリングを使用します。 ピアリングされた仮想ネットワーク間のネットワーク トラフィックはプライベートであり、Azure のバックボーン ネットワーク上に保持されます。

- [Virtual WAN 内の ExpressRoute](virtual-wan-expressroute-portal.md)

- [サイト間 VPN の概要](virtual-wan-site-to-site-portal.md)

- [ポイント対サイト VPN の概要](virtual-wan-point-to-site-portal.md)

- [Private Link](howto-private-link.md)

**Azure Security Center の監視** : 現在は使用できません

**責任** : 共有

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Azure サービスへのプライベート ネットワーク アクセスを確立する

**ガイダンス** :Azure Private Link を使用して、インターネットを経由せずに、仮想ネットワークから Azure Virtual WAN へのプライベート アクセスを有効にします。 プライベート アクセスは、Azure サービスによって提供される認証およびトラフィックのセキュリティに加えて、もう 1 つの多層防御手段となります。

- [Azure Private Link について](../private-link/private-link-overview.md)

- [Virtual Wan プライベート リンク](howto-private-link.md)

**Azure Security Center の監視** : 現在は使用できません

**責任** : Customer

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: 外部ネットワーク攻撃からアプリケーションやサービスを保護する

**ガイダンス** :分散型サービス拒否 (DDoS) 攻撃、アプリケーション固有の攻撃、未承諾で悪意の可能性があるインターネット トラフィックなど、外部ネットワークからの攻撃に対して Azure Virtual WAN リソースを保護します。 

Azure Firewall を使用して、インターネットや他の外部の場所からの、悪意がある可能性のあるトラフィックから、アプリケーションやサービスを保護します。 Azure 仮想ネットワークへの攻撃に対するアセット用の Azure DDoS Protection を選択します。 Azure Security Center を使用して、ネットワーク関連のリスクに関する構成ミスのリスクを検出します。

- [Azure Firewall のドキュメント](/azure/firewall)

- [Azure portal を使用した Azure DDoS Protection Standard の管理](/azure/virtual-network/manage-ddos-protection) 

- [Azure Security Center の推奨事項](../security-center/recommendations-reference.md#recs-network)

**Azure Security Center の監視** : はい

**責任** : 共有

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: 侵入検出および侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス** :Azure Virtual WAN で侵入検出または防止システムをデプロイする最善の方法は、仮想ハブに接続されているスポーク ネットワークでネットワーク仮想アプライアンスを使用することです。  詳細については、参照リンクにあるルーティングのシナリオを参照してください。 

- [シナリオ - NVA を経由するトラフィックのルーティング](scenario-route-through-nva.md)

**Azure Security Center の監視** : 現在は使用できません

**責任** : Customer

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: ネットワーク セキュリティ規則を簡略化する

**ガイダンス** :Virtual WAN リソース用に構成された Azure ネットワーク セキュリティ グループまたは Azure Firewall でのネットワーク アクセス制御を定義するには、Azure Virtual Network サービス タグを使用します。 

セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたは宛先フィールドでサービス タグ名 (例: {VirtualWAN:Virtual Network}) を指定すると、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

**Azure Security Center の監視** : 現在は使用できません

**責任** : Customer

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: セキュリティで保護されたドメイン ネーム サービス (DNS)

**ガイダンス** :Azure Virtual WAN には、ポイント対サイト VPN ゲートウェイ用のカスタム DNS サーバーが用意されています。 

DNS セキュリティのベスト プラクティスに従って、未解決の DNS、DNS アンプ攻撃、DNS のポイズニングとスプーフィングなどの一般的な攻撃を軽減します。

権限のある DNS サービスとして Azure DNS が使用されている場合は、Azure ロールベースのアクセス制御 (Azure RBAC) とリソース ロックを使用して、DNS ゾーンとレコードが偶発的または悪意のある変更から確実に保護されるようにします。

- [Azure DNS の概要](../dns/dns-overview.md) 

- [セキュリティで保護されたドメイン ネーム システム (DNS) デプロイ ガイド](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) 

- [未解決の DNS エントリを防ぎ、サブドメインの乗っ取りを回避する](../security/fundamentals/subdomain-takeover.md)

**Azure Security Center の監視** : 現在は使用できません

**責任** : Customer

## <a name="identity-management"></a>ID 管理

*詳細については、[Azure セキュリティ ベンチマークの「ID 管理](/azure/security/benchmarks/security-controls-v2-identity-management).* 」を参照してください。

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1:Azure Active Directory を中央 ID および認証システムとして標準化する

**ガイダンス** :Azure Virtual WAN のポイント対サイト VPN は、Azure Active Directory (Azure AD) に統合されます。これは、Azure の既定の ID とアクセス管理サービスです。 Azure AD を標準化して、次のリソースでの組織の ID とアクセス管理を統制する必要があります。

- Azure portal、Azure Storage、Azure Virtual Machines (Linux と Windows)、Azure Key Vault、PaaS、SaaS アプリケーションなどの Microsoft Cloud リソース。
- Azure 上のアプリケーションや企業ネットワーク リソースなどの組織のリソース。

組織のクラウド セキュリティ プラクティスの中で高い優先順位で Azure AD をセキュリティ保護します。 Azure Security Center のセキュリティ スコア機能を使用して ID とセキュリティ体制を評価し、構成がベスト プラクティスの推奨事項とどの程度一致しているかを判定します。 セキュリティ体制を改善するための Microsoft のベスト プラクティスの推奨事項を実装します。

さらに、Azure AD では外部 ID もサポートされます。これにより、Microsoft アカウントを持たないユーザーが、外部 ID を使用してアプリケーションやリソースにサインインできるようになります。 参照リンクで、ポイント対サイト VPN シナリオでの Azure AD の使用に関する情報を確認してください。

- [P2S OpenVPN プロトコル接続用の Azure Active Directory (AD) テナントを作成する](openvpn-azure-ad-tenant-multi-app.md)

- [ユーザー VPN 用に Azure Active Directory 認証を構成する](virtual-wan-point-to-site-azure-ad.md)

- [Azure Active Directory のテナント](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Azure AD インスタンスを作成して構成する方法](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [アプリケーションに外部 ID プロバイダーを使用する](/azure/active-directory/b2b/identity-providers)

**Azure Security Center の監視** : はい

**責任** : 共有

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4:すべての Azure Active Directory ベースのアクセスに強力な認証制御を使用する

**ガイダンス** :Azure Virtual WAN のポイント対サイト VPN は、Azure Active Directory (Azure AD) に統合されます。これは、多要素認証による強力な認証コントロールと強力なパスワードレス手法をサポートします。

- 多要素認証 - Azure AD の多要素認証を有効にし、多要素認証の設定のベスト プラクティスに関する Azure Security Center の ID とアクセス管理の推奨事項に従います。 多要素認証は、すべての選択されたユーザーに対して、またはサインインの条件とリスク要因に基づいてユーザー単位のレベルで適用できます。

- パスワードレス認証 - 3 つのパスワードレス認証オプションを使用できます。 これらには、Windows Hello for Business、Microsoft Authenticator アプリ、スマート カードなどのオンプレミスの認証方法が含まれます。

管理者と特権ユーザーについては、確実に最高レベルの強力な認証方法を使用し、その後、適切で強力な認証ポリシーを他のユーザーにロールアウトします。

- [Virtual WAN の P2S VPN で MFA を有効にする方法](openvpn-azure-ad-mfa.md)

**Azure Security Center の監視** : はい

**責任** : 共有

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6:条件に基づいて Azure リソースへのアクセスを制限する

**ガイダンス** :Azure Virtual WAN のポイント対サイト VPN では、ユーザー定義の条件に基づいてよりきめ細かなアクセス制御を行うために Azure AD 条件付きアクセスがサポートされます。 たとえば、特定の IP 範囲からのユーザー ログインでは、ログインに多要素認証を使用する必要があります。 さまざまなユース ケースに対してきめ細かな認証セッション管理ポリシーを使用することもできます。

- [一般的な条件付きアクセス ポリシー](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [条件付きアクセスを使用して認証セッション管理を構成する](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

- [Virtual Wan の P2S VPN 条件付きアクセス](openvpn-azure-ad-mfa.md#conditional)

**Azure Security Center の監視** : 現在は使用できません

**責任** : Customer

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7:意図しない資格情報の公開を排除する

**ガイダンス** : コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

GitHub の場合、ネイティブ シークレット スキャン機能を使用して、コード内で資格情報やその他の形式のシークレットを識別できます。

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [GitHub シークレット スキャン](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Azure Security Center の監視** : 現在は使用できません

**責任** : Customer

## <a name="privileged-access"></a>特権アクセス

*詳細については、[Azure セキュリティ ベンチマークの「特権アクセス](/azure/security/benchmarks/security-controls-v2-privileged-access)」を参照してください。*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2:ビジネス クリティカルなシステムへの管理アクセスを制限する

**ガイダンス** :Azure Virtual WAN では、Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、アカウントが属するサブスクリプションと管理グループへの特権アクセスを許可するアカウントを制限することにより、ビジネス クリティカルなシステムへのアクセスを分離します。

また、ビジネス クリティカルなシステムにインストールされたエージェントを使用して、Active Directory ドメイン コントローラー、セキュリティ ツール、システム管理ツールなど、ビジネス クリティカルなアクセスへの管理アクセス権を持つ管理、ID、およびセキュリティ システムへのアクセスも制限します。 これらの管理システムおよびセキュリティ システムを侵害した攻撃者は、それらをすぐに悪用してビジネス クリティカルな資産を侵害することができます。

一貫したアクセス制御を確保するため、自分の企業のセグメント化戦略にすべての種類のアクセス制御を合わせる必要があります。

- [Azure のコンポーネントと参照モデル](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [管理グループ アクセス](../governance/management-groups/overview.md#management-group-access) 

- [Azure サブスクリプション管理者](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center の監視** : 現在は使用できません

**責任** : Customer

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6:特権アクセス ワークステーションを使用する

**ガイダンス** :セキュリティで保護された分離したワークステーションは、管理者、開発者、重要なサービス オペレーターのような機密性の高い役割のセキュリティには非常に重要です。 高度にセキュリティ保護されたユーザー ワークステーションまたは Azure Bastion を管理タスクに使用します。 Azure Active Directory (Azure AD)、Microsoft Defender Advanced Threat Protection (ATP)、または Microsoft Intune を使用して、管理タスクのためにセキュリティで保護されたマネージド ユーザー ワークステーションを展開します。 セキュリティで保護されたワークステーションを一元管理して、強力な認証、ソフトウェアとハードウェアのベースライン、制限された論理アクセスとネットワーク アクセスなどのセキュリティで保護された構成を実施できます。

- [特権アクセス ワークステーションを理解する](../active-directory/devices/concept-azure-managed-workstation.md)

- [特権アクセス ワークステーションを展開する](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center の監視** : 現在は使用できません

**責任** : Customer

## <a name="data-protection"></a>データ保護

*詳細については、 [Azure セキュリティ ベンチマークの「データ保護](/azure/security/benchmarks/security-controls-v2-data-protection)」を参照してください。*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4:転送中の機密情報を暗号化する

**ガイダンス** :外部およびパブリック ネットワーク上のトラフィックにとって、暗号化は重要です。

- アクセス制御を使用し、攻撃者に簡単に読み取られたり変更されたりしないように、暗号化を使用して "帯域外" 攻撃 (トラフィックのキャプチャなど) から転送中のデータを保護する必要があります。

- HTTP トラフィックの場合、Azure リソースに接続するすべてのクライアントのネゴシエートには、必ず TLS v1.2 以上を使用します。

- リモート管理には、暗号化されていないプロトコルではなく、SSH (Linux の場合) または RDP/TLS (Windows の場合) を使用します。 SSL、TLS、または SSH の古いバージョンとプロトコル、および弱い暗号は無効にする必要があります。

- Azure では、基盤となるインフラストラクチャで、Azure データセンター間のデータ トラフィックに対して、既定により転送中のデータの暗号化が提供されます。

一般に、セキュリティ保護された Microsoft バックボーンで暗号化を提供し、サイト間 VPN、Azure ExpressRoute を介したサイト間 VPN、ポイント対サイト ユーザー VPN でトラフィックを暗号化する機会を提供します。

- [Azure での転送中の暗号化の概要](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [TLS セキュリティに関する情報](/security/engineering/solving-tls1-problem)

- [転送中の Azure データの二重暗号化](../security/fundamentals/double-encryption.md#data-in-transit)

**Azure Security Center の監視** : はい

**責任** : 共有

## <a name="asset-management"></a>アセット管理

*詳細については、[Azure セキュリティ ベンチマークの「アセット管理](/azure/security/benchmarks/security-controls-v2-asset-management)」を参照してください。*

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2:セキュリティ チームが資産インベントリとメタデータに確実にアクセスできるようにする

**ガイダンス** :Azure リソース、リソース グループ、サブスクリプションにタグを適用して、それらを論理的に分類できます。 各タグは、名前と値のペアで構成されます。 たとえば、運用環境のすべてのリソースには名前 "環境" と値 "運用" を適用できます。

また、Azure Virtual WAN によって、Azure Resource Manager ベースのリソースのデプロイと Azure Resource Graph のクエリもサポートされます。 

- 資産のタグ付けの詳細については、「[リソースの名前付けとタグ付けの意思決定ガイド](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)」を参照

- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center の資産インベントリ管理](../security-center/asset-inventory.md)

**Azure Security Center の監視** : はい

**責任** : Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3:承認された Azure サービスのみを使用する

**ガイダンス** :Azure Policy を使用して、ご利用の環境でプロビジョニングできるサービスを制限します。 Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。また、Azure Monitor を使用して、承認されていないサービスが検出されたときにアラートをトリガーするルールを作成します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](../governance/policy/samples/built-in-policies.md#general)

- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**Azure Security Center の監視** : 現在は使用できません

**責任** : Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス** :"Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure 条件付きアクセスを使用します。

- [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center の監視** : 現在は使用できません

**責任** : Customer

## <a name="logging-and-threat-detection"></a>ログと脅威検出

*詳細については、[Azure セキュリティ ベンチマークの「ログと脅威検出](/azure/security/benchmarks/security-controls-v2-logging-threat-protection)」を参照してください。*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2:Azure ID とアクセスの管理のために脅威検出を有効にする

**ガイダンス** :Azure Active Directory (Azure AD) では、次のユーザー ログが記録され、これは Azure AD レポートで確認できます。また、より高度な監視と分析のユース ケースの場合は、Azure Monitor、Azure Sentinel、SIEM または監視ツールと統合できます。 次のとおりです。

- サインイン - サインイン レポートは、マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報を提供します。
- 監査ログ - Azure AD 内のさまざまな機能によって行われたすべての変更についてログによる追跡可能性を提供します。 監査ログの例として、ユーザー、アプリ、グループ、ロール、ポリシーの追加や削除など、Azure AD 内のあらゆるリソースに加えられた変更があります。
- リスクの高いサインイン - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によってサインインが試行された可能性があることを示す指標です。
- リスクのフラグ付きユーザー - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。

Azure Security Center を使用して、認証試行の失敗 (サブスクリプションでの非推奨のアカウントなど) 回数が多すぎるなど、特定の不審なアクティビティに対してアラートを生成することができます。 Security Center の脅威保護モジュールを使用すると、基本的なセキュリティ検疫監視に加えて、個々の Azure コンピューティング リソース (仮想マシン、コンテナー、アプリ サービスなど)、データ リソース (SQL DB、ストレージなど)、Azure サービス レイヤーから、さらに詳細なセキュリティ アラートを収集することもできます。 この機能を使用することにより、個々のリソース内でアカウントの異常を確認できます。

- [Azure Active Directory の監査アクティビティ レポート](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure Identity Protection を有効にする](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center の監視** : はい

**責任** : 共有

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3:Azure ネットワーク アクティビティのログ記録を有効にする

**ガイダンス** :VPN パケット キャプチャ ツールを使用して、Azure Virtual WAN リソース間を移動するネットワーク パケットを記録します。 これは、ハイブリッド ネットワークに関連するデバッグ プロセスに役立ちます。 ネットワーク セキュリティ グループを Virtual WAN にデプロイすることはできませんが、スポーク仮想ネットワーク リソースにデプロイすることはできます。

トラフィックの監査のために、ネットワーク セキュリティ グループでネットワーク セキュリティ グループのフロー ログを有効にします。

Azure Traffic Analytics 機能を有効にして、ストレージ アカウントに保持されているフロー ログを処理し、それを Log Analytics ワークスペースに送信します。 Traffic Analytics により、Azure ネットワークのトラフィック フローに関する追加の分析情報が提供されます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

- [ネットワーク セキュリティ グループのフローのログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md) 

Virtual Wan では、有効にする必要がある DNS クエリ ログの生成または処理を行いません。

**Azure Security Center の監視** : 現在は使用できません

**責任** : Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4:Azure リソースのログ記録を有効にする

**ガイダンス** :自動的に有効になる Azure アクティビティ ログには、読み取り (GET) 操作を除いて、Azure Virtual WAN リソースに対するすべての書き込み操作 (PUT、POST、DELETE) が含まれます。 アクティビティ ログを使用すると、トラブルシューティング時にエラーを見つけたり、組織内のユーザーがリソースをどのように変更したかを監視したりすることができます。 ただし、Virtual WAN では、Azure リソース ログは生成されません。

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/platform/diagnostic-settings.md) 
- [Azure でのログ記録とログのさまざまな種類について](../azure-monitor/platform/platform-logs-overview.md)

**Azure Security Center の監視** : はい

**責任** : 共有

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5:セキュリティ ログの管理と分析を一元化する

**ガイダンス** :ログ記録のストレージと分析を一元化して、相関関係を有効にします。 ログ ソースごとに、データ所有者、アクセス ガイダンス、ストレージの場所、データの処理とアクセスに使用するツール、データ保持の要件を割り当てたことを確認します。 Azure アクティビティ ログを一元的なログ記録システムに統合します。 

Azure Monitor を介してログを取り込み、エンドポイント デバイス、ネットワーク リソース、およびその他のセキュリティ システムによって生成されたセキュリティ データを集計します。 Azure Monitor で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行します。長期のアーカイブ ストレージには Azure Storage アカウントを使用します。 さらに、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードします。

多くの組織では、頻繁に使用される "ホット" データに対しては Azure Sentinel を、使用頻度の低い "コールド" データに対しては Azure Storage を使用することを選択しています。

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/platform/diagnostic-settings.md) 

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視** : 現在は使用できません

**責任** : 共有

## <a name="incident-response"></a>インシデント対応

*詳細については、 [Azure セキュリティ ベンチマークの「インシデント対応](/azure/security/benchmarks/security-controls-v2-incident-response)」を参照してください。*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: 準備 – インシデント対応プロセスを Azure 用に更新する

**ガイダンス** :組織において、セキュリティ インシデントに対応するためのプロセスが用意されていること、Azure のこれらのプロセスが更新されていること、それらのプロセスを定期的に使用して準備されていることを確認します。 可能な場合は、サービス内容をインシデント対応プロセスに含めるようにします。

- [企業環境全体にセキュリティを実装する](https://aka.ms/AzSec4) 
- [インシデント対応リファレンス ガイド](https://aka.ms/IRRG)

**Azure Security Center の監視** : 現在は使用できません

**責任** : Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: 準備 – インシデント通知をセットアップする

**ガイダンス** :Azure Security Center でセキュリティ インシデントの連絡先情報を設定します。 この連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 また、インシデント対応のニーズに応じて、異なる Azure サービスでインシデント アラートと通知をカスタマイズするオプションもあります。

セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が完全解決されるようにします。

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md) 

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center の監視** : はい

**責任** : Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: 検出と分析 – 高品質のアラートに基づいてインシデントを作成する

**ガイダンス** : Azure Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

さらに、サブスクリプション (運用、非運用など) を明確にマークし、Azure リソースを明確に識別および分類するための命名システムを作成します。

- [Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視** : はい

**責任** : Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: 検出と分析 – インシデントを調査する

**ガイダンス** : 組織のインシデント対応ガイドを作成します。 定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。

- [Azure Security Center 内でワークフロー自動化を構成する方法](../security-center/security-center-planning-and-operations-guide.md) 

- [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process) 

- [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process) 

- [NIST の出版物「IT 計画と機能に関するテスト、トレーニング、演習プログラムのガイド」を参照してください。](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center の監視** : 現在は使用できません

**責任** : Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: 検出と分析 – インシデントの優先順位を付ける

**ガイダンス** : Azure Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

さらに、サブスクリプション (運用、非運用など) を明確にマークし、Azure リソースを明確に識別および分類するための命名システムを作成します。

- [Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視** : はい

**責任** : 共有

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: 包含、根絶、復旧 – インシデントの処理を自動化する

**ガイダンス** :Azure Security Center のワークフローの自動化機能を使用して、セキュリティ アラートおよび推奨事項に対して "Azure Logic Apps" 経由で応答を自動的にトリガーします。

- [ワークフローの自動化と Logic Apps を構成する方法](../security-center/workflow-automation.md)

**Azure Security Center の監視** : はい

**責任** : Customer

## <a name="posture-and-vulnerability-management"></a>体制と脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「体制と脆弱性の管理](/azure/security/benchmarks/security-controls-v2-vulnerability-management)」を参照してください。*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: 定期的に攻撃シミュレーションを実施する

**ガイダンス** :要件に応じて、Azure リソースの侵入テストまたはレッド チーム アクティビティを実施し、セキュリティに関するすべての重大な調査結果が確実に修復されるようにします。

お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft クラウド侵入テストの実施ルールに従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対して、Microsoft の戦略を使用し、Red Teaming およびライブ サイト侵入テストを実施します。

- [Azure での侵入テスト](../security/fundamentals/pen-testing.md)

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視** : 適用なし

**責任** : Customer

## <a name="endpoint-security"></a>エンドポイント セキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「エンドポイントのセキュリティ](/azure/security/benchmarks/security-controls-v2-endpoint-security)」を参照してください。*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1:エンドポイントでの検出と対応 (EDR) を使用する

**ガイダンス** :顧客に対して、[エンドポイントの検出と応答] 設定の構成を明示的に許可しません。 しかし、Azure Virtual WAN オファリングで使用される Virtual Machines では、これらの機能を使用します。 これらの一般的な機能の詳細については、参照リンクを参照してください。 

- [Microsoft Defender Advanced Threat Protection の概要](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Windows サーバー向けの Microsoft Defender ATP サービス](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Windows 以外のサーバー向けの Microsoft Defender ATP サービス](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Azure Security Center の監視** : はい

**責任** : 共有

## <a name="governance-and-strategy"></a>ガバナンスと戦略

*詳細については、[Azure セキュリティ ベンチマークの「ガバナンスと戦略](/azure/security/benchmarks/security-controls-v2-governance-strategy)」を参照してください。*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: 資産の管理とデータ保護の戦略を定義する 

**ガイダンス** :システムとデータを継続的に監視および保護するための明確な戦略が文書化および伝達されるようにします。 ビジネスに不可欠なデータとシステムの検出、評価、保護、監視の優先順位を決定します。 

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

- ビジネス リスクに応じたデータ分類標準
- リスクと資産のインベントリに対するセキュリティ組織の可視性 
- Azure サービスを使用するためのセキュリティ組織の承認 
- ライフサイクルを通じた資産のセキュリティ
- 組織のデータ分類に従った必要なアクセス制御戦略
- Azure ネイティブおよびサードパーティのデータ保護機能の使用
- 転送中および保存中のユース ケースのデータ暗号化要件
- 適切な暗号化標準

追加情報については、参照リンクを参照してください。

- [Azure セキュリティ アーキテクチャに関する推奨事項 - ストレージ、データ、暗号化](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Azure のセキュリティの基礎 - Azure のデータ セキュリティ、暗号化、ストレージ](../security/fundamentals/encryption-overview.md)

- [クラウド導入フレームワーク - Azure のデータ セキュリティと暗号化のベスト プラクティス](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

**Azure Security Center の監視** : 現在は使用できません

**責任** : Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: 企業のセグメント化戦略を定義する 

**ガイダンス** :ID、ネットワーク、アプリケーション、サブスクリプション、管理グループ、その他のコントロールを利用し、アセットへのアクセスをセグメント化する企業規模の戦略を確立します。 セキュリティ分離の必要性と、互いと通信し、データにアクセスする必要があるシステムの日常的操作を有効にするという必要性のバランスを慎重に取ります。

セグメント化戦略が、ネットワーク セキュリティ、ID とアクセス モデル、アプリケーションのアクセス許可とアクセス モデル、人的プロセスの制御など、あらゆる種類のコントロールで一貫して確実に実装されるようにします。

- [Azure のセグメント化戦略に関するガイド (動画)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure のセグメント化戦略に関するガイド (ドキュメント)](/security/compass/governance#enterprise-segmentation-strategy)

- [ネットワークのセグメント化を企業のセグメント化戦略に合わせる](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center の監視** : 現在は使用できません

**責任** : Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: セキュリティ態勢管理の戦略を定義する

**ガイダンス** :個々の資産とそれらがホストされている環境に対するリスクを継続的に測定し、軽減します。 高い価値を持つ資産と、攻撃に晒される可能性の高い対象領域 (公開されたアプリケーション、ネットワークのイングレス ポイントとエグレス ポイント、ユーザーと管理者のエンドポイントなど) を優先します。

- [Azure セキュリティ ベンチマーク - 体制と脆弱性の管理](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Azure Security Center の監視** : 現在は使用できません

**責任** : Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: 組織の役割、責任、責務を整合させる

**ガイダンス** :セキュリティ組織における役割と責任に関する明確な戦略が文書化されて伝えられるようにします。 セキュリティに関する決定についてわかりやすく説明すること、共有される責任モデルについて全員を教育すること、クラウドをセキュリティで保護するテクノロジについて技術チームを教育することに基づいて作業に優先順位を付けます。

- [Azure のセキュリティのベスト プラクティス 1 – 人: クラウド セキュリティに関する取り組みについてチームを教育する](https://aka.ms/AzSec1)

- [Azure のセキュリティのベスト プラクティス 2 - 人: クラウド セキュリティ テクノロジについてチームを教育する](https://aka.ms/AzSec2)

- [Azure のセキュリティのベスト プラクティス 3 - プロセス: クラウドのセキュリティに関する意思決定のアカウンタビリティを割り当てる](https://aka.ms/AzSec3)

**Azure Security Center の監視** : 現在は使用できません

**責任** : Customer

### <a name="gs-5-define-network-security-strategy"></a>GS-5: ネットワーク セキュリティ戦略を定義する

**ガイダンス** :組織全体のセキュリティ アクセス制御戦略の一環として、Azure ネットワーク セキュリティ アプローチを確立します。 この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

- 一元的なネットワーク管理とセキュリティ責任
- エンタープライズ セグメント化戦略と一致する仮想ネットワーク セグメント化モデル
- さまざまな脅威と攻撃のシナリオにおける修復戦略
- インターネット エッジとイングレスおよびエグレス戦略
- クラウドとオンプレミスのハイブリッド相互依存戦略
- 最新のネットワーク セキュリティ成果物 (例: ネットワーク図、参照ネットワーク アーキテクチャ)

追加情報については、参照リンクを参照してください。

- [Azure のセキュリティのベスト プラクティス 11 - アーキテクチャ。単一の統合セキュリティ戦略](https://aka.ms/AzSec11)

- [Azure セキュリティ ベンチマーク - ネットワーク セキュリティ](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Azure のネットワーク セキュリティの概要](../security/fundamentals/network-overview.md)

- [エンタープライズ ネットワーク アーキテクチャ戦略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center の監視** : 現在は使用できません

**責任** : Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: ID と特権アクセス戦略を定義する

**ガイダンス** :組織全体のセキュリティ アクセス制御戦略の一環として、Azure の ID と特権アクセス アプローチを確立します。 この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

- 一元化された ID と認証システム、および他の内部および外部 ID システムとのその相互接続
- さまざまなユース ケースおよび条件における強力な認証方法
- 高い特権を持つユーザーの保護
- 異常なユーザー アクティビティの監視と処理  
- ユーザー ID とアクセスの確認と調整のプロセス

追加情報については、参照リンクを参照してください。

- [Azure セキュリティ ベンチマーク - ID 管理](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure セキュリティ ベンチマーク - 特権アクセス](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure のセキュリティのベスト プラクティス 11 - アーキテクチャ。単一の統合セキュリティ戦略](https://aka.ms/AzSec11)

- [Azure ID 管理のセキュリティの概要](../security/fundamentals/identity-management-overview.md)

**Azure Security Center の監視** : 現在は使用できません

**責任** : Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: ログ記録と脅威対応戦略を定義する

**ガイダンス** :コンプライアンス要件を満たしながら脅威を迅速に検出して修復するための、ログ記録と脅威対応戦略を確立します。 統合と手動手順ではなく脅威に集中できるように、高品質のアラートとシームレスなエクスペリエンスをアナリストに提供することを優先します。 

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

- セキュリティ運用 (SecOps) 組織の役割と責任
- NIST または他の業界フレームワークと一致する、明確に定義されたインシデント対応プロセス
- 脅威の検出、インシデント対応、コンプライアンスのニーズに対応するためのログのキャプチャと保持
- SIEM、Azure のネイティブ機能、その他のソース (重要な顧客、サプライヤー、公的機関とのコミュニケーションおよび通知計画) を使用した、脅威に関する情報の一元的な可視化と関連付け
- ログ記録と脅威の検出、フォレンジクス、攻撃の修復と根絶など、インシデント処理に対する Azure ネイティブおよびサードパーティのプラットフォームの使用
- インシデントとインシデント発生後のアクティビティを処理するためのプロセス (教訓や証拠の保持など)

追加情報については、参照リンクを参照してください。
- [Azure セキュリティ ベンチマーク - ログと脅威検出](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure セキュリティ ベンチマーク - インシデント対応](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure のセキュリティのベスト プラクティス 4 - プロセス: クラウドのインシデント対応プロセスを更新する](https://aka.ms/AzSec4)

- [Azure 導入フレームワーク、ログ、およびレポートの決定ガイド](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

**Azure Security Center の監視** : 現在は使用できません

**責任** : Customer

## <a name="next-steps"></a>次のステップ

- 「[Azure セキュリティ ベンチマーク V2 の概要](/azure/security/benchmarks/overview)」を参照してください。
- [Azure セキュリティ ベースライン](/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
