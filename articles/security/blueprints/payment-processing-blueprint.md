---
title: Azure のセキュリティとコンプライアンスのブループリント - PCI DSS 準拠の支払い処理環境
description: Azure のセキュリティとコンプライアンスのブループリント - PCI DSS 準拠の支払い処理環境
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 2f1e00a8-0dd6-477f-9453-75424d06a1df
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2018
ms.author: jomolesk
ms.openlocfilehash: 223829df11bb1c9add811b40b55e47ee1fbb1fe4
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751840"
---
# <a name="azure-security-and-compliance-blueprint---pci-dss-compliant-payment-processing-environments"></a>Azure のセキュリティとコンプライアンスのブループリント - PCI DSS 準拠の支払い処理環境

## <a name="overview"></a>概要

この「PCI DSS 準拠環境での支払い処理」は、機密性の高いペイメント カード データの処理に適した PCI DSS 準拠 PaaS (Platform-as-a-Service) 環境についてのガイダンスを提供します。 これは一般的な参照アーキテクチャを紹介し、Microsoft Azure の導入を簡素化するために設計されています。 このブループリントは、デプロイの手間とコストを削減するためのクラウドベースのアプローチを求めている組織のニーズを満たす、エンド ツー エンドのソリューションを示しています。

このブループリントは、ペイメント カード データの収集、格納、取得に関する厳格な Payment Card Industry Data Security Standards (PCI DSS 3.2) の要件を満たすのに役立つよう設計されています。 これは、エンド ツー エンドの Azure ベース PaaS ソリューションとしてデプロイされた安全な複数の階層からなる準拠環境でのクレジット カード データ (カード番号、有効期限、検証データなど) の適切な処理を示しています。 PCI DSS 3.2 要件とこのソリューションの詳細については、「[PCI DSS 要件 - 概略](pci-dss-requirements-overview.md)」をご覧ください。

このブループリントは、特定の要件を深く理解するための基礎となるように意図されています。運用環境でそのまま使用しないでください。 この環境に変更せずにアプリケーションをデプロイするだけでは、カスタム ソリューションに向けた PCI DSS 準拠ソリューションの要件を完全に満たすには不十分です。 以下の点に注意してください。
- このブループリントでは、お客様が PCI DSS に準拠した方法で Microsoft Azure を使用するのに役立つベースラインが提供されます。
- PCI DSS への準拠を達成するには、お客様の実稼働ソリューションが認定セキュリティ評価機関から認証を受ける必要があります。
- お客様は、この基本アーキテクチャを使用してビルドしたソリューションの適切なセキュリティとコンプライアンスのレビューを実施する責任を負います。要件は、お客様の実装と地理的条件によって変化する可能性があるからです。  

このソリューションの機能の概要を簡単に把握するために、ソリューションのデプロイの説明とデモンストレーションを行っている[このビデオ](https://aka.ms/pciblueprintvideo)をご覧ください。

## <a name="solution-components"></a>ソリューションのコンポーネント

この基本アーキテクチャは次のコンポーネントで構成されています。

- **アーキテクチャ図**:  この図は、Contoso Webstore ソリューションで使用される参照アーキテクチャを示します。
- **デプロイ テンプレート**:  このデプロイでは、[Azure Resource Manager テンプレート](/azure/azure-resource-manager/resource-group-overview#template-deployment) を使用してアーキテクチャのコンポーネントを Microsoft Azure に自動的にデプロイします (これは、セットアップ時に構成パラメーターを指定することによって行います)。
- **自動化されたデプロイ スクリプト**:  これらのスクリプトを使用して、エンド ツー エンド ソリューションをデプロイします。 次のスクリプトが含まれます。
    - モジュールのインストールと[グローバル管理者](/azure/active-directory/active-directory-assign-admin-roles-azure-portal)のセットアップ スクリプト。インストールを行い、必要な PowerShell モジュールとグローバル管理者ロールが正しく構成されていることを確認するために使用します。
    - インストール PowerShell スクリプト。[SQL データベース サンプル](https://github.com/Microsoft/azure-sql-security-sample)のコンテンツを使用して事前に構築されたデモ Web アプリケーションを含む、.zip ファイル 1 個と .bacpac ファイル 1 個を通じて提供されるエンド ツー エンドのソリューションをデプロイするために使用します。 このソリューションのソース コードは [GitHub](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms) でレビュー用に入手できます。 

## <a name="architectural-diagram"></a>アーキテクチャ図

![](images/pci-architectural-diagram.png)

## <a name="user-scenario"></a>ユーザー シナリオ

ブループリントでは、以下のユース ケースが対処されます。

> このシナリオは、ある架空の Web ストアがペイメント カードの処理を Azure ベースの PaaS ソリューションに移行した方法を示しています。 このソリューションは、支払いデータを含む基本的なユーザー情報のコレクションを処理します。 このソリューションが、このカード所有者データを使用して支払い処理を行うことはありません。データの収集後は、お客様が支払い処理業者とのトランザクションを開始および完了する責任を負います。 詳細については、「[Review and Guidance for Implementation](https://aka.ms/pciblueprintprocessingoverview)」(実装のためのレビューとガイダンス) をご覧ください。

### <a name="use-case"></a>ユース ケース
*Contoso Webstore* という名前の小規模な Web ストアが、自社の支払いシステムをクラウドに移行するための準備を完了しました。 このストアは、購入のプロセスをホストし、従業員が顧客からクレジット カード支払いデータを収集できるようにするために、Microsoft Azure を選択しました。

管理者は、クラウド生まれのソリューションという目標を達成するために迅速にデプロイできるソリューションを探しています。 管理者はこの概念実証 (POC) を使用して、Payment Card Industry Data Security Standard (PCI DSS) の厳格な要件を満たしながらどのように Azure で支払いカード データを収集、格納、取得できるかを利害関係者と話し合おうとしています。

> お客様は、この POC で使用されているアーキテクチャを使用してビルドしたソリューションの適切なセキュリティとコンプライアンスのレビューを実施する責任を負います。要件は、お客様の実装と地理的条件によって変化する可能性があるからです。 PCI DSS に準拠するには、お客様の実稼働可能なソリューションが認定セキュリティ評価機関から直接認証を受ける必要があります。

### <a name="elements-of-the-foundational-architecture"></a>基本アーキテクチャの要素

この基本アーキテクチャは、次の架空の要素を使用して設計されています。

ドメイン サイト `contosowebstore.com`

ユース ケースについて説明し、ユーザー インターフェイスを理解するための手がかりとなるユーザー ロール。

#### <a name="role-site-and-subscription-admin"></a>ロール: サイトおよびサブスクリプションの管理者

|項目      |例|
|----------|------|
|ユーザー名: |`adminXX@contosowebstore.com`|
| 名前: |`Global Admin Azure PCI Samples`|
|ユーザー タイプ:| `Subscription Administrator and Azure Active Directory Global Administrator`|

- 管理者アカウントは、マスクが解除されたクレジット カード情報の読み取りを行えません。 すべてのアクションがログ記録されます。
- 管理者アカウントは、SQL Database の管理やログインを実行できません。
- 管理者アカウントは、Active Directory およびサブスクリプションを管理できます。

#### <a name="role-sql-administrator"></a>ロール: SQL 管理者

|項目      |例|
|----------|------|
|ユーザー名: |`sqlAdmin@contosowebstore.com`|
| 名前: |`SQLADAdministrator PCI Samples`|
| 名: |`SQL AD Administrator`|
|姓: |`PCI Samples`|
|ユーザー タイプ:| `Administrator`|

- sqladmin アカウントは、フィルター処理されていないクレジット カード情報を表示できません。 すべてのアクションがログ記録されます。
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

- Edna は顧客情報の作成および読み取りができます。
- Edna は顧客情報を変更できます。
- Edna はクレジット カード番号、有効期限、および CVV 情報の上書きおよび置換を実行できます。

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
- [Azure Security Center](https://azure.microsoft.com/services/security-center) と [Azure Advisor](/azure/advisor/advisor-security-recommendations) による、その他の保護と通知の提供。 Azure Security Center は評価システムも提供します。

#### <a name="virtual-network"></a>Virtual network

この基本アーキテクチャは、10.0.0.0/16 のアドレス空間 を使用してプライベート仮想ネットワークを定義します。

#### <a name="network-security-groups"></a>ネットワーク セキュリティ グループ

各ネットワーク階層に専用のネットワーク セキュリティ グループ (NSG) があります。
- ファイアウォールおよび Application Gateway WAF 用の DMZ ネットワーク セキュリティ グループ
- ジャンプボックス (要塞ホスト) 管理用の NSG
- App Service Environment 用の NSG

各 NSG には、ソリューションの安全かつ適切な操作のために開かれる固有のポートとプロトコルがあります。 さらに、各 NSG で次の構成を使用できます。
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

[Log Analytics](https://azure.microsoft.com/services/log-analytics) は Contoso Webstore に、すべてのシステムおよびユーザー アクティビティの広範なログ記録 (カード所有者データのログ記録を含む) を提供できます。 正確性を確保するために、変更内容を確認および検証できます。 

- **アクティビティ ログ**: [アクティビティ ログ](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)は、サブスクリプションのリソースに対して実行された操作に関する知見を提供します。
- **診断ログ**: [診断ログ](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)は、リソースによって出力されるすべてのログです。 これらのログには、Windows イベント システム ログ、Azure Blob Storage、テーブル、キューのログが含まれます。
- **ファイアウォール ログ**: Application Gateway は、完全な診断およびアクセス ログを提供します。 ファイアウォール ログは、WAF が有効になっている Application Gateway リソースで使用できます。
- **ログのアーカイブ**: すべての診断ログは、暗号化された集中型の Azure ストレージ アカウントに書き込まれ、定義済みのリテンション期間 (2 日間) にわたってアーカイブされるように構成されます。 その後、ログは Azure Log Analytics に接続されて処理、格納、ダッシュボード化されます。 [Log Analytics](https://azure.microsoft.com/services/log-analytics) は、クラウドおよびオンプレミス環境内のリソースで生成されたデータの収集と分析に役立つサービスです。

### <a name="encryption-and-secrets-management"></a>暗号化とシークレットの管理

Contoso Webstore は、すべてのクレジット カード データを暗号化し、Azure Key Vault を使用してキーを管理することで、CHD が取得されるのを防ぎます。

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。 
- [SQL TDE](/sql/relational-databases/security/encryption/transparent-data-encryption) は、お客様のカード所有者データ、有効期限、CVV をすべて暗号化するために使用されます。
- データは [Azure Disk Encryption](/azure/security/azure-security-disk-encryption) と BitLocker を使用してディスクに格納されます。

### <a name="identity-management"></a>ID 管理

次のテクノロジによって、Azure 環境に ID 管理機能が提供されます。
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) は、マイクロソフトが提供する、マルチテナントに対応したクラウドベースのディレクトリおよび ID の管理サービスです。 ソリューションのすべてのユーザー (SQL Database にアクセスするユーザーを含む) は、Azure Active Directory で作成されています。
- アプリケーションに対する認証は Azure AD を使用して行われます。 詳細については、「[Azure Active Directory とアプリケーションの統合](/azure/active-directory/develop/active-directory-integrating-applications)」をご覧ください。 さらに、データベース列の暗号化でも、アプリケーションを Azure SQL Database に対して認証するために Azure AD が使用されます。 詳細については、「[Always Encrypted: Protect sensitive data in SQL Database](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)」(Always Encrypted: SQL Database で機密データを保護する) をご覧ください。 
- [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) は、組織の ID に影響する潜在的な脆弱性を検出し、組織の ID に関連する検出された疑わしいアクションに対する自動応答を構成し、疑わしいインシデントを調査して適切なアクションを実行して解決します。
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
- [TLS 1.0](/azure/app-service-web/app-service-app-service-environment-custom-settings) の無効化 - PCI DSS の観点から非推奨になった TLS プロトコル
- [TLS 暗号](/azure/app-service-web/app-service-app-service-environment-custom-settings)の変更
- [受信トラフィック N/W ポート](/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)のコントロール 
- [WAF – データの制限](/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- [SQL Database トラフィック](/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)の許可


#### <a name="jumpbox-bastion-host"></a>ジャンプボックス (要塞ホスト)

App Service 環境はセキュリティで保護され、ロック ダウンされているため、必要な DevOps の変更やリリースを許可するメカニズム (Kudu を使用して Web アプリを監視する機能など) が必要です。 仮想マシンは NAT Load Balancer の背後で保護され、TCP 3389 以外のポートで VM に接続できます。 

仮想マシンは、次の構成を使用してジャンプボックス (要塞ホスト) として作成されています。

-   [マルウェア対策拡張機能](/azure/security/azure-security-antimalware)
-   [OMS 拡張機能](/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure 診断拡張機能](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   Azure Key Vault を使用した [Azure Disk Encryption](/azure/security/azure-security-disk-encryption) (Azure Government、PCI DSS、HIPAA、およびその他の要件に準拠)
-   使用されていない仮想マシン リソースの消費を抑えるための[自動シャット ダウン ポリシー](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)

### <a name="security-and-malware-protection"></a>セキュリティとマルウェア対策

[Azure Security Center](https://azure.microsoft.com/services/security-center/) では、すべての Azure リソースのセキュリティの状態を一元的に把握できます。 セキュリティの制御が適切かつ正しく構成されているかを一目で確認し、注意が必要なリソースを素早く特定できます。  

[Azure Advisor](/azure/advisor/advisor-overview) は、ベスト プラクティスに従って Azure デプロイメントを最適化できるようにする、個人用に設定されたクラウド コンサルタントです。 Azure のリソースの構成と利用統計情報を分析し、Azure リソースの費用対効果、パフォーマンス、高可用性、およびセキュリティを向上させるために役立つソリューションを推奨します。

Azure Cloud Services および Virtual Machines 向けの [Microsoft マルウェア対策](/azure/security/azure-security-antimalware)は、ウイルスやスパイウェアなどの悪意のあるソフトウェアを識別して削除するリアルタイム保護機能であり、既知のマルウェアや不要なソフトウェアが Azure システムへのインストールまたは実行を試みた場合に警告する構成可能なアラートを備えています。

### <a name="operations-management"></a>運用管理

#### <a name="application-insights"></a>Application Insights

[Application Insights](https://azure.microsoft.com/services/application-insights/) を使用すると、アプリケーションのパフォーマンス管理と瞬時の分析によって、行動につながる知見を得ることができます。

#### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/) は Azure のサービスであり、クラウドおよびオンプレミスの環境内にあるリソースで生成されたデータを収集して分析するのに役立ちます。

#### <a name="management-solutions"></a>管理ソリューション

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

既定のデプロイは、正常かつ安全な構成の状態を示し、セキュリティ センターの推奨事項のベースラインを提供することを目的としています。 データ収集は Azure Security Center から有効化できます。 詳細については、「[Azure Security Center の概要](/azure/security-center/security-center-get-started)」(Azure Security Center - はじめに) をご覧ください。

## <a name="deploy-the-solution"></a>ソリューションのデプロイ方法

このソリューションをデプロイするためのコンポーネントは、[PCI ブループリントのコード リポジトリ](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms)から入手できます。 この基本アーキテクチャをデプロイするには、Microsoft PowerShell v5 を使用していくつかの手順を実行済みである必要があります。 Web サイトに接続するには、カスタム ドメイン名 (contoso.com など) を指定する必要があります。 これは、手順 2 のプライマリ デプロイ スクリプトのガイド付きユーザー プロンプトを通じて指定します。 詳細については、「[Azure Web Apps のカスタム ドメイン名を購入する](/azure/app-service-web/custom-dns-web-site-buydomains-web-app)」をご覧ください。 カスタム ドメイン名は、ソリューションを正常にデプロイおよび実行するためには必要ありませんが、デモンストレーション用の Web サイトに接続する際に必要になります。

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
    ```
    
    詳細な使用手順については、「[Script Instructions - Deploy and Configure Azure Resources](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md)」(スクリプトの手順 - Azure リソースのデプロイと構成) をご覧ください。 このスクリプトは、Contoso Web Store のデモをサポートするため、または PCI コンプライアンスをサポートするための環境をデプロイする初期ステップを試行するために使用できます。 
    
    ```PowerShell
    .\1A-ContosoWebStoreDemoAzureResources.ps1
    ```
    
    Contoso Web Store のデモのデプロイをサポートするための詳細な使用方法については、[Contoso Web Store デモ Azure リソースのスクリプトの使用方法](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1A-ContosoWebStoreDemoAzureResources.md)に関するページを参照してください。 このスクリプトは、Contoso Web Store デモ インフラストラクチャのデプロイに使用できます。 
    
    これらのスクリプトは、互いに独立して使用することが想定されています。 ソリューションを最もよく理解するには、ソリューションのサポートに必要な Azure リソースを特定するためのデモ デプロイを完了することをお勧めします。 
    
3. ログ記録と監視。 ソリューションのデプロイ後は、Log Analytics ワークスペースを開くことができ、ソリューションのリポジトリで提供されているサンプル テンプレートを使用して、監視ダッシュボードをどのように構成できるかを示すことができます。 サンプル テンプレートについては、[omsDashboards フォルダー](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md)を参照してください。 テンプレートが正しくデプロイされるために、データが Log Analytics に収集される必要があります。 これには、サイトの活動によっては、最大 1 時間かそれ以上かかる場合があります。
 
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

お客様は、「[Responsibility Summary Matrix](https://aka.ms/pciblueprintcrm32)」(責任の概要) のコピーを保持する責任があります。これは、お客様が責任を負う PCI DSS 要件と、Microsoft Azure が責任を負う PCI DSS 要件の概要を示したものです。

## <a name="pci-compliance-review"></a>PCI へのコンプライアンスのレビュー 

このソリューションは、Coalfire systems, Inc. (PCI-DSS の認定セキュリティ機関) によってレビューされました。 「[PCI Compliance Review and guidance for implementation](https://aka.ms/pciblueprintprocessingoverview)」(PCI へのコンプライアンスのレビューおよび実装のためのガイダンス) には、監査人によるソリューションのレビューと、このブループリントを実稼働可能なデプロイに変える際の考慮事項が記載されています。

## <a name="disclaimer-and-acknowledgements"></a>免責事項および確認

*2017 年 9 月*

- このドキュメントは、参考目的でのみ使用してください。 マイクロソフトおよび AVYAN は、本文書の情報に対して、明示的、黙示的、法的であるかを問わず、一切の保証を行わないものとします。 このドキュメントは "現状のまま" 提供されます。 このドキュメントに記載された情報と見解は、URL やその他のインターネット Web サイトの参照も含め、予告なく変更する可能性があります。 このドキュメントをお読みになったお客様は、自己責任でドキュメントをご利用ください。  
- このドキュメントは、Microsoft または Avyan の製品またはソリューションに含まれる知的財産に対するいかなる法的権利もお客様に提供するものではありません。  
- お客様の社内での参照目的に限り、このドキュメントを複製し使用することができます。  

  > [!NOTE]
  > このドキュメントに記載されている特定の推奨事項に従った結果、Azure でのデータ、ネットワーク、コンピューティング リソースの使用量が増加したり、ライセンス コストやサブスクリプション コストが増加したりする場合があります。  

- このドキュメントに記載したソリューションは基本アーキテクチャとして使用することを目的としており、実稼働目的で現状のまま使用することはできません。 PCI へのコンプライアンスを達成するためには、お客様ご自身が認定セキュリティ機関にご相談いただく必要があります。  
- このページに記載されているすべての顧客名、トランザクション レコード、およびすべての関連データは架空のものであり、この基本アーキテクチャ用に作成され、説明のためだけに使用されています。 実在するものとは一切関係ありません。  
- このソリューションは Microsoft と Avyan Consulting の共同によって開発され、[MIT ライセンス](https://opensource.org/licenses/MIT)の下で提供されています。
- このソリューションは、Microsoft の PCI-DSS 監査人である Coalfire によってレビューされました。 [PCI へのコンプライアンスのレビュー](https://aka.ms/pciblueprintcrm32)には、このソリューションおよび対応する必要があるコンポーネントの、独立したサードパーティによるレビューが記載されています。 
