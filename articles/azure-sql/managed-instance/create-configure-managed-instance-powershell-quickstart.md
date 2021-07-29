---
title: Azure SQL Managed Instance の作成 - クイックスタート
description: Azure PowerShell を使用して Azure SQL Managed Instance のインスタンスを作成します。
services: sql-managed-instance
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: contperf-fy21q1, devx-track-azurecli, devx-track-azurepowershell
ms.devlang: ''
ms.topic: quickstart
author: MashaMSFT
ms.author: mathoma
ms.reviewer: ''
ms.date: 06/25/2021
ms.openlocfilehash: 6697cb9b89d3cb960f2def9ff4dcf478080b7b07
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2021
ms.locfileid: "113221045"
---
# <a name="quickstart-create-a-managed-instance-using-azure-powershell"></a>クイックスタート: Azure PowerShell を使用してマネージド インスタンスを作成する

このクイックスタートでは、Azure PowerShell を使用して [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) のインスタンスを作成する方法について説明します。 


## <a name="prerequisite"></a>前提条件

- 有効な Azure サブスクリプション アカウントがない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
- 最新バージョンの [Azure PowerShell](/powershell/azure/install-az-ps)。 

## <a name="set-variables"></a>変数の設定

SQL Manged Instance を作成するには、Azure 内でリソースをいくつか作成する必要があります。そのため、作業を簡単にするため、Azure PowerShell コマンドでは変数が利用されます。 変数を定義し、同じ PowerShell セッション内の各セクションでコマンドレットを実行します。 

```azurepowershell-interactive
$NSnetworkModels = "Microsoft.Azure.Commands.Network.Models"
$NScollections = "System.Collections.Generic"
# The SubscriptionId in which to create these objects
$SubscriptionId = ''
# Set the resource group name and location for your managed instance
$resourceGroupName = "myResourceGroup-$(Get-Random)"
$location = "eastus2"
# Set the networking values for your managed instance
$vNetName = "myVnet-$(Get-Random)"
$vNetAddressPrefix = "10.0.0.0/16"
$miSubnetName = "myMISubnet-$(Get-Random)"
$miSubnetAddressPrefix = "10.0.0.0/24"
#Set the managed instance name for the new managed instance
$instanceName = "myMIName-$(Get-Random)"
# Set the admin login and password for your managed instance
$miAdminSqlLogin = "SqlAdmin"
$miAdminSqlPassword = "ChangeYourAdminPassword1"
# Set the managed instance service tier, compute level, and license mode
$edition = "General Purpose"
$vCores = 4
$maxStorage = 128
$computeGeneration = "Gen5"
$license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server licence that can be used for AHB discount
```

## <a name="create-resource-group"></a>リソース グループの作成 

まず、Azure に接続し、サブスクリプション コンテキストを設定し、リソース グループを作成します。 

これを行うには、この PowerShell スクリプトを実行します。 

```azurepowershell-interactive

## Connect to Azure
Connect-AzAccount

# Set subscription context
Set-AzContext -SubscriptionId $SubscriptionId 

# Create a resource group
$resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
```

## <a name="configure-networking"></a>ネットワークを構成する

リソース グループの作成後、仮想ネットワーク、サブネット、ネットワーク セキュリティ グループ、ルーティング テーブルなど、ネットワーク リソースを構成します。 この例では、「**Delegate subnet for Managed Instance deployment**」(Managed Instance デプロイのデリゲート サブネット) スクリプトの使用を実演します。これは GitHub で [delegate-subnet.ps1](https://github.com/microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet) として入手できます。

これを行うには、この PowerShell スクリプトを実行します。 

```azurepowershell-interactive

# Configure virtual network, subnets, network security group, and routing table
$virtualNetwork = New-AzVirtualNetwork `
                      -ResourceGroupName $resourceGroupName `
                      -Location $location `
                      -Name $vNetName `
                      -AddressPrefix $vNetAddressPrefix

                  Add-AzVirtualNetworkSubnetConfig `
                      -Name $miSubnetName `
                      -VirtualNetwork $virtualNetwork `
                      -AddressPrefix $miSubnetAddressPrefix |
                  Set-AzVirtualNetwork
                  
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

$parameters = @{
    subscriptionId = $SubscriptionId
    resourceGroupName = $resourceGroupName
    virtualNetworkName = $vNetName
    subnetName = $miSubnetName
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters

$virtualNetwork = Get-AzVirtualNetwork -Name $vNetName -ResourceGroupName $resourceGroupName
$miSubnet = Get-AzVirtualNetworkSubnetConfig -Name $miSubnetName -VirtualNetwork $virtualNetwork
$miSubnetConfigId = $miSubnet.Id
```

## <a name="create-managed-instance"></a>マネージド インスタンスの作成 

セキュリティを強化するため、SQL Managed Instance の資格情報には複雑でランダム化されたパスワードを作成してください。 

```azurepowershell-interactive
# Create credentials
$secpassword = ConvertTo-SecureString $miAdminSqlPassword -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential ($miAdminSqlLogin, $secpassword)
```

次に、SQL Managed Instance を作成します。 

```azurepowershell-interactive
# Create managed instance
New-AzSqlInstance -Name $instanceName `
                      -ResourceGroupName $resourceGroupName -Location $location -SubnetId $miSubnetConfigId `
                      -AdministratorCredential $credential `
                      -StorageSizeInGB $maxStorage -VCore $vCores -Edition $edition `
                      -ComputeGeneration $computeGeneration -LicenseType $license
```

この操作の完了には時間がかかることがあります。 詳細については、[管理操作](management-operations-overview.md)に関するページを参照してください。


## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループとマネージド インスタンスを保持して次の手順に進み、クライアント仮想マシンを利用して SQL Managed Instance に接続する方法について学習してください。 

これらのリソースの使用が完了したら、作成したリソース グループを削除することができます。これにより、サーバーとその中の単一データベースも削除されます。

```azurepowershell-interactive
# Clean up deployment 
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```


## <a name="next-steps"></a>次のステップ

SQL Managed Instance の作成後、クライアント VM をデプロイして SQL Managed Instance に接続し、サンプル データベースを復元します。 

> [!div class="nextstepaction"]
> [クライアント VM の作成](connect-vm-instance-configure.md)
> [データベースの復旧](restore-sample-database-quickstart.md)


