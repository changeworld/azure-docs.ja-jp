---
title: Azure PowerShell を使用して Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる
description: Azure PowerShell を使用して Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる方法について説明します。
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a4d06256407fd42bdfa9f92cc2306df7dd33983c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087858"
---
# <a name="join-azure-ssis-integration-runtime-to-a-virtual-network-via-azure-powershell"></a>Azure PowerShell を使用して Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

この記事では、Azure PowerShell を使用して、Azure Data Factory (ADF) の既存の Azure-SQL Server Integration Services (SSIS) 統合ランタイム (IR) を仮想ネットワークに参加させる方法について説明します。 

## <a name="create-variables"></a>変数の作成

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Azure Resource Manager or Classic
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server configured with a private endpoint/IP firewall rule/virtual network service endpoint or Azure SQL Managed Instance that joins a virtual network to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend Azure Resource Manager virtual network, because classic virtual network will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for Azure SQL Database server configured with a virtual network service endpoint or a different subnet from the one used for Azure SQL Managed Instance that joins a virtual network
$SubnetId = $VnetId + '/subnets/' + $SubnetName 
# Virtual network injection method: Standard or Express. For comparison, see https://docs.microsoft.com/azure/data-factory/azure-ssis-integration-runtime-virtual-network-configuration.
$VnetInjectionMethod = "Standard" # Standard by default, whereas Express lets you use the express virtual network injection method
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

## <a name="configure-a-virtual-network"></a>仮想ネットワークを構成する

Azure-SSIS IR を仮想ネットワークに参加させる前に、仮想ネットワークを構成する必要があります。 Azure-SSIS IR を仮想ネットワークに参加させるよう、仮想ネットワークのアクセス許可および設定を自動的に構成するには、次のスクリプトを追加します。

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

[Azure-SSISIR の作成](create-azure-ssis-integration-runtime-powershell.md)に関する記事では、Azure-SSIS IR を作成し、同じスクリプト内の仮想ネットワークに参加させる方法が説明されています。 既に Azure-SSIS IR がある場合は、次の手順に従って仮想ネットワークに参加させます。 
1. Azure-SSIS IR を停止します。 
1. 仮想ネットワークに参加させるよう Azure-SSIS IR を構成します。 
1. Azure-SSIS IR を開始します。 

## <a name="stop-your-azure-ssis-ir"></a>Azure-SSIS IR を停止する

Azure-SSIS IR を仮想ネットワークに参加させるには、先に停止させる必要があります。 このコマンドは、そのすべてのノードを解放し、課金を停止します。

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

## <a name="configure-your-azure-ssis-ir-to-join-a-virtual-network"></a>仮想ネットワークに参加させるよう Azure-SSIS IR を構成する

Azure-SSIS IR を仮想ネットワークに参加させるには、`Set-AzDataFactoryV2IntegrationRuntime` コマンドを実行します。 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -SubnetId $SubnetId `
    -VNetInjectionMethod $VnetInjectionMethod

# Add public IP address parameters if you use the standard virtual network injection method and bring your own static public IP addresses
if($VnetInjectionMethod -eq "Standard")
{
    if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
    {
        $publicIPs = @($FirstPublicIP, $SecondPublicIP)
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -PublicIPs $publicIPs
    }
}
```

## <a name="start-your-azure-ssis-ir"></a>Azure-SSIS IR を開始する

Azure-SSIS IR を開始するには、次のコマンドを実行します。 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

高速または標準の仮想ネットワーク インジェクション方法を使用した場合、このコマンドの完了には、それぞれ 5 分、または 20 ～ 30 分かかります。

## <a name="next-steps"></a>次のステップ

- [仮想ネットワークを構成して Azure-SSIS IR のインジェクションを行う](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [高速仮想ネットワーク インジェクション方法](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [標準仮想ネットワーク インジェクション方法](azure-ssis-integration-runtime-standard-virtual-network-injection.md)
- [ADF UI を使用して仮想ネットワークに Azure SSIS IR を参加させる](join-azure-ssis-integration-runtime-virtual-network-ui.md)

Azure-SSIS IR の詳細については、次の記事を参照してください。 

- 「[Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime)」。 この記事では、Azure-SSIS IR を含め、IR に関する全般的な概念情報が説明されています。 
- [チュートリアル:Azure への SSIS パッケージのデプロイに関するチュートリアル](tutorial-deploy-ssis-packages-azure.md)の手順に従って作成します。 このチュートリアルでは、Azure-SSIS IR の作成手順を示しています。 ここでは、SSISDB をホストするために Azure SQL Database サーバーを使用しています。 
- 「[Azure-SSIS IR を作成する](create-azure-ssis-integration-runtime.md)」。 この記事は、このチュートリアルを拡張しています。 仮想ネットワーク サービス エンドポイント、IP ファイアウォール規則、プライベート エンドポイントで構成された Azure SQL Database サーバー、または仮想ネットワークに参加する Azure SQL Managed Instance を使用して SSISDB をホストする手順について説明しています。 Azure-SSIS IR を仮想ネットワークに参加させる方法について説明します。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR についての情報を取得して理解するための方法を示します。
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md): この記事では、Azure-SSIS IR を停止、開始、または削除する方法を示しています。 また、ノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。
