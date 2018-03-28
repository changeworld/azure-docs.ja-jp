---
title: Azure 仮想マシン スケール セットのディスク暗号化 | Microsoft Docs
description: Azure PowerShell を使用して、仮想マシン スケール セット内の VM インスタンスと接続ディスクを暗号化する方法について説明します。
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: iainfou
ms.openlocfilehash: 856d4bc7dd636b3a2f3d072a10989cafd7efd6a6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set"></a>仮想マシン スケール セット内の OS および接続されているデータ ディスクを暗号化する
業界標準の暗号化テクノロジを使用して保存データを保護および防御するために、仮想マシン スケール セットは Azure Disk Encryption (ADE) をサポートしています。 Windows および Linux の仮想マシン スケール セットで暗号化を有効にすることができます。 詳細については、[Windows 用および Linux 用の Azure Disk Encryption](../security/azure-security-disk-encryption.md) に関するページを参照してください。

> [!NOTE]
>  仮想マシン スケール セット用 Azure Disk Encryption は、現在プレビュー段階であり、すべての Azure パブリック リージョンで利用できます。 
>
> 現在のプレビューでは、スケール セット VM の再イメージ操作とアップグレード操作はサポートされていません。 プレビューでは、テスト環境でのみスケール セットの暗号化を使用することをお勧めします。 プレビューでは、OS イメージをアップグレードする必要のある運用環境でディスクの暗号化を有効にしないでください。

Azure Disk Encryption は次の場合にサポートされます。
- 管理ディスクで作成されたスケール セット。ネイティブ (または管理対象ではない) ディスク スケール セットの場合はサポートされません。
- Windows スケール セット内の OS とデータ ボリューム。 Windows スケール セットの OS とデータ ボリュームでは、暗号化の無効化がサポートされています。
- Linux スケール セット内のデータ ボリューム。 Linux スケール セット用の現在のプレビューでは、OS ディスクの暗号化はサポートされていません。


## <a name="prerequisites"></a>前提条件
この記事では、Azure PowerShell モジュール バージョン 5.3.0 以降が必要です。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

[Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) を使用して、仮想マシン スケール セットのディスク暗号化をプレビューするための Azure サブスクリプションを登録します。 

```powershell
Login-AzureRmAccount
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

[Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature) から *Registered* 状態が返されるまで約 10 分間待ってから、`Microsoft.Compute` プロバイダーを再登録します。 

```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```


## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>ディスクの暗号化を有効にした Azure Key Vault を作成する
Azure Key Vault は、キー、シークレット、パスワードを格納して、アプリケーションとサービスに安全に実装できるようにします。 暗号化キーは、ソフトウェア保護を使って Azure Key Vault に格納されます。または、FIPS 140-2 レベル 2 標準に認定された Hardware Security Module (HSM) でキーをインポートまたは生成することもできます。 これらの暗号化キーは、VM に接続された仮想ディスクの暗号化/暗号化解除に使われます。 これらの暗号化キーの制御を維持し、その使用を監査することができます。

[New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) を使用して、Key Vault を作成します。 ディスク暗号化に Key Vault を使用できるようにするには、*EnabledForDiskEncryption* パラメーターを設定します。 次の例では、リソース グループ名、Key Vault 名、および場所の変数も定義しています。 独自の Key Vault 名を指定します。

```powershell
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzureRmResourceGroup -Name $rgName -Location $location
New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```


### <a name="use-an-existing-key-vault"></a>既存の Key Vault を使用する
この手順は、ディスク暗号化で使用する既存の Key Vault がある場合にのみ必要です。 前のセクションで Key Vault を作成した場合は、この手順をスキップしてください。

[Set-AzureRmKeyVaultAccessPolicy](/powershell/module/AzureRM.KeyVault/Set-AzureRmKeyVaultAccessPolicy) を使用して、ディスク暗号化のスケール セットと同じサブスクリプションとリージョン内にある既存の Key Vault を有効にすることができます。 次のように、*$vaultName* 変数で既存の Key Vault 名を定義します。

```powershell
$vaultName="myexistingkeyvault"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```


## <a name="create-a-scale-set"></a>スケール セットを作成する
まず、[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) を使用して、VM インスタンスの管理者のユーザー名とパスワードを設定します。

```powershell
$cred = Get-Credential
```

それでは、仮想マシン スケール セットの作成に移りましょう。これには、[New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) を使います。 個々の VM インスタンスにトラフィックを分散するために、ロード バランサーも作成されます。 ロード バランサーには、TCP ポート 80 上のトラフィックを分散するルールだけでなく、TCP ポート 3389 上のリモート デスクトップ トラフィックと TCP ポート 5985 上の PowerShell リモート処理を許可するルールも含まれています。

```powershell
$vmssName="myScaleSet"

New-AzureRmVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```


## <a name="enable-encryption"></a>暗号化を有効にする
スケールセット内の VM インスタンスを暗号化するには、まず [Get-AzureRmKeyVault](/powershell/module/AzureRM.KeyVault/Get-AzureRmKeyVault) を使用して Key Vault URI とリソース ID に関する一部の情報を取得します。 [Set-AzureRmVmssDiskEncryptionExtension](/powershell/module/AzureRM.Compute/Set-AzureRmVmssDiskEncryptionExtension) でこれらの変数を使用して暗号化プロセスを開始します。

```powershell
$diskEncryptionKeyVaultUrl=(Get-AzureRmKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzureRmKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

プロンプトが表示されたら、「*y*」と入力して、スケール セット VM インスタンス上のディスク暗号化プロセスを続行します。


## <a name="check-encryption-progress"></a>暗号化の進行状況を確認する
ディスク暗号化の状態を確認するには、[Get-AzureRmVmssDiskEncryption](/powershell/module/AzureRM.Compute/Get-AzureRmVmssDiskEncryption) を使用します。

```powershell
Get-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

VM インスタンスが暗号化されると、*EncryptionSummary* コードで、次の出力例のように *ProvisioningState/succeeded* コードが報告されます。

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```


## <a name="disable-encryption"></a>暗号化を無効にする
暗号化された VM インスタンス ディスクを使用しない場合は、次のように [Disable-AzureRmVmssDiskEncryption](/powershell/module/AzureRM.Compute/Disable-AzureRmVmssDiskEncryption) で暗号化を無効にすることができます。

```powershell
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```


## <a name="next-steps"></a>次の手順
この記事では、Azure PowerShell を使用して仮想マシン スケール セットを暗号化しました。 また、[Azure CLI 2.0](virtual-machine-scale-sets-encrypt-disks-cli.md) や、[Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) または [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox) 用のテンプレートを使用することもできます。
