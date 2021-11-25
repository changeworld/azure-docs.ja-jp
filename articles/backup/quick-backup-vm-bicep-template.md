---
title: クイックスタート - Bicep テンプレートによる VM のバックアップ
description: Bicep テンプレートを使用して仮想マシンをバックアップする方法について説明します
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 11/17/2021
ms.reviewer: Daya-Patil
ms.custom: mvc,subject-bicepqs, devx-track-azurepowershell
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: d72e48e0bb7116bd1a828a3d772b6f283e6fd0a2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725239"
---
#  <a name="back-up-a-virtual-machine-in-azure-with-a-bicep-template"></a>Bicep テンプレートを使用して Azure で仮想マシンをバックアップする

[Azure Backup](backup-overview.md) を使用すると、Azure portal、PowerShell、CLI、Azure Resource Manager、Bicep などの複数のオプションを使用して Azure VM をバックアップできます。 この記事では、Azure Bicep テンプレートと Azure PowerShell を使用して Azure VM をバックアップする方法について説明します。 このクイックスタートでは、Bicep テンプレートをデプロイして Recovery Services コンテナーを作成するプロセスを中心に説明します。 Bicep テンプレートの開発に関する詳細については、[Bicep のドキュメント](../azure-resource-manager/bicep/deploy-cli.md)と[テンプレート リファレンス](/azure/templates/microsoft.recoveryservices/allversions)をご覧ください。

Bicep は、Azure リソースを宣言によってデプロイするための言語です。 JSON の代わりに Bicep を使用して、Azure Resource Manager テンプレート (ARM テンプレート) を開発できます。 Bicep の構文により複雑さが軽減され、開発エクスペリエンスが向上します。 Bicep は ARM テンプレート JSON に対する透過的な抽象化であり、JSON テンプレートのすべての機能が提供されます。 デプロイ中に、Bicep CLI により、Bicep ファイルが ARM テンプレート JSON に変換されます。 Bicep ファイルでは、リソースを作成するための一連のプログラミング コマンドを記述せずに、Azure リソースとリソース プロパティを記述します。

ARM テンプレートで有効なリソースの種類、API バージョン、およびプロパティは、Bicep ファイルでも有効です。

## <a name="prerequisites"></a>前提条件

Bicep の開発環境を設定するには、「[Bicep ツールをインストールする](../azure-resource-manager/bicep/install.md)」を参照してください。

>[!Note]
>記事で詳細に説明されているように、最新の [Azure PowerShell モジュール](/powershell/azure/new-azureps-module-az?view=azps-6.6.0)と Bicep CLI をインストールします。

## <a name="review-the-template"></a>テンプレートを確認する

下で使用されているテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/recovery-services-create-vm-and-configure-backup/)からのものです。 このテンプレートを使用すると、保護のために _DefaultPolicy_ を使用して構成された単純な Windows VM と Recovery Services コンテナーをデプロイできます。

```json
@description('Specifies a name for generating resource names.')
@maxLength(8)
param projectName string

@description('Specifies the location for all resources.')
param location string = resourceGroup().location

@description('Specifies the administrator username for the Virtual Machine.')
param adminUsername string

@description('Specifies the administrator password for the Virtual Machine.')
@secure()
param adminPassword string

@description('Specifies the unique DNS Name for the Public IP used to access the Virtual Machine.')
param dnsLabelPrefix string

@description('Virtual machine size.')
param vmSize string = 'Standard_A2'

@description('Specifies the Windows version for the VM. This will pick a fully patched image of this given Windows version.')
@allowed([
  '2008-R2-SP1'
  '2012-Datacenter'
  '2012-R2-Datacenter'
  '2016-Nano-Server'
  '2016-Datacenter-with-Containers'
  '2016-Datacenter'
  '2019-Datacenter'
  '2019-Datacenter-Core'
  '2019-Datacenter-Core-smalldisk'
  '2019-Datacenter-Core-with-Containers'
  '2019-Datacenter-Core-with-Containers-smalldisk'
  '2019-Datacenter-smalldisk'
  '2019-Datacenter-with-Containers'
  '2019-Datacenter-with-Containers-smalldisk'
])
param windowsOSVersion string = '2016-Datacenter'

var storageAccountName = '${projectName}store'
var networkInterfaceName = '${projectName}-nic'
var vNetAddressPrefix = '10.0.0.0/16'
var vNetSubnetName = 'default'
var vNetSubnetAddressPrefix = '10.0.0.0/24'
var publicIPAddressName = '${projectName}-ip'
var vmName = '${projectName}-vm'
var vNetName = '${projectName}-vnet'
var vaultName = '${projectName}-vault'
var backupFabric = 'Azure'
var backupPolicyName = 'DefaultPolicy'
var protectionContainer = 'iaasvmcontainer;iaasvmcontainerv2;${resourceGroup().name};${vmName}'
var protectedItem = 'vm;iaasvmcontainerv2;${resourceGroup().name};${vmName}'
var networkSecurityGroupName = 'default-NSG'

resource storageAccount 'Microsoft.Storage/storageAccounts@2021-06-01' = {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}

resource publicIPAddress 'Microsoft.Network/publicIPAddresses@2020-06-01' = {
  name: publicIPAddressName
  location: location
  properties: {
    publicIPAllocationMethod: 'Dynamic'
    dnsSettings: {
      domainNameLabel: dnsLabelPrefix
    }
  }
}

resource networkSecurityGroup 'Microsoft.Network/networkSecurityGroups@2020-06-01' = {
  name: networkSecurityGroupName
  location: location
  properties: {
    securityRules: [
      {
        name: 'default-allow-3389'
        properties: {
          priority: 1000
          access: 'Allow'
          direction: 'Inbound'
          destinationPortRange: '3389'
          protocol: 'Tcp'
          sourceAddressPrefix: '*'
          sourcePortRange: '*'
          destinationAddressPrefix: '*'
        }
      }
    ]
  }
}

resource vNet 'Microsoft.Network/virtualNetworks@2020-06-01' = {
  name: vNetName
  location: location
  properties: {
    addressSpace: {
      addressPrefixes: [
        vNetAddressPrefix
      ]
    }
    subnets: [
      {
        name: vNetSubnetName
        properties: {
          addressPrefix: vNetSubnetAddressPrefix
          networkSecurityGroup: {
            id: networkSecurityGroup.id
          }
        }
      }
    ]
  }
}

resource networkInterface 'Microsoft.Network/networkInterfaces@2020-06-01' = {
  name: networkInterfaceName
  location: location
  properties: {
    ipConfigurations: [
      {
        name: 'ipconfig1'
        properties: {
          privateIPAllocationMethod: 'Dynamic'
          publicIPAddress: {
            id: publicIPAddress.id
          }
          subnet: {
            id: '${vNet.id}/subnets/${vNetSubnetName}'
          }
        }
      }
    ]
  }
}

resource virtualMachine 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  name: vmName
  location: location
  properties: {
    hardwareProfile: {
      vmSize: vmSize
    }
    osProfile: {
      computerName: vmName
      adminUsername: adminUsername
      adminPassword: adminPassword
    }
    storageProfile: {
      imageReference: {
        publisher: 'MicrosoftWindowsServer'
        offer: 'WindowsServer'
        sku: windowsOSVersion
        version: 'latest'
      }
      osDisk: {
        createOption: 'FromImage'
      }
      dataDisks: [
        {
          diskSizeGB: 1023
          lun: 0
          createOption: 'Empty'
        }
      ]
    }
    networkProfile: {
      networkInterfaces: [
        {
          id: networkInterface.id
        }
      ]
    }
    diagnosticsProfile: {
      bootDiagnostics: {
        enabled: true
        storageUri: storageAccount.properties.primaryEndpoints.blob
      }
    }
  }
}

resource recoveryServicesVault 'Microsoft.RecoveryServices/vaults@2020-02-02' = {
  name: vaultName
  location: location
  sku: {
    name: 'RS0'
    tier: 'Standard'
  }
  properties: {}
}

resource vaultName_backupFabric_protectionContainer_protectedItem 'Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems@2020-02-02' = {
  name: '${vaultName}/${backupFabric}/${protectionContainer}/${protectedItem}'
  properties: {
    protectedItemType: 'Microsoft.Compute/virtualMachines'
    policyId: '${recoveryServicesVault.id}/backupPolicies/${backupPolicyName}'
    sourceResourceId: virtualMachine.id
  }
} 

```

上のテンプレート内に定義されているリソースは次のとおりです。

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
- [Microsoft.RecoveryServices/vaults](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults)
- [Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems](/azure/templates/microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems)

## <a name="deploy-the-template"></a>テンプレートのデプロイ

テンプレートをデプロイするには、 _[試してみる]_ を選択して Azure Cloud Shell を開き、シェル ウィンドウに次の PowerShell スクリプトを貼り付けます。 コードを貼り付けるには、シェル ウィンドウを右クリックして、 **[貼り付け]** を選択します。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (for example, centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.recoveryservices/recovery-services-create-vm-and-configure-backup/main.bicep"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix 

```

## <a name="validate-the-deployment"></a>デプロイの検証

### <a name="start-a-backup-job"></a>バックアップ ジョブを開始する

このテンプレートにより、VM が作成され、その VM でバックアップが可能になります。 テンプレートをデプロイしたら、バックアップ ジョブを開始する必要があります。 詳細については、「[バックアップ ジョブを開始する](quick-backup-vm-powershell.md#start-a-backup-job)」を参照してください。

### <a name="monitor-the-backup-job"></a>バックアップ ジョブを監視する

バックアップ ジョブを監視するには、「[バックアップ ジョブの監視](quick-backup-vm-powershell.md#monitor-the-backup-job)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

- VM をバックアップする必要がなくなった場合は、クリーンアップできます。
- VM の復元を試す場合は、クリーンアップ プロセスをスキップしてください。
- 既存の VM を使用した場合は、最後の [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドレットを省略して、リソース グループと VM を保持することができます。

次の手順に従います。

1. 保護を無効にし、復元ポイントとコンテナーを削除します。

1. リソース グループおよび関連付けられている VM リソースを次のように削除します。

   ```azurepowershell
   Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
   $vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
   Remove-AzRecoveryServicesVault -Vault $vault
   Remove-AzResourceGroup -Name "myResourceGroup" 

   ```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Recovery Services コンテナーを作成し、VM の保護を有効にし、最初の復元ポイントを作成しました。

- Azure portal 内で VM をバックアップする[方法の詳細](tutorial-backup-vm-at-scale.md)
- VM をすばやく復元する[方法の詳細](tutorial-restore-disk.md)
- Bicep テンプレートを作成する[方法の詳細](../azure-resource-manager/bicep/quickstart-create-bicep-use-visual-studio-code.md?tabs=CLI)。
- Azure Resource Manager テンプレート (ARM テンプレート) を Bicep ファイルに逆コンパイルする[方法の詳細](../azure-resource-manager/bicep/decompile.md?tabs=azure-cli)。


