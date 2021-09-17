---
title: Azure PowerShell の例 - Azure クラウド サービス (延長サポート) を更新する
description: Azure クラウド サービス (延長サポート) のデプロイを更新するサンプル スクリプト
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 22d02ef7c395ae6f7cdbbf739ed5107436bd169c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751096"
---
# <a name="update-an-azure-cloud-service-extended-support"></a>Azure Cloud Services (延長サポート) を更新する

ここに挙げたサンプルでは、Azure クラウド サービス (延長サポート) の既存のデプロイを更新する際のさまざまな方法を紹介します。

## <a name="add-an-extension-to-existing-cloud-service"></a>既存のクラウド サービスに拡張機能を追加する
次の一連のコマンドは、ContosOrg という名前のリソース グループに属する、ContosoCS という名前の既存のクラウド サービスに RDP 拡張機能を追加します。
```powershell
# Create RDP extension object
$rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name "RDPExtension" -Credential $credential -Expiration $expiration -TypeHandlerVersion "1.2.1"
# Get existing cloud service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Add RDP extension to existing cloud service extension object
$cloudService.ExtensionProfile.Extension = $cloudService.ExtensionProfile.Extension + $rdpExtension
# Update cloud service
$cloudService | Update-AzCloudService
```

## <a name="remove-all-extensions-from-a-cloud-service"></a>クラウド サービスから拡張機能をすべて削除する
次の一連のコマンドは、ContosOrg という名前のリソース グループに属する、ContosoCS という名前の既存のクラウド サービスからすべての拡張機能を削除します。
```powershell
# Get existing cloud service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Set extension to empty list
$cloudService.ExtensionProfile.Extension = @()
# Update cloud service
$cloudService | Update-AzCloudService
```

## <a name="remove-the-remote-desktop-extension-from-cloud-service"></a>クラウド サービスからリモート デスクトップ拡張機能を削除する
次の一連のコマンドは、ContosOrg という名前のリソース グループに属する、ContosoCS という名前の既存のクラウド サービスから RDP 拡張機能を削除します。
```powershell
# Get existing cloud service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Remove extension by name RDPExtension
$cloudService.ExtensionProfile.Extension = $cloudService.ExtensionProfile.Extension | Where-Object { $_.Name -ne "RDPExtension" }
# Update cloud service
$cloudService | Update-AzCloudService
```

## <a name="scale-out--scale-in-role-instances"></a>ロール インスタンスをスケールアウトまたはスケールインする
次の一連のコマンドは、ContosOrg という名前のリソース グループに属する、ContosoCS という名前のクラウド サービスのロール インスタンス数をスケールアウトおよびスケールインする方法を示しています。
```powershell
# Get existing cloud service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

# Scale-out all role instance count by 1
$cloudService.RoleProfile.Role | ForEach-Object {$_.SkuCapacity += 1}

# Scale-in ContosoFrontend role instance count by 1
$role = $cloudService.RoleProfile.Role | Where-Object {$_.Name -eq "ContosoFrontend"}
$role.SkuCapacity -= 1

# Update cloud service configuration as per the new role instance count
$cloudService.Configuration = $configuration

# Update cloud service
$cloudService | Update-AzCloudService
```

## <a name="next-steps"></a>次のステップ
Azure Cloud Services (延長サポート) の詳細については、[Azure Cloud Services (延長サポート) の概要](overview.md)に関するページを参照してください。
- [Cloud Services (延長サポート) のサンプル リポジトリ](https://github.com/Azure-Samples/cloud-services-extended-support)を確認します。
