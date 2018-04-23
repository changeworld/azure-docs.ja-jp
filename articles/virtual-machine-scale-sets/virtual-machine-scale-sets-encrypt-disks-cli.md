---
title: Azure 仮想マシン スケール セットでディスクを暗号化する | Microsoft Docs
description: 仮想マシン スケール セット内の接続されているディスクを暗号化する方法について説明します。
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
ms.date: 01/26/2018
ms.author: iainfou
ms.openlocfilehash: 570764ad5d657a8b1efa2425423a89ddc518451c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set"></a>仮想マシン スケール セット内の OS および接続されているデータ ディスクを暗号化する
[Azure 仮想マシン スケール セット](/azure/virtual-machine-scale-sets/)は、Azure Disk Encryption (ADE) をサポートしています。  Windows および Linux の仮想マシン スケール セットで Azure Disk Encryption を有効にすると、業界標準の暗号化テクノロジを使用して、保存されているスケール セット データを保護および防御することができます。 詳細については、Windows および Linux 仮想マシン用 Azure Disk Encryption に関するドキュメントを参照してください。

> [!NOTE]
>  仮想マシン スケール セット用 Azure Disk Encryption は、現在パブリック プレビュー段階であり、すべての Azure パブリック リージョンで利用できます。

Azure Disk Encryption は次の場合にサポートされます。
- 管理ディスクで作成されたスケール セット。ネイティブ (または管理対象ではない) ディスク スケール セットの場合はサポートされません。
- Windows スケール セット内の OS とデータ ボリューム。 Windows スケール セットの OS とデータ ボリュームでは、暗号化の無効化がサポートされています。
- Linux スケール セット内のデータ ボリューム。 Linux スケール セット用の現在のプレビューでは、OS ディスクの暗号化はサポートされていません。

現在のプレビューでは、スケール セット VM の再イメージ操作とアップグレード操作はサポートされていません。 仮想マシンのスケール セット プレビューの Azure Disk Encryption は、テスト環境でのみ推奨されます。 プレビューでは、暗号化スケール セット内の OS イメージをアップグレードする必要のある運用環境でディスクの暗号化を有効にしないでください。

Linux スケール セット データ ディスクの暗号化のエンドツーエンド バッチ ファイルの例については、[こちら](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat)を参照してください。  この例では、リソース グループ、Linux スケール セットを作成し、5 GB のデータ ディスクをマウントし、仮想マシン スケール セットを暗号化します。

## <a name="prerequisites"></a>前提条件
暗号化コマンドが含まれる [Azure Powershell](https://github.com/Azure/azure-powershell/releases) または [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) の最新バージョンをインストールします。

仮想マシン スケール セット (プレビュー) の Azure Disk Encryption では、次の PowerShell コマンドを使用してサブスクリプションを自己登録する必要があります。 

```powershell
Connect-AzureRmAccount
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

次のコマンドで 'Registered' 状態が返されるまで約 10 分間待ちます。 

```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>ディスクの暗号化を有効にした Azure キー コンテナーを作成する
スケール セットと同じサブスクリプションとリージョンで新しいキー コンテナーを作成し、'EnabledForDiskEncryption' アクセス ポリシーを設定します。

```azurecli
rgname="linuxdatadiskencryptiontest"
VaultName="encryptionvault321"

az keyvault create --name $VaultName --resource-group $rgname --enabled-for-disk-encryption
```

または、ディスクの暗号化用のスケール セットと同じサブスクリプションとリージョンで既存のキー コンテナーを有効にします。

```azurecli
VaultName="encryptionvault321"
az keyvault update --name $VaultName --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>暗号化を有効にする

次のコマンドは、同じリソース グループのキー コンテナーを使用して、実行中のスケール セット内のデータ ディスクを暗号化します。 また、テンプレートを使用して、実行中の [Windows スケール セット](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)または [Linux スケール セット](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)内のディスクを暗号化することもできます。

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"
EncryptionKeyVaultUrl="https://encryptionvaultlinuxsf.vault.azure.net"
VaultResourceId="/subscriptions/0754ecc2-d80d-426a-902c-b83f4cfbdc95/resourceGroups/linuxdatadiskencryptiontest/providers/Microsoft.KeyVault/vaults/encryptionvaultlinuxsf"

az vmss encryption enable -g $ResourceGroup -n $VmssName --disk-encryption-keyvault $VaultResourceId --volume-type DATA
az vmss update-instances -g $ResourceGroup -n $VmssName --instance-ids *
```

## <a name="check-encryption-progress"></a>暗号化の進行状況を確認する

スケール セットの暗号化の状態を表示するには、次のコマンドを使用します。

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"

az vmss encryption show -g $ResourceGroup -n $VmssName
```

## <a name="disable-encryption"></a>暗号化を無効にする
実行中の仮想マシン スケール セットで暗号化を無効にするには、次のコマンドを使用します。 また、テンプレートを使用して、実行中の [Windows VM スケール セット](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)または [Linux VM スケール セット](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)で暗号化を無効にすることもできます。

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"

az vmss encryption disable -g $ResourceGroup -n $VmssName
```

