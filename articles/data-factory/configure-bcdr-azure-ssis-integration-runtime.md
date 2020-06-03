---
title: Azure-SSIS 統合ランタイムを SQL Database のフェールオーバー用に構成する
description: この記事では、Azure SQL Database geo レプリケーションを使用して SSISDB データベース用に Azure-SSIS 統合ランタイムとフェールオーバーを構成する方法について説明します
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: 795247cd0d6adfd27115b73c1d0de02e6810d670
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201139"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-sql-database-geo-replication-and-failover"></a>SQL Database geo レプリケーションとフェールオーバーを使用して Azure-SSIS 統合ランタイムを構成する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

この記事では、Azure SQL Database geo レプリケーションを使用して、SSISDB データベース用に Azure-SSIS 統合ランタイム (IR) を構成する方法について説明します。 フェールオーバーが発生したときに、セカンダリ データベースにより Azure-SSIS IR の動作を維持できます。

SQL Database の geo レプリケーションとフェールオーバーの詳細については、[アクティブ geo レプリケーションと自動フェールオーバー グループの概要](../sql-database/sql-database-geo-replication-overview.md)に関するページを参照してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-a-sql-database-managed-instance"></a>SQL Database マネージド インスタンスによる Azure-SSIS IR フェールオーバー

### <a name="prerequisites"></a>前提条件

Azure SQL Database マネージド インスタンスでは、*データベース マスター キー (DMK)* を使用して、データベースに格納されるデータ、資格情報、および接続情報がセキュリティで保護されます。 DMK の暗号化を自動的に解除できるように、*サーバー マスター キー (SMK)* を使用してキーのコピーが暗号化されます。 

SMK はフェールオーバー グループにレプリケートされません。 フェールオーバー後の DMK 復号化のために、プライマリ インスタンスとセカンダリ インスタンスの両方にパスワードを追加する必要があります。

1. プライマリ インスタンスで SSISDB に対して次のコマンドを実行します。 この手順では、新しい暗号化パスワードが追加されます。

    ```sql
    ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
    ```

2. Azure SQL Database マネージド インスタンス上にフェールオーバー グループを作成します。

3. 新しい暗号化パスワードを使用して、セカンダリ インスタンスに **sp_control_dbmasterkey_password**  を実行します。

    ```sql
    EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
        @password = N'<password>', @action = N'add';  
    GO
    ```

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>シナリオ 1: Azure-SSIS IR が読み取りと書き込みのリスナー エンドポイントを指している

Azure-SSIS IR が読み取りと書き込みのリスナー エンドポイントを指すようにするには、まず、プライマリ サーバー エンドポイントを指す必要があります。 SSISDB をフェールオーバー グループに配置した後は、読み取りと書き込みのリスナー エンドポイントに変更して Azure-SSIS IR を再起動することができます。

#### <a name="solution"></a>解決策

フェールオーバーが発生した場合は、次の手順を行います。

1. プライマリ リージョンの Azure-SSIS IR を停止します。

2. セカンダリ インスタンスでカスタム セットアップを行うには、新しいリージョン、仮想ネットワーク、および Shared Access Signature (SAS) URI 情報を使用して Azure-SSIS IR を編集します。 Azure-SSIS IR が読み取りと書き込みのリスナーを指し、エンドポイントが Azure-SSIS IR に対して透過的であるため、エンドポイントを編集する必要はありません。

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Azure-SSIS IR を再起動します。

### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>シナリオ 2: Azure-SSIS IR がプライマリ サーバー エンドポイントを指している

このシナリオは、Azure-SSIS IR がプライマリ サーバー エンドポイントを指している場合に適しています。

#### <a name="solution"></a>解決策

フェールオーバーが発生した場合は、次の手順を行います。

1. プライマリ リージョンの Azure-SSIS IR を停止します。

2. セカンダリ インスタンスの新しいリージョン、エンドポイント、および仮想ネットワークの情報を使って Azure-SSIS IR を編集します。

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Azure-SSIS IR を再起動します。

### <a name="scenario-3-azure-ssis-ir-is-pointing-to-a-public-endpoint-of-a-sql-database-managed-instance"></a>シナリオ 3: Azure-SSIS IR が SQL Database マネージド インスタンスのパブリック エンドポイントを指している

このシナリオは、Azure-SSIS IR が Azure SQL Database マネージド インスタンスのパブリック エンドポイントを指していて、仮想ネットワークに参加していない場合に適しています。 シナリオ 2 との唯一の違いは、フェールオーバー後に Azure-SSIS IR の仮想ネットワーク情報を編集する必要がないことです。

#### <a name="solution"></a>解決策

フェールオーバーが発生した場合は、次の手順を行います。

1. プライマリ リージョンの Azure-SSIS IR を停止します。

2. セカンダリ インスタンスの新しいリージョンとエンドポイントの情報で Azure-SSIS IR を編集します。

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Azure-SSIS IR を再起動します。

### <a name="scenario-4-attach-an-existing-ssisdb-instance-ssis-catalog-to-a-new-azure-ssis-ir"></a>シナリオ 4: 既存の SSISDB インスタンス (SSIS カタログ) を新しい Azure-SSIS IR にアタッチする

このシナリオは、現在のリージョンで Azure Data Factory または Azure-SSIS IR の障害が発生した場合に、SSISDB によって新しい Azure-SSIS IR を新しいリージョン内で操作する場合に適しています。

#### <a name="solution"></a>解決策

フェールオーバーが発生した場合は、次の手順を行います。

> [!NOTE]
> 手順 4 (IR の作成) に PowerShell を使用します。 そうしないと、SSISDB が既に存在していることを示すエラーが Azure portal によって報告されます。

1. プライマリ リージョンの Azure-SSIS IR を停止します。

2. ストアド プロシージャを実行して、 **\<new_data_factory_name\>** と **\<new_integration_runtime_name\>** からの接続を受け入れるように SSISDB 内のメタデータを更新します。
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. 新しいリージョンに **\<new_data_factory_name\>** という名前の新しいデータ ファクトリを作成します。

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
    ```
    
    この PowerShell コマンドの詳細については、[PowerShell を使用した Azure データ ファクトリの作成](quickstart-create-data-factory-powershell.md)に関するページを参照してください。

4. Azure PowerShell を使用して、新しいリージョンに **\<new_integration_runtime_name\>** という名前の新しい Azure-SSIS IR を作成します。

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    この PowerShell コマンドの詳細については、「[Azure Data Factory で Azure-SSIS 統合ランタイムを作成する](create-azure-ssis-integration-runtime.md)」を参照してください。



## <a name="azure-ssis-ir-failover-with-sql-database"></a>SQL Database による Azure-SSIS IR フェールオーバー

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>シナリオ 1: Azure-SSIS IR が読み取りと書き込みのリスナー エンドポイントを指している

このシナリオは、次の場合に適しています。

- Azure-SSIS IR がフェールオーバー グループの読み取りと書き込みのリスナー エンドポイントを指している。
- SQL Database サーバーが仮想ネットワーク サービス エンドポイントに関する規則で "*構成されていない*"。

Azure-SSIS IR が読み取りと書き込みのリスナー エンドポイントを指すようにするには、まず、プライマリ サーバー エンドポイントを指す必要があります。 SSISDB をフェールオーバー グループに配置した後は、読み取りと書き込みのリスナー エンドポイントに変更して Azure-SSIS IR を再起動することができます。

#### <a name="solution"></a>解決策

フェールオーバーが発生すると、Azure-SSIS IR に対して透過的になります。 Azure-SSIS IR は、フェールオーバー グループの新しいプライマリに自動的に接続します。 

Azure-SSIS IR のリージョンまたはその他の情報を更新する場合は、これを停止し、編集して、再起動することができます。


### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>シナリオ 2: Azure-SSIS IR がプライマリ サーバー エンドポイントを指している

このシナリオは、Azure-SSIS IR がプライマリ サーバー エンドポイントを指している場合に適しています。

#### <a name="solution"></a>解決策

フェールオーバーが発生した場合は、次の手順を行います。

1. プライマリ リージョンの Azure-SSIS IR を停止します。

2. セカンダリ インスタンスの新しいリージョン、エンドポイント、および仮想ネットワークの情報を使って Azure-SSIS IR を編集します。

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Azure-SSIS IR を再起動します。

### <a name="scenario-3-attach-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>シナリオ 3: 既存の SSISDB (SSIS カタログ) を新しい Azure-SSIS IR にアタッチする

このシナリオは、セカンダリ リージョンで新しい Azure-SSIS IR をプロビジョニングする場合に適しています。 これは、現在のリージョンで Azure Data Factory または Azure-SSIS IR の障害が発生した場合に、ご利用の SSISDB によって新しい Azure-SSIS IR を新しいリージョン内で操作し続ける場合にも適しています。

#### <a name="solution"></a>解決策

フェールオーバーが発生した場合は、次の手順を行います。

> [!NOTE]
> 手順 4 (IR の作成) に PowerShell を使用します。 そうしないと、SSISDB が既に存在していることを示すエラーが Azure portal によって報告されます。

1. プライマリ リージョンの Azure-SSIS IR を停止します。

2. ストアド プロシージャを実行して、 **\<new_data_factory_name\>** と **\<new_integration_runtime_name\>** からの接続を受け入れるように SSISDB 内のメタデータを更新します。
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. 新しいリージョンに **\<new_data_factory_name\>** という名前の新しいデータ ファクトリを作成します。

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
    ```
    
    この PowerShell コマンドの詳細については、[PowerShell を使用した Azure データ ファクトリの作成](quickstart-create-data-factory-powershell.md)に関するページを参照してください。

4. Azure PowerShell を使用して、新しいリージョンに **\<new_integration_runtime_name\>** という名前の新しい Azure-SSIS IR を作成します。

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    この PowerShell コマンドの詳細については、「[Azure Data Factory で Azure-SSIS 統合ランタイムを作成する](create-azure-ssis-integration-runtime.md)」を参照してください。


## <a name="next-steps"></a>次のステップ

Azure SSIS IR の以下のその他の構成オプションを検討してください。

- [Azure-SSIS 統合ランタイムをハイ パフォーマンス用に構成する](configure-azure-ssis-integration-runtime-performance.md)

- [Azure-SSIS 統合ランタイムの設定をカスタマイズする](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Azure-SSIS 統合ランタイムの Enterprise Edition をプロビジョニングする](how-to-configure-azure-ssis-ir-enterprise-edition.md)
