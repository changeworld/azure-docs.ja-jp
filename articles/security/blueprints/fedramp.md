---
title: Azure のセキュリティとコンプライアンスのブループリント - FedRAMP Web アプリケーションの自動化
description: Azure のセキュリティとコンプライアンスのブループリント - FedRAMP Web アプリケーションの自動化
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 10ed297180f68fcaf006f2778990879be02f994d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
ms.locfileid: "31418932"
---
# <a name="azure-security-and-compliance-blueprint---fedramp-web-applications-automation"></a>Azure のセキュリティとコンプライアンスのブループリント - FedRAMP Web アプリケーションの自動化

## <a name="overview"></a>概要

[Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov) は、米国政府全体で実行されるプログラムであり、クラウド製品とサービスに対するセキュリティの評価、承認、および継続的な監視に対する標準化された手法を提供します。 この Azure のセキュリティとコンプライアンスのブループリント自動化では、インターネットに接続する単純な Web アプリケーションに適している FedRAMP 準拠 IaaS (サービスとしてのインフラストラクチャ) 環境をデプロイするためのガイダンスを提供します。 このソリューションは、一般的なリファレンス アーキテクチャでの Azure リソースのデプロイと構成を自動化して、お客様が特定のセキュリティ要件と準拠要件をどのように満たすことができるかを示し、お客様が Azure 上に独自のソリューションをビルドして構成するための基礎として機能します。 このソリューションは、NIST SP 800-53 に基づく FedRAMP High ベースラインのコントロールのサブセットを実装します。 FedRAMP High 要件とこのソリューションの詳細については、[FedRAMP High 要件 - 概略](fedramp-controls-overview.md)に関する記事をご覧ください。 ***注: このソリューションのデプロイ先は Azure Government です。***

このアーキテクチャは、特定の要件に合わせて調整するための基礎として使用されることを目的としており、そのまま運用環境に適用しないでください。 変更せずにこの環境にアプリケーションをデプロイすることは、FedRAMP High ベースラインの要件を完全に満たすためには不十分です。 以下の点に注意してください。
- このアーキテクチャは、お客様が FedRAMP に準拠した方法で Azure を使用するためのベースラインを提供します。
- お客様は、このアーキテクチャを使用してビルドしたソリューションの適切なセキュリティとコンプライアンスの評価を実施する責任を負います。要件は、お客様の実装によって変化する可能性があるからです。 

このソリューションの機能の概要を簡単に把握するために、ソリューションのデプロイの説明とデモンストレーションを行っている[このビデオ](https://aka.ms/fedrampblueprintvideo)をご覧ください。

デプロイ手順については、[ここ](https://aka.ms/fedrampblueprintrepo)をクリックしてください。

## <a name="solution-components"></a>ソリューションのコンポーネント

この Azure のセキュリティとコンプライアンスのブループリント自動化は、IaaS Web アプリケーション リファレンス アーキテクチャと事前構成済みのセキュリティ コントロールを自動的にデプロイして、お客様が FedRAMP 要件への準拠を実現することを支援します。 このソリューションは、リソースのデプロイと構成をガイドする Azure Resource Manager テンプレートと PowerShell スクリプトで構成されています。 Azure からのセキュリティ コントロールの継承と、NIST SP 800-53 セキュリティ コントロールと合致するリソースと構成のデプロイを示す[コンプライアンス ドキュメント](#compliance-documentation)が付属しています。組織は、そのドキュメントを使用して、コンプライアンスの義務に迅速に対応できます。

## <a name="architecture-diagram"></a>アーキテクチャ ダイアグラム

このソリューションは、データベース バックエンドがある IaaS Web アプリケーション向けのリファレンス アーキテクチャをデプロイします。 アーキテクチャには、Web 層、データ層、Active Directory インフラストラクチャ、アプリケーション ゲートウェイ、およびロード バランサーが含まれています。 Web 層とデータ層にデプロイされる仮想マシンは可用性セット内に構成され、SQL Server インスタンスは高可用性のために AlwaysOn 可用性グループ内に構成されます。 仮想マシンはドメインに参加し、Active Directory グループ ポリシーを使用して、オペレーティング システム レベルでセキュリティとコンプライアンスの構成が適用されます。 管理ジャンプボックス (要塞ホスト) は、デプロイされたリソースにアクセスするためのセキュリティで保護された接続を管理者に提供します。

![alt text](images/fedramp-architectural-diagram.png?raw=true "Azure のセキュリティとコンプライアンスのブループリント - FedRAMP Web アプリケーションの自動化")

このソリューションでは、次の Azure サービスを使用します。 デプロイ アーキテクチャについて詳しくは、「[デプロイ アーキテクチャ](#deployment-architecture)」セクションをご覧ください。

* **Azure Virtual Machines**
    - (1) 管理/要塞 (Windows Server 2016 Datacenter)
    - (2) Active Directory ドメイン コントローラー (Windows Server 2016 Datacenter)
    - (2) SQL Server クラスター ノード (Windows Server 2012 R2 上の SQL Server 2016)
    - (1) SQL Server ミラーリング監視 (Windows Server 2016 Datacenter)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
* **可用性セット**
    - (1) Active Directory ドメイン コントローラー
    - (1) SQL クラスター ノードとミラーリング監視
    - (1) Web/IIS
* **Azure Virtual Network**
    - (1) /16 仮想ネットワーク
    - (5) /24 サブネット
    - 両方のドメイン コントローラーに DNS 設定が設定されます
* **Azure Load Balancer**
    - (1) SQL ロード バランサー
* **Azure Application Gateway**
    - (1) WAF Application Gateway が有効
      - ファイアウォール モード - 防止
      - ルール セット: OWASP 3.0
      - リスナー: ポート 443
* **Azure Storage**
    - (7) Geo 冗長ストレージ アカウント
* **Azure Backup**
    - (1) Recovery Services コンテナー
* **Azure Key Vault**
    - (1) キー コンテナー
* **Azure Active Directory**
* **Azure Resource Manager**
* **Azure Log Analytics**
    - (1) Log Analytics ワークスペース
* **Azure Automation**
    - (1) Automation アカウント

## <a name="deployment-architecture"></a>デプロイメント アーキテクチャ

以下のセクションでは、開発と実装の要素について詳しく説明します。

### <a name="network-segmentation-and-security"></a>ネットワークのセグメント化とセキュリティ

#### <a name="application-gateway"></a>Application Gateway

このアーキテクチャでは、OWASP ルールセットを有効にした、Web アプリケーション ファイアウォール (WAF) がある Application Gateway を使用してセキュリティの脆弱性のリスクを軽減します。 その他の機能には次のものがあります。

- [エンド ツー エンド SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL オフロード](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)の有効化
- [TLS v1.0 および v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) の無効化
- [Web アプリケーション ファイアウォール](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (WAF モード)
- OWASP 3.0 ルールセットを使用した[防止モード](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal)

#### <a name="virtual-network"></a>Virtual Network

このアーキテクチャは、10.200.0.0/16 のアドレス空間 を使用してプライベート仮想ネットワークを定義します。

#### <a name="network-security-groups"></a>ネットワーク セキュリティ グループ

このソリューションは、分離された Web サブネット、データベース サブネット、Active Directory サブネット、管理サブネットを持つアーキテクチャの仮想ネットワーク内にリソースをデプロイします。 サブネット間のトラフィックをシステムと管理機能に必要なものだけに制限するために、個々のサブネットに適用されるネットワーク セキュリティ グループの規則に従って、サブネットが論理的に分離されます。

ソリューションでデプロイされる[ネットワーク セキュリティ グループ](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json)の構成を参照してください。 組織は、[こちらのドキュメント](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)をガイドとして使用して上のファイルを編集することで、ネットワーク セキュリティ グループを構成できます。

各サブネットには、専用のネットワーク セキュリティ グループ (NSG) があります。
- Application Gateway 用の 1 つの NSG (LBNSG)
- ジャンプボックス用の 1 つの NSG (MGTNSG)
- プライマリおよびバックアップ ドメイン コントローラー用の 1 つの NSG (ADNSG)
- SQL サーバーとファイル共有のミラーリング監視用の 1 つの NSG (SQLNSG)
- Web 層用の 1 つの NSG (WEBNSG)

#### <a name="subnets"></a>サブネット

各サブネットは、対応する NSG に関連付けられています。

### <a name="data-at-rest"></a>保存データ

このアーキテクチャは、さまざまな暗号化手段を使用して、保存データを保護します。

#### <a name="azure-storage"></a>Azure Storage

保存データの暗号化要件を満たすために、すべてのストレージ アカウントは、[ストレージ サービスの暗号化](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)を使用します。

#### <a name="sql-database"></a>SQL Database

SQL Database は、[Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) を使用するように構成されます。TDE は、データとログ ファイルのリアルタイムの暗号化と暗号化解除を実行して保存情報を保護します。 TDE は、保存されているデータが未承認のアクセスの対象になっていないことを保証します。 

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption

暗号化された Windows IaaS 仮想マシン ディスクに対して Azure Disk Encryption が使用されます。 [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) は、Windows の BitLocker 機能を活用して、OS とデータ ディスクのボリュームを暗号化します。 このソリューションは、ディスクの暗号化キーを制御および管理できるように、Azure Key Vault と統合されています。

### <a name="logging-and-auditing"></a>ログ記録と監査

[Log Analytics](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) は、システムの正常性だけではなく、システムとユーザーのアクティビティを幅広く記録します。 

- **アクティビティ ログ**: [アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)は、サブスクリプションのリソースに対して実行された操作に関する知見を提供します。
- **診断ログ**: [診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)は、リソースによって出力されるすべてのログです。 これらのログには、Windows イベント システム ログ、Azure ストレージ ログ、Key Vault 監査ログ、および Application Gateway のアクセス ログとファイアウォール ログが含まれます。
- **ログのアーカイブ:** Azure のアクティビティ ログと診断ログを Azure Log Analytics に接続して、Azure Log Analytics で処理、保存、およびダッシュボードに表示できます。 リテンション期間は構成可能であり、組織固有の保有要件を満たすために最長 730 日までの日数を設定できます。

### <a name="secrets-management"></a>シークレットの管理

このソリューションでは、Azure Key Vault を使用してキーとシークレットを管理します。

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。 
- このソリューションは、IaaS 仮想マシンのディスク暗号化キーとシークレットを管理できるように、Azure Key Vault と統合されています。

### <a name="identity-management"></a>ID 管理

次のテクノロジによって、Azure 環境に ID 管理機能が提供されます。
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) は、マイクロソフトが提供する、マルチテナントに対応したクラウドベースのディレクトリおよび ID の管理サービスです。
- Azure AD を使用して、お客様がデプロイした Web アプリケーションを認証できます。 詳細については、「[Azure Active Directory とアプリケーションの統合](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)」をご覧ください。  
- [Azure のロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) は、Azure の厳密に対象を絞ったアクセス管理を実現します。 サブスクリプションのアクセスはサブスクリプション管理者に制限され、リソースへのアクセスはユーザー ロールに基づいて制限できます。
- デプロイされた IaaS Active Directory インスタンスは、デプロイされた IaaS 仮想マシンの ID を OS レベルで管理します。
   
### <a name="compute-resources"></a>コンピューティング リソース

#### <a name="web-tier"></a>Web 層

このソリューションは、Web 層の仮想マシンを[可用性セット](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)に配置します。 可用性セットは、可用性を向上させるために仮想マシンが複数の分離されたハードウェア クラスターに分散されることを保証します。

#### <a name="database-tier"></a>データベース層

このソリューションは、データベース層の仮想マシンを、[AlwaysOn 可用性グループ](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)として可用性セットに配置します。 Always On 可用性グループ機能は、高可用性とディザスター リカバリ用の機能を提供します。 

#### <a name="active-directory"></a>Active Directory

このソリューションによってデプロイされたすべての仮想マシンはドメインに参加し、Active Directory グループ ポリシーを使用して、オペレーティング システム レベルでセキュリティとコンプライアンスの構成が適用されます。 Active Directory 仮想マシンは可用性セットに配置されます。


#### <a name="jumpbox-bastion-host"></a>ジャンプボックス (要塞ホスト)

管理ジャンプボックス (要塞ホスト) は、デプロイされたリソースにアクセスするためのセキュリティで保護された接続を管理者に提供します。 ジャンプボックス仮想マシンが配置されている管理サブネットに関連付けられている NSG では、RDP 用の TCP ポート 3389 での接続のみが許可されます。 

### <a name="malware-protection"></a>マルウェア対策

仮想マシン向けの [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) は、ウイルスやスパイウェアなどの悪意のあるソフトウェアを識別して削除するリアルタイム保護機能を提供し、既知のマルウェアや不要なソフトウェアが保護されている仮想マシンへのインストールまたは実行を試みた場合に警告する構成可能なアラートを備えています。

### <a name="patch-management"></a>更新プログラムの管理

この Azure のセキュリティとコンプライアンスのブループリント自動化によってデプロイされる Windows 仮想マシンは、Windows Update サービスから自動的に更新プログラムを受け取るように、既定で構成されます。 また、このソリューションによってデプロイされる Azure Automation ソリューションにより、更新プログラムのデプロイを作成し、必要に応じて Windows サーバーに修正プログラムをデプロイできます。

### <a name="operations-management"></a>運用管理

#### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/) は、Azure やオンプレミス環境のリソースによって生成されるデータの収集と分析を可能にするサービスです。

#### <a name="management-solutions"></a>管理ソリューション

このソリューションの一部として、次の管理ソリューションがあらかじめインストールされています。
- [AD 評価](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)
- [マルウェア対策評価](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware)
- [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)
- [セキュリティと監査](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)
- [SQL の評価](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)
- [更新管理](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management)
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)
- [Azure Activity Logs](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)
- [変更の追跡](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)

## <a name="compliance-documentation"></a>コンプライアンス ドキュメント

### <a name="customer-responsibility-matrix"></a>お客様の責任マトリックス

[お客様の責任マトリックス](https://aka.ms/blueprinthighcrm) (Excel ブック) は、FedRAMP High ベースラインで要求されるすべてのセキュリティ コントロールを一覧表示します。 このマトリックスは、コントロール (またはコントロールのサブパート) ごとに、そのコントロールの実装を実装する責任が、Microsoft にあるのか、お客様にあるのか、または両者で共有されるのかを示します。 

### <a name="control-implementation-matrix"></a>コントロールの実装マトリックス

[コントロールの実装マトリックス](https://aka.ms/blueprintwacim) (Excel ブック) は、FedRAMP High ベースラインで要求されるすべてのセキュリティ コントロールを一覧表示します。 このマトリックスは、お客様の責任マトリックスでお客様の責任と指定されているコントロール (またはコントロールのサブパート) ごとに、1) ブループリント自動化がコントロールを実装するかどうか、2) 実装がコントロール要件とどのように合致しているかの説明を示します。 このコンテンツは、[こちら](fedramp-controls-overview.md)からもアクセスできます。

## <a name="deploy-the-solution"></a>ソリューションのデプロイ方法

この Azure のセキュリティとコンプライアンスのブループリント自動化は、Azure Resource Manager の API サービスが Azure 内にリソースをデプロイするために処理する JSON 構成ファイルと PowerShell スクリプトで構成されています。 詳しいデプロイ手順については、[こちら](https://aka.ms/fedrampblueprintrepo)をご覧ください。 ***注: このソリューションのデプロイ先は Azure Government です。***

#### <a name="quickstart"></a>クイック スタート
1. [この](https://aka.ms/fedrampblueprintrepo) GitHub リポジトリをローカル ワークステーションに複製するかダウンロードします。

2. 配置前 PowerShell スクリプト (azure-blueprint/predeploy/Orchestration_InitialSetup.ps1) を実行します。

3. 下のボタンをクリックし、Azure ポータルにサインインします。必要な ARM テンプレート パラメーターを入力し、**[購入]**  をクリックします。

    [![Azure へのデプロイ](http://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="disclaimer"></a>免責事項

- このドキュメントは、参考目的でのみ使用してください。 マイクロソフトは、本文書の情報に対して、明示的、黙示的、法的であるかを問わず、一切の保証を行わないものとします。 このドキュメントは "現状のまま" 提供されます。 このドキュメントに記載された情報と見解は、URL やその他のインターネット Web サイトの参照も含め、予告なく変更する可能性があります。 このドキュメントをお読みになったお客様は、自己責任でドキュメントをご利用ください。  
- このドキュメントは、Microsoft 製品に含まれる知的財産に対するいかなる法的権利もお客様に提供するものではありません。  
- お客様の社内での参照目的に限り、このドキュメントを複製し使用することができます。  
- このドキュメントに記載されている特定の推奨事項に従った結果、Azure でのデータ、ネットワーク、コンピューティング リソースの使用量が増加したり、ライセンス コストやサブスクリプション コストが増加したりする場合があります。  
- このアーキテクチャは、特定の要件に合わせて調整するための基礎として使用されることを目的としており、そのまま運用環境に適用しないでください。
- このドキュメントは、参照資料として作成されており、お客様が特定のコンプライアンス要件や規制に適合するためのすべての手段を定義するために使用されるべきではありません。 お客様は、承認された顧客実装について、組織の法務課からのアドバイスを受けてください。
