---
title: Azure のセキュリティとコンプライアンスのブループリント - NIST SP 800-171 のための PaaS Web アプリケーション
description: Azure のセキュリティとコンプライアンスのブループリント - PaaS Web アプリケーション NIST SP 800-171
services: security
author: jomolesk
ms.assetid: eea21a0a-5930-43e8-937f-5419c20744c9
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 8411e9d2d4118c4e1f656b5bc6e9dafb311aeb26
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39392589"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-nist-special-publication-800-171"></a>Azure のセキュリティとコンプライアンスのブループリント - NIST Special Publication 800-171 のための PaaS Web アプリケーション

## <a name="overview"></a>概要
[NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) では、非連邦情報システムおよび組織に存在する管理対象の非機密情報 (CUI) を保護するためのガイドラインが提供されます。 NIST SP 800-171 では、CUI の機密性を保護するための 14 個のセキュリティ要件ファミリが確立されています。

この Azure のセキュリティとコンプライアンスのブルー プリントでは、NIST SP 800-171 制御のサブセットを実装する、Azure のサービスとしてのプラットフォーム (PaaS) Web アプリケーションをお客様がデプロイするのに役立つガイダンスが提供されます。 このソリューションは、お客様が特定のセキュリティとコンプライアンスの要件を満たすことができる方法を例示するものであり、Azure でお客様が独自の Web アプリケーションをビルドして構成するための基礎として機能します。

この参照アーキテクチャ、関連する実装ガイド、および脅威モデルは、お客様固有の要件に適合させるための基礎として使用されることを目的としており、運用環境でそのまま使用することはできません。 変更を加えずにこのアーキテクチャをデプロイすることは、NIST SP 800-171 の要件を完全に満たすには不十分です。 お客様は、このアーキテクチャを使用してビルドしたソリューションの適切なセキュリティとコンプライアンスの評価を実施する責任を負います。要件は、お客様の実装によって変化する可能性があるからです。

## <a name="architecture-diagram-and-components"></a>アーキテクチャ ダイアグラムとコンポーネント

この Azure のセキュリティとコンプライアンスのブループリントでは、バックエンドに Azure SQL Database バックエンドを使用する PaaS Web アプリケーション向けの参照アーキテクチャが提供されます。 Web アプリケーションは、分離された Azure Application Service Environment でホストされます。これは、Azure データセンター内のプライベートな専用環境です。 この環境では、Azure によって管理されている仮想マシン間に Web アプリケーションのトラフィックを負荷分散します。 このアーキテクチャには、ネットワーク セキュリティ グループ、Application Gateway、Azure DNS、および Load Balancer も含まれています。

分析とレポートを強化するために、Azure SQL Database を列ストア インデックスを使って構成できます。 SQL Database は、顧客の用途に対応するために、スケールアップ、スケールダウン、完全な切り離しが可能です。 すべての SQL トラフィックは、自己署名証明書を含めることによって、SSL で暗号化されます。 ベスト プラクティスとして、Azure では、セキュリティ強化のために信頼された証明書機関を利用することをお勧めします。

このソリューションでは、Azure Storage アカウントを使用します。お客様は、Storage Service Encryption を使用して保存データの機密性を保持するように、Azure Storage アカウントを構成できます。 Azure は、回復性のために、お客様が選択したデータセンター内にデータの 3 つのコピーを保存します。 geo 冗長ストレージにより、データは数百マイル離れたセカンダリ データセンターにレプリケートされ、そのデータセンター内に 3 つのコピーとして再度保存されます。これにより、プライマリ データ センターでの有害事象によってデータが失われるのを防ぐことができます。

セキュリティ強化のために、このソリューションのすべてのリソースは、Azure Resource Manager を通じてリソース グループとして管理されます。 デプロイされたリソースへのアクセス (Azure Key Vault 内のキーへのアクセスを含む) を制御するために、Azure Active Directory のロールベースのアクセス制御が使用されます。 システムの正常性は、Azure Monitor によって監視されます。 お客様が両方の監視サービスを構成してログをキャプチャし、簡単にナビゲートできる単一のダッシュボードにシステムの正常性を表示できます。

Azure SQL Database は一般的に、SQL Server Management Studio 経由で管理されます。SQL Server Management Studio は、セキュアな VPN または ExpressRoute 接続経由で Azure SQL Database にアクセスするように構成されたローカル コンピューターから実行されます。

さらに、Application Insights は、Log Analytics を通して、リアルタイムでアプリケーションのパフォーマンス管理と分析を行います。 **Microsoft では、参照アーキテクチャのサブネットに対する管理とデータ インポートのために、VPN または ExpressRoute 接続を構成することを推奨しています。**

![NIST SP 800-171 のための PaaS Web アプリケーションの参照アーキテクチャ ダイアグラム](images/nist171-paaswa-architecture.png "NIST SP 800-171 のための PaaS Web アプリケーションの参照アーキテクチャ ダイアグラム")

このソリューションでは、次の Azure サービスを使用します。 これについては、「[デプロイ アーキテクチャ](#deployment-architecture)」セクションで詳しく説明します。

- Azure Virtual Machines
    - (1) 管理/要塞 (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (1) /16 ネットワーク
    - (4) /24 ネットワーク
    - (4) ネットワーク セキュリティ グループ
- Application Gateway
    - Web アプリケーション ファイアウォール
        - ファイアウォール モード: 防止
        - ルール セット: OWASP
        - リスナー ポート: 443
- Application Insights
- Azure Active Directory
- Azure Application Service Environment v2
- Azure Automation
- Azure DNS
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Log Analytics
- Azure Automation
- Azure Web アプリ

## <a name="deployment-architecture"></a>デプロイメント アーキテクチャ
次のセクションで、デプロイと実装の要素について詳しく説明します。

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) によって、ソリューション内の複数のリソースを 1 つのグループとして操作できます。 ソリューションのこれらすべてのリソースを、1 回の連携した操作でデプロイ、更新、または削除できます。 デプロイメントにはテンプレートを使用しますが、このテンプレートは、テスト、ステージング、運用環境などのさまざまな環境に使用できます。 Resource Manager には、デプロイ後のリソースの管理に役立つ、セキュリティ、監査、タグ付けの機能が用意されています。

**要塞ホスト**: 要塞ホストは、この環境にデプロイされたリソースへのユーザーのアクセスを許可する単一エントリ ポイントです。 要塞ホストは、セーフ リスト上のパブリック IP アドレスからのリモート トラフィックのみを許可することで、デプロイ済みのリソースへのセキュリティで保護された接続を提供します。 リモート デスクトップ (RDP) トラフィックを許可するには、トラフィックのソースがネットワーク セキュリティ グループに定義されている必要があります。

このソリューションでは、次の構成を持つドメイン参加済み要塞ホストとして仮想マシンを作成します。
-   [マルウェア対策拡張機能](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Azure 診断拡張機能](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   Azure Key Vault を使用した [Azure ディスクの暗号化](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)
-   未使用時の仮想マシン リソースの消費を抑えるための[自動シャットダウン ポリシー](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) の有効化。実行中のオペレーティング システムから分離されている保護された環境で、資格情報とその他のシークレットが実行されるようにします。

**Azure Web App**: [Azure Web Apps](https://docs.microsoft.com/azure/app-service/) によって、インフラストラクチャを管理することなく、お好きなプログラミング言語で Web アプリケーションを構築してホストすることができます。 Azure Web Apps では、自動スケールと高可用性が実現されるほか、Windows と Linux の両方がサポートされています。さらに、GitHub、Visual Studio Team Services、または任意の Git リポジトリからの自動デプロイが可能になります。

**Application Service Environment v2**: [Azure Application Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro) は、App Service アプリケーションを大規模かつ安全に実行するために完全に分離された専用の環境を提供する、App Service の機能です。

Application Service Environment は、単一のアプリケーションのみを実行するために分離され、常に仮想ネットワークにデプロイされます。 この分離機能により、参照アーキテクチャでテナントを完全に分離し、Azure のマルチテナント環境から参照アーキテクチャを除外することができます。 顧客は、受信および送信アプリケーション ネットワーク トラフィックをきめ細かく制御できます。また、アプリケーションは、オンプレミスの企業リソースへの仮想ネットワーク経由のセキュリティで保護された高速接続を確立できます。 お客様は、Application Service Environment を使用して、負荷メトリック、利用可能な予算、定義されたスケジュールに基づいて "自動スケール" することができます。

このアーキテクチャに Application Service Environment を使用することで、次の制御や構成が可能になります。

- セキュリティ保護された Azure 仮想ネットワーク内でのホストと、ネットワーク セキュリティ規則
- HTTPS 通信用の自己署名内部ロード バランサー証明書。 ベスト プラクティスとして、Microsoft では、セキュリティ強化のために信頼された証明書機関を利用することをお勧めします。
- [内部負荷分散モード](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer) (モード 3)
- [TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings) の無効化
- [TLS 暗号](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)の変更
- [受信トラフィック N/W ポート](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)のコントロール
- [Web アプリケーション ファイアウォール - データの制限](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- [Azure SQL Database トラフィック](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)の許可

### <a name="virtual-network"></a>Virtual Network
このアーキテクチャは、10.200.0.0/16 のアドレス空間 を使用してプライベート仮想ネットワークを定義します。

**ネットワーク セキュリティ グループ**: [ネットワーク セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)には、仮想ネットワーク内のトラフィックを許可または拒否するアクセス制御リストが含まれています。 ネットワーク セキュリティ グループを使用して、サブネットまたは個々の仮想マシン レベルでトラフィックを保護できます。 次のネットワーク セキュリティ グループが存在します。
- Application Gateway 用の 1 つのネットワーク セキュリティ グループ
- Application Service Environment 用の 1 つのネットワーク セキュリティ グループ
- Azure SQL Database 用の 1 つのネットワーク セキュリティ グループ
- 要塞ホスト用の 1 つのネットワーク セキュリティ グループ

各ネットワーク セキュリティ グループでは、ソリューションが安全かつ適切に機能できるように、固有のポートとプロトコルが開かれます。 さらに、各ネットワーク セキュリティ グループで次の構成を使用できます。
  - [診断ログとイベント](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)が有効化され、ストレージ アカウントに格納される
  - Log Analytics が[ネットワーク セキュリティ グループの診断](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)に接続される

**サブネット**: 各サブネットは、対応するネットワーク セキュリティ グループに関連付けられています。

**Azure DNS**: ドメイン ネーム システム (DNS) は、Web サイトまたはサービスの名前をその IP アドレスに変換する (または解決する) 役割を担います。 [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) は、DNS ドメインのホスティング サービスであり、Azure インフラストラクチャを使用した名前解決を提供します。 Azure でドメインをホストすることで、その他の Azure サービスと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。 Azure DNS は、プライベート DNS ドメインもサポートします。

**Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) によって、アプリケーションを拡張し、サービスを高可用性にすることができます。 ロード バランサーは、受信と送信のどちらのシナリオもサポートし、低遅延と高スループットを実現できるだけでなく、あらゆる TCP アプリケーションと UDP アプリケーションの数百万ものフローにスケールアップできます。

### <a name="data-in-transit"></a>転送中のデータ
Azure では、Azure データセンターとの間のすべての通信を既定で暗号化します。 Azure Portal での Azure Storage に対するすべてのトランザクションは HTTPS 経由で行われます。

### <a name="data-at-rest"></a>保存データ

このアーキテクチャでは、暗号化、データベース監査などの手段によって保存データを保護します。

**Azure Storage**: 暗号化された保存データの要件を満たすために、すべての [Azure Storage](https://azure.microsoft.com/services/storage/) で [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption) が使用されます。 これは、NIST SP 800-171 によって定義されている組織のセキュリティ コミットメントとコンプライアンス要件のサポートにおいてデータを保護するために役立ちます。

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) は、Windows の BitLocker 機能を活用して、データ ディスクのボリュームを暗号化します。 このソリューションは、ディスクの暗号化キーを制御および管理できるように、Azure Key Vault と統合されています。

**Azure SQL Database**: Azure SQL Database インスタンスは、次のデータベース セキュリティ対策を使用します。
-   [Active Directory 認証と承認](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication)を使用して、データベース ユーザーの ID 管理と他の Microsoft サービスを一元管理できます。
-   [SQL Database の監査](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started)では、データベース イベントを追跡し、Azure Storage アカウントの監査ログにイベントを書き込みます。
-   Azure SQL Database は、[Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) を使用するように構成されます。Transparent Data Encryption は、データベース、関連付けられたバックアップ、トランザクション ログ ファイルのリアルタイムの暗号化と暗号化解除を実行して保存情報を保護します。 Transparent Data Encryption は、保存されているデータが未承認のアクセスの対象になっていないことを保証します。
-   [ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)は、適切なアクセス許可が付与されていない限り、データベース サーバーへのすべてのアクセスを阻止します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。
-   [SQL の脅威検出](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)は、不審なデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃、および異常なデータベース アクセス パターンに対するセキュリティの警告を提供することで、潜在的な脅威の検出とそれらの脅威への対応を可能にします。
-   [暗号化された列](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)により、データベース システム内で機微なデータがプレーンテキストとして表示されないことが確保されます。 データ暗号化を有効にした後は、キーへのアクセス権を持つクライアント アプリケーションまたはアプリケーション サーバーのみが、プレーンテキスト データにアクセスできます。
- [動的データ マスク](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)では、特権のないユーザーまたはアプリケーションに対してデータをマスクすることで、機微なデータの露出が制限されます。 動的データ マスクは、潜在的な機微なデータを自動的に検出し、適用する適切なマスクを提案できます。 これは、不正アクセスによるデータベースの終了が発生しないように機微なデータへのアクセスを削減するために役立ちます。 **お客様は、ご利用のデータベース スキーマに準拠するように動的データ マスクの設定を調整する必要があります。**

### <a name="identity-management"></a>ID 管理
次のテクノロジによって、Azure 環境でのデータへのアクセスを管理する機能が提供されます。
-   [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) は、Microsoft が提供する、マルチテナントに対応したクラウドベースのディレクトリおよび ID 管理サービスです。 このソリューションのすべてのユーザー (Azure SQL Database にアクセスするユーザーを含む) は、Azure Active Directory で作成されています。
-   アプリケーションに対する認証は Azure Active Directory を使用して行われます。 詳細については、「[Azure Active Directory とアプリケーションの統合](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)」をご覧ください。 さらに、データベース列の暗号化では、Azure SQL Database に対してアプリケーションを認証するために Azure Active Directory が使用されます。 詳細については、[SQL Database で機微なデータを保護する](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)方法に関するページを参照してください。
-   [Azure ロールベースのアクセス制御](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)によって、管理者は、業務の遂行に必要なアクセスのみをユーザーに付与するきめの細かいアクセス許可を定義できます。 すべてのユーザーに Azure リソースへの無制限アクセスを許可する代わりに、管理者は、リソースやデータにアクセスするための特定のアクションのみを許可できます。 サブスクリプションへのアクセスは、サブスクリプションの管理者に制限されます。
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) により、特定の情報にアクセスできるユーザーの数を最小限に抑えることができます。  管理者は、Azure Active Directory Privileged Identity Management を使用して、特権 ID と特権 ID によるリソースへのアクセスを検出、制限、監視できます。 この機能を使用して、必要に応じて、オンデマンドの Just-In-Time 管理アクセスを適用することもできます。
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) は、組織の ID に影響を及ぼす可能性のある脆弱性を検出し、組織の ID に関連する検出された不審なアクションに対する自動応答を構成します。さらに、不審なインシデントを調査し、適切なアクションを実行してそれらを解決します。

### <a name="security"></a>セキュリティ
**シークレットの管理**: ソリューションでは、キーとシークレットの管理に [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) を使用します。 Azure Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。 次の Azure Key Vault 機能は、お客様がデータを保護するのに役立ちます。
- 必要に応じて、高度なアクセス ポリシーが構成されます。
- Key Vault のアクセス ポリシーは、キーとシークレットに対する最低限必要なアクセス許可で定義されます。
- Key Vault のすべてのキーとシークレットに有効期限があります。
- Key Vault 内のすべてのキーは、特殊なハードウェア セキュリティ モジュールによって保護されます。 キーの種類は、ハードウェア セキュリティ モジュールによって保護された 2048 ビット RSA キーです。
- ロールベースのアクセス制御を使用して、すべてのユーザーと ID に最低限必要なアクセス許可が付与されます。
- Key Vault の診断ログは、少なくとも 365 日のリテンション期間で有効になっています。
- キーに対して許可される暗号化操作は必要なものに制限されます。

**Azure Security Center**: [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) を使用すると、お客様はワークロード全体へのセキュリティ ポリシーの一元的な適用と管理、脅威にさらされる状態の軽減、攻撃の検出とその対応を行うことができます。 さらに、Azure Security Center は、Azure サービスの既存の構成にアクセスして、セキュリティ状況の改善とデータの保護に役立つ、構成とサービスに関する推奨事項を提供します。

Azure Security Center では、さまざまな検出機能を使用して、お客様の環境を対象とする攻撃の可能性を通知します。 これらのアラートには、アラートをトリガーした要因、対象となったリソース、攻撃元に関する重要な情報が含まれています。 Azure Security Center には、事前定義された一連の[セキュリティ アラート](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)が用意されています。これらは、脅威または疑わしいアクティビティが発生した際にトリガーされます。 Azure Security Center の[カスタム アラート ルール](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)を使用すると、お客様が環境から既に収集されているデータに基づいて、新しいセキュリティ アラートを定義できます。

Azure Security Center では、お客様の潜在的なセキュリティの問題の検出と対処が簡単になるように、優先度付けしたセキュリティの警告とインシデントを提供しています。 インシデント対応チームによる脅威の調査と修復を支援するために、検出された脅威ごとに[脅威インテリジェンス レポート](https://docs.microsoft.com/azure/security-center/security-center-threat-report)が生成されます。

**Azure Application Gateway**: このアーキテクチャでは、OWASP ルールセットを有効にした、Web アプリケーション ファイアウォールが構成された Azure Application Gateway を使用してセキュリティの脆弱性のリスクを軽減します。 その他の機能には次のものがあります。

- [エンド ツー エンド SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL オフロード](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)の有効化
- [TLS v1.0 および v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) の無効化
- [Web アプリケーション ファイアウォール](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (防止モード)
- OWASP 3.0 ルールセットを使用した[防止モード](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal)
- [診断ログ](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)の有効化
- [カスタム正常性プローブ](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) と [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) による、その他の保護と通知の提供。 Azure Security Center は評価システムも提供します。

### <a name="logging-and-auditing"></a>ログ記録と監査

Azure サービスは、システムの正常性だけではなく、システムとユーザーのアクティビティも詳細に記録します。
- **アクティビティ ログ**: [アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) は、サブスクリプション内のリソースに対して実行された操作に関する分析情報を提供します。 アクティビティ ログは、操作のイニシエーター、発生時刻、および状態の判断に役立ちます。
- **診断ログ**: [診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)には、各リソースによって出力されるすべてのログが含まれます。 これらのログには、Windows イベント システム ログ、Azure Storage ログ、Key Vault 監査ログ、および Application Gateway のアクセス ログとファイアウォール ログが含まれます。 すべての診断ログは、暗号化され、集中管理された Azure Storage アカウントに書き込まれ、アーカイブされます。 リテンション期間には、組織固有の保有要件を満たすために最長 730 日までの日数をユーザーが設定できます。

**Log Analytics**: これらのログは、処理、格納、ダッシュボードのレポート化を行うために、[Log Analytics](https://azure.microsoft.com/services/log-analytics/) に統合されます。 収集されたデータは、その型ごとに Operations Management Suite のワークスペース内の別個のテーブルにまとめられ、すべてのデータがその収集元にかかわらず一斉に分析できる状態になります。 さらに、Azure Security Center を Log Analytics と統合することで、お客様は Log Analytics クエリを使用してセキュリティ イベント データにアクセスして、それを他のサービスからのデータと組み合わせることができます。

このアーキテクチャの一部として、次の Log Analytics [管理ソリューション](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)が含まれます。
-   [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): この Active Directory 正常性チェック ソリューションは、一定の間隔でサーバー環境のリスクと正常性を評価し、デプロイされたサーバー インフラストラクチャに固有の推奨事項を重要度別に示した一覧を提供します。
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): この SQL 正常性チェック ソリューションは、一定の間隔でサーバー環境のリスクと正常性を評価し、デプロイされたサーバー インフラストラクチャに固有の推奨事項を重要度別に示した一覧をお客様に提供します。
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Agent Health ソリューションは、デプロイされたエージェント数とその地理的配置に加え、応答しないエージェント数やオペレーショナル データを送信しているエージェント数を報告します。
-   [Activity Logs Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Activity Log Analytics ソリューションは、すべての Azure サブスクリプション間の Azure アクティビティ ログの分析に役立ちます。

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) は、Runbook の格納、実行、管理を行います。 このソリューションでは、Runbook を使用して、Azure SQL Database からログを収集できます。 Automation [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) ソリューションは、お客様が環境の変更を簡単に識別できるようにします。

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) は、監査、アラートの作成、データのアーカイブ、Azure リソース内の API 呼び出しの追跡を実行できるようにすることで、パフォーマンスの追跡、セキュリティの維持、傾向の識別を実行できるようにします。

**Application Insights**: [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) は、複数のプラットフォームで使用できる Web 開発者向けの拡張可能なアプリケーション パフォーマンス管理サービスです。 Application Insights は、パフォーマンスの異常を検出します。それを使用して、実行中の Web アプリケーションを監視できます。 これには強力な分析ツールが組み込まれており、お客様が問題を診断する場合や、ユーザーがアプリを使用して実行している操作を把握するのに役立ちます。 それは、パフォーマンスやユーザビリティを継続的に向上させるうえで役立つように設計されています。

## <a name="threat-model"></a>脅威モデル

この参照アーキテクチャのデータ フロー ダイアグラムを[ダウンロード](https://aka.ms/nist171-paaswa-tm)するか、下記を参照してください。 このモデルは、修正を行う際に、お客様がシステム インフラストラクチャの潜在的なリスクを理解するために役立ちます。

![NIST SP 800-171 のための PaaS Web アプリケーションの脅威モデル](images/nist171-paaswa-threat-model.png "NIST SP 800-171 のための PaaS Web アプリケーションの脅威モデル")

## <a name="compliance-documentation"></a>コンプライアンス ドキュメント
[Azure のセキュリティとコンプライアンスのブループリント - NIST SP 800-171 顧客責任マトリックス](https://aka.ms/nist171-crm)には、NIST SP 800-171 で必要とされるすべてのセキュリティ制御が一覧表示されています。 このマトリックスには、各制御の実装が、Microsoft、お客様、または両者間での共有の責任のどれに該当するかが、詳細に示されています。

[Azure のセキュリティとコンプライアンスのブループリント - NIST SP 800-171 PaaS Web アプリケーション制御実装マトリックス](https://aka.ms/nist171-paaswa-cim)には、PaaS Web アプリケーション アーキテクチャによって対応される NIST SP 800-171 制御に関する情報が示されています。対象となる各制御の要件を満たす実装の詳細な説明も含まれています。

## <a name="guidance-and-recommendations"></a>ガイダンスと推奨事項
### <a name="vpn-and-expressroute"></a>VPN と ExpressRoute
この PaaS Web アプリケーションの参照アーキテクチャの一部としてデプロイされるリソースへの接続を安全に確立するために、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) またはセキュリティ保護された VPN トンネルを構成する必要があります。 ExpressRoute または VPN を適切に設定することで、転送中のデータに対して保護のレイヤーを追加できます。

Azure を使用してセキュリティ保護された VPN トンネルを実装することにより、オンプレミス ネットワークと Azure 仮想ネットワークの間で仮想プライベート接続を作成できます。 この接続はインターネットをまたいでいるため、お客様は自社ネットワークと Azure の間の暗号化されたリンクの "トンネル" を通して情報を送信できます。 サイト間 VPN は、数十年にわたってあらゆる規模の企業に導入されている安全で成熟したテクノロジです。 このオプションでは、暗号化メカニズムとして [IPsec トンネル モード](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10))が使用されています。

VPN トンネル内のトラフィックはサイト間 VPN を使用してインターネット上を転送されるため、Microsoft は、さらに高いセキュリティで保護されたもう 1 つの接続オプションを提供します。 Azure ExpressRoute は、オンプレミスの場所または Exchange ホスティング プロバイダーと Azure の間に確立される専用 WAN リンクです。 ExpressRoute 接続はインターネットを経由しないため、これらの接続はインターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。 さらに、これはお客様の通信プロバイダーの直接接続であり、データがインターネット上を移動しないため、インターネットに公開されることはありません。

オンプレミス ネットワークを Azure に拡張するセキュアなハイブリッド ネットワークを実装するためのベスト プラクティスが[提供されています](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

## <a name="disclaimer"></a>免責事項

 - このドキュメントは、参考目的でのみ使用してください。 マイクロソフトは、本文書の情報に対して、明示的、黙示的、法的であるかを問わず、一切の保証を行わないものとします。 このドキュメントは "現状のまま" 提供されます。 このドキュメントに記載された情報と見解は、URL やその他のインターネット Web サイトの参照も含め、予告なく変更する可能性があります。 このドキュメントをお読みになったお客様は、自己責任でドキュメントをご利用ください。
 - このドキュメントは、Microsoft 製品に含まれる知的財産に対するいかなる法的権利もお客様に提供するものではありません。
 - お客様の社内での参照目的に限り、このドキュメントを複製し使用することができます。
 - このドキュメントに記載されている特定の推奨事項に従った結果、Azure でのデータ、ネットワーク、コンピューティング リソースの使用量が増加したり、ライセンス コストやサブスクリプション コストが増加したりする場合があります。
 - このアーキテクチャは、特定の要件に合わせて調整するための基礎として使用されることを目的としており、そのまま運用環境に適用しないでください。
 - このドキュメントは、参照資料として作成されており、お客様が特定のコンプライアンス要件や規制に適合するためのすべての手段を定義するために使用されるべきではありません。 お客様は、承認された顧客実装について、組織の法務課からのアドバイスを受けてください。
