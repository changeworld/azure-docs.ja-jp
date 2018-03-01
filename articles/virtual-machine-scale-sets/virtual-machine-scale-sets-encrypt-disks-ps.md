---
title: "Azure 仮想マシン スケール セットのディスクの暗号化 | Microsoft Docs"
description: "接続されたディスクを仮想マシン スケール セット内に暗号化する方法について説明します。"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: iainfou
ms.openlocfilehash: dddcece9f7566961b256369330661e5dbd5d4665
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set"></a>OS と接続されたデータ ディスクの仮想マシン スケール セット内への暗号化
Azure [仮想マシン スケール セット](/azure/virtual-machine-scale-sets/)では、Azure Disk Encryption (ADE) をサポートします。  Windows と Linux の仮想マシン スケール セットに対して Azure Disk Encryption を有効にして、業界標準の暗号化テクノロジを使用して保存時のスケール セット データを保護できます。 詳細については、Windows と Linux の仮想マシン用の Azure Disk Encryption に関する記事をご覧ください。

> [!NOTE]
>  仮想マシン スケール セット用の Azure Disk Encryption は、現在パブリック プレビュー段階であり、すべての Azure パブリック リージョンで利用可能です。

Azure Disk Encryption のサポート対象を次に示します。
- 管理対象ディスクを使用して作成されたスケール セット。ネイティブ (アンマネージ) ディスク スケール セットはサポートされません。
- Windows スケール セット内の OS とデータ ボリューム。 Windows スケール セット内の OS と データ ボリュームに対する暗号化の無効化がサポートされます。
- Linux スケール セット内のデータ ボリューム。 Linux スケール セットの現在のプレビューでは、OS ディスクの暗号化はサポートされません。

スケール セット VM の再イメージ化操作とアップグレード操作は、現在のプレビューではサポートされません。 仮想マシン スケール セット用の Azure Disk Encryption のプレビューは、テスト環境でのみ推奨されます。 このプレビューでは、実稼働環境でのディスクの暗号化は有効にしないでください。実稼働環境では、暗号化されるスケール セット内の OS イメージをアップグレードする必要があります。

## <a name="prerequisites"></a>前提条件
暗号化コマンドが含まれている最新バージョンの [Azure Powershell](https://github.com/Azure/azure-powershell/releases) をインストールします。

仮想マシン スケール セット用の Azure Disk Encryption のプレビューでは、次の PowerShell コマンドを使用して、サブスクリプションを自己登録する必要があります。 

```powershell
Login-AzureRmAccount
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

次のコマンドで '登録済み' 状態が返されるまで、約 10 分間待機します。 

```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>ディスクの暗号化が有効な Azure キー コンテナーを作成する
スケール セットと同じサブスクリプションとリージョン内に新しいキー コンテナーを作成し、'EnabledForDiskEncryption' アクセス ポリシーを設定します。

```powershell
$rgname="windatadiskencryptiontest"
$VaultName="encryptionvault321"

New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $rgName -Location southcentralus -EnabledForDiskEncryption
``` 

または、ディスク暗号化用のスケール セットと同じサブスクリプションとリージョン内の既存のキー コンテナーを有効にします。

```powershell
$VaultName="encryptionvault321"
Set-AzureRmKeyVaultAccessPolicy -VaultName $VaultName -EnabledForDiskEncryption
```

## <a name="enable-encryption"></a>暗号化を有効にする
次のコマンドは、同じリソース グループ内のキー コンテナーを使用して、実行中のスケール セット内にデータ ディスクを暗号化します。 テンプレートを使用して、ディスクを実行中の [Windows スケール セット](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)または [Linux スケール セット](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)内に暗号化することもできます。

```powershell
$rgname="windatadiskencryptiontest"
$VmssName="nt1vm"
$DiskEncryptionKeyVaultUrl="https://encryptionvault321.vault.azure.net"
$KeyVaultResourceId="/subscriptions/0754ecc2-d80d-426a-902c-b83f4cfbdc95/resourceGroups/windatadiskencryptiontest/providers/Microsoft.KeyVault/vaults/encryptionvault321"

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName `
    -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId –VolumeType Data
```

## <a name="check-encryption-progress"></a>暗号化の進行状況をチェックする
次のコマンドを使用して、スケール セットの暗号化の状態を表示します。

```powershell
$rgname="windatadiskencryptiontest"
$VmssName="nt1vm"
Get-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

Get-AzureRmVmssVMDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName -InstanceId "4"
```

## <a name="disable-encryption"></a>暗号化を無効にする
次のコマンドを使用して、実行中の仮想マシン スケール セット上の暗号化を無効にします。 テンプレートを使用して、実行中の [Windows スケール セット](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)または [Linux スケール セット](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)内の暗号化を無効にすることもできます。

```powershell
$rgname="windatadiskencryptiontest"
$VmssName="nt1vm"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName
```
