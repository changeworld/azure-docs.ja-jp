---
title: ディスク上の Azure Disk Encryption をアップグレードする方法
description: この記事では、ディスク上の Azure Disk Encryption をアップグレードする手順について説明します
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.author: mbaldwin
ms.date: 05/27/2021
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 0cdc32e303de7d9a26a99d1f08188dc54539930b
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111758240"
---
# <a name="upgrading-the-azure-disk-encryption-version"></a>Azure Disk Encryption バージョンのアップグレード

最初のバージョンの Azure Disk Encryption (ADE) は、認証に Azure Active Directory (AAD) を使用していましたが、現在のバージョンは違います。  最新バージョンを使用することを強くお勧めします。

## <a name="determine-ade-version"></a>ADE のバージョンを確認する

移行対象の ADE のバージョンは次のとおりです。
- **Windows:** 1.1.* (VM 上の ADE は 2.2 にアップグレードする必要があります)
- **Linux:** 0.1.* (VM 上の ADE は 1.2 にアップグレードする必要があります)

VM が暗号化された ADE のバージョンは、Azure CLI、Azure PowerShell、Azure portal のいずれかで確認できます。

# <a name="cli"></a>[CLI](#tab/CLI)

ADE のバージョンを確認するには、Azure CLI の [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view) コマンドを実行します。

```azurecli-interactive
az vm get-instance-view --resource-group  <ResourceGroupName> --name <VMName> 
```

出力から AzureDiskEncryption 拡張機能を見つけて、出力内の "TypeHandlerVersion" フィールドからバージョン番号を特定します。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

ADE のバージョンを確認するには、Azure PowerShell の [Get-AzVM](/powershell/module/az.compute/get-azvm) を実行します。

```azurepowershell-interactive
Get-AzVM -ResourceGroupName <ResourceGroupName> -Name <VMName> -Status
```

出力から AzureDiskEncryption 拡張機能を見つけて、出力内の "TypeHandlerVersion" フィールドからバージョン番号を特定します。

# <a name="portal"></a>[ポータル](#tab/Portal)

Azure portal 内で、お使いの VM の [拡張機能] ブレードに移動します。

:::image type="content" source="../media/disk-encryption/ade-version-1.png" alt-text="ADE バージョン検索のポータルのスクリーンショット 1":::

Windows 用の "AzureDiskEncryption" 拡張機能または Linux 用の "AzureDiskEncryptionForLinux" 拡張機能を選択し、[バージョン] フィールドでバージョン番号を見つけます。

:::image type="content" source="../media/disk-encryption/ade-version-2.png" alt-text="ADE バージョン検索のポータルのスクリーンショット 2":::

---

## <a name="how-to-migrate"></a>移行方法

Azure Disk Encryption (AAD あり) から Azure Disk Encryption (AAD なし) への移行は、Azure PowerShell でのみ行うことができます。 最新バージョンの Azure PowerShell と、[Azure PowerShell Az モジュール バージョン 5.9.0 ](/powershell/azure/new-azureps-module-az)以上がインストールされていることを確認します。

Azure Disk Encryption (AAD あり) から Azure Disk Encryption (AAD なし) にアップグレードするには、PowerShell の [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) コマンドレットを使用します。 

> [!WARNING]
> Set-AzVMDiskEncryptionExtension コマンドレットは、Azure Disk Encryption (AAD あり) を使用して暗号化された VM 上でのみ使用してください。 暗号化されていない VM、または Azure Disk Encryption (AAD なし) で暗号化された VM を移行しようとすると、ターミナル エラーが発生します。

```azurepowershell-interactive
Set-AzVMDiskEncryptionExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Migrate
```

コマンドレットにより確認を求めるダイアログが表示されたら、「Y」と入力します。  ADE バージョンが更新され、VM が再起動されます。 出力は次のようになります。

```bash
> Set-AzVMDiskEncryptionExtension -ResourceGroupName myResourceGroup -VMName myVM -Migrate

Update AzureDiskEncryption version?
This cmdlet updates Azure Disk Encryption version to single pass (Azure Disk Encryption without AAD). This may reboot
the machine and takes 10-15 minutes to finish. Are you sure you want to continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
Azure Disk Encryption Extension Public Settings
"KeyVaultResourceId": /subscriptions/ea500758-3163-4849-bd2c-3e50f06efa7a/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault
"SequenceVersion":
"MigrateFlag": Migrate
"KeyVaultURL": https://myKeyVault.vault.azure.net/
"AADClientID": d29edf8c-3fcb-42e7-8410-9e39fdf0dd70
"KeyEncryptionKeyURL":
"KekVaultResourceId":
"EncryptionOperation": EnableEncryption
"AADClientCertThumbprint":
"VolumeType":
"KeyEncryptionAlgorithm":

Running ADE extension (with AAD) for -Migrate..
ADE extension (with AAD) is now complete. Updating VM model..
Running ADE extension (without AAD) for -Migrate..
ADE extension (without AAD) is now complete. Clearing VM model..

RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```


> [!IMPORTANT]
> アップグレードが完了するには、少なくとも 10 分から 15 分かかります。 アップグレードの進行中に、コマンドレットを取り消さないでください。 これにより、VM の正常性がリスクにさらされます。

## <a name="next-steps"></a>次のステップ

- [Azure Disk Encryption のトラブルシューティング](disk-encryption-troubleshooting.md)
