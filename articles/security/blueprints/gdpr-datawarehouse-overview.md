---
title: Azure のセキュリティとコンプライアンスのブルー プリント - GDPR のためのデータ ウェアハウス
description: Azure のセキュリティとコンプライアンスのブルー プリント - GDPR のためのデータ ウェアハウス
services: security
author: jomolesk
ms.assetid: 7a33fb3b-cfb6-49dd-ab4d-c53cf0d5da41
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: a091f4c941b1f4a338ad23956dbba3a7b89b87e8
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161865"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-gdpr"></a>Azure のセキュリティとコンプライアンスのブルー プリント: GDPR のためのデータ ウェアハウス

## <a name="overview"></a>概要
一般データ保護規制 (GDPR) には、個人情報の収集、保管、および使用に関する多くの要件があり、個人データの特定と保護、透明性要件への対応、個人データの侵害の検知と報告、およびプライバシー管理者と他の従業員のトレーニングを組織がどのように実施するかについての要件が含まれています。 GDPR は、個人による個人データの管理能力を強め、個人データの収集、処理、または分析を行う組織に対して多数の新しい義務を課しています。 GDPR は、欧州連合 (EU) の居住者に商品やサービスを提供する組織、または EU の居住者に結び付くデータの収集と分析を行う組織を対象とする新しい規則です。 GDPR は、組織の所在地に関係なく適用されます。

Microsoft では、業界をリードするセキュリティ対策とプライバシー ポリシーを用いてクラウド内のデータを保護するように Azure を設計しています。この中に、GDPR によって識別される個人データのカテゴリも含まれています。 Microsoft の[契約条項](http://aka.ms/Online-Services-Terms)は、Microsoft がデータ処理者の要件に準拠していることを明言しています。

この Azure のセキュリティとコンプライアンスのブループリントは、GDPR の要件を支援するデータ ウェアハウス アーキテクチャを Azure にデプロイするためのガイダンスを提供します。 このソリューションは、特定のセキュリティとコンプライアンスの要件を満たすことができる方法を例示し、Azure で独自のデータ ウェアハウス ソリューションをビルドして構成するための基礎として機能します。 この参照アーキテクチャを活用し、Microsoft の [4 段階のプロセス](https://aka.ms/gdprebook)に従うことで、GDPR への準拠を実現できます。
1. 検出: どのような個人データがどこに存在するかを識別します。
2. 管理: 個人データがどのように使用され、アクセスされるかを制御します。
3. 保護: 脆弱性とデータ侵害の防止、検知、および対応を行うためのセキュリティ コントロールを確立します。
4. 報告: 必要なドキュメントを保持し、データ要求と侵害通知を管理します。

このリファレンス アーキテクチャ、関連する実装ガイド、および脅威モデルは、お客様固有の要件に適合させるための基礎として使用されることを目的としており、運用環境でそのまま使用することはできません。 以下の点に注意してください。
- このアーキテクチャは、GDPR に準拠した方法で Azure にワークロードをデプロイするためのベースラインを提供します。
- お客様は、このアーキテクチャを使用してビルドしたソリューションの適切なセキュリティとコンプライアンスの評価を実施する責任を負います。これは、要件がお客様の実装によって変化する可能性があるからです。

## <a name="architecture-diagram-and-components"></a>アーキテクチャ ダイアグラムとコンポーネント
このソリューションは、高パフォーマンスのセキュリティで保護されたクラウド データ ウェアハウスを実装する参照アーキテクチャを提供します。 このアーキテクチャには、2 つの分離されたデータ層があります。片方は、クラスター化された SQL 環境の中でデータのインポート、格納、およびステージングが行われる層であり、他方は、ETL ツール (例: [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) の T-SQL クエリ) を使用して処理するためにデータが読み込まれる Azure SQL Data Warehouse 用の層です。 データが Azure SQL Data Warehouse に格納された後、大規模な分析を実行できます。

Azure では、お客様向けのさまざまなレポートと分析サービスを提供しています。 このソリューションには、Azure SQL Data Warehouse からレポートをすばやく作成するための SQL Server Reporting Services (SSRS) が含まれています。 すべての SQL トラフィックは、自己署名証明書を含めることによって、SSL で暗号化されます。 ベスト プラクティスとして、Azure では、セキュリティ強化のために信頼された証明書機関を利用することをお勧めします。

Azure SQL Data Warehouse 内のデータは、単票形式ストレージを持つリレーショナル テーブルに格納されます。この形式は、クエリのパフォーマンスを向上させると同時に、データ ストレージ コストを大幅に削減します。  用途の要件に応じて、コンピューティング リソースを必要とするアクティブなプロセスが存在しない場合は、Azure SQL Data Warehouse のコンピューティング リソースのスケールアップ、スケールダウン、または完全なシャット ダウンを実行できます。

SQL Load Balancer は、SQL トラフィックを管理することで高パフォーマンスを保証します。 この参照アーキテクチャ内のすべての仮想マシンは、高可用性とディザスター リカバリー機能のために AlwaysOn 可用性グループ内に構成された SQL Server インスタンスを使用して可用性セット内にデプロイされます。

このデータ ウェアハウスの参照アーキテクチャには、アーキテクチャ内のリソースを管理するための Active Directory (AD) 層も含まれています。 Active Directory サブネットによって、大規模な AD フォレスト構造への容易な導入が可能になり、大規模なフォレストへのアクセスが利用できない場合でも、環境を継続的に運用できます。 すべての仮想マシンは、Active Directory 層に対してドメイン参加済みであり、Active Directory グループ ポリシーを使用して、オペレーティング システム レベルでセキュリティとコンプライアンスの構成が適用されます。

仮想マシンは管理要塞ホストとして機能し、デプロイされたリソースにアクセスするためのセキュリティで保護された接続を管理者に提供します。 データは、この管理要塞ホストを通してステージング領域に読み込まれます。 **Azure では参照アーキテクチャのサブネットに対する管理とデータ インポートのために、VPN または Azure ExpressRoute 接続を構成することを推奨しています。**

![GDPR のためのデータ ウェアハウスの参照アーキテクチャの図](images/gdpr-datawarehouse-architecture.png?raw=true "GDPR のためのデータ ウェアハウスの参照アーキテクチャの図")

このソリューションでは、次の Azure サービスを使用します。 デプロイ アーキテクチャの詳細については、「[デプロイ アーキテクチャ](#deployment-architecture)」セクションを参照してください。

-   Azure Virtual Machines
    - (1) 要塞ホスト
    -   (2) Active Directory ドメイン コントローラー
    -   (2) SQL Server クラスター ノード
    -   (1) SQL Server 監視
-   可用性セット
    - Active Directory ドメイン コントローラー
    - SQL クラスター ノードと監視
-   仮想ネットワーク
    - (4) サブネット
    -   (4) ネットワーク セキュリティ グループ
- SQL Data Warehouse
- SQL Server Reporting Services
- Azure SQL Load Balancer
- Azure Active Directory
- Recovery Services コンテナー
- Azure Key Vault
- Operations Management Suite (OMS)
- Azure Data Catalog
- Azure Security Center

## <a name="deployment-architecture"></a>デプロイ アーキテクチャ
次のセクションで、デプロイと実装の要素について詳しく説明します。

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) は、超並列処理 (MPP) を利用して、ペタバイト単位のデータに対して複雑なクエリを短時間で実行するクラウドベースのエンタープライズ データ ウェアハウス (EDW) であり、ユーザーはこのウェアハウスによって、個人データを効率的に識別できます。 ユーザーは、簡単な PolyBase T-SQL クエリを使用して、SQL Data Warehouse にビッグ データをインポートし、MPP の機能を利用して高パフォーマンスの分析を実行できます。

**SQL Server Reporting Services (SSRS)**: [SQL Server Reporting Services](https://docs.microsoft.com/en-us/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) によって、Azure SQL Data Warehouse のテーブル、グラフ、マップ、ゲージ、マトリックスなどを含むレポートをすばやく作成できます。

**Azure Data Catalog**: [Azure Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) は、データを管理しているユーザーが、データ ソースを容易に検出し、把握できるようにします。 一般的なデータ ソースの登録、タグ付け、および個人データの検索を実行できます。 データは元の場所に残りますが、メタデータのコピーと、データ ソースの場所に対する参照が Data Catalog に追加されます。 このメタデータのインデックスも作成されるので、検索で簡単に各データ ソースを見つけられるようになり、データ ソースを検出するユーザーが理解しやすくなります。

**要塞ホスト**: 要塞ホストは、この環境にデプロイされたリソースへのユーザーのアクセスを許可する単一エントリ ポイントです。 要塞ホストは、セーフ リスト上のパブリック IP アドレスからのリモート トラフィックのみを許可することで、デプロイ済みのリソースへのセキュリティで保護された接続を提供します。 リモート デスクトップ (RDP) トラフィックを許可するには、トラフィックのソースがネットワーク セキュリティ グループ (NSG) に定義されている必要があります。

このソリューションでは、次の構成を持つドメイン参加済み要塞ホストとして仮想マシンを作成します。
-   [マルウェア対策拡張機能](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [OMS 拡張機能](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure 診断拡張機能](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   Azure Key Vault を使用した [Azure ディスクの暗号化](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)
-   未使用時の仮想マシン リソースの消費を抑えるための[自動シャットダウン ポリシー](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) の有効化。実行中のオペレーティング システムから分離されている保護された環境で、資格情報とその他のシークレットが実行されるようにします。

### <a name="virtual-network"></a>仮想ネットワーク
この参照アーキテクチャは、アドレス空間が 10.0.0.0/16 であるプライベート仮想ネットワークを定義します。

**ネットワーク セキュリティ グループ**: [NSG](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) には、VNet 内のトラフィックを許可または拒否するアクセス制御リスト (ACL) が含まれます。 NSG を使用して、サブネットまたは個々の VM レベルでトラフィックを保護できます。 次の NSG が存在します。
  - データ層 (SQL Server クラスター、SQL Server 監視、および SQL Load Balancer) 用の NSG
  - 管理要塞ホスト用の NSG
  - Active Directory 用の NSG
  - SQL Server Reporting Services 用の NSG

各 NSG では、ソリューションが安全かつ適切に機能できるように、固有のポートとプロトコルが開かれます。 さらに、各 NSG で次の構成が有効になります。
  - [診断ログとイベント](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)が有効化され、ストレージ アカウントに格納される
  - OMS Log Analytics が [NSG の診断](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)に接続される

**サブネット**: 各サブネットは、対応する NSG に関連付けられています。

### <a name="data-at-rest"></a>保存データ
このアーキテクチャでは、暗号化やデータベース監査などの複数の手段によって保存データを保護します。

**Azure Storage**: 暗号化された保存データの要件を満たすために、すべての [Azure Storage](https://azure.microsoft.com/services/storage/) で [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption) が使用されます。 これは、GDPR によって定義されている組織のセキュリティ コミットメントとコンプライアンス要件のサポートにおいて個人データを保護するために役立ちます。

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) は、Windows の BitLocker 機能を活用して、OS とデータ ディスクのボリューム暗号化を提供します。 このソリューションは、ディスク暗号化キーの制御と管理を実行できるように、Azure Key Vault と統合されます。

**Azure SQL Database**: Azure SQL Database インスタンスは、次のデータベース セキュリティ対策を使用します。
-   [AD 認証と承認](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)を使用して、データベース ユーザーの ID 管理と他の Microsoft サービスを一元管理できます。
-   [SQL Database の監査](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started)では、データベース イベントを追跡し、Azure Storage アカウントの監査ログにイベントを書き込みます。
-   SQL Database は、[Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) を使用するように構成されます。TDE は、データベース、関連付けられたバックアップ、およびトランザクション ログ ファイルのリアルタイムの暗号化と暗号化解除を実行して保存情報を保護します。 TDE は、保存されている個人データが未承認のアクセスの対象になっていないことを保証します。
-   [ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)は、適切なアクセス許可が付与されていない限り、データベース サーバーへのすべてのアクセスを阻止します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。
-   [SQL の脅威検出](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)は、不審なデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃、および異常なデータベース アクセス パターンに対するセキュリティの警告を提供することで、潜在的な脅威の検出とそれらの脅威への対応を可能にします。
-   [常に暗号化された列](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)は、データベース システム内の機微な個人データがプレーンテキストとして表示されることがないことを保証します。 データ暗号化を有効にした後は、キーへのアクセス権を持つクライアント アプリケーションまたはアプリケーション サーバーのみが、プレーンテキスト データにアクセスできます。
- [拡張プロパティ](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql)機能を使用して、データ サブジェクトの処理を中止できます。この機能により、カスタム プロパティをデータベース オブジェクトに追加し、データを "中止" とタグ付けして、関連付けられた個人データの処理を防ぐアプリケーション ロジックをサポートできます。
- [行レベルのセキュリティ](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)によって、データへのアクセスを制限するポリシーを定義して、処理を中止できます。
- [SQL Database 動的データ マスク (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) は、特権のないユーザーまたはアプリケーションに対してデータをマスクすることで機微な個人データの露出を制限します。 DDM は、潜在的に機微なデータを自動的に検出し、適用する適切なマスクを提案できます。 これは、GDPR 保護の対象となる個人データを識別し、不正アクセスによるデータベースの終了が発生しないようにアクセスを削減するために役立ちます。 **注: お客様は、お使いのデータベース スキーマに準拠するように DDM 設定を調整する必要があります。**

### <a name="identity-management"></a>ID 管理
次のテクノロジによって、Azure 環境での個人データへのアクセスを管理する機能が提供されます。
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) は、Microsoft が提供する、マルチテナントに対応したクラウドベースのディレクトリおよび ID 管理サービスです。 このソリューションのすべてのユーザーは、SQL Database にアクセスするユーザーも含めて、AAD 内に作成されます。
-   アプリケーションに対する認証は AAD を使用して行われます。 詳細については、「[Azure Active Directory とアプリケーションの統合](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)」をご覧ください。 さらに、データベース列の暗号化では、AAD を使用して、アプリケーションを Azure SQL Database に対して認証します。 詳細については、[SQL Database 内の機微なデータを保護する](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)方法に関するページを参照してください。
-   [Azure ロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) によって、管理者は、業務の遂行に必要なアクセスのみをユーザーに付与するきめの細かいアクセス許可を定義できます。 すべてのユーザーに Azure リソースへの無制限のアクセス許可を付与する代わりに、管理者は、個人データにアクセスするための特定のアクションのみを許可できます。 サブスクリプションへのアクセスは、サブスクリプションの管理者に制限されます。
- [AAD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) により、特定の情報 (個人情報など) にアクセスできるユーザーの数を最小限に抑えることができます。  管理者は、AAD Privileged Identity Management を使用して、特権 ID と特権 ID によるリソースへのアクセスを検出、制限、および監視できます。 この機能を使用して、必要に応じて、オンデマンドの Just-In-Time 管理アクセスを適用することもできます。
- [AAD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) は、組織の ID に影響を及ぼす可能性のある脆弱性を検出し、組織の ID に関連する検出された不審なアクションに対する自動応答を構成します。さらに、不審なインシデントを調査し、適切なアクションを実行してそれらを解決します。

### <a name="security"></a>セキュリティ
**シークレットの管理**: ソリューションでは、キーとシークレットの管理に [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) を使用します。 Azure Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。 次の Azure Key Vault 機能は、個人データとそのようなデータへのアクセスを保護するために役立ちます。
- 必要に応じて、高度なアクセス ポリシーが構成されます。
- Key Vault のアクセス ポリシーは、キーとシークレットに対する最低限必要なアクセス許可で定義されます。
- Key Vault のすべてのキーとシークレットに有効期限があります。
- Key Vault 内のすべてのキーは、特殊なハードウェア セキュリティ モジュール (HSM) によって保護されます。 キーの種類は、HSM 保護された 2048 ビット RSA キーです。
- RBAC を使用して、すべてのユーザーと ID に最低限必要なアクセス許可が付与されます。
- Key Vault の診断ログは、少なくとも 365 日のリテンション期間で有効になっています。
- キーに対して許可される暗号化操作は必要なものに制限されます。

**パッチ管理**: この参照アーキテクチャの一部としてデプロイされる Windows 仮想マシンは、Windows Update サービスから自動的に更新プログラムを受け取るように、既定で構成されます。 また、このソリューションには、OMS [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) サービスが含まれます。このサービスによって、必要に応じて更新されたデプロイを作成して、仮想マシンに修正プログラムを適用できます。

**マルウェア対策**: 仮想マシン向けの [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) は、ウイルスやスパイウェアなどの悪意のあるソフトウェアを識別して削除するリアルタイム保護機能を提供し、既知のマルウェアや不要なソフトウェアが保護されている仮想マシンへのインストールまたは実行を試みた場合に警告する構成可能なアラートを備えています。

**セキュリティ アラート**: [Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro) により、トラフィックの監視、ログの収集、および脅威のデータ ソース分析を実行できます。 さらに、Azure Security Center は、Azure サービスの既存の構成にアクセスして、セキュリティ状況の改善と個人データの保護に役立つ、構成とサービスに関する推奨事項を提供します。 Azure Security Center には、インシデント対応チームによる脅威の調査と修復を支援するために、検出された脅威に関する情報を含む[脅威インテリジェンス レポート](https://docs.microsoft.com/en-us/azure/security-center/security-center-threat-report)が用意されています。

### <a name="business-continuity"></a>ビジネス継続性
**高可用性**: サーバー ワークロードは、Azure の仮想マシンの高可用性を確保するために[可用性セット](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にグループ化されます。 Azure SLA を 99.95% 満たすように、計画または計画外メンテナンス イベントの間に、少なくとも 1 つの仮想マシンが利用可能です。

**Recovery Services コンテナー**: [Recovery Services コンテナー](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview)はバックアップ データを保管し、このアーキテクチャの Azure Virtual Machines のすべての構成を保護します。 Recovery Services コンテナーを使用すると、VM 全体を復元せずに IaaS VM からファイルとフォルダーを復元できるため、復元時間を短縮できます。

### <a name="logging-and-auditing"></a>ログ記録と監査
[Operations Management Suite (OMS)](https://docs.microsoft.com/en-us/azure/operations-management-suite/operations-management-suite-overview) は、システムの正常性だけではなく、システムとユーザーのアクティビティを幅広く記録します。 OMS [Log Analytics](https://azure.microsoft.com/services/log-analytics/) ソリューションは、Azure やオンプレミス環境のリソースによって生成されるデータを収集して分析します。
- **アクティビティ ログ**: [アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) は、サブスクリプションのリソースに対して実行された操作に関する分析情報を提供します。 アクティビティ ログは、操作のイニシエーター、発生時刻、および状態の判断に役立ちます。
- **診断ログ**: [診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)には、各リソースによって出力されるすべてのログが含まれます。 これらのログには、Windows イベント システム ログと、Azure Blob Storage、テーブル、キューのログが含まれます。
- **ログのアーカイブ**: すべての診断ログは、暗号化され、集中管理された Azure Storage アカウントに書き込まれ、アーカイブされます。 リテンション期間には、組織固有の保有要件を満たすために最長 730 日までの日数をユーザーが設定できます。 これらのログは、処理、格納、およびダッシュボードのレポート化を行うために、Azure Log Analytics に接続されます。

さらに、このアーキテクチャの一部として、次の OMS ソリューションが含まれます。
-   [AD Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): この Active Directory 正常性チェック ソリューションは、一定の間隔でサーバー環境のリスクと正常性を評価し、デプロイされたサーバー インフラストラクチャに固有の推奨事項を重要度別に示した一覧を提供します。
-   [Antimalware Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): このマルウェア対策ソリューションは、マルウェア、脅威、および保護の状態に関する報告を行います。
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Azure Automation ソリューションは、Runbook の格納、実行、および管理を行います。
-   [セキュリティおよび監査](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): セキュリティおよび監査ダッシュボードは、セキュリティ ドメイン、注意が必要な問題、検出、脅威インテリジェンス、および一般的なセキュリティの照会に関するメトリックを示すことで、リソースのセキュリティ状態に対する高度な分析情報を提供します。
-   [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): この SQL 正常性チェック ソリューションは、一定の間隔でサーバー環境のリスクと正常性を評価し、デプロイされたサーバー インフラストラクチャに固有の推奨事項を重要度別に示した一覧を提供します。
-   [Update Management](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): Update Management ソリューションは、利用可能な更新プログラムの状態や必要な更新プログラムのインストール プロセスなど、オペレーティング システムのセキュリティ更新プログラムを管理できるようにします。
-   [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Agent Health ソリューションは、デプロイされたエージェント数とその地理的配置に加え、応答しないエージェント数やオペレーショナル データを送信しているエージェント数を報告します。
-   [Azure Activity Logs](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Activity Log Analytics ソリューションは、すべての Azure サブスクリプションにわたる Azure アクティビティ ログの分析に役立ちます。
-   [Change Tracking](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Change Tracking ソリューションは、環境の変更を簡単に識別できるようにします。

## <a name="threat-model"></a>脅威モデル

この参照アーキテクチャのデータ フロー ダイアグラムを[ダウンロード](https://aka.ms/gdprDWdfd)するか、下記を参照してください。 このモデルは、修正を行う際に、システム インフラストラクチャの潜在的なリスクを理解するために役立ちます。

![GDPR 用データ ウェアハウス脅威モデル](images/gdpr-datawarehouse-threat-model.png?raw=true "GDPR 用データ ウェアハウス脅威モデル")

## <a name="compliance-documentation"></a>コンプライアンス ドキュメント
[Azure のセキュリティとコンプライアンスのブループリント - GDPR: お客様の責任マトリックス](https://aka.ms/gdprCRM)は、すべての GDPR 記事におけるデータ管理者とデータ処理者の責任を一覧表示します。 Azure サービスでは、通常は、お客様がデータ管理者の役割を、Microsoft がデータ処理者の役割を務めることに注意してください。

[Azure のセキュリティとコンプライアンスのブループリント - GDPR: データ ウェアハウス実装マトリックス](https://aka.ms/gdprDW)は、データ ウェアハウス アーキテクチャによって対応される GDPR の条項に関する情報を示します。各条項で取り上げられている要件に適合する実装の詳細な説明も含まれています。

## <a name="guidance-and-recommendations"></a>ガイダンスと推奨事項
### <a name="vpn-and-expressroute"></a>VPN と ExpressRoute
セキュリティ保護された VPN トンネルまたは [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) は、デプロイされるリソースへの接続を安全に確立するために、このデータ ウェアハウス参照アーキテクチャの一部として構成される必要があります。 ExpressRoute または VPN を適切に設定することで、転送中のデータに対して保護のレイヤーを追加できます。

Azure を使用してセキュリティ保護された VPN トンネルを実装することにより、オンプレミス ネットワークと Azure 仮想ネットワークの間で仮想プライベート接続を作成できます。 この接続はインターネットをまたいでいるため、自社ネットワークと Azure の間の暗号化されたリンクの "トンネル" を通して情報を送信できます。 サイト間 VPN は、数十年にわたってあらゆる規模の企業に導入されている安全で成熟したテクノロジです。 このオプションでは、暗号化メカニズムとして [IPsec トンネル モード](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10))が使用されています。

VPN トンネル内のトラフィックはサイト間 VPN を使用してインターネット上を転送されるため、Microsoft は、さらに高いセキュリティで保護されたもう 1 つの接続オプションを提供します。 Azure ExpressRoute は、オンプレミスの場所または Exchange ホスティング プロバイダーと Azure の間に確立される専用 WAN リンクです。 ExpressRoute 接続はインターネットを経由しないため、これらの接続はインターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。 さらに、これはお客様の通信プロバイダーの直接接続であり、データがインターネット上を移動しないため、インターネットに公開されることはありません。

オンプレミス ネットワークを Azure に拡張するセキュアなハイブリッド ネットワークを実装するためのベスト プラクティスが[提供されています](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

### <a name="extract-transform-load-etl-process"></a>抽出、変換、読み込み (ETL) プロセス
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) は、別の ETL やインポート ツールなしで Azure SQL Data Warehouse にデータを読み込むことができます。 PolyBase では、T-SQL クエリを使用してデータにアクセスできます。 Microsoft のビジネス インテリジェンスと分析スタックに加え、SQL Server 対応のサードパーティ ツールを PolyBase で使用できます。

### <a name="azure-active-directory-setup"></a>Azure Active Directory の設定
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) は、デプロイの管理と環境を操作する担当者へのアクセスのプロビジョニングを行うために不可欠です。 既存の Windows Server Active Directory は、[4 回のクリック](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express)で AAD と統合できます。 また、デプロイされた Active Directory インフラストラクチャを AAD フォレストのサブドメインにすることで、デプロイされた Active Directory インフラストラクチャ (ドメイン コントローラー) を既存の AAD に連結できます。

### <a name="optional-services"></a>省略可能なサービス
Azure では、フォーマットされたデータとフォーマットされていないデータの格納とステージングを支援する多様なサービスを提供しています。 お客様の要件に応じて、次のサービスをこの参照アーキテクチャに追加できます。
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) は、ETL (抽出 - 変換 - 読み込み)、ELT (抽出 - 読み込み - 変換)、およびデータ統合という複雑なハイブリッド プロジェクト用に構築されたマネージド クラウド サービスです。 Azure Data Factory には、個人データのトレースと検索に役立つ機能があり、監視データの到着と送信元を識別する視覚化と監視ツールが含まれています。 Azure Data Factory を使用して、異なるデータ ストアからデータを取り込む、パイプラインと呼ばれるデータ駆動型ワークフローを作成してスケジュールを設定できます。 これらのパイプラインを使用して、内部ソースと外部ソースの両方からデータを取り込むことができます。 その後、データの処理と変換を行って、Azure SQL Data Warehouse などのデータ ストアに出力できます。
- 非構造化データを [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) にステージングできます。これにより、運用上の予備的な分析用の単一の場所で、サイズ、種類、および取り込み速度に関係なくデータをキャプチャできます。  Azure Data Lake には、データの抽出と変換を可能にする機能があります。 Azure Data Lake Store は Hadoop エコシステムのほとんどのオープン ソース コンポーネントと互換性があり、Azure SQL Data Warehouse などの他の Azure サービスと緊密に統合しています。

## <a name="disclaimer"></a>免責事項

 - このドキュメントは、参考目的でのみ使用してください。 マイクロソフトは、本文書の情報に対して、明示的、黙示的、法的であるかを問わず、一切の保証を行わないものとします。 このドキュメントは "現状のまま" 提供されます。 このドキュメントに記載された情報と見解は、URL やその他のインターネット Web サイトの参照も含め、予告なく変更する可能性があります。 このドキュメントをお読みになったお客様は、自己責任でドキュメントをご利用ください。
 - このドキュメントは、Microsoft 製品に含まれる知的財産に対するいかなる法的権利もお客様に提供するものではありません。
 - お客様の社内での参照目的に限り、このドキュメントを複製し使用することができます。
 - このドキュメントに記載されている特定の推奨事項に従った結果、Azure でのデータ、ネットワーク、コンピューティング リソースの使用量が増加したり、ライセンス コストやサブスクリプション コストが増加したりする場合があります。
 - このアーキテクチャは、特定の要件に合わせて調整するための基礎として使用されることを目的としており、そのまま運用環境に適用しないでください。
 - このドキュメントは、参照資料として作成されており、お客様が特定のコンプライアンス要件や規制に適合するためのすべての手段を定義するために使用されるべきではありません。 お客様は、承認された顧客実装について、組織の法務課からのアドバイスを受けてください。
