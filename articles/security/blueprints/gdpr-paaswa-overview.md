---
title: 'Azure のセキュリティとコンプライアンスのブループリント: GDPR のための PaaS Web アプリケーション'
description: 'Azure のセキュリティとコンプライアンスのブループリント: GDPR のための PaaS Web アプリケーション'
services: security
author: jomolesk
ms.assetid: 229759a1-f984-4985-a3fd-3719a7d1c8ff
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: 805c983c0c2db4256dade641f32363ca6514023c
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53600813"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-gdpr"></a>Azure のセキュリティとコンプライアンスのブループリント: GDPR のための PaaS Web アプリケーション

## <a name="overview"></a>概要
一般データ保護規則 (GDPR) には、個人情報の収集、保管、および使用に関する多くの要件があり、個人データの特定と保護、透明性要件への対応、個人データの侵害の検知と報告、およびプライバシー管理者と他の従業員のトレーニングを組織がどのように実施するかについての要件が含まれています。 GDPR は、個人による個人データの管理能力を強め、個人データの収集、処理、または分析を行う組織に対して多数の新しい義務を課しています。 GDPR は、欧州連合 (EU) の居住者に商品やサービスを提供する組織、または EU の居住者に結び付くデータの収集と分析を行う組織を対象とする新しい規則です。 GDPR は、組織の所在地に関係なく適用されます。

Microsoft では、業界をリードするセキュリティ対策とプライバシー ポリシーを用いてクラウド内のデータを保護するように Azure を設計しています。この中に、GDPR によって識別される個人データのカテゴリも含まれています。 Microsoft の[契約条項](https://aka.ms/Online-Services-Terms)は、Microsoft がデータ処理者の要件に準拠していることを明言しています。

このAzure のセキュリティとコンプライアンスのブループリントでは、インターネットに接続する単純な Web アプリケーションに適している サービスとしてのプラットフォーム (PaaS) 環境をデプロイするためのガイダンスを提供します。 このソリューションは、GDPR の特定のセキュリティとコンプライアンスの要件を満たすことができる方法を例示し、Azure で独自の PaaS Web アプリケーション ソリューションをビルドして構成するための基礎として機能します。 GDPR コンプライアンスの実現に向けて、お客様はこの参照アーキテクチャを活用し、Microsoft の [4 段階のプロセス](https://aka.ms/gdprebook)を取り入れることができます。
1. 検出:どのような個人データがどこに存在するかを識別します。
2. 管理:個人データがどのように使用され、アクセスされるかを制御します。
3. 保護:脆弱性とデータ侵害の防止、検知、および対応を行うためのセキュリティ コントロールを確立します。
4. 報告:必要なドキュメントを保持し、データ要求と侵害通知を管理します。

この参照アーキテクチャ、関連する実装ガイド、および脅威モデルは、お客様固有の要件に適合させるための基礎として使用されることを目的としており、運用環境でそのまま使用することはできません。 以下の点に注意してください。
- このアーキテクチャは、GDPR に準拠した方法でお客様が Azure にワークロードをデプロイするためのベースラインを提供します。
- お客様は、このアーキテクチャを使用してビルドしたソリューションの適切なセキュリティとコンプライアンスの評価を実施する責任を負います。これは、要件がお客様の実装によって変化する可能性があるからです。

## <a name="architecture-diagram-and-components"></a>アーキテクチャ ダイアグラムとコンポーネント
このソリューションは、Azure SQL Database バックエンドがある PaaS Web アプリケーション向けのリファレンス アーキテクチャを用意します。 Web アプリケーションは、分離された Azure App Service Environment でホストされます。これは、Azure データセンター内のプライベートな専用環境です。 この環境では、Azure によって管理されている VM 間に Web アプリケーションのトラフィックを負荷分散します。 このアーキテクチャには、ネットワーク セキュリティ グループ、Application Gateway、Azure DNS、および Load Balancer も含まれています。 また、Azure Monitor ではシステム正常性のリアルタイム分析も提供されます。 **Azure では、参照アーキテクチャのサブネットに対する管理とデータ インポートのために、VPN または ExpressRoute 接続を構成することを推奨しています。**

![GDPR のための PaaS Web アプリケーションの参照アーキテクチャ図](images/gdpr-paaswa-architecture.png?raw=true "GDPR のための PaaS Web アプリケーションの参照アーキテクチャ図")

このソリューションでは、次の Azure サービスを使用します。 デプロイ アーキテクチャについて詳しくは、「[デプロイ アーキテクチャ](#deployment-architecture)」セクションをご覧ください。

- Azure Active Directory (AAD)
- Azure Key Vault
- Azure SQL Database
- Application Gateway
    - (1) WAF Application Gateway が有効
        - ファイアウォール モード:防止
        - ルール セット:OWASP 3.0
        - リスナー: ポート 443
- Azure の仮想ネットワーク
- ネットワーク セキュリティ グループ
- Azure DNS
- Azure Storage
- Azure Monitor
- Application Insights
- Azure Security Center
- App Service Environment v2
- Azure Load Balancer
- Azure Web アプリ
- Azure Resource Manager

## <a name="deployment-architecture"></a>デプロイメント アーキテクチャ
次のセクションで、デプロイと実装の要素について詳しく説明します。

**Azure Resource Manager**:[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) によって、ソリューション内の複数のリソースを 1 つのグループとして操作できます。 ソリューションのこれらすべてのリソースを、1 回の連携した操作でデプロイ、更新、または削除できます。 デプロイメントにはテンプレートを使用しますが、このテンプレートは、テスト、ステージング、運用環境などのさまざまな環境に使用できます。 Resource Manager には、デプロイ後のリソースの管理に役立つ、セキュリティ、監査、タグ付けの機能が用意されています。

**App Service Environment v2**:[Azure App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro) は、App Service アプリケーションを大規模かつ安全に実行するために完全に分離された専用の環境を提供する、App Service の機能です。

ASE は、単一の顧客のアプリケーションだけを実行するために分離され、常に仮想ネットワークにデプロイされます。 顧客は、受信および送信アプリケーション ネットワーク トラフィックをきめ細かく制御できます。また、アプリケーションは、オンプレミスの企業リソースへの仮想ネットワーク経由のセキュリティで保護された高速接続を確立できます。

このアーキテクチャに ASE を使用することで、以下のコントロール/構成が可能になりました。

- セキュリティ保護された Azure Virtual Network 内でのホストと、ネットワーク セキュリティ規則
- HTTPS 通信用の自己署名 ILB 証明書で構成された ASE
- [内部負荷分散モード](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer) (モード 3)
- [TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings) の無効化
- [TLS 暗号](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)の変更
- [受信トラフィック N/W ポート](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)のコントロール
- [WAF – データの制限](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- [Azure SQL Database トラフィック](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)の許可

**Azure Web アプリ**:[Azure App Service](https://docs.microsoft.com/azure/app-service/) を使用すると、お客様はインフラストラクチャを管理することなく、任意のプログラミング言語で Web アプリケーションを構築し、ホストできます。 Azure Web Apps では、自動スケールと高可用性が実現されるほか、Windows と Linux の両方がサポートされています。さらに、GitHub、Azure DevOps、または任意の Git リポジトリからの自動デプロイが可能になります。

### <a name="virtual-network"></a>Virtual Network
このアーキテクチャは、アドレス空間が 10.200.0.0/16 であるプライベート仮想ネットワークを定義します。

**ネットワーク セキュリティ グループ**:[NSG](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) には、VNet 内のトラフィックを許可または拒否するアクセス制御リスト (ACL) が含まれます。 NSG を使用して、サブネットまたは個々の VM レベルでトラフィックを保護できます。 次の NSG が存在します。
- Application Gateway 用の 1 つの NSG
- App Service Environment 用の 1 つの NSG
- Azure SQL Database 用の 1 つの NSG

各 NSG では、ソリューションが安全かつ適切に機能できるように、固有のポートとプロトコルが開かれます。 さらに、各 NSG で次の構成が有効になります。
  - [診断ログとイベント](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)が有効化され、ストレージ アカウントに格納される
  - Log Analytics が [NSG の診断](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)に接続される

**サブネット**:各サブネットは、対応する NSG に関連付けられています。

**Azure DNS**:ドメイン ネーム システム (DNS) は、Web サイトまたはサービスの名前をその IP アドレスに変換する (または解決する) 役割を担います。 [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) は、DNS ドメインのホスティング サービスであり、Azure インフラストラクチャを使用した名前解決を提供します。 Azure でドメインをホストすることで、その他の Azure サービスと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。 Azure DNS は、プライベート DNS ドメインもサポートします。

**Azure Load Balancer**:[Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) によって、アプリケーションを拡張し、サービスを高可用性にすることができます。 ロード バランサーは、受信と送信のどちらのシナリオもサポートし、低遅延と高スループットを実現できるだけでなく、あらゆる TCP アプリケーションと UDP アプリケーションの数百万ものフローにスケールアップできます。

### <a name="data-in-transit"></a>転送中のデータ
Azure では、Azure データセンターとの間のすべての通信を既定で暗号化します。 Azure Portal での Azure Storage に対するすべてのトランザクションは HTTPS 経由で行われます。

### <a name="data-at-rest"></a>保存データ

このアーキテクチャでは、暗号化、データベース監査などの手段によって保存データを保護します。

**Azure Storage**:暗号化された保存データの要件を満たすために、すべての [Azure Storage](https://azure.microsoft.com/services/storage/) で [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption) が使用されます。 これは、GDPR によって定義されている組織のセキュリティ コミットメントとコンプライアンス要件のサポートにおいて個人データを保護するために役立ちます。

**Azure Disk Encryption**
: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) は、Windows の BitLocker 機能を活用して、OS とデータ ディスクのボリュームを暗号化します。 このソリューションは、ディスクの暗号化キーを制御および管理できるように、Azure Key Vault と統合されています。

**Azure SQL Database**:Azure SQL Database インスタンスは、次のデータベース セキュリティ対策を使用します。
-   [AD 認証と承認](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)を使用して、データベース ユーザーの ID 管理と他の Microsoft サービスを一元管理できます。
-   [SQL Database の監査](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started)では、データベース イベントを追跡し、Azure Storage アカウントの監査ログにイベントを書き込みます。
-   Azure SQL Database は、[Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) を使用するように構成されます。TDE は、データベース、関連付けられたバックアップ、およびトランザクション ログ ファイルのリアルタイムの暗号化と暗号化解除を実行して保存情報を保護します。 TDE は、保存されている個人データが未承認のアクセスの対象になっていないことを保証します。
-   [ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)は、適切なアクセス許可が付与されていない限り、データベース サーバーへのすべてのアクセスを阻止します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。
-   [SQL の脅威検出](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)は、不審なデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃、および異常なデータベース アクセス パターンに対するセキュリティの警告を提供することで、潜在的な脅威の検出とそれらの脅威への対応を可能にします。
-   [常に暗号化された列](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)は、データベース システム内の機微な個人データがプレーンテキストとして表示されることがないことを保証します。 データ暗号化を有効にした後は、キーへのアクセス権を持つクライアント アプリケーションまたはアプリケーション サーバーのみが、プレーンテキスト データにアクセスできます。
- [拡張プロパティ](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql)を使用して、データ サブジェクトの処理を中止できます。カスタム プロパティをデータベース オブジェクトに追加し、データを "中止" とタグ付けして、関連付けられた個人データの処理を防ぐアプリケーション ロジックをサポートできます。
- [行レベルのセキュリティ](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)によって、データへのアクセスを制限するポリシーを定義して、処理を中止できます。
- [SQL Database 動的データ マスク (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) は、特権のないユーザーまたはアプリケーションに対してデータをマスクすることで機微な個人データの露出を制限します。 DDM は、潜在的に機微なデータを自動的に検出し、適用する適切なマスクを提案できます。 これは、GDPR 保護の対象となる個人データを識別し、不正アクセスによるデータベースの終了が発生しないようにアクセスを削減するために役立ちます。 **注:お客様は、お使いのデータベース スキーマに準拠するように DDM 設定を調整する必要があります。**

### <a name="identity-management"></a>ID 管理
次のテクノロジによって、Azure 環境での個人データへのアクセスを管理する機能が提供されます。
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) は、Microsoft が提供する、マルチテナントに対応したクラウドベースのディレクトリおよび ID 管理サービスです。 このソリューションのすべてのユーザーは、Azure SQL Database にアクセスするユーザーも含めて、AAD 内に作成されます。
-   アプリケーションに対する認証は AAD を使用して行われます。 詳細については、「[Azure Active Directory とアプリケーションの統合](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)」をご覧ください。 さらに、データベース列の暗号化では、AAD を使用して、アプリケーションを Azure SQL Database に対して認証します。 詳細については、[SQL Database で機微なデータを保護する](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)方法に関するページを参照してください。
-   [Azure ロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) によって、管理者は、業務の遂行に必要なアクセスのみをユーザーに付与するきめの細かいアクセス許可を定義できます。 すべてのユーザーに Azure リソースへの無制限のアクセス許可を付与する代わりに、管理者は、個人データにアクセスするための特定のアクションのみを許可できます。 サブスクリプションへのアクセスは、サブスクリプションの管理者に制限されます。
- [AAD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) により、お客様は特定の情報 (個人情報など) にアクセスできるユーザーの数を最小限に抑えることができます。  管理者は、AAD Privileged Identity Management を使用して、特権 ID と特権 ID によるリソースへのアクセスを検出、制限、および監視できます。 この機能を使用して、必要に応じて、オンデマンドの Just-In-Time 管理アクセスを適用することもできます。
- [AAD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) は、組織の ID に影響を及ぼす可能性のある脆弱性を検出し、組織の ID に関連する検出された不審なアクションに対する自動応答を構成します。さらに、不審なインシデントを調査し、適切なアクションを実行してそれらを解決します。

### <a name="security"></a>セキュリティ
**シークレットの管理**: ソリューションでは、キーとシークレットの管理に [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) を使用します。 Azure Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。 次の Azure Key Vault 機能は、お客様が個人データとそのようなデータへのアクセスを保護するために役立ちます。
- 必要に応じて、高度なアクセス ポリシーが構成されます。
- Key Vault のアクセス ポリシーは、キーとシークレットに対する最低限必要なアクセス許可で定義されます。
- Key Vault のすべてのキーとシークレットに有効期限があります。
- Key Vault 内のすべてのキーは、特殊なハードウェア セキュリティ モジュール (HSM) によって保護されます。 キーの種類は、HSM 保護された 2048 ビット RSA キーです。
- RBAC を使用して、すべてのユーザーと ID に最低限必要なアクセス許可が付与されます。
- Key Vault の診断ログは、少なくとも 365 日のリテンション期間で有効になっています。
- キーに対して許可される暗号化操作は必要なものに制限されます。

**セキュリティのアラート**:[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) により、お客様はトラフィックの監視、ログの収集、および脅威のデータ ソース分析を実行できます。 さらに、Azure Security Center は、Azure サービスの既存の構成にアクセスして、セキュリティ状況の改善と個人データの保護に役立つ、構成とサービスに関する推奨事項を提供します。 Azure Security Center には、インシデント対応チームによる脅威の調査と修復を支援するために、検出された脅威に関する情報を含む[脅威インテリジェンス レポート](https://docs.microsoft.com/azure/security-center/security-center-threat-report)が用意されています。

**Application Gateway**: このアーキテクチャでは、OWASP ルールセットを有効にした、Web アプリケーション ファイアウォール (WAF) がある Application Gateway を使用してセキュリティの脆弱性のリスクを軽減します。 その他の機能には次のものがあります。

- [エンド ツー エンド SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL オフロード](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)の有効化
- [TLS v1.0 および v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) の無効化
- [Web アプリケーション ファイアウォール](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (WAF モード)
- OWASP 3.0 ルールセットを使用した[防止モード](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal)
- [診断ログ](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)の有効化
- [カスタム正常性プローブ](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) と [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) による、その他の保護と通知の提供。 Azure Security Center は評価システムも提供します。

### <a name="logging-and-auditing"></a>ログ記録と監査

Azure Monitor は、システムの正常性だけではなく、システムとユーザーのアクティビティを詳細に記録します。 これは Azure やオンプレミス環境のリソースによって生成されるデータを収集して分析します。
- **[アクティビティ ログ]**:[アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)は、サブスクリプションのリソースに対して実行された操作に関する分析情報を提供します。 アクティビティ ログは、操作のイニシエーター、発生時刻、および状態の判断に役立ちます。
- **診断ログ**:[診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)には、各リソースによって出力されるすべてのログが含まれます。 これらのログには、Windows イベント システム ログ、Azure Storage ログ、Key Vault 監査ログ、および Application Gateway のアクセス ログとファイアウォール ログが含まれます。
- **ログのアーカイブ**:すべての診断ログは、暗号化され、集中管理された Azure Storage アカウントに書き込まれ、アーカイブされます。 リテンション期間には、組織固有の保有要件を満たすために最長 730 日までの日数をユーザーが設定できます。 これらのログは、処理、格納、およびダッシュボードのレポート化を行うために、Azure Log Analytics に接続されます。

さらに、このアーキテクチャの一部として、次の監視ソリューションが含まれます。
-   [AD Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment):Active Directory 正常性チェック ソリューションは、一定の間隔でサーバー環境のリスクと正常性を評価し、デプロイされたサーバー インフラストラクチャに固有の推奨事項を重要度別に示した一覧を提供します。
-   [Antimalware Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware):このマルウェア対策ソリューションは、マルウェア、脅威、および保護の状態に関する報告を行います。
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker):Azure Automation ソリューションは、Runbook の格納、実行、および管理を行います。 このソリューションでは、Runbook を使用して、Application Insights と Azure SQL Database からログを収集できます。
-   [Security and Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started):Security and Audit ダッシュボードは、セキュリティ ドメイン、注意が必要な問題、検出、脅威に関する知識、および一般的なセキュリティの照会に関するメトリックを提供することで、リソースのセキュリティ ステータスに対する高度な分析情報を提供します。
-   [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment):SQL 正常性チェック ソリューションは、一定の間隔でサーバー環境のリスクと正常性を評価し、デプロイされたサーバー インフラストラクチャに固有の推奨事項を重要度別に示した一覧をお客様に提供します。
-   [Update Management](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management):Update Management ソリューションは、利用可能な更新プログラムのステータスや必要な更新プログラムのインストール プロセスなど、オペレーティング システムのセキュリティ更新プログラムをお客様が管理できるようにします。
-   [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth):Agent Health ソリューションは、デプロイされたエージェント数とその地理的配置に加え、応答しないエージェント数やオペレーショナル データを送信しているエージェント数を報告します。
-   [Azure Activity Logs](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity):Activity Log Analytics ソリューションは、顧客向けのすべての Azure サブスクリプションにわたる Azure アクティビティ ログの分析に役立ちます。
-   [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking):Change Tracking ソリューションは、お客様が環境の変更を簡単に識別できるようにします。

**Azure Monitor**
: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) は、監査、アラートの作成、データのアーカイブ、および Azure リソース内の API 呼び出しの追跡を実行できるようにすることで、パフォーマンスの追跡、セキュリティの維持、および傾向の識別を実行できるようにします。

**Application Insights**
: [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) は、複数のプラットフォームで使用できる Web 開発者向けの拡張可能なアプリケーション パフォーマンス管理 (APM) サービスです。 Application Insights は、パフォーマンスの異常を検出します。それを使用して、実行中の Web アプリケーションを監視できます。 組み込まれている強力な分析ツールを使えば、問題を診断し、ユーザーがアプリを使用して実行している操作を把握できます。 それは、パフォーマンスやユーザビリティを継続的に向上させるうえで役立つように設計されています。

## <a name="threat-model"></a>脅威モデル

この参照アーキテクチャのデータ フロー ダイアグラムを[ダウンロード](https://aka.ms/gdprPaaSdfd)するか、下記を参照してください。 このモデルは、修正を行う際に、システム インフラストラクチャの潜在的なリスクを理解するために役立ちます。

![GDPR 脅威モデルのための PaaS Web アプリケーション](images/gdpr-paaswa-threat-model.png?raw=true "GDPR 脅威モデルのための PaaS Web アプリケーション")

## <a name="compliance-documentation"></a>コンプライアンス ドキュメント
[Azure のセキュリティとコンプライアンスのブループリント - GDPR: お客様の責任マトリックス](https://aka.ms/gdprCRM)は、すべての GDPR 記事におけるデータ管理者とデータ処理者の責任を一覧表示します。 Azure サービスでは、通常は、お客様がデータ管理者の役割を、Microsoft がデータ処理者の役割を務めることに注意してください。

[Azure のセキュリティとコンプライアンスの青写真 - GDPR: PaaS Web アプリケーションの実装のマトリックス](https://aka.ms/gdprPaaSWeb)PaaS Web アプリケーション アーキテクチャによって対応される GDPR の条項に関する情報を示します。各条項で取り上げられている要件に適合する実装の詳細な説明も含まれています。


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
