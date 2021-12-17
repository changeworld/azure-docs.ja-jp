---
title: Azure PowerShell を使用して VM 拡張機能を有効にする
description: この記事では、Azure PowerShell を使用して、ハイブリッド環境で実行されている Azure Arc 対応サーバーに仮想マシン拡張機能をデプロイする方法について説明します。
ms.date: 10/21/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 03fb598989c7f308b3e481824e17d423f6d31824
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470625"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Azure PowerShell を使用して Azure VM 拡張機能を有効にする

この記事では、Azure PowerShell を使用して、Azure Arc 対応サーバーでサポートされている Azure VM 拡張機能を Linux または Windows ハイブリッド マシンにデプロイ、更新、アンインストールする方法を示します。

> [!NOTE]
> Azure Arc 対応サーバーでは、Azure 仮想マシンへの VM 拡張機能のデプロイと管理はサポートされていません。 Azure VM については、次の [VM 拡張機能の概要](../../virtual-machines/extensions/overview.md) に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

- Azure PowerShell を搭載するコンピューター 手順については、 [Azure PowerShell のインストールおよび構成](/powershell/azure/)を参照してください。

Azure PowerShell を使用して、Azure Arc 対応サーバーで管理されているハイブリッド サーバー上の VM 拡張機能を管理するには、事前に `Az.ConnectedMachine` モジュールをインストールする必要があります。 これらの管理操作はワークステーションから実行できます。Azure Arc 対応サーバー上で実行する必要はありません。

Azure Arc 対応サーバーで次のコマンドを実行します。

`Install-Module -Name Az.ConnectedMachine`.

インストールが完了すると、次のメッセージが返されます。

`The installed extension `Az.ConnectedMachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>拡張機能を有効にする

Azure Arc 対応サーバーで VM 拡張機能を有効にするには、[New-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/new-azconnectedmachineextension) と `-Name`、`-ResourceGroupName`、`-MachineName`、`-Location`、`-Publisher`、-`ExtensionType`、`-Settings` の各パラメーターを使用します。

次の例では、Azure Arc 対応 Linux サーバーで Log Analytics VM 拡張機能を有効にします。

```powershell
$Setting = @{ "workspaceId" = "workspaceId" }
$protectedSetting = @{ "workspaceKey" = "workspaceKey" }
New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachineName" -Location "regionName" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType "OmsAgentForLinux"
```

Azure Arc 対応 Windows サーバーで Log Analytics VM 拡張機能を有効にするには、前の例の `-ExtensionType` パラメーターの値を `"MicrosoftMonitoringAgent"` に変更します。

次の例では、Azure Arc 対応サーバーでカスタム スクリプト拡張機能を有効にします。

```powershell
$Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
New-AzConnectedMachineExtension -Name "custom" -ResourceGroupName "myResourceGroup" -MachineName "myMachineName" -Location "regionName" -Publisher "Microsoft.Compute"  -Settings $Setting -ExtensionType CustomScriptExtension
```

次の例では、Azure Arc 対応 Windows サーバーで Microsoft Antimalware 拡張機能を有効にします。

```powershell
$Setting = @{ "AntimalwareEnabled" = $true }
New-AzConnectedMachineExtension -Name "IaaSAntimalware" -ResourceGroupName "myResourceGroup" -MachineName "myMachineName" -Location "regionName" -Publisher "Microsoft.Azure.Security" -Settings $Setting -ExtensionType "IaaSAntimalware"
```

### <a name="key-vault-vm-extension"></a>Key Vault VM 拡張機能

> [!WARNING]
> 多くの場合、PowerShell クライアントでは、`[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.` エラーを伴って akvvm_service が失敗する原因となる settings.json で、`\` が `"` に追加されます。

次の例では、Azure Arc 対応サーバーで Key Vault VM 拡張機能を有効にします。

```powershell
# Build settings
    $settings = @{
      secretsManagementSettings = @{
       observedCertificates = @(
        "observedCert1"
       )
      certificateStoreLocation = "myMachineName" # For Linux use "/var/lib/waagent/Microsoft.Azure.KeyVault.Store/"
      certificateStore = "myCertificateStoreName"
      pollingIntervalInS = "pollingInterval"
      }
    authenticationSettings = @{
     msiEndpoint = "http://localhost:40342/metadata/identity"
     }
    }

    $resourceGroup = "resourceGroupName"
    $machineName = "myMachineName"
    $location = "regionName"

    # Start the deployment
    New-AzConnectedMachineExtension -ResourceGroupName $resourceGRoup -Location $location -MachineName $machineName -Name "KeyVaultForWindows or KeyVaultforLinux" -Publisher "Microsoft.Azure.KeyVault" -ExtensionType "KeyVaultforWindows or KeyVaultforLinux" -Setting (ConvertTo-Json $settings)
```

## <a name="list-extensions-installed"></a>インストールされている拡張機能を一覧表示する

Azure Arc 対応サーバー上の VM 拡張機能の一覧を取得するには、[Get-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) と `-MachineName` および `-ResourceGroupName` パラメーターを使用します。

例:

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="update-extensions"></a>拡張機能の更新

インストールされた拡張機能を再構成するには、[Update-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/update-azconnectedmachineextension) コマンドレットを `-Name`、`-MachineName`、`-ResourceGroupName`、`-Settings` の各パラメーターと共に使用します。

拡張機能に必要な変更を行うためのさまざまな方法については、コマンドレットのリファレンス記事を参照してください。

## <a name="remove-extensions"></a>拡張機能を削除する

Azure Arc 対応サーバー上にインストールされた VM 拡張機能を削除するには、[Remove-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension) と `-Name`、`-MachineName`、`-ResourceGroupName` の各パラメーターを使用します。

たとえば、Linux 用の Log Analytics VM 拡張機能を削除するには、次のコマンドを実行します。

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>次のステップ

- [Azure CLI](manage-vm-extensions-cli.md)、[Azure portal](manage-vm-extensions-portal.md)、または [Azure Resource Manager テンプレート](manage-vm-extensions-template.md)を使用して、VM 拡張機能をデプロイ、管理、削除できます。

- トラブルシューティングに関する情報は、[VM 拡張機能のトラブルシューティング ガイド](troubleshoot-vm-extensions.md)に記載されています。
