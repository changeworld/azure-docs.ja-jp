---
title: Azure PowerShell を使用して Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる
description: Azure PowerShell を使用し、Azure-SSIS 統合ランタイムを Azure 仮想ネットワークに参加させる方法について説明します。
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/16/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b9d264bf4e52ec79a70d6efcd326f395685d12b2
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2021
ms.locfileid: "129403136"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network-with-azure-powershell"></a>Azure PowerShell を使用して Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="define-the-variables"></a>変数を定義する

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use SQL Database with IP firewall rules/virtual network service endpoints or SQL Managed Instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for SQL Database with virtual network service endpoints, or a different subnet from the one used for SQL Managed Instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

## <a name="configure-a-virtual-network"></a>仮想ネットワークを構成する

Azure-SSIS IR を仮想ネットワークに参加させる前に、仮想ネットワークを構成する必要があります。 Azure-SSIS IR が仮想ネットワークを参加させるための仮想ネットワークのアクセス許可および設定を自動的に構成するには、次のスクリプトを追加します。

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

## <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Azure-SSIS IR を作成して仮想ネットワークに参加させる

Azure-SSIS IR を作成し、作成と同時に仮想ネットワークに参加させることができます。 完全なスクリプトと手順については、[Azure-SSIS IR の作成](create-azure-ssis-integration-runtime-powershell.md)に関する記事を参照してください。

## <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>既存の Azure-SSIS IR を仮想ネットワークに参加させる

[Azure-SSISIR の作成](create-azure-ssis-integration-runtime.md)に関する記事では、Azure-SSIS IR を作成し、同じスクリプト内の仮想ネットワークに参加させる方法が説明されています。 既に Azure-SSIS IR がある場合は、次の手順に従って仮想ネットワークに参加させます。 
1. Azure-SSIS IR を停止します。 
1. 仮想ネットワークに参加するように Azure-SSIS IR を構成します。 
1. Azure-SSIS IR を開始します。 

## <a name="stop-the-azure-ssis-ir"></a>Azure-SSIS IR を停止する

Azure-SSIS IR を仮想ネットワークに参加させるには、先に停止する必要があります。 このコマンドは、そのすべてのノードを解放し、課金を停止します。

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

## <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Azure-SSIS IR が参加するように仮想ネットワーク設定を構成する

Azure SSIS が参加する仮想ネットワークの設定を構成するには、次のスクリプトを使用します。 

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

## <a name="configure-the-azure-ssis-ir"></a>Azure-SSIS IR を構成する

Azure-SSIS IR を仮想ネットワークに参加させるには、`Set-AzDataFactoryV2IntegrationRuntime` コマンドを実行します。 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -VnetId $VnetId `
    -Subnet $SubnetName

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

## <a name="start-the-azure-ssis-ir"></a>Azure-SSIS IR を開始する

Azure-SSIS IR を開始するには、次のコマンドを実行します。 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

このコマンドは、完了するまでに 20 ～ 30 分かかります。

## <a name="next-steps"></a>次のステップ
- [Azure-SSIS Integration Runtime を仮想ネットワークに参加させる - 概要](join-azure-ssis-integration-runtime-virtual-network.md)
- [Azure-SSIS Integration Runtime 仮想ネットワーク構成の詳細](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Azure Data Factory Studio UI を使用して Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる](join-azure-ssis-integration-runtime-virtual-network-ui.md)

Azure-SSIS IR の詳細については、次の記事を参照してください。 
- 「[Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime)」。 この記事では、Azure-SSIS IR を含め、IR に関する全般的な概念情報が説明されています。 
- [チュートリアル:Azure への SSIS パッケージのデプロイに関するチュートリアル](./tutorial-deploy-ssis-packages-azure.md)の手順に従って作成します。 このチュートリアルでは、Azure-SSIS IR の作成手順を示しています。 Azure SQL Database を使って SSIS カタログをホストします。 
- 「[Azure-SSIS IR を作成する](create-azure-ssis-integration-runtime.md)」。 この記事は、このチュートリアルを拡張しています。 仮想ネットワーク サービス エンドポイントまたは仮想ネットワーク内の SQL Managed Instance を含む Azure SQL Database を使用して、SSIS カタログをホストする手順について説明されます。 Azure-SSIS IR を仮想ネットワークに参加させる方法が示されます。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR に関する情報を取得する方法が説明されています。 返された情報の状態の説明が提供されます。 
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md): この記事では、Azure-SSIS IR を停止、開始、または削除する方法を示しています。 また、ノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。
