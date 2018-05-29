---
title: 'Azure Security and Compliance Blueprint: FFIEC Financial Services 規制対象ワークロード'
description: 'Azure Security and Compliance Blueprint: FFIEC Financial Services 規制対象ワークロード'
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 17794288-9074-44b5-acc8-1dacceb3f56c
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2018
ms.author: jomolesk
ms.openlocfilehash: f1339af22132d19f14ea8ebb72fe0e6bd45b7fad
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895556"
---
# <a name="azure-security-and-compliance-blueprint---ffiec-financial-services-regulated-workloads"></a>Azure Security and Compliance Blueprint: FFIEC Financial Services 規制対象ワークロード

## <a name="overview"></a>概要

Azure Security and Compliance Blueprint: FFIEC Financial Services 規制対象ワークロードは、クラウドで機微なデータを処理するように設計された、セキュリティで保護され、規格に準拠する、サービスとしてのプラットフォーム (PaaS) Web アプリケーションのデプロイを支援します。 ブループリントは、Microsoft Azure ソリューションの導入を簡素化するために役立つ単純な参照アーキテクチャとデザインを紹介する自動化スクリプトとガイダンスで構成されます。 このブループリントは、クラウドへのデプロイの手間とコストを削減する方法を探し求めている組織のニーズを満たす、エンド ツー エンドのソリューションを示しています。

このブループリントは、米国公認会計士協会によって規定された、SOC 1、SOC 2、PCI DDS (Payment Card Industry Data Security Standards) 協議会の DSS 3.2、機微な金融データの収集、格納、取得のための FFIEC などの厳格な準拠規格の要件を満たすように設計されています。 それは、セキュリティで保護され、規格に準拠した多層環境で金融データを管理するソリューションをデプロイすることで、そのようなデータの適切な処理方法を示しています。 ソリューションは、エンド ツー エンドの Azure ベースの PaaS ソリューションとしてデプロイされます。 

ブループリントは、お客様の出発点として、またセキュリティで保護されたデータをクラウドで管理するための要件を理解するための基盤として使用されることを目的としています。 ソリューションは、運用環境へのデプロイで現状のまま使用すべきではなく、Azure サービスを理解して設計し、デプロイするために使用します。それは、お客様が、セキュリティで保護され、規格に準拠した方法で Microsoft Azure を使用するためのベースラインとして設計されています。

このブループリントに基づくお客様のすべての実稼働ソリューションは、認定監査者によって認証される必要があります。 ソリューションは、お客様の実装と地理的条件によって異なる可能性があります。

このソリューションの機能の概要を簡単に把握するために、ソリューションのデプロイの説明とデモンストレーションを行っている[こちらのビデオ](https://aka.ms/fsiblueprintvideo)をご覧ください。

## <a name="solution-components"></a>ソリューションのコンポーネント

アーキテクチャは次のコンポーネントで構成され、Azure PCI DSS コンプライアンス ソリューションのデプロイ機能を使用します。

- **アーキテクチャ図**:  この図は、Contoso Webstore ソリューションで使用される参照アーキテクチャを示します。
- **デプロイ テンプレート**:  このデプロイでは、[Azure Resource Manager テンプレート](/azure/azure-resource-manager/resource-group-overview#template-deployment) を使用してアーキテクチャのコンポーネントを Microsoft Azure に自動的にデプロイします (これは、セットアップ時に構成パラメーターを指定することによって行います)。
- **自動化されたデプロイ スクリプト**:  これらのスクリプトを使用して、エンド ツー エンド ソリューションをデプロイします。 次のスクリプトが含まれます。
    - モジュールのインストールと[グローバル管理者](/azure/active-directory/active-directory-assign-admin-roles-azure-portal)のセットアップ スクリプト。インストールを行い、必要な PowerShell モジュールとグローバル管理者ロールが正しく構成されていることを確認するために使用します。 
    - インストール PowerShell スクリプト。エンド ツー エンドのソリューションをデプロイするために使用します。事前構築済みのデモ Web アプリケーションと [SQL データベースのサンプル](https://github.com/Microsoft/azure-sql-security-sample)を含む .zip ファイルと .bacpac ファイルによって提供されます。 コンテンツ。 このソリューションのソース コードは、[Payment Processing Blueprint コード リポジトリ][code-repo]でレビュー用に入手できます。 

## <a name="architectural-diagram"></a>アーキテクチャ図

![](images/pci-architectural-diagram.png)

## <a name="user-scenario"></a>ユーザー シナリオ

ブループリントは、次のユース ケースに対応します。

> このシナリオは、架空の Web ストアが機微なデータを PaaS クラウド Azure ベース ソリューションに移行した方法を示しています。 サンプル ソリューションは、基本ユーザー情報と選択した機微なデータの処理と収集を示しています。 この作業は、Azure Security and Compliance Blueprint - PCI DSS 準拠の支払い処理環境から借用されています。 この作業の詳細については、[実装のレビューとガイダンス](https://aka.ms/pciblueprintprocessingoverview)に関するドキュメントに、PCI DSS 準拠環境のレビューが記載されています。

### <a name="use-case"></a>ユース ケース
*Contoso Webstore* という名前の小規模の Web ストアは、顧客の支払い情報を含む金融データをクラウドに移行しようとしています。 

Contoso Webstore の管理者は、この目標を達成するために迅速にデプロイできるソリューションを探しています。 管理者は、この概念実証 (POC) を使用して、Azure で厳格な準拠要件に従ってどのように金融データを収集、格納、取得できるかを利害関係者と話し合おうとしています。

> お客様は、この POC で使用されているアーキテクチャを使用してビルドしたソリューションの適切なセキュリティとコンプライアンスのレビューを実施する責任を負います。要件は、お客様の実装と地理的条件によって変化する可能性があるからです。 

### <a name="elements-of-the-foundational-architecture"></a>基本アーキテクチャの要素

この基本アーキテクチャは、次の架空の要素を使用して設計されています。

ドメイン サイト `contosowebstore.com`

ユース ケースについて説明し、ユーザー インターフェイスを理解するための手がかりとなるユーザー ロールが採用されます。

#### <a name="role-site-and-subscription-admin"></a>ロール: サイトおよびサブスクリプションの管理者

|項目      |例|
|----------|------|
|ユーザー名: |`adminXX@contosowebstore.com`|
| 名前: |`Global Admin Azure PCI Samples`|
|ユーザー タイプ:| `Subscription Administrator and Azure Active Directory Global Administrator`|

- 管理者アカウントは、マスクが解除された金融情報を読み取ることはできません。 すべてのアクションがログ記録されます。
- 管理者アカウントは、SQL Database の管理やログインを実行できません。
- 管理者アカウントは、Active Directory とサブスクリプションを管理できます。

#### <a name="role-sql-administrator"></a>ロール: SQL 管理者

|項目      |例|
|----------|------|
|ユーザー名: |`sqlAdmin@contosowebstore.com`|
| 名前: |`SQLADAdministrator PCI Samples`|
| 名: |`SQL AD Administrator`|
|姓: |`PCI Samples`|
|ユーザー タイプ:| `Administrator`|

- sqladmin アカウントは、フィルター処理されていない金融情報を表示できません。 すべてのアクションがログ記録されます。
- sqladmin アカウントは SQL Database を管理できます。

#### <a name="role-clerk"></a>ロール: 従業員

|項目      |例|
|----------|------|
|ユーザー名:| `receptionist_EdnaB@contosowebstore.com`|
| 名前: |`Edna Benson`|
| 名:| `Edna`|
|姓:| `Benson`|
| ユーザー タイプ: |`Member`|

Edna Benson は受付担当兼、営業部長です。 彼女は、顧客情報が正確であり、課金が完了したことを確認する責任を負っています。 Edna は Contoso Webstore デモ Web サイトとのすべてのやり取りのためにログインしているユーザーです。 Edna は次の権限を持っています。 

- Edna は顧客情報の作成と読み取りを実行できます。
- Edna は顧客情報を変更できます。
- Edna は金融情報を上書きできます。
- Edna アカウントは、フィルター処理されていない金融情報を表示できません。



### <a name="contoso-webstore---estimated-pricing"></a>Contoso Webstore - 推定料金

この基本アーキテクチャおよびサンプル Web アプリケーションでは月額料金と時間あたりの使用コストが発生するため、ソリューションのサイズを決定する際にそれらを考慮する必要があります。 これらのコストは [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用して推定できます。 2017 年 9 月の時点で、このソリューションの毎月の推定コストはおよそ 2500 ドルです。これには ASE v2 に対する毎月 1000 ドルの使用料金が含まれます。 これらのコストは使用量に基づいて変化し、変更される可能性があります。 デプロイ時点でより正確な見積もりのために月間推定コストを計算するのはお客様の責任です。 

このソリューションは次の Azure サービスを使用しています。 デプロイ アーキテクチャの詳細については、「[デプロイメント アーキテクチャ](#deployment-architecture)」セクションをご覧ください。

>- Application Gateway
>- Azure Active Directory
>- App Service Environment v2
>- Log Analytics
>- Azure Key Vault
>- ネットワーク セキュリティ グループ
>- Azure SQL DB
>- Azure Load Balancer
>- Application Insights
>- Azure Security Center
>- Azure Web アプリ
>- Azure Automation
>- Azure Automation Runbooks
>- Azure DNS
>- Azure Virtual Network
>- Azure Virtual Machine
>- Azure リソース グループおよびポリシー
>- Azure Blob Storage
>- Azure Active Directory のロールベースのアクセス制御 (RBAC)

## <a name="deployment-architecture"></a>デプロイメント アーキテクチャ

以下のセクションでは、開発と実装の要素について詳しく説明します。

### <a name="network-segmentation-and-security"></a>ネットワークのセグメント化とセキュリティ

![](images/pci-tiers-diagram.png)

#### <a name="application-gateway"></a>Application Gateway

この基本アーキテクチャでは、OWASP ルールセットを有効にした、Web アプリケーション ファイアウォール (WAF) がある Application Gateway を使用してセキュリティの脆弱性のリスクを軽減します。 その他の機能には次のものがあります。

- [エンド ツー エンド SSL](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL オフロード](/azure/application-gateway/application-gateway-ssl-portal)の有効化
- [TLS v1.0 および v1.1](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) の無効化
- [Web アプリケーション ファイアウォール](/azure/application-gateway/application-gateway-webapplicationfirewall-overview) (WAF モード)
- OWASP 3.0 ルールセットを使用した[防止モード](/azure/application-gateway/application-gateway-web-application-firewall-portal)
- [診断ログ](/azure/application-gateway/application-gateway-diagnostics)の有効化
- [カスタム正常性プローブ](/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) と [Azure Advisor](/azure/advisor/advisor-security-recommendations) による、保護の追加と通知の提供。 Azure Security Center は評価システムも提供します。

#### <a name="virtual-network"></a>Virtual network

この基本アーキテクチャは、10.0.0.0/16 のアドレス空間 を使用してプライベート仮想ネットワークを定義します。

#### <a name="network-security-groups"></a>ネットワーク セキュリティ グループ

各ネットワーク階層に専用のネットワーク セキュリティ グループ (NSG) があります。

- ファイアウォールおよび Application Gateway WAF 用の DMZ ネットワーク セキュリティ グループ
- ジャンプボックス (要塞ホスト) 管理用の NSG
- App Service Environment 用の NSG

各 NSG には、ソリューションの安全かつ適切な操作のために開かれる固有のポートとプロトコルがあります。 

さらに、各 NSG で次の構成を使用できます。

- [診断ログとイベント](/azure/virtual-network/virtual-network-nsg-manage-log)の有効化 (ストレージ アカウントに格納されます) 
- Log Analytics の [NSG の診断](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)への接続

 
#### <a name="subnets"></a>サブネット
 各サブネットが、対応する NSG に関連付けられていることを確認してください。

#### <a name="custom-domain-ssl-certificates"></a>カスタム ドメインの SSL 証明書
 HTTPS トラフィックは、カスタム ドメインの SSL 証明書を使用して有効化されます。

### <a name="data-at-rest"></a>保存データ

このアーキテクチャは、暗号化、データベース監査などの手段を使用して保存データを保護します。

#### <a name="azure-storage"></a>Azure Storage

暗号化された保存データの要件を満たすために、すべての [Azure Storage](https://azure.microsoft.com/services/storage/) で [Storage サービスの暗号化](/azure/storage/storage-service-encryption)が使用されます。

#### <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database インスタンスは、次のデータベース セキュリティ対策を使用します。

- [AD の認証と承認](/azure/sql-database/sql-database-aad-authentication)
- [SQL Database 監査](/azure/sql-database/sql-database-auditing-get-started)
- [透過的なデータ暗号化](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)
- ASE ワーカー プールとクライアント IP の管理を可能にする[ファイアウォール規則](/azure/sql-database/sql-database-firewall-configure)
- [SQL の脅威の検出](/azure/sql-database/sql-database-threat-detection-get-started)
- [Always Encrypted 列](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)
- デプロイ後 PowerShell スクリプトを使用した、[SQL Database の動的データ マスク](/azure/sql-database/sql-database-dynamic-data-masking-get-started)

### <a name="logging-and-auditing"></a>ログ記録と監査

[Log Analytics](https://azure.microsoft.com/services/log-analytics) は Contoso Webstore に、すべてのシステムおよびユーザー アクティビティの広範なログ記録 (財務データのログ記録を含む) を提供できます。 正確性を確保するために、変更内容を確認および検証できます。 

- **アクティビティ ログ。**  [アクティビティ ログ](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)は、サブスクリプション内のリソースに対して実行された操作に関する情報を提供します。
- **診断ログ。**  [診断ログ](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)は、リソースによって出力されるすべてのログです。 これらのログには、Windows イベント システム ログ、Azure Blob Storage ログ、テーブル、キューのログが含まれます。
- **ファイアウォール ログ。**  Application Gateway は、完全な診断ログとアクセス ログを提供します。 ファイアウォール ログは、WAF が有効になっている Application Gateway リソースで使用できます。
- **ログのアーカイブ。**  すべての診断ログは、一元化され暗号化された Azure Storage アカウントに書き込まれ、定義済みのリテンション期間 (2 日間) にわたってアーカイブされるように構成されます。 その後、ログは Azure Log Analytics に接続されて処理、格納、ダッシュボード化されます。 [Log Analytics](https://azure.microsoft.com/services/log-analytics) は、クラウドおよびオンプレミス環境内のリソースで生成されたデータの収集と分析に役立つサービスです。

### <a name="encryption-and-secrets-management"></a>暗号化とシークレットの管理

Contoso Webstore は、すべての機微なデータを暗号化し、Azure Key Vault を使用してキーを管理することで、CHD が取得されるのを防ぎます。

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。 
- [SQL TDE](/sql/relational-databases/security/encryption/transparent-data-encryption) を使用して、すべての顧客の金融データが暗号化されます。
- データは [Azure Disk Encryption](/azure/security/azure-security-disk-encryption) と BitLocker を使用してディスクに格納されます。

### <a name="identity-management"></a>ID 管理

次のテクノロジによって、Azure 環境に ID 管理機能が提供されます。

- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) は、マイクロソフトが提供する、マルチテナントに対応したクラウドベースのディレクトリおよび ID 管理サービスです。 ソリューションのすべてのユーザー (SQL Database にアクセスするユーザーを含む) は、Azure Active Directory で作成されています。
- アプリケーションに対する認証は Azure AD を使用して行われます。 詳細については、「[Azure Active Directory とアプリケーションの統合](/azure/active-directory/develop/active-directory-integrating-applications)」をご覧ください。 さらに、データベース列の暗号化でも、Azure AD を使用して、アプリケーションが Azure SQL Database に対して認証されます。 詳細については、「[Always Encrypted: Protect sensitive data in SQL Database](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)」(Always Encrypted: SQL Database で機密データを保護する) をご覧ください。 
- [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) は、組織の ID に影響する可能性がある潜在的な脆弱性を検出し、組織の ID に関連する検出された疑わしいアクションに対する自動応答を構成し、疑わしいインシデントを調査して適切なアクションを実行して解決します。
- [Azure のロールベースのアクセス制御 (RBAC)](/azure/role-based-access-control/role-assignments-portal) は、Azure の厳密に対象を絞ったアクセス管理を実現します。 サブスクリプションへのアクセスはサブスクリプション管理者に制限され、Azure Key Vault へのアクセスはすべてのユーザーに制限されます。

Azure SQL Database のセキュリティ機能の使用方法の詳細については、「[Contoso Clinic デモ アプリケーション](https://github.com/Microsoft/azure-sql-security-sample)」サンプルをご覧ください。
   
### <a name="web-and-compute-resources"></a>Web およびコンピューティング リソース

#### <a name="app-service-environment"></a>App Service 環境

[Azure App Service](/azure/app-service/) は、Web アプリをデプロイするためのマネージ サービスです。 Contoso Webstore アプリケーションは [App Service Web アプリ](/azure/app-service-web/app-service-web-overview)としてデプロイされます。

[Azure App Service Environment (ASE v2)](/azure/app-service/app-service-environment/intro) は、App Service アプリを高スケールで安全に実行するために完全に分離された専用環境を提供する、App Service の機能です。 これは、この基本アーキテクチャで PCI DSS への準拠を実現するために使用される Premium サービス プランです。

ASE は、単一の顧客のアプリケーションだけを実行するために分離され、常に仮想ネットワークにデプロイされます。 顧客は、受信および送信アプリケーション ネットワーク トラフィックをきめ細かく制御できます。また、アプリケーションは、オンプレミスの企業リソースへの仮想ネットワーク経由のセキュリティで保護された高速接続を確立できます。

このアーキテクチャに ASE を使用することで、以下のコントロール/構成が可能になりました。

- セキュリティ保護された Virtual Network 内でのホストと、ネットワーク セキュリティ規則
- HTTPS 通信用の自己署名 ILB 証明書で構成された ASE
- [内部負荷分散モード](/azure/app-service-web/app-service-environment-with-internal-load-balancer) (モード 3)
- [TLS 1.0](/azure/app-service-web/app-service-app-service-environment-custom-settings) の無効化
- [TLS 暗号](/azure/app-service-web/app-service-app-service-environment-custom-settings)の変更
- [受信トラフィック N/W ポート](/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)のコントロール 
- [WAF – データの制限](/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- [SQL Database トラフィック](/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)の許可


#### <a name="jumpbox-bastion-host"></a>ジャンプボックス (要塞ホスト)

App Service 環境はセキュリティで保護され、ロック ダウンされているため、必要な DevOps の変更やリリースを許可するメカニズム (Kudu を使用して Web アプリを監視する機能など) が必要です。 仮想マシンは NAT Load Balancer の背後で保護され、TCP 3389 以外のポートで VM に接続できようにします。 

仮想マシンは、次の構成を使用してジャンプボックス (要塞ホスト) として作成されています。

-   [マルウェア対策拡張機能](/azure/security/azure-security-antimalware)
-   [Log Analytics 拡張機能](/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure 診断拡張機能](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   Azure Key Vault を使用した [Azure ディスクの暗号化](/azure/security/azure-security-disk-encryption) 
-   未使用時の仮想マシン リソースの消費を抑えるための[自動シャットダウン ポリシー](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)

### <a name="security-and-malware-protection"></a>セキュリティとマルウェア対策

[Azure Security Center](https://azure.microsoft.com/services/security-center/) は、すべての Azure リソースのセキュリティ状態を一元的に表示します。 セキュリティ制御が適切かつ正しく構成されているかを一目で確認し、注意が必要なリソースを素早く特定できます。  

[Azure Advisor](/azure/advisor/advisor-overview) は、ベスト プラクティスに従って Azure デプロイメントを最適化できるようにする、個人用に設定されたクラウド コンサルタントです。 Azure リソースの構成と使用量テレメトリを分析し、リソースの費用対効果、パフォーマンス、高可用性、およびセキュリティを向上させるために役立つソリューションを推奨します。

Azure Cloud Services および Virtual Machines 向けの [Microsoft マルウェア対策](/azure/security/azure-security-antimalware)は、ウイルスやスパイウェアなどの悪意のあるソフトウェアを識別して削除するリアルタイム保護機能であり、既知のマルウェアや不要なソフトウェアが Azure システムへのインストールや実行を試みた場合に警告する構成可能なアラートを備えています。

### <a name="operations-management"></a>運用管理

#### <a name="application-insights"></a>Application Insights

[Application Insights](https://azure.microsoft.com/services/application-insights/) を使用すると、アプリケーションのパフォーマンス管理と瞬時の分析によって、行動につながる知見を得ることができます。

#### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/) は、クラウドおよびオンプレミス環境内のリソースで生成されたデータの収集と分析に役立つサービスです。

#### <a name="managment-solutions"></a>管理ソリューション

以下の追加の管理ソリューションについて検討と構成を行ってください。 
- [アクティビティ ログ分析](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
- [Azure Networking Analytics](/azure/log-analytics/log-analytics-azure-networking-analytics?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Azure SQL Analytics](/azure/log-analytics/log-analytics-azure-sql)
- [変更の追跡](/azure/log-analytics/log-analytics-change-tracking?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Key Vault Analytics](/azure/log-analytics/log-analytics-azure-key-vault?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [サービス マップ](/azure/operations-management-suite/operations-management-suite-service-map)
- [セキュリティと監査](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [マルウェア対策](/azure/log-analytics/log-analytics-malware?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [更新管理](/azure/operations-management-suite/oms-solution-update-management)

### <a name="security-center-integration"></a>Security Center の統合

既定のデプロイの目的は、正常かつセキュリティで保護された構成の状態を示す Security Center の推奨事項のベースラインを提供することです。 データ収集は Azure Security Center から有効化できます。 詳細については、「[Azure Security Center の概要](/azure/security-center/security-center-get-started)」(Azure Security Center - はじめに) をご覧ください。

## <a name="deploy-the-solution"></a>ソリューションのデプロイ方法

このソリューションをデプロイするためのコンポーネントは、[Blueprint のコード リポジトリ][code-repo]から入手できます。 この基本アーキテクチャをデプロイするには、Microsoft PowerShell v5 を使用していくつかの手順を実行済みである必要があります。 Web サイトに接続するには、カスタム ドメイン名 (contoso.com など) を指定する必要があります。 これは手順 2 の`-customHostName` スイッチを使用して指定されます。 詳細については、「[Azure Web Apps のカスタム ドメイン名を購入する](/azure/app-service-web/custom-dns-web-site-buydomains-web-app)」をご覧ください。 カスタム ドメイン名は、ソリューションを正常にデプロイおよび実行するためには必要ありませんが、デモンストレーション用の Web サイトに接続する際に必要になります。

スクリプトにより、指定した Azure AD テナントにドメイン ユーザーが追加されます。 テストとして使用する新しい Azure AD テナントを作成することをお勧めします。

デプロイ中に問題が発生した場合は、「[FAQ and troubleshooting](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/pci-faq.md)」(FAQ とトラブルシューティング) をご覧ください。

ソリューションのデプロイ時には、PowerShell のクリーン インストールを使用することを強くお勧めします。 または、インストール スクリプトを正しく実行するために必要な最新のモジュールを使用していることを確認してください。 この例では、ジャンプボックス (要塞ホスト) にログインして以下のコマンドを実行します。 これにより、カスタム ドメイン コマンドが有効になります。


1. 必要なモジュールをインストールし、管理者ロールを正しく設定します。
 
    ```powershell
     .\0-Setup-AdministrativeAccountAndPermission.ps1 
        -azureADDomainName contosowebstore.com
        -tenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -subscriptionId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -configureGlobalAdmin 
        -installModules
    ```
    詳細な使用手順については、「[Script Instructions - Setup Administrative Account and Permission](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/0-Setup-AdministrativeAccountAndPermission.md)」(スクリプトの手順 - 管理者アカウントとアクセス許可の設定) をご覧ください。
    
2. solution-update-management をインストールします。 
 
    ```powershell
    .\1-DeployAndConfigureAzureResources.ps1 
        -resourceGroupName contosowebstore
        -globalAdminUserName adminXX@contosowebstore.com 
        -globalAdminPassword **************
        -azureADDomainName contosowebstore.com 
        -subscriptionID XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
        -suffix PCIcontosowebstore
        -customHostName contosowebstore.com
        -sqlTDAlertEmailAddress edna@contosowebstore.com 
        -enableSSL
        -enableADDomainPasswordPolicy 
    ```
    
    詳細な使用手順については、「[Script Instructions - Deploy and Configure Azure Resources](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md)」(スクリプトの手順 - Azure リソースのデプロイと構成) をご覧ください。
    
3. Log Analytics のログ記録および監視。 ソリューションがデプロイされると、Log Analytics ワークスペースを開くことができ、ソリューションのリポジトリで提供されているサンプル テンプレートを使用して、監視ダッシュボードをどのように構成できるかを示すことができます。 サンプル テンプレートについては、[omsDashboards フォルダー](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md)を参照してください。 テンプレートが正しくデプロイされるために、データが Log Analytics に収集される必要があります。 これには、サイトの活動によっては、最大 1 時間かそれ以上かかる場合があります。
 
    Log Analytics のログ記録を設定する際は、以下のリソースを含めることを検討してください。
 
    - Microsoft.Network/applicationGateways
    - Microsoft.Network/NetworkSecurityGroups
    - Microsoft.Web/serverFarms
    - Microsoft.Sql/servers/databases
    - Microsoft.Compute/virtualMachines
    - Microsoft.Web/sites
    - Microsoft.KeyVault/Vaults
    - Microsoft.Automation/automationAccounts
 

    
## <a name="threat-model"></a>脅威モデル

Contoso Webstore [ブループリントの脅威モデル](https://aka.ms/pciblueprintthreatmodel)のためのデータ フロー ダイアグラム (DFD) と脅威モデルの例です。

![](images/pci-threat-model.png)



## <a name="customer-responsibility-matrix"></a>お客様の責任マトリックス

お客様は、「[Responsibility Summary Matrix](https://aka.ms/fsiblueprintcrm)」(責任の概要) のコピーを保持する責任があります。これは、お客様が責任を負う FFIEC 要件と、Microsoft Azure が責任を負う FFIEC 要件の概要を示したものです。



## <a name="disclaimer-and-acknowledgments"></a>免責事項および確認

*2017 年 9 月*

- このドキュメントは、参考目的でのみ使用してください。 マイクロソフトおよび AVYAN は、本文書の情報に対して、明示的、黙示的、法的であるかを問わず、一切の保証を行わないものとします。 このドキュメントは "現状のまま" 提供されます。 このドキュメントに記載された情報と見解は、URL やその他のインターネット Web サイトの参照も含め、予告なく変更される可能性があります。 このドキュメントをお読みになったお客様は、自己責任でドキュメントをご利用ください。  
- このドキュメントは、Microsoft または Avyan の製品またはソリューションに含まれる知的財産に対するいかなる法的権利もお客様に提供するものではありません。  
- お客様の社内での参照目的に限り、このドキュメントを複製し使用することができます。  

  > [!NOTE]
  > このドキュメントに記載されている特定の推奨事項に従った結果、Azure でのデータ、ネットワーク、コンピューティング リソースの使用量が増加したり、ライセンス コストやサブスクリプション コストが増加したりする場合があります。  

- このドキュメントに記載したソリューションは基本アーキテクチャとして使用することを目的としており、実稼働目的で現状のまま使用することはできません。 コンプライアンスに準拠するには、監査者に相談して、最終的なお客様のソリューションを検証する必要があります。  
- このページに記載されているすべての顧客名、トランザクション レコード、およびすべての関連データは架空のものであり、この基本アーキテクチャ用に作成され、説明のためだけに使用されています。 実在するものとは一切関係ありません。  
- このソリューションは Microsoft と Avyan Consulting の共同によって開発され、[MIT ライセンス](https://opensource.org/licenses/MIT)の下で提供されています。

