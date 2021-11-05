---
title: Azure Data Factory で Azure-SSIS 統合ランタイムを作成する
description: Azure 上で SSIS パッケージをデプロイして実行できるように、Azure-SSIS 統合ランタイムを Azure Data Factory に作成する方法について説明します。
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/22/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 70e745d18699c6a1acb707528ec38641a1fe7a02
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842893"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Azure Data Factory で Azure-SSIS 統合ランタイムを作成する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事では、Azure-SQL Server Integration Services (SSIS) 統合ランタイム (IR) を Azure Data Factory (ADF) でプロビジョニングする手順について説明します。 Azure-SSIS IR では、次のことがサポートされます。

- Azure SQL Database サーバーまたは Managed Instance をホストとする SSIS カタログ (SSISDB) にデプロイされたパッケージを実行する (プロジェクト デプロイ モデル)
- Azure SQL Managed Instance にホストされた SQL Server データベース (MSDB)、Azure Files、ファイル システムのいずれかにデプロイされたパッケージを実行する (パッケージ デプロイ モデル)

Azure-SSIS IR のプロビジョニング後は、使い慣れたツールを使用して、Azure にパッケージをデプロイして実行することができます。 これらのツールは既に Azure に対応しています。たとえば、SQL Server Data Tools (SSDT) や SQL Server Management Studio (SSMS) のほか、[dtutil](/sql/integration-services/dtutil-utility)、[AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md) などのコマンド ライン ユーティリティがあります。

[Azure-SSIS IR のプロビジョニング](./tutorial-deploy-ssis-packages-azure.md)のチュートリアルに関するページでは、Azure-SSIS IR を Azure portal または Data Factory アプリから作成する方法が紹介されています。 このチュートリアルでは、必要に応じて Azure SQL Database サーバーまたはマネージド インスタンスを使用して SSISDB をホストする方法についても説明されています。 この記事ではそのチュートリアルをさらに掘り下げており、次の任意の操作を行う方法について説明しています。

- IP ファイアウォール規則/仮想ネットワーク サービス エンドポイントを備えた Azure SQL Database サーバーまたはプライベート エンドポイントを備えたマネージド インスタンスを使用して SSISDB をホストします。 前提条件として、Azure-SSIS IR を仮想ネットワークに参加させるために、仮想ネットワークのアクセス許可と設定を構成する必要があります。

- Azure Active Directory (Azure AD) 認証とデータ ファクトリの指定のシステム/ユーザー割り当てマネージド ID を使用し、Azure SQL Database サーバーまたはマネージド インスタンスに接続します。 前提条件として、SSISDB インスタンスを作成できるデータベース ユーザーとしてデータ ファクトリの指定のシステム/ユーザー割り当てマネージド ID を追加する必要があります。

- Azure-SSIS IR を仮想ネットワークに参加させるか、またはセルフホステッド IR を Azure-SSIS IR のプロキシとして構成してオンプレミスのデータにアクセスします。

これらの記事では、[Azure portal](create-azure-ssis-integration-runtime-portal.md)、[Azure PowerShell](create-azure-ssis-integration-runtime-powershell.md)、[Azure Resource Manager テンプレート](create-azure-ssis-integration-runtime-resource-manager-template.md)を使用して Azure-SSIS IR をプロビジョニングする方法を示します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure サブスクリプション**。 まだサブスクリプションをお持ちでない場合は、[無料試用版](https://azure.microsoft.com/pricing/free-trial/)アカウントを作成できます。

- **Azure SQL Database サーバーまたは SQL Managed Instance (オプション)** 。 まだデータベース サーバーまたはマネージド インスタンスをお持ちでない場合は、あらかじめ Azure portal でデータベース サーバーを作成しておいてください。 その後、Data Factory によって、このデータベース サーバーに SSISDB インスタンスが作成されます。 

  データベース サーバーまたはマネージド インスタンスは、統合ランタイムと同じ Azure リージョンに作成することをお勧めします。 この構成により、統合ランタイムは、Azure リージョンをまたがることなく SSISDB に実行ログを書き込むことができます。

  次の点に留意してください。

  - SSISDB インスタンスは、単一データベースやエラスティック プールの一部として、またはマネージド インスタンス上に自動的に作成できます。 パブリック ネットワークからアクセスできるほか、仮想ネットワークに参加させてアクセスすることもできます。 SSISDB のホストとなる SQL Database または SQL Managed Instance を選択する場合のガイダンスについては、この記事の[SQL Database と SQL Managed Instance の比較](#comparison-of-sql-database-and-sql-managed-instance)に関するセクションを参照してください。 
  
    IP ファイアウォール規則または仮想ネットワーク サービス エンドポイントを備えた Azure SQL Database サーバー、またはプライベート エンドポイントを備えた SQL Managed Instance を使用して SSISDB をホストする場合、またはセルフホステッド IR を構成せずにオンプレミスのデータへのアクセスを必要としている場合は、Azure-SSIS IR を仮想ネットワークに参加させる必要があります。 詳細については、「[Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる](./join-azure-ssis-integration-runtime-virtual-network.md)」を参照してください。

  - データベース サーバーで **[Azure サービスへのアクセスを許可]** の設定が有効になっていることを確認します。 この設定は、IP ファイアウォール規則または仮想ネットワーク サービス エンドポイントを備えた Azure SQL Database サーバー、またはプライベート エンドポイントを備えた SQL Managed Instance を使用して SSISDB をホストする場合は適用されません。 詳細については、「[Azure SQL Database のセキュリティ保護](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules)」を参照してください。 PowerShell を使用してこの設定を有効にするには、「[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)」を参照してください。

  - クライアント マシンの IP アドレス、またはクライアント マシンの IP アドレスを含む IP アドレスの範囲を、データベース サーバーのファイアウォール設定にあるクライアント IP アドレスの一覧に追加します。 詳細については、「[Azure SQL Database のサーバーレベルとデータベースレベルのファイアウォール規則](../azure-sql/database/firewall-configure.md)」を参照してください。

  - SQL 認証とサーバー管理者の資格情報を使用して、または Azure AD 認証とデータ ファクトリの指定のシステム/ユーザー割り当てマネージド ID を使用して、データベース サーバーに接続できます。 後者の場合、データベース サーバーへのアクセス許可が割り当てられている Azure AD グループにデータ ファクトリの指定のシステム/ユーザー割り当てマネージド ID を追加する必要があります。 詳細については、「[Azure-SSIS 統合ランタイムに対して Azure Active Directory 認証を有効にする](./enable-aad-authentication-azure-ssis-ir.md)」を参照してください。

  - データベース サーバーに SSISDB インスタンスがまだないことを確認します。 Azure-SSIS IR のプロビジョニングでは、既存の SSISDB インスタンスの使用はサポートされていません。

- **Azure Resource Manager 仮想ネットワーク (オプション)** 。 次の条件に 1 つでも当てはまる場合は、Azure Resource Manager 仮想ネットワークが必要です。

  - IP ファイアウォール規則/仮想ネットワーク サービス エンドポイントを備えた Azure SQL Database サーバーまたはプライベート エンドポイントを備えたマネージド インスタンスで SSISDB をホストしている。

  - セルフホステッド IR を構成せずに、Azure-SSIS IR 上で実行されている SSIS パッケージからオンプレミス データ ストアに接続する必要がある。

- **Azure PowerShell (オプション)** . PowerShell スクリプトを実行して Azure-SSIS IR をプロビジョニングする場合は、[Azure PowerShell のインストールおよび構成方法](/powershell/azure/install-az-ps)に関する記事の指示に従ってください。

### <a name="regional-support"></a>リージョン サポート

Data Factory と Azure-SSIS IR が使用可能な Azure リージョンの一覧については、[Data Factory と SSIS IR が利用可能なリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)に関するページを参照してください。

### <a name="comparison-of-sql-database-and-sql-managed-instance"></a>SQL Database と SQL Managed Instance の比較

次の表では、Azure-SSIR IR に関連する Azure SQL Database サーバーと SQL Managed Instance の一部の機能を比較しています。

| 特徴量 | SQL Database| SQL Managed Instance |
|---------|--------------|------------------|
| **スケジュール設定** | SQL Server エージェントは使用できません。<br/><br/>[Data Factory パイプラインでのパッケージの実行のスケジュール](/sql/integration-services/lift-shift/ssis-azure-schedule-packages#activity)に関するセクションを参照してください。| マネージド インスタンス エージェントを使用できます。 |
| **認証** | **db_owner** ロールのメンバーとしてデータ ファクトリのマネージド ID を持つ Azure AD グループを表す包含データベース ユーザーを使用して SSISDB を作成できます。<br/><br/>[Azure SQL Database サーバーで SSISDB を作成するための Azure AD 認証の有効化](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-authentication-on-azure-sql-database)に関するページを参照してください。 | データ ファクトリのマネージド ID を表す包含データベース ユーザーを使用して SSISDB を作成できます。 <br/><br/>[Azure SQL Managed Instance で SSISDB を作成するための Azure AD 認証の有効化](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-authentication-on-azure-sql-managed-instance)に関するページを参照してください。 |
| **サービス レベル** | Azure SQL Database サーバーで Azure-SSIS IR を作成するときに、SSISDB のサービス レベルを選択できます。 複数のサービス レベルがあります。 | マネージド インスタンスで Azure-SSIS IR を作成するときは、SSISDB のサービス レベルを選択することはできません。 マネージド インスタンス上のすべてのデータベースは、そのインスタンスに割り当てられた同じリソースを共有します。 |
| **Virtual Network** | IP ファイアウォール規則/仮想ネットワーク サービス エンドポイントを備えた Azure SQL Database サーバーを使用している場合、Azure-SSIS IR は Azure Resource Manager 仮想ネットワークに参加できます。 | プライベート エンドポイントを備えたマネージド インスタンスを使用している場合、Azure-SSIS IR は Azure Resource Manager 仮想ネットワークに参加できます。 マネージド インスタンスでパブリック エンドポイントを有効にしない場合、仮想ネットワークが必要です。<br/><br/>マネージド インスタンスと同じ仮想ネットワークに Azure-SSIS IR を参加させる場合は、Azure-SSIS IR を、必ずマネージド インスタンスとは異なるサブネットに配置します。 マネージド インスタンスとは異なる仮想ネットワークに Azure-SSIS IR を参加させる場合、仮想ネットワーク ピアリングかネットワーク間接続のいずれかをお勧めします。 [Azure SQL Database Managed Instance へのアプリケーションの接続](../azure-sql/managed-instance/connect-application-instance.md)に関する記事を参照してください。 |
| **分散トランザクション** | この機能はエラスティック トランザクションを介してサポートされます。 Microsoft 分散トランザクション コーディネーター (MSDTC) トランザクションはサポートされていません。 SSIS パッケージが MSDTC を使用して分散トランザクションを調整している場合は、Azure SQL Database 用のエラスティック トランザクションに移行することを検討してください。 詳細については、「[クラウド データベースにまたがる分散トランザクション](../azure-sql/database/elastic-transactions-overview.md)」を参照してください。 | サポートされていません。 |
| | | |


## <a name="next-steps"></a>次のステップ

- [Azure portal を使用して Azure-SSIS IR をプロビジョニングする方法を学習する](create-azure-ssis-integration-runtime-portal.md)。
- [Azure PowerShell を使用して Azure-SSIS IR をプロビジョニングする方法を学習する](create-azure-ssis-integration-runtime-powershell.md)。
- [Azure Resource Manager テンプレートを使用して Azure-SSIS IR をプロビジョニングする方法を学習する](create-azure-ssis-integration-runtime-resource-manager-template.md)。
- [Azure Data Factory で SSIS パッケージをデプロイして実行します](create-azure-ssis-integration-runtime-deploy-packages.md)。

このドキュメントでは、Azure SSIS IR に関する、次のような他のトピックも参照してください。

- [Azure-SSIS 統合ランタイム](concepts-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR など、統合ランタイムに関する一般的な情報が説明されています。
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR についての情報を取得して理解するための方法を示します。
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md): この記事では、Azure-SSIS IR を停止、開始、または削除する方法を示しています。 また、ノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。
- [Azure での SSIS パッケージのデプロイ、実行、監視](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Azure での SSISDB への接続](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Windows 認証を使用したオンプレミス データ ソースへの接続](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Azure でのパッケージ実行のスケジュール設定](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
