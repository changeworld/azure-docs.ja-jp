---
title: Azure のエンドツーエンド セキュリティ | Microsoft Docs
description: この記事では、クラウド リソースの安全性確保と保護、および脅威の検出と調査に役立つ Azure サービスのマップを示します。
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/07/2021
ms.author: terrylan
ms.openlocfilehash: 3ea3c2bcb878dbd8a712e6076dda09853f55e297
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310344"
---
# <a name="end-to-end-security-in-azure"></a>Azure のエンドツーエンド セキュリティ
アプリケーションとサービスに Azure を使用する最大の理由の 1 つは、さまざまなセキュリティ ツールや機能を活用できることです。 これらのツールや機能により、Azure プラットフォーム上にセキュリティで保護されたソリューションを作成できるようになります。 Microsoft Azure では、透過的な説明責任を実現しつつ、顧客データの機密性、整合性、および可用性を提供しています。

次の図とドキュメントでは、Azure のセキュリティ サービスについて説明します。 これらのセキュリティ サービスは、ビジネスのセキュリティ ニーズを満たし、クラウド内のユーザー、デバイス、リソース、データ、アプリケーションを保護するのに役立ちます。

## <a name="microsoft-security-services-map"></a>Microsoft セキュリティ サービス マップ

セキュリティ サービス マップによって、保護するリソース (列) ごとにサービスが整理されます。 また、この図では、サービスを次のカテゴリ (行) にグループ化しています。

- 安全性の確保と保護 - ID、ホスト、ネットワーク、データにまたがって、徹底的な多層防御戦略を実装できるようにするサービスです。 このセキュリティ サービスと機能のコレクションにより、Azure 環境全体のセキュリティ体制を解釈し、向上させる方法が提供されます。
- 脅威の検出 – 疑わしいアクティビティを特定し、脅威の軽減を促進するサービスです。
- 調査と対応 – ログ データを取得するサービスです。これにより、疑わしいアクティビティを評価して対応することができます。

図には、Azure セキュリティ ベンチマーク プログラムが含まれます。これは、Azure で使用するサービスをセキュリティで保護するために使用できる、影響力の高いセキュリティに関する推奨事項のコレクションです。

:::image type="content" source="media/end-to-end/security-diagram.svg" alt-text="Azure のエンドツーエンド セキュリティ サービスを示す図。" border="false":::

## <a name="security-controls-and-baselines"></a>セキュリティ制御とベースライン
[Azure セキュリティ ベンチマーク](../benchmarks/introduction.md) プログラムには、Azure で使用するサービスをセキュリティで保護するために使用できる、影響力の高いセキュリティに関する推奨事項のコレクションが含まれています。

- セキュリティ制御 - これらの推奨事項は、一般に Azure テナントと Azure サービス全体に適用されます。 各推奨事項では、通常、ベンチマークの計画、承認、または実装に関与している利害関係者の一覧を特定します。
- サービス ベースライン - これらによって、個々の Azure サービスにコントロールが適用され、そのサービスのセキュリティ構成に関する推奨事項が提供されます。

## <a name="secure-and-protect"></a>安全性確保と保護

:::image type="content" source="media/end-to-end/secure-and-protect.svg" alt-text="クラウド リソースの安全性確保と保護を支援する Azure サービスを示す図。" border="false":::

| サービス | 説明 |
|------|--------|
| [Azure Security Center](../../security-center/security-center-introduction.md)| データ センターのセキュリティ体制を強化する統合インフラストラクチャ セキュリティ管理システムです。Azure 内かどうかにかかわらずクラウド内とオンプレミスのハイブリッド ワークロード全体を保護する高度な脅威防止機能があります。 |
| **ID&nbsp; & &nbsp;アクセス&nbsp;管理** | |
| [Azure Active Directory (AD)](../../active-directory/fundamentals/active-directory-whatis.md)| Microsoft のクラウドベースの ID とアクセスの管理サービスです。  |
|  | [条件付きアクセス](../../active-directory/conditional-access/overview.md)は、Azure AD で使用されるツールです。このツールによって、ID シグナルをまとめて、決定を行い、組織のポリシーを適用することができます。 |
|  | [Domain Services](../../active-directory-domain-services/overview.md) は、マネージド ドメイン サービス (ドメイン参加、グループ ポリシー、ライトウェイト ディレクトリ アクセス プロトコル (LDAP)、Kerberos または NTLM 認証など) を提供するために、Azure AD で使用されるツールです。 |
|  | [Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) は Azure AD のサービスで、これにより、お客様の組織内の重要なリソースへのアクセスを管理、制御、監視できるようになります。 |
|  | [Multi-factor authentication](../../active-directory/authentication/concept-mfa-howitworks.md) は、もう 1 つの形式の認証を要求することによって、データとアプリケーションへのアクセスを保護できるようにするために、Azure AD によって使用されるツールです。 |
| [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) | 組織は、このツールを使用して、ID ベースのリスクの検出と修復の自動化、ポータルでのデータを使用したリスク調査、さらなる分析のためのサードパーティ製ユーティリティへのリスク検出データのエクスポートを実行できます。 |
| **インフラストラクチャ &nbsp;&&nbsp; ネットワーク** |  |
| [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) | Azure 仮想ネットワークとオンプレミスの場所との間で暗号化されたトラフィックをパブリック インターネット経由で送信したり、Azure 仮想ネットワーク間で暗号化されたトラフィックを Microsoft ネットワーク経由で送信したりするために使用される仮想ネットワーク ゲートウェイです。 |
| [Azure DDoS Protection Standard](../../ddos-protection/ddos-protection-overview.md) | DDoS 攻撃から保護するための強化された DDoS 軽減機能が提供されます。 この機能は、仮想ネットワーク内にあるお客様固有の Azure リソースを保護するために、自動的に調整されます。 |
| [Azure Front Door](../../frontdoor/front-door-overview.md) | Microsoft グローバル エッジ ネットワークを使用してセキュリティで保護された高速でスケーラビリティの高い Web アプリを作成するための、スケーラブルなグローバル エントリポイントです。 |
| [Azure Firewall](../../firewall/overview.md) | Azure Virtual Network リソースを保護するクラウドベースのマネージド ネットワーク セキュリティ サービスです。 組み込みの高可用性とクラウドの無制限のスケーラビリティを備えた、完全にステートフルなサービスとしてのファイアウォールです。 |
| [Azure Key Vault](../../key-vault/general/overview.md) | トークン、パスワード、証明書、API キー、およびその他のシークレットのセキュリティで保護されたシークレット ストアです。 Key Vault を、データの暗号化に使用される暗号化キーの作成と制御に使用することもできます。 |
| [Key Vault Managed HSM (プレビュー)](../../key-vault/managed-hsm/overview.md) | 標準準拠のフル マネージド、高可用性、シングル テナント クラウド サービスです。これにより、FIPS 140-2 レベル 3 適合の HSM を使用してクラウド アプリケーションの暗号化キーを保護することができます。 |
| [Azure Private Link](../../private-link/private-link-overview.md) | お使いの仮想ネットワーク内のプライベート エンドポイント経由で Azure PaaS サービス (Azure Storage、SQL Database など) と Azure でホストされている顧客所有の、またはパートナーのサービスにアクセスできるようになります。 |
| [Azure Application Gateway](../../application-gateway/overview.md) | Web トラフィックの高度なロード バランサーです。これにより、Web アプリケーションへのトラフィックを管理できるようになります。 Application Gateway では、URI パスやホスト ヘッダーなど、HTTP 要求の追加属性に基づいてルーティングを決定できます。 |
| [Azure Service Bus](../../service-bus-messaging/service-bus-messaging-overview.md) | メッセージ キューと、パブリッシュとサブスクライブのトピックが備わったフル マネージド エンタープライズ メッセージ ブローカーです。 Service Bus は、アプリケーションとサービスを相互に分離するために使用されます。 |
| [Web アプリケーション ファイアウォール](../../web-application-firewall/overview.md) | 一般的な悪用や脆弱性からの Web アプリケーションの一元的な保護が提供されます。 WAF は Azure Application Gateway と Azure Front Door を使用してデプロイできます。 |
| **データとアプリケーション** |  |
| [Azure Backup](../../backup/backup-overview.md) | データをバックアップし、それを Microsoft Azure クラウドから回復するための、シンプルで安全かつコスト効率の高いソリューションが提供されます。 |
| [Azure Storage Service Encryption](../../storage/common/storage-service-encryption.md) | データが保存される前にデータを自動で暗号化したり、データを取得するときに自動で暗号化を解除したりすることができるようになります。 |
| [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) | クラウドベースのソリューションです。これにより、組織は内容にラベルを適用することによって、ドキュメントと電子メールの検出、分類、および保護を実行できるようになります。 |
| [API Management](../../api-management/api-management-key-concepts.md) | この手段によって、既存のバックエンド サービスに対して一貫性のある最新の API ゲートウェイを作成できます。 |
| [Azure Confidential Computing](../../confidential-computing/overview.md) | 機密データがクラウドで処理されている間も、そのデータを隔離することができるようになります。 |
| [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops) | 開発プロジェクトでは、Azure DevOps に格納されている場合に、セキュリティとガバナンスのテクノロジ、運用方法、コンプライアンス ポリシーの複数の層からメリットを得ることができます。 |
| **カスタマー アクセス** |  |
| [Azure AD 外部 ID](../../active-directory/external-identities/compare-with-b2c.md) | Azure AD 内で外部 ID を使用すると、ご自身の組織外のユーザーに組織のアプリとリソースへのアクセスを許可することができ、その際、外部ユーザーは希望する任意の ID を使用してサインインできます。 |
|  | [Azure AD B2B](../../active-directory/external-identities/what-is-b2b.md) コラボレーションを使用して、外部ユーザーとアプリやリソースを共有できるようになります。 |
|  | [Azure AD B2C](../../active-directory-b2c/overview.md) により、1 日あたり何百万ものユーザーと数十億の認証をサポートし、サービス拒否、パスワード スプレー、ブルート フォース攻撃などの脅威を監視し、自動的に処理することができます。 |

## <a name="detect-threats"></a>脅威を検出する

:::image type="content" source="media/end-to-end/detect-threats.svg" alt-text="脅威を検出する Azure サービスを示す図。" border="false":::

| サービス | 説明 |
|------|--------|
| [Azure Defender](../../security-center/azure-defender.md) | Azure とハイブリッド リソースおよびワークロードの高度でインテリジェントな保護が実現されます。 Security Center の Azure Defender ダッシュボードにより、環境のクラウド ワークロード保護機能を可視化し、制御できます。 |
| [Azure Sentinel](../../sentinel/overview.md) | スケーラブルでクラウドネイティブのセキュリティ情報イベント管理 (SIEM) およびセキュリティ オーケストレーション自動応答 (SOAR) ソリューションです。 Sentinel によって、高度なセキュリティ分析と脅威インテリジェンスが企業全体で実現され、アラートの検出、脅威の可視性、予防的な捜索、脅威への対応のための 1 つのソリューションが提供されます。 |
| **ID&nbsp; & &nbsp;アクセス&nbsp;管理** |  |
| [Microsoft 365 Defender](https://docs.microsoft.com/microsoft-365/security/defender/microsoft-365-defender) | 統合された侵害前後のエンタープライズ防御スイートです。これによって、高度な攻撃に対して統合された保護が提供されるように、エンドポイント、ID、電子メール、およびアプリケーション間の検出、防止、調査、および対応がネイティブに調整されます。 |
|  | [Microsoft Defender for Endpoint](https://docs.microsoft.com/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint.md) は、企業ネットワークによる高度な脅威に対する防御、検出、調査、対応を支援するように、設計されたエンタープライズ エンドポイント セキュリティ プラットフォームです。 |
|  | [Microsoft Defender for Identity](https://docs.microsoft.com/defender-for-identity/what-is) はクラウド ベースのセキュリティ ソリューションです。これによって、オンプレミスの Active Directory シグナルを利用した、組織に向けられた高度な脅威、侵害された ID、および悪意のあるインサイダーによるアクションの識別、検出、調査支援が行われます。 |
| [Azure AD Identity Protection](../../active-directory/identity-protection/howto-identity-protection-configure-notifications.md) | ユーザーのリスクとリスク検出の管理に役立つ 2 種類の自動通知電子メール (危険な状態のユーザーが検出されたことを示す電子メールおよび週間ダイジェスト電子メール) が送信されます。 |
| **インフラストラクチャとネットワーク** |  |
| [Azure Defender for IoT](../../defender-for-iot/overview.md) | IoT/OT デバイス、脆弱性、脅威を特定するための統合セキュリティ ソリューションです。 これを使用すれば、既存の IoT/OT デバイスを保護する必要があるか、新しい IoT イノベーションにセキュリティを組み込む必要があるかにかかわらず、IoT/OT 環境全体をセキュリティで保護することができます。 |
| [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) | Azure 仮想ネットワーク内のリソースの監視、診断、メトリックの表示、ログの有効化または無効化を行うツールが提供されます。 Network Watcher は、仮想マシン、仮想ネットワーク、アプリケーション ゲートウェイ、ロード バランサーなどの IaaS 製品のネットワーク正常性を監視および修復するように設計されています。 |
| [Azure Policy 監査ログ](../../governance/policy/overview.md) | 組織の標準を適用し、コンプライアンスを大規模に評価するのに役立ちます。 Azure Policy では、アクティビティ ログが使用されます。これは、イベント ソース、日付、ユーザー、タイムスタンプ、送信元アドレス、送信先アドレス、その他の役立つ要素を含めるために、自動的に有効になります。 |
| **データとアプリケーション** |  |
| [Azure Defender for container registries](../../security-center/defender-for-container-registries-introduction.md) | Azure Resource Manager ベースの Azure Container Registry レジストリ内のイメージをスキャンし、画像の脆弱性をより詳細に把握するための脆弱性スキャナーが含まれています。 |
| [Azure Defender for Kubernetes](../../security-center/defender-for-kubernetes-introduction.md) | Azure Kubernetes Service (AKS) によって取得されたログを使用して AKS のマネージド サービスを監視することで、クラスター レベルの脅威の防止が提供されます。 |
| [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) | 複数のクラウドで動作するクラウド アクセス セキュリティ ブローカー (CASB) です。 お使いのクラウド サービス全体にわたるサイバー攻撃の脅威を特定し、対処するために、豊富な表示機能、データ送受信の制御、高度な分析を備えています。 |

## <a name="investigate-and-respond"></a>調査と対応

:::image type="content" source="media/end-to-end/investigate-and-respond.svg" alt-text="脅威の調査と対応に役立つ Azure サービスを示す図。" border="false":::

| サービス | 説明 |
|------|--------|
| [Azure Sentinel](../../sentinel/hunting.md) | 組織のデータ ソースにわたってセキュリティ上の脅威を検出するための、強力な検索とクエリのツールです。 |
| [Azure &nbsp;Monitor&nbsp; ログ&nbsp;と&nbsp;メトリック](../../azure-monitor/overview.md) | クラウドおよびオンプレミス環境のテレメトリを収集、分析し、対応する包括的なソリューションが提供されます。 Azure Monitor では、分析、視覚化、およびアラートを行うために使用できる共通データ プラットフォームにさまざまなソースからデータが[収集されて集計されます](../../azure-monitor/data-platform.md#observability-data-in-azure-monitor)。 |
| **ID&nbsp; & &nbsp;アクセス&nbsp;管理** |  |
| [Azure&nbsp; AD&nbsp; レポート&nbsp;と&nbsp;監視](https://docs.microsoft.com/azure/active-directory/reports-monitoring/) | [Azure AD レポート](../../active-directory/reports-monitoring/overview-reports.md)を使用すると、ご利用の環境におけるアクティビティを包括的に把握することができます。 |
|  | [Azure AD 監視](../../active-directory/reports-monitoring/overview-monitoring.md)を使用すると、Azure AD のアクティビティ ログを別のエンドポイントにルーティングできます。|
| [Azure AD PIM 監査履歴](../../active-directory/privileged-identity-management/pim-how-to-use-audit-log.md) | すべての特権ロールでの過去 30 日間におけるすべてのロール割り当てとアクティブ化を確認できます。 |
| **データとアプリケーション** |  |
| [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/investigate) | クラウド環境で起こっていることをより深く解釈するためのツールが提供されます。 |

## <a name="next-steps"></a>次のステップ

- [クラウドにおける共同責任](shared-responsibility.md)について理解します。

- 悪意のあるユーザーと悪意のないユーザーの両方に対する [Azure クラウドでの分離の選択肢](isolation-choices.md) について解釈してください。