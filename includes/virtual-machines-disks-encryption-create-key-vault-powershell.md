---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/25/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f74228b918b3d87ab77b75132501327b08b25668
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131022050"
---
1. 最新の [Azure PowerShell バージョン](/powershell/azure/install-az-ps)がインストールされており、Connect-AzAccount を使用して Azure アカウントにサインインしていることを確認します。

1. Azure Key Vault と暗号化キーのインスタンスを作成します。

    Key Vault インスタンスを作成する場合、消去保護を有効にする必要があります。 消去保護では、保持期間が経過するまで、削除されたキーを完全に削除できないようになります。 これらの設定は、誤って削除したためにデータが失われるのを防ぎます。 これらの設定は、Key Vault を使用してマネージド ディスクを暗号化する場合は必須です。
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName `
    -ResourceGroupName $ResourceGroupName `
    -Location $LocationName `
    -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName `
          -Name $keyName `
          -Destination $keyDestination 
    ```

1.    DiskEncryptionSet のインスタンスを作成します。 RotationToLatestKeyVersionEnabled を $true に設定すると、キーの自動ローテーションを有効にできます。 自動ローテーションを有効にすると、ディスク暗号化セットを参照するすべてのマネージド ディスク、スナップショット、およびイメージがシステムによって自動的に更新され、1 時間以内に新しいバージョンのキーが使用されます。  
    
        ```powershell
      $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName `
            -SourceVaultId $keyVault.ResourceId `
            -KeyUrl $key.Key.Kid `
            -IdentityType SystemAssigned `
            -RotationToLatestKeyVersionEnabled $false

       $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName `
               -ResourceGroupName $ResourceGroupName `
               -InputObject $desConfig
        ```

1.    DiskEncryptionSet リソースに Key Vault へのアクセス権を付与します。

        > [!NOTE]
        > Azure がお使いの Azure Active Directory にご自分の DiskEncryptionSet の ID を作成するのには数分かかる場合があります。 次のコマンドを実行しているときに "Active Directory オブジェクトが見つかりません" のようなエラーが表示された場合は、数分待ってから再試行してください。
        
        ```powershell  
        Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
        ```

### <a name="use-a-key-vault-in-a-different-subscription"></a>別なサブスクリプションのキー コンテナーを使用する

また、1 つのサブスクリプションから Azure Key vault を一元的に管理し、Key Vault に格納されているキーを使用して、組織内の他のサブスクリプションのマネージド ディスクとスナップショットを暗号化することもできます。 これによりセキュリティチームは、単一のサブスクリプションに対する堅牢なセキュリティポリシーを適用し、簡単に管理することができます。

> [!IMPORTANT]
> この構成では、Key Vault とディスク暗号化セットの両方が同じリージョンにあり、同じテナントを使用している必要があります。

次のスクリプトは、同じリージョンで別のサブスクリプションの Key Vault にあるキーを使用するようにディスク暗号化セットを構成する方法の一例です。

```azurepowershell
$sourceSubscriptionId="<sourceSubID>"
$sourceKeyVaultName="<sourceKVName>"
$sourceKeyName="<sourceKeyName>"

$targetSubscriptionId="<targetSubID>"
$targetResourceGroupName="<targetRGName>"
$targetDiskEncryptionSetName="<targetDiskEncSetName>"
$location="<targetRegion>"

Set-AzContext -Subscription $sourceSubscriptionId

$key = Get-AzKeyVaultKey -VaultName $sourceKeyVaultName -Name $sourceKeyName

Set-AzContext -Subscription $targetSubscriptionId

$desConfig=New-AzDiskEncryptionSetConfig -Location $location `
-KeyUrl $key.Key.Kid `
-IdentityType SystemAssigned `
-RotationToLatestKeyVersionEnabled $false

$des=New-AzDiskEncryptionSet -Name $targetDiskEncryptionSetName `
-ResourceGroupName $targetResourceGroupName `
-InputObject $desConfig
```